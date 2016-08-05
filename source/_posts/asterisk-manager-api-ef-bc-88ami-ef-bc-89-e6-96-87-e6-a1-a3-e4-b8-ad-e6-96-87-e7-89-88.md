---
title: Asterisk manager API（AMI）文档(中文版)
id: 37
categories:
  - asterisk
  - 开发相关
date: 2011-07-10 10:48:15
tags:
---

转自[seven407的专栏](http://blog.csdn.net/seven407/article/details/5292110)

Asterisk控制接口（AMI）允许管理客户端程序连接到一个asterisk实例并且可以通过TCP/IP流发送命令或读取事件。这在试图跟踪asterisk的状态或其中的电话客户端状态时很有用，**AMI**说明了客户端基于的一般(可能是动态的)规则。

一种简单的"key: value"协议用于已连接的管理客户端和asteriskＰＢＸ之间的信息传递。每行都用回车换行（/r/n）来结束。

&nbsp;

**协议特点：**

在发送命令前，你必须建立一个连接。

在认证后，数据包任何时间都可以在两个方向上互相传输。

数据包第一行为"Action"时表明是由管理客户端发送到asterisk，第一行为"Event"或"Response"表明有asterisk发送到管理客户端。数据包内各行顺序可以变化，对数据包没有影响。

回车换行由于区分每一行，空行（两个连续回车换行）用于表明命令结束，Asterisk开始处理命令。

&nbsp;

**数据包类型：**

数据包的类型通过一下几种关键字来划分

Action:一个由管理客户端发送到Asterisk的数据包，请求执行特定的动作。只有有限的几种动作供客户端使用，每个动作是由Asterisk服务器上的模块所决定的。一次只能执行一个动作，该动作数据包包含了动作名称以及执行所必需的参数。

Response:Asterisk对管理客户端动作的响应。

Event: 有关Asterisk核心或扩展模块发生事件的信息。

&nbsp;

**建立管理连接和用户身份验证：**

为了与Asterisk进行管理通话，管理客 户端必须建立到Asterisk服务器侦听端口（一般是5038）的TCP/IP连接，并使用登陆动作进行身份验证。这就要求在Asterisk服务器上 创建用户账户，用户账户在/etc/asterisk/manager.conf中配置。账户由允许登陆的主机，登录密码以及被授予的权限列表组成。

登录和身份验证的经理，你必须发送一个" _登录_ "行动，用您的用户名和秘密（密码作为参数）。 Here is an example:下面是一个例子：

Action: login
Username: admin
Secret: secret

&nbsp;

你还可以通过"Events: off"阻止事件数据包发送到你的连接。例如：

Action: login
Username: admin
Secret: secret
Events: off

&nbsp;

**动作数据包：**

向Asterisk发送动作时，额外的关键字可以提供进一步的动作信息。例如，你可能希望指定号码或通道的断开。此外，如果您的操作会导致Asterisk来执行拨号规则中的条目，你可以传递变量给拨号规则。这与你传递关键字的方式是一样的。

向Asterisk发送动作数据包时，按照以下格式：

Action: &lt;_action type_&gt;&lt;_CRLF_&gt;
&lt;_Key 1_&gt;: &lt;_Value 1_&gt;&lt;_CRLF_&gt;
&lt;_Key 2_&gt;: &lt;_Value 2_&gt;&lt;_CRLF_&gt;
...
Variable: &lt;_Variable 1_&gt;=&lt;_Value 1_&gt;&lt;_CRLF_&gt;
Variable: &lt;_Variable 2_&gt;=&lt;_Value 2_&gt;&lt;_CRLF_&gt;
...
&lt;_CRLF_&gt;

&nbsp;

**管理动作（** **Action** **）：**

通过CLI命令show manager commands的输出结果就是管理动作列表。

具体如下：

**AbsoluteTimeout** **：**这个命令将要求Asterisk在指定秒数后挂断特定的通道，从而有效地结束通话。

参数：

Channel :哪些渠道挂断，如SIP/123-1c20

Timeout :到通道应挂断的秒数

例子：

请求信息

Action: AbsoluteTimeout

Channel: SIP/123-1c20

Timeout: 20

&nbsp;

返回成功信息

Response: Success

Message: Timeout Set

&nbsp;

返回失败信息

Response: Error

Message: No such channel

&nbsp;

**ChangeMonitor** **：**更改记录某通道信息文件名。

参数：

Channel：通道名称

File：文件名

&nbsp;

例子：

发送请求

ACTION: ChangeMonitor

Channel: SIP/x7065558529-1c20

File: 20050103-140105_cc51

&nbsp;

返回失败信息

Response: Error

Message: No such channel

&nbsp;

返回成功信息

Response: Success

Message: Stopped monitoring channel

&nbsp;

**Command** **：**发送Asterisk CLI命令。

参数：

Command：Asterisk CLI 命令。

&nbsp;

例子：

发送请求

ACTION: COMMAND

command: Show Channels

&nbsp;

返回信息

Response: Follows

Channel (Context Extension Pri ) State Appl. Data

0 active channel(s)

--END COMMAND-

&nbsp;

注意：返回的事件响应结果并不是发送一个回车换行（/r/n）,而只发送回车（/n）,如果你在中断处理程序中只等待回车换行，那么这条信息将会被被抛弃。

例如command返回信息如下：

Response: Follows/r/n
Channel Location State Application(Data)/n
SIP/x7065551212b-1af (None) Ringing AppDial((Outgoing Line))/n
SIP/x7065551212c-2aa 29@default:2 Ring Dial(SIP/x7065551212b)/n
2 active channels/n
1 active call/r/n
--END COMMAND--/r/n
/r/n

&nbsp;

&nbsp;

**Events** **：**事件控制流，用于启用/禁用发送给管理客户端的事件。

参数：

EventMask: 'on'表示所有事件均会被发送到管理客户端。

'off'表示所有时间都不会被发送。

'system,call,log'用于选择被发送的事件。

提示：有些事件是启用或禁已经在.conf文件中配置了， They might be disabled by default.他们可能是默认为禁用。

&nbsp;

例子：

注意在此例子中EventMask事件没有立即返回的响应信息，只有在设备注销时才返回相关信息。

发送请求：

ACTION: EVENTS
EVENTMASK: ON
返回信息：
Event: PeerStatus
Peer: SIP/ChannelName
PeerStatus: Unregistered
Cause: Expired

&nbsp;

当EventMask参数为'off'时会立即返回响应信息，如下：

发送：

ACTION: EVENTS
EVENTMASK: OFF
返回：
Response: Events Off

&nbsp;

**ExtensionState** **：**检查分机的状态。

参数：Exten，Context，ActionID

&nbsp;

例子：

发送：

ACTION: ExtensionState

Context: default

Exten: 2001

ActionID: 1

&nbsp;

接收：

Response: Success

ActionID: 1

Message: Extension Status

Exten: 2001

Context: default

Hint: SIP/2001

Status: 0

&nbsp;

Status状态代码：

-1 =扩展找不到

0 =空闲

1 =在使用

2 =忙

4 =不可用

8 =振铃

16 =正等待

&nbsp;

**GetVar**：获取一个通道变量。

参数：Channel，Variable，actionID

ActionID是一个可选的参数匹配信息。

&nbsp;

例子：

发送：

ACTION: GetVar

Channel: SIP/5060-44d225d0

Variable: extension

成功返回：

Response: Success

Value: 17065551419

失败返回：

Response: Error

Message: No such channel

或

Response: Success

varname: (null)

&nbsp;

Hangup：挂断指定通道。

参数：Channel

&nbsp;

例子：

&nbsp;

发送：

ACTION: Hangup

Channel: SIP/x7065558529-99a0

&nbsp;

接收：

Event: Unlink

Channel1: SIP/127.180.254.109-44df88e8

Channel2: SIP/x7065558529-99a0

Uniqueid1: 1124989110.20473

Uniqueid2: 1124989110.20474

&nbsp;

Event: Hangup

Channel: SIP/x7065558529-99a0

Uniqueid: 1124989110.20474

Cause: 16

&nbsp;

Event: Hangup

Channel: SIP/127.180.254.109-44df88e8

Uniqueid: 1124989110.20473

Cause: 16

&nbsp;

Response: Success

Message: Channel Hungup

&nbsp;

**IAXpeers** **：**显示iax信息列表。

例子：

&nbsp;

发送：

Action: IAXPeers

&nbsp;

接收：

Name/Username Host Mask Port Status

8002 (Unspecified) (D) 255.255.255.255 0 UNKNOWN

8001 192.168.0.91 (D) 255.255.255.255 4569 OK (5 ms)

2 iax2 peers [1 online, 1 offline, 0 unmonitored]

&nbsp;

**ListCommands** **：**显示命令列表。

参数：ActionID

&nbsp;

例子:

发送：

Action：ListCommands

接收：Response: Success

AbsoluteTimeout: Set Absolute Timeout (Priv: call,all)

AgentCallbackLogin: Sets an agent as logged in by callback (Priv: agent,all)

AgentLogoff: Sets an agent as no longer logged in (Priv: agent,all)

Agents: Lists agents and their status (Priv: agent,all)

ChangeMonitor: Change monitoring filename of a channel (Priv: call,all)

Command: Execute Asterisk CLI Command (Priv: command,all)

DBGet: Get DB Entry (Priv: system,all)

DBPut: Put DB Entry (Priv: system,all)

Events: Control Event Flow (Priv: )

ExtensionState: Check Extension Status (Priv: call,all)

Getvar: Gets a Channel Variable (Priv: call,all)

Hangup: Hangup Channel (Priv: call,all)

IAXnetstats: Show IAX Netstats (Priv: )

IAXpeers: List IAX Peers (Priv: )

ListCommands: List available manager commands (Priv: )

Logoff: Logoff Manager (Priv: )

MailboxCount: Check Mailbox Message Count (Priv: call,all)

MailboxStatus: Check Mailbox (Priv: call,all)

MeetmeMute: Mute a Meetme user (Priv: call,all)

MeetmeUnmute: Unmute a Meetme user (Priv: call,all)

Monitor: Monitor a channel (Priv: call,all)

Originate: Originate Call (Priv: call,all)

Park: Park a channel (Priv: call,all)

ParkedCalls: List parked calls (Priv: )

PauseMonitor: Pause monitoring of a channel (Priv: call,all)

Ping: Keepalive command (Priv: )

PlayDTMF: Play DTMF signal on a specific channel. (Priv: call,all)

QueueAdd: Add interface to queue. (Priv: agent,all)

QueuePause: Makes a queue member temporarily unavailable (Priv: agent,all)

QueueRemove: Remove interface from queue. (Priv: agent,all)

Queues: Queues (Priv: )

QueueStatus: Queue Status (Priv: )

Redirect: Redirect (transfer) a call (Priv: call,all)

SetCDRUserField: Set the CDR UserField (Priv: call,all)

Setvar: Set Channel Variable (Priv: call,all)

SIPpeers: List SIP peers (text format) (Priv: system,all)

SIPshowpeer: Show SIP peer (text format) (Priv: system,all)

Status: Lists channel status (Priv: call,all)

StopMonitor: Stop monitoring a channel (Priv: call,all)

UnpauseMonitor: Unpause monitoring of a channel (Priv: call,all)

UserEvent: Send an arbitrary event (Priv: user,all)

WaitEvent: Wait for an event to occur (Priv: )

ZapDialOffHook: Dial over channel while offhook (Priv: )

ZapDNDoff: Toggle channel Do Not Disturb status OFF (Priv: )

ZapDNDon: Toggle channel Do Not Disturb status ON (Priv: )

ZapHangup: Hangup Channel (Priv: )

ZapRestart: Fully Restart channels (terminates calls) (Priv: )

ZapShowChannels: Show status channels (Priv: )

ZapTransfer: Transfer Channel (Priv: )

&nbsp;

**Logoff** **：**注销管理客户端。

例子：

发送：

ACTION: LOGOFF

接收：

Response: Goodbye

Message: Thanks for all the fish.

&nbsp;

**MailboxCount** **：**检查语音信箱是否有新信息。

参数：MailBox，actionID

例子：

发送：

Action: MailboxCount

Mailbox: 2001@device

接收:

Response: Success

Message: Mailbox Message Count

Mailbox: 2001@device

NewMessages: 1

OldMessages: 0

&nbsp;

**MailboxStatus:**检查语音信箱状态。

参数：MailBox，actionID

例子：

发送：

Action: MailboxStatus

Mailbox: 2001@device

接收:

Response: Success

Message: Mailbox Status

Mailbox: 2001@device

Waiting: 1

&nbsp;

Monitor：监听某个通道。

参数：Channel, File, Format, Mix

例子：

发送:

ACTION: Monitor

Channel: SIP/x7062618529-643d

File: channelsavefile

Mix: 1

&nbsp;

返回：

Response: Success

Message: Started monitoring channel

&nbsp;

失败返回

Response: Error

Message: No such channel

&nbsp;

**Originate** **：**产生一个呼叫。**(privilege: call,all)**

参数：

Channel：呼叫连接产生的通道。

Context：呼叫连接所使用的Context（Context，Exten，Priority必须同时使用）

Exten： 呼叫连接所使用的分机（Context，Exten，Priority必须同时使用）

&nbsp;

Priority：呼叫连接的优先级。（Context，Exten，Priority必须同时使用）

TimeOut：呼叫连接的超时（，单位为毫秒，默认为30000毫秒）

CallID：呼叫连接的来电显示。

Variable：通道变量设置。本地通道和连接通道都将被设置。

Account：呼叫所用密码。

Application：使用此次呼叫的应用程序。（使用参数和数据）

Data：应用程序参数所使用的数据。

Async：呼叫是否为异步。（允许多次调用不等待响应生成）

ActionID**：**请 求标识符。 It allows you to identify the response to this  request.它允许您确定这一请求的响应信息。 You may use a number or a string. Useful when  you make several simultaneous requests.当您有用几个并发请求，您可以使用一个数字或字符串来区分。

&nbsp;

事件的顺序:首先SGI通道鸣响，然后当他被接听后，通过上下文呼叫另一端的分机。注意超时只用于呼叫通道，呼叫分机时的超时另外指定。

&nbsp;

使用_异步_导致1 **OriginateResponse**事件，包含了失败的_原因_。 Reason may be one of the following:原因可能是下列之一：
0 = no such extension or number 0 =没有这样的延长或编号
1 = no answer 1 =无应答
4 = answered 4 =回答
8 = congested or not available 8 =拥挤或不可用

&nbsp;

例1（从SIP通道呼叫一个分机，这将导致外部呼叫无法产生直到sip通道挂断）

发送：

Action: Originate
Channel: SIP/101test
Context: default
Exten: 8135551212
Priority: 1
Callerid: 3125551212
Timeout: 30000
Variable: var1=23|var2=24|var3=25
ActionID: ABC45678901234567890

&nbsp;

分机8135551212是外呼号码。这个例子表明了变量的使用方法

&nbsp;

例2（从呼出通道呼叫一个本地分机，这将导致该分机不再响铃直到zap通道挂断）

发送：

Action: Originate

Channel: Zap/g2/8135551212

Context: default

Exten: 101

Priority: 1

Timeout: 30000

Callerid: 3125551212

&nbsp;

101为一个本地分机号。

&nbsp;

例3（运行系统命令）

发送：

Action: Originate
Channel: Local/1@dummy
Application: system
Data: /path/to/script

&nbsp;

提示：如果你希望通道连接到任意分机和上下文，需要指定一个本地通道

&nbsp;

**ParkedCalls** **：**设置显示保持的呼叫列表。

参数：ActionID

发送：

Action: PakedCalls

&nbsp;

返回：

Response: Success
Message: Parked calls will follow

Event: ParkedCallsComplete

**Ping**

例子：

发送：

Action: ping

返回：

Response: Pong

&nbsp;

**QueueAdd** **：**添加队列成员。

参数：

Queue：添加成员的队列名。

Interface: 成员名（例如sip/2001）

Penalty：数值，值越高越靠后。

Paused：暂停（true）或不暂停（false）

ActionID：（前面已介绍）

&nbsp;

例子：

发送：

Action: QueueAdd
Queue: 200
Interface: sip/3001
Penalty: 1
Paused: true

返回：

Response: Success

Message: Added interface to queue

&nbsp;

Event: QueueMemberAdded

Privilege: agent,all

Queue: 200

Location: sip/3001

MemberName: sip/3001

Membership: dynamic

Penalty: 1

CallsTaken: 0

LastCall: 0

Status: 5

Paused: 1

&nbsp;

**QueueRemove** **：**移除队列成员。

参数：

Queue：移除成员的队列名。

Interface: 成员名（例如sip/2001）

&nbsp;

例子：

发送：

Action: QueueRemove
Queue: 200
Interface: sip/3001

返回：

Response: Success

Message: Removed interface from queue

&nbsp;

Event: QueueMemberRemoved

Privilege: agent,all

Queue: 200

Location: sip/3001

MemberName: sip/3001

&nbsp;

**Queues** **：**返回全部队列的配置以及电话数据信息。

例子：

发送：

Action： Queues

返回：

200 has 0 calls (max unlimited) in 'ringall' strategy (0s holdtime), W:0, C:0, A:0, SL:0.0% within 0s

Members:

Local/2002@from-internal/n (Invalid) has taken no calls yet

Local/2003@from-internal/n (Invalid) has taken no calls yet

Local/2001@from-internal/n (Invalid) has taken no calls yet

No Callers

&nbsp;

default has 0 calls (max unlimited) in 'ringall' strategy (0s holdtime), W:0, C:0, A:0, SL:0.0% within 0s

No Members

No Callers

&nbsp;

**QueueStatus** **：**返回全部队列的电话数据信息。

例子：

发送：

Action: QueueStatus

返回：

Response: Success

Message: Queue status will follow

&nbsp;

Event: QueueParams

Queue: 200

Max: 0

Calls: 0

Holdtime: 0

Completed: 0

Abandoned: 0

ServiceLevel: 0

ServicelevelPerf: 0.0

Weight: 0

&nbsp;

Event: QueueMember

Queue: 200

Name: Local/2002@from-internal/n

Location: Local/2002@from-internal/n

Membership: static

Penalty: 0

CallsTaken: 0

LastCall: 0

Status: 4

Paused: 0

&nbsp;

Event: QueueMember

Queue: 200

Name: Local/2003@from-internal/n

Location: Local/2003@from-internal/n

Membership: static

Penalty: 0

CallsTaken: 0

LastCall: 0

Status: 4

Paused: 0

&nbsp;

Event: QueueMember

Queue: 200

Name: Local/2001@from-internal/n

Location: Local/2001@from-internal/n

Membership: static

Penalty: 0

CallsTaken: 0

LastCall: 0

Status: 4

Paused: 0

&nbsp;

Event: QueueParams

Queue: default

Max: 0

Calls: 0

Holdtime: 0

Completed: 0

Abandoned: 0

ServiceLevel: 0

ServicelevelPerf: 0.0

Weight: 0

&nbsp;

Event: QueueStatusComplete

&nbsp;

**Redirect** **：**重定向一个呼叫。

参数：

Channel：重定向的通道。

ExtraChannel：重定向的第二通道（可选）

Exten：重定向的分机。

Context: 重定向的上下文。

Priority：重定向的优先级

ActionID：（可选）

&nbsp;

例子（把两个呼叫重定向到meetme房间）

Action: Redirect

Channel: Zap/73-1

ExtraChannel: SIP/199testphone-1f3c

Exten: 8600029

Context: default

Priority: 1

&nbsp;

&nbsp;

8600029的上下文是default，并将进入meetme房间。

&nbsp;

**SetCDRUserField** **：**设置用户的呼叫详细记录。

参数：UserField，Channel，Append

&nbsp;

**SetVar** **：**设置通道变量。

参数：Channel, Variable, Value

&nbsp;

例子：

发送：

Action: Setvar
Channel: Zap/1-1
Variable: AtestVariable
Value: This is now set

&nbsp;

**SIPpeers** **：**显示sip用户列表。

例子：

发送：

Action: SIPpeers

返回

Response: Success

Message: Peer status list will follow

&nbsp;

Event: PeerEntry

Channeltype: SIP

ObjectName: 3001

ChanObjectType: peer

IPaddress: -none-

IPport: 0

Dynamic: yes

Natsupport: yes

VideoSupport: no

ACL: no

Status: UNKNOWN

RealtimeDevice: no

&nbsp;

Event: PeerEntry

Channeltype: SIP

ObjectName: 2003

ChanObjectType: peer

IPaddress: -none-

IPport: 0

Dynamic: yes

Natsupport: yes

VideoSupport: no

ACL: no

Status: UNKNOWN

RealtimeDevice: no

&nbsp;

Event: PeerEntry

Channeltype: SIP

ObjectName: 2002

ChanObjectType: peer

IPaddress: -none-

IPport: 0

Dynamic: yes

Natsupport: yes

VideoSupport: no

ACL: no

Status: UNKNOWN

RealtimeDevice: no

&nbsp;

Event: PeerEntry

Channeltype: SIP

ObjectName: 2001

ChanObjectType: peer

IPaddress: -none-

IPport: 0

Dynamic: yes

Natsupport: yes

VideoSupport: no

ACL: no

Status: UNKNOWN

RealtimeDevice: no

&nbsp;

Event: PeerlistComplete

ListItems: 4

&nbsp;

**Status** **：**返回所有开放通道或指定通道的状态。

参数：Channel (可选), ActionID (可选)

例子：

发送：

Action：Status

&nbsp;

返回：

Response: Success

Message: Channel status will follow

&nbsp;

Event: StatusComplete

&nbsp;

**StopMonitor** **：**停止检测某通道。

参数：Channel

例子：

发送：

Action: StopMonitor
Channel: Zap/1-1

&nbsp;

**ZapDialOffhook** **：**从空闲的ZAP通道拨打呼叫。

参数：

ZapChannel: ZAP 通道
Number: 所拨打的号码

&nbsp;

**ZapDNDoff** **：**绑定ZAP通道而不影响其关闭状态

参数：ZAPChannel

&nbsp;

**ZapDNDon** **：**绑定ZAP通道而不影响其开放状态

参数：ZAPChannel

&nbsp;

**ZapHangup** **：**挂断ZAP通道。

参数：ZAPChannel

&nbsp;

**ZapTransfer** **：**转拨ZAP通道。

参数：ZAPChannel

&nbsp;

**ZapShowChannels** **：**显示所有ZAP通道信息。

参数：ActionID（可选）

&nbsp;

Asterisk 1.2.1新增：

**AgentCallBackLogin** **：**设置用户以回拨方式登录。

参数：

Agent:用户登录的ID
Exten: 用于回拨的分机
Context: 用于回拨的上下文
AckCall: 设置在用户回拨时进行验证

WrapupTime: 挂断重拨的最小间隔时间

ActionID:

&nbsp;

例子：

发送：

Action: AgentCallBackLogin
Agent: 1234
Exten: 1234
Context: myqueues
AckCall: true
WrapupTime: 30
ActionID: 12345

&nbsp;

**AgentLogoff** **：**注销一个用户。

参数：

Agent：用户登录Id

&nbsp;

例子：

发送：

Action: AgentLogoff
Agent: 1234

&nbsp;

**Agents** **：**显示所有用户信息。

例子：

发送：

Action: Agents

&nbsp;

**DBGet** **：**查询数据库信息。

参数：Family，Key

&nbsp;

发送：

Action: DBGet
Family:
Key:

失败返回：
Response: Error
Message: Database entry not found

成功返回：
Response: Success
Message: Result will follow

Event: DBGetResponse
Family:
Key:
Val:

&nbsp;

在Perl脚本中的运用：

use Asterisk::Manager;
my $astman = new Asterisk::Manager;
$astman-&gt;user('');
$astman-&gt;secret('');
$astman-&gt;host('');
$astman-&gt;connect || die $astman-&gt;error . "/n";

$astman-&gt;sendcommand(Action =&gt; 'DBGet', Family =&gt; '', Key =&gt; '');
my @result = $astman-&gt;sendcommand(Event =&gt; 'DBGetResponse');
$astman-&gt;disconnect;

my $value = $result[7]; -&gt; Value 7 is the returned Value

&nbsp;

**DBPut:**更新数据库信息。

发送：

Action: DBPut
Family:
Key:
Value:  (note, as of Asterisk 1.4 The Manager API wants Val: )

返回：
Response: Success
Message: Updated database successfully

&nbsp;

使用方法同DBGet一样。

&nbsp;

**QueuePause** **：**停止/开启队列中某个成员的使用。

参数：

Queue: 成员所在队列名.（可选，如无此项将在成员所在的所有队列停止成员的使用）

Interface: 队列成员名 (如sip/1000, zap/1-1, agent/1000)

Paused: 停止或开启 (true or false)

&nbsp;

**SIPshowPeer** **：显示指定** **peer** **信息**

参数: peer:指定的peer。

&nbsp;

Asterisk 1.4.0新增：

**PlayDTMF** **：**在指定通道上发送双音多频信号数字。

参数：

Channel：指定的通道

Digit：双音多频信号数字

&nbsp;

发送：

Action: PlayDTMF
Channel: SIP/123-e2b2
Digit: 1

成功返回：

Response: Success
Message: DTMF successfully queued

失败返回：

Response: Error
Message: No such channel

&nbsp;

**UpdateConfig** **：**更新配置文件。

参数：

SrcFilename: 要读取的配置文件(.conf)

DstFilename: 要更新的配置文件(.conf)

Reload: 是否重新加载（asterisk或者模块名称）

Action-XXXXXX: 采取的动作 （NewCat、RenameCat、DelCat、Update、Delete、Append）

Cat-XXXXXX: 操作对象

Var-XXXXXX: 采用的变量

Value-XXXXXX: 变量的值

Match-XXXXXX: 其他匹配信息

&nbsp;

例1：（更新manager.conf）

action: updateconfig

reload: yes

srcfilename: manager.conf

dstfilename: manager.conf

action-000000: append

cat-000000: newuser

var-000000: secret

value-000000: nottelling

例2:(更新extension.conf，通过'&gt;'增加前缀)

action:updateconfig
reload:yes
srcfilename: extensions.conf
dstfilename: extensions.conf
Action-000000:append
Cat-000000: ami-test
Var-000000: exten
Value-000000: &gt;999,1,Dial(SIP/Bob)

&nbsp;

例3：（删除manager.conf中的配置信息）

action: updateconfig

srcfilename: manager.conf

dstfilename: manager.conf

action-000000: delete

cat-000000: newuser

var-000000: secret

value-000000: nottelling

match-000000: nottelling

&nbsp;

删除时必须设置'value'和'match'，不设置'match'将会删除变量为'var'所有条目

&nbsp;

**GetConfig** **：**显示配置文件内容，主要给AJAM和asterisk图形界面使用。

变量：FileName：显示的文件名（.conf）。

&nbsp;

&nbsp;

&nbsp;

&nbsp;

**Asterisk** **事件（** **Event** **）** **：**

以下的事件都在asterisk源码中定义。

&nbsp;

**用户状态事件：**

&nbsp;

'Agentcallbacklogin' 事件 ：

描述:

[来源于chan_agent.c]

&nbsp;

示例:

Event: Agentcallbacklogin

Agent:

Loginchan:

Uniqueid:

&nbsp;

'Agentcallbacklogoff'事件

描述:

[来源于chan_agent.c]

&nbsp;

示例:

Event: Agentcallbacklogoff

Agent:

Loginchan:

Logintime:

Reason: Autologoff

Uniqueid:

&nbsp;

Event: Agentcallbacklogoff

Agent:

Loginchan:

Logintime:

Uniqueid:

&nbsp;

&nbsp;

'AgentCalled'事件

描述:

[来源于 app_queue.c]

&nbsp;

示例:

Event: AgentCalled

AgentCalled:

ChannelCalling:

CallerID:

Context:

Extension:

Priority:

&nbsp;

&nbsp;

'AgentComplete'事件

描述:

[来源于 app_queue.c]

&nbsp;

示例:

Event: AgentComplete

Queue:

Uniqueid:

Channel:

Member:

MemberName:

HoldTime:

TalkTime:

Reason:

&nbsp;

&nbsp;

'AgentConnect'事件

描述:

[来源于 app_queue.c]

&nbsp;

示例:

Event: AgentConnect

Queue:

Uniqueid:

Channel:

Member:

MemberName:

Holdtime:

BridgedChannel:

&nbsp;

&nbsp;

&nbsp;

'AgentDump'事件

描述:

[来源于 app_queue.c]

&nbsp;

示例:

Event: AgentDump

Queue:

Uniqueid:

Channel:

Member:

MemberName:

&nbsp;

&nbsp;

'Agentlogin'事件

描述:

[来源于 chan_agent.c]

&nbsp;

示例:

Event: Agentlogin

Agent:

Channel:

Uniqueid:

&nbsp;

&nbsp;

'Agentlogoff'事件

描述:

[来源于 chan_agent.c]

&nbsp;

示例:

Event: Agentlogoff

Agent:

Logintime:

Uniqueid:

&nbsp;

&nbsp;

'QueueMemberAdded'事件

描述:

&nbsp;

队列增加成员动作

[来源于 app_queue.c]

&nbsp;

示例:

Queue: testing

Location: Agent/AgentId

Membership: dynamic

Penalty: 0

CallsTaken: 0

LastCall: 0

Status: 4

Paused: 1

&nbsp;

&nbsp;

'QueueMemberPaused'事件

&nbsp;

描述:

&nbsp;

响应动作: QueuePause

[来源于 app_queue.c]

&nbsp;

示例:

Event: QueueMemberPaused

Location:

MemberName:

Paused:

&nbsp;

&nbsp;

'QueueMemberStatus'事件

描述:

[来源于 app_queue.c]

&nbsp;

&nbsp;

Status的值可能为:

/*设备是有效的，但不知道的通道状态*/

define AST_DEVICE_UNKNOWN 0

&nbsp;

/*设备没有使用 */

define AST_DEVICE_NOT_INUSE 1

&nbsp;

/*设备使用中 */

define AST_DEVICE_INUSE 2

&nbsp;

&nbsp;

/*设备忙 */

define AST_DEVICE_BUSY 3

&nbsp;

/*设备无效 */

define AST_DEVICE_INVALID 4

&nbsp;

/*设备不可用 */

define AST_DEVICE_UNAVAILABLE 5

&nbsp;

/* 设备在响铃*/

define AST_DEVICE_RINGING 6

&nbsp;

/*设备在响铃并在使用中*/

define AST_DEVICE_RINGINUSE 7

&nbsp;

/*设备在等待 */

define AST_DEVICE_ONHOLD 8

&nbsp;

&nbsp;

示例:

Event: QueueMemberStatus

Queue:

Location:

MemberName:

Membership:

Penalty:

CallsTaken:

LastCall:

Status:

Paused:

&nbsp;

&nbsp;

-

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

**命令状态事件：**

&nbsp;

**呼叫状态事件：**

&nbsp;

'CDR'事件

描述:

[来源于 cdr_manager.c]

&nbsp;

必须在 cdr_manager.conf 配置文件中有：

&nbsp;

general

enabled = yes

&nbsp;

示例:

Event: Cdr

AccountCode:

Source:

Destination:

DestinationContext:

CallerID:

Channel:

DestinationChannel:

LastApplication:

LastData:

StartTime:

AnswerTime:

EndTime:

Duration:

BillableSeconds:

Disposition:

AMAFlags:

UniqueID:

UserField:

&nbsp;

&nbsp;

'Dial'事件

描述:

[来源于 app_dial.c]

&nbsp;

示例:

Event: Dial

Privilege: call,all

Source: Local/900@default-2dbf,2

Destination: SIP/900-4c21

CallerID:

CallerIDName: default

SrcUniqueID: 1149161705.2

DestUniqueID: 1149161705.4

&nbsp;

&nbsp;

'ExtensionStatus'事件

描述:

[来源于 manager.c]

&nbsp;

示例:

Event: ExtensionStatus

Exten:

Context:

Status:

&nbsp;

&nbsp;

'Hangup'事件

描述:

[来源于 channel.c]

&nbsp;

示例:

Event: Hangup

Channel: SIP/101-3f3f

Uniqueid: 1094154427.10

Cause: 0

&nbsp;

Cause 代码：

&nbsp;

未分配 = 1

无到达路由 = 2

无路由目的地 = 3

无效通道 = 6

正在通话中 = 7

正常挂机 = 16

用户忙 = 17

无应答 = 18

无人接听 = 19

拒绝接听 = 21

号码已更改 = 22

目的次序不对 = 27

无效的数字格式 = 28

设备拒绝 = 29

查询响应 = 30

正常未指定 = 31

正常的呼叫拥塞 = 34

网络状态差 = 38

正常的临时故障 = 41

交换机拥塞 = 42

信息过时 = 43

请求通道无效 = 44

被抢占 = 45

无呼叫保持或呼叫等待 = 50

禁止呼出 = 52

禁止呼入 = 54

负载过重导致失效 = 57

负载过多导致不可达 = 58

负载过多导致为实现 = 65

通道未实现 = 66

设备未实现 = 69

无效的呼叫证明 = 81

不相容的目的地 = 88

无效的不明信息 = 95

IE浏览器关闭 = 96

不识别的信息类型 = 97

错误信息 = 98

无IE浏览器 = 99

无效的IE信息 = 100

呼叫状态错误 = 101

计时到达 = 102

强制性IE浏览器长度误差 = 103

协议错误 = 111

互通 = 127

未定义的 = 0

&nbsp;

&nbsp;

'MusicOnHold'事件

描述:

事件表示电话等待时，播放音乐

示例:

&nbsp;

Event: MusicOnHold

Channel:

State:

Uniqueid:

&nbsp;

&nbsp;

'Join'事件

描述:

[来源于 app_queue.c]

&nbsp;

示例:

Event: Join

Channel:

CallerID:

Queue:

Position:

Count:

&nbsp;

&nbsp;

'Leave'事件

描述:

[来源于 app_queue.c]

&nbsp;

示例:

Event: Leave

Channel:

Queue:

Count:

&nbsp;

&nbsp;

'Link'事件

描述:

当两个通道已连接后开始交换声音数据时发生'link'事件

&nbsp;

示例:

&nbsp;

Event: Link

Channel1: SIP/101-3f3f

Channel2: Zap/2-1

Uniqueid1: 1094154427.10

Uniqueid2: 1094154427.11

&nbsp;

&nbsp;

'MeetmeJoin'事件

描述:

[来源于 app_meetme.c]

&nbsp;

示例:

Event: MeetmeJoin

Channel:

Uniqueid:

Meetme:

Usernum:

&nbsp;

&nbsp;

'MeetmeLeave'事件

描述:

[来源于 app_meetme.c]

&nbsp;

示例:

Event: MeetmeLeave

Channel:

Uniqueid:

Meetme:

Usernum:

&nbsp;

&nbsp;

'MeetmeStopTalking'事件

描述:

[来源于 app_meetme.c]

&nbsp;

注意：

这要求在meetme应用中以选中T选

&nbsp;

示例:

&nbsp;

Event: MeetmeStopTalking

Privilege: call,all

Channel: SIP/200-ABC1

Uniqueid: 1234567890.1

Meetme: 400

Usernum: 2

&nbsp;

&nbsp;

&nbsp;

'MeetmeTalking'事件

描述:

[来源于 app_meetme.c]

&nbsp;

注意：

这要求在meetme应用中以选中T选项

&nbsp;

示例:

&nbsp;

Event: MeetmeTalking

Privilege: call,all

Channel: SIP/200-ABC1

Uniqueid: 1234567890.1

Meetme: 400

Usernum: 2

&nbsp;

&nbsp;

'MessageWaiting'事件

描述:

[来源于 app_voicemail.c]

&nbsp;

示例:

Event: MessageWaiting

Mailbox: @

Waiting:

New:

Old:

&nbsp;

Event: MessageWaiting

Mailbox:

Waiting:

&nbsp;

&nbsp;

'Newcallerid'事件

描述:

[来源于 channel.c]

&nbsp;

示例:

Event: Newcallerid

Channel:

Callerid:

Uniqueid:

&nbsp;

&nbsp;

'Newchannel'事件

描述:

[来源于 channel.c]

&nbsp;

示例:

Event: Newchannel

Channel: Zap/2-1

State: Rsrvd

Callerid:

Uniqueid: 1094154427.11

&nbsp;

Event: Newchannel

Channel: SIP/101-3f3f

State: Ring

Callerid: 101

Uniqueid: 1094154427.10

&nbsp;

&nbsp;

'Newexten'事件

描述:

当一项PBX函数运行（例如执行拨号规则）时发生此事件。

&nbsp;

示例:

Event: Newexten

Channel: SIP/101-00c7

Context: macro-ext

Extension: s

Priority: 3

Application: Goto

AppData: s-BUSY

Uniqueid: 1094154321.8

&nbsp;

Event: Newexten

Channel: SIP/101-3f3f

Context: local_extensions

Extension: 917070

Priority: 1

Application: AGI

AppData: /etc/asterisk/agi/ks_doorman_pickup.py|channel_up

Uniqueid: 1094154427.10

&nbsp;

Event: Newexten

Channel: SIP/101-3f3f

Context: local_extensions

Extension: 917070

Priority: 2

Application: Dial

AppData: Zap/G1/17070

Uniqueid: 1094154427.10

&nbsp;

&nbsp;

'ParkedCall'事件

描述:

[来源于 res_features.c]

&nbsp;

示例:

Event: ParkedCall

Exten:

Channel:

From:

Timeout:

CallerID:

&nbsp;

&nbsp;

'Rename'事件

描述:

[来源于 channel.c: channel 'rename' event]

&nbsp;

示例:

Event: Rename

Oldname:

Newname:

Uniqueid:

&nbsp;

&nbsp;

'SetCDRUserField'事件

描述:

[来源于 app_setcdruserfield.c]

&nbsp;

示例:

&nbsp;

&nbsp;

'Unlink'事件

描述:

&nbsp;

当两个连接的通道断开是发生此事件，通常为挂机时

&nbsp;

示例:

&nbsp;

Event: Unlink

Channel1: SIP/101-3f3f

Channel2: Zap/2-1

Uniqueid1: 1094154427.10

Uniqueid2: 1094154427.11

&nbsp;

&nbsp;

'UnParkedCall'事件

描述:

[来源于 res_features.c]

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

**日志状态事件：**

&nbsp;

**系统状态事件：**

&nbsp;

'Alarm'事件:

描述:

[来源于 chan_zap.c]

&nbsp;

示例:

Event: Alarm

Alarm:&lt;(Red|Yellow|Blue|No|Unknown)Alarm|Recovering|Loopback|Not Open|None&gt;

Channel:

&nbsp;

&nbsp;

'AlarmClear'事件:

描述:

[来源于 chan_zap.c]

&nbsp;

示例:

Event: AlarmClear

Channel:

&nbsp;

&nbsp;

'DNDState'事件:

描述:

[来源于 chan_dahdi.c]

&nbsp;

示例:

Event: DNDState

Channel: Zap/1

Status:

&nbsp;

&nbsp;

'LogChannel'事件

描述:

[来源于 logger.c]

&nbsp;

示例:

Event: LogChannel

Channel: /var/log/asterisk/messages

Enabled: Yes

&nbsp;

Event: LogChannel

Channel: /var/log/asterisk/messages

Enabled: No

Reason: 13 - Permission denied

&nbsp;

&nbsp;

&nbsp;

'PeerStatus'事件

描述:

当用户注册或注销时发生此事件

[来源于 chan_sip.c, chan_iax2.c]

&nbsp;

示例:

Event: PeerStatus

Peer: SIP/2005

PeerStatus: Registered

&nbsp;

Event: PeerStatus

Peer: SIP/2005

PeerStatus: Unregistered

Cause: Expired

&nbsp;

Event: PeerStatus

Peer: IAX2/2007

PeerStatus:

Time: 1000

&nbsp;

&nbsp;

&nbsp;

'Registry'事件

描述:

&nbsp;

当应户注册时发生的事件

[来源于 chan_sip.c, chan_iax2.c]

&nbsp;

示例:

Event: Registry

Channel: SIP

Domain: sip.domain

Status: Registered

&nbsp;

&nbsp;

'Reload'事件

描述:

当 "RELOAD" 命令执行时发生此事件

[来源于 manager.c]

&nbsp;

示例:

Event: Reload

Message: Reload Requested

&nbsp;

&nbsp;

'Shutdown'事件

描述:

[来源于 asterisk.c]

&nbsp;

示例:

Event: Shutdown

Shutdown:

Restart:

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

**User Status** **事件：**

&nbsp;

'UserEvent'事件

描述:

[来源于 app_userevent.c]

&nbsp;

示例:

Event:

Channel:

Uniqueid:

&nbsp;

Event:

Channel:

Uniqueid:

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

**详细状态事件：**
<pre>'Newstate'事件
</pre>
示例：

Event: Newstate

Channel: Zap/2-1

State: Dialing

Callerid: 101

Uniqueid: 1094154427.11

&nbsp;

Event: Newstate

Channel: Zap/2-1

State: Up

Callerid: 101

Uniqueid: 1094154427.11

&nbsp;

&nbsp;

'ParkedCallsComplete:'事件

描述：

发生在ParkedCalls事件之后

示例：

Event: ParkedCallsComplete

&nbsp;

&nbsp;

'QueueParams:'事件

描述：

响应Queues动作的事件

示例：

Event: QueueParams

Queue: sales

Max: 0

Calls: 0

Holdtime: 0

Completed: 0

Abandoned: 0

ServiceLevel: 0

ServicelevelPerf: 0.0
<pre>'QueueMember'事件
</pre>
描述：

响应Queues动作并且队列中有成员是发生的事件。

示例：

Event: QueueMember

Queue: sales

Location: SIP/101

Membership: dynamic

Penalty: 0

CallsTaken: 0

LastCall: 0
<pre>'QueueStatusEnd'事件
</pre>
描述：

响应Queues动作表示输出结束。

示例：

Event: QueueStatusEnd

&nbsp;
<pre>'Status'事件
</pre>
示例：

Event: Status

Channel: Zap/2-1

CallerID: 101

Account:

State: Up

Link: SIP/101-5cf0

Uniqueid: 1094166088.26

&nbsp;

Event: Status

Channel: SIP/101-5cf0

&nbsp;

CallerID: 101

Account:

State: Up

Context: local_extensions

Extension: 917070

Priority: 2

Seconds: 11

Link: Zap/2-1

Uniqueid: 1094166088.25

&nbsp;

'StatusComplete'事件

描述：

响应Status动作的事件，表示状态输出结束。

示例：

Event: StatusComplete

&nbsp;

&nbsp;

'ZapShowChannels'事件

描述：

响应ZapShowChannels动作的事件。

示例：

Event: ZapShowChannels

Channel: 2

Signalling: FXS Kewlstart

Context: pstn_menu

Alarm: No Alarm

&nbsp;

&nbsp;

'ZapShowChannelsComplete事件

描述：

响应ZapShowChannels动作的事件，表示输出结束。

示例：

Event: ZapShowChannelsComplete

&nbsp;

&nbsp;

&nbsp;

**动作响应（** **Response** **）**

格式：

Response: ( Success或Error)

Message: （显示信息）

&nbsp;

示例：

无动作或输入有误：

Response: Error

Message: Missing action in request

&nbsp;

未知命令：

Response: Error

Message: Invalid/unknown command

&nbsp;

无权限：

Response: Error

Message: Permission denied

&nbsp;

成功：

Response: Success

Message: Zap channel status will follow

&nbsp;