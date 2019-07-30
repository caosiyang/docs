# Thunderbird邮件客户端配置

Thunderbird是Linux邮件客户端

## 账户配置

添加账户

右键左侧Local Folders的settings进入account settings，左下角account actions有账户的添加/删除等操作，
配置接收/发送邮件服务器就ok了。

## 同步和存储配置

默认同步所有邮件，
在account settings下点击目标账户的Synchronization&Strorage标签，在Disk Space下设置好要同步邮件，支持同步最近一段时间的邮件。

## 订阅邮件目录

右键邮件账户，点击Subcribe, 选择需要同步的远程邮件目录。

## 邮件操作

对于 已发送/删除/草稿 
thunderbird 默认将其存放在远程邮件账户的Trash Sent Drafts，
如果远程邮件账户下没有相应的同名目录，将会创建。

我们用的邮件服务器，多是中文的邮件目录，所以需要重新设置。

比如公司用的outlook，我重新配置了
Server Settings下 将Trash 替换为 已删除的邮件
Copies & Folders 下 将Sent 替换为 已发送邮件；Drafts 替换为 草稿



这里配置已发送/删除/草稿邮件的存放位置。

Server Settings下新邮件检查间隔，删除操作（移动到指定邮件目录）。

我是遵循outlook web app的操作逻辑，目标位置都设置的远程目录，然后同步到本地。

## 发送服务器

左侧 OutgoingServer(SMTP) 可以管理不同smtp

目标账户下可以配置为其选择smtp。



## 邮件签名

account settings的默认tab下的Signature text，

## 用户名密码管理

account settings的默认tab下的 Manage Identities下