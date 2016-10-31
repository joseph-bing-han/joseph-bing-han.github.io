---
title: 32位Ubuntu Desktop 10.04编译HiPop for php
id: 107
categories:
  - Linux
  - php
  - 开发相关
date: 2012-12-07 02:58:41
tags:
---

转载请注明来源:[Blog of Joseph Han](http://blog.joseph-han.net/ "Blog of Joseph Han")

最进想实验Facebook的Hipop for php
结果发现官方支持的是64位系统，在我的系统中无法安装。
还有有高人改出了32位系统专用的版本，checkout出来后编译安装成功，特记录下安装过程，以防忘记。

sudo apt-get install git-core cmake g++ libboost-dev flex  bison re2c libmysqlclient-dev libxml2-dev libmcrypt-dev libicu-dev  openssl binutils-dev libcap-dev libgd2-xpm-dev zlib1g-dev libtbb-dev  libonig-dev libpcre3-dev autoconf libtool libcurl4-openssl-dev  libboost-system-dev libboost-program-options-dev  libboost-filesystem-dev wget libmemcached-dev
注：我的系统因为进行过升级，部分内容版本过高，均强制指定使用低版本
cd ~/code/
mkdir make
cd make
git clone git://github.com/metagoto/hiphop-php.git
cd hiphop-php
export CMAKE_PREFIX_PATH=`/bin/pwd`/..
export HPHP_HOME=`/bin/pwd`
export HPHP_LIB=`/bin/pwd`/bin
git submodule init
git submodule update

fatal: reference is not a tree: 208b1a075c2448e1fc0c61d4bb268b161a3d992a
Unable to checkout '208b1a075c2448e1fc0c61d4bb268b161a3d992a' in submodule path 'src/third_party/libmbfl'

这个错误是因为包libmbfl已经改名为libmbf-old，而上面的代码中还没来得及做相应的修改，解决方法是将libmbf-old源代码下载
cd src/third_party/
git clone git@github.com:scottmac/libmbfl-old.git
cp -r libmbfl-old/* libmbfl/

cd ../../../

wget https://github.com/downloads/libevent/libevent/libevent-1.4.13-stable.tar.gz
tar -xzvf libevent-1.4.13-stable.tar.gz

cd libevent-1.4.13-stable/
cp ../hiphop-php/src/third_party/libevent.fb-changes.diff ./
patch -p0 &lt; libevent.fb-changes.diff

这里需要说明的是：-p0 这个参数和官方安装教程不一样，官方用的是 -p1，patch过程中出错的可以都试试。
patch过程非常重要，不能出一点差错，否则后续工作将无法进行，即使安装上了，后面安装hiphop时也会抛出该包不是hiphop版本的错误。

./configure --prefix=$CMAKE_PREFIX_PATH
make
make install

wget http://curl.haxx.se/download/curl-7.20.0.tar.gz
tar -xvzf curl-7.20.0.tar.gz
cd curl-7.20.0
cp ../hiphop-php/src/third_party/libcurl.fb-changes.diff ./
patch -p0 &lt; libcurl.fb-changes.diff
./configure --prefix=$CMAKE_PREFIX_PATH
make
make install
cd ..

git clone git://github.com/bagder/curl.git
cd curl
cat ../hiphop-php/src/third_party/libcurl-7.22.1.fb-changes.diff | patch -p0
./buildconf
./configure --prefix=$CMAKE_PREFIX_PATH
make
make install
cd ..

cd hiphop-php
cmake .
make

安装成功后编译一个test.php试试
~/make/hiphop-php/src/hphp/hphp test.php --keep-tempdir=1 --log=3