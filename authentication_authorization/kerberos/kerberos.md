# Kerberos

--------

## 概述

一种认证协议，在公共、存在安全风险网络环境中，为面向连接的应用，提供
[认证服务。](https://github.com/zhangchenwhu/zhangchen/blob/master/authentication_authorization/authenticaton_and_authorization.md)
Kerberos认证具有以下特点：

1.  用户的密码不会在网络上传输
1.  用户的密码不会在客户端上以任何形式保存
1.  用户的密码以加密的形式保存在认证服务器的数据库中
1.  在整个会话过程中，用户只需要输入一次密码
1.  认证信息只保存在认证服务器中
1.  支持双向认证
1.  支持数据加密
1.  需要依赖一个第三方安全节点

--------

## 核心概念

### Realm

需要进行认证的主机集合，域由域名标识，域名的格式类似dns域名，但是大小写敏感，并且一般用大写，例如：EXAMPLE.COM

### Pricipal

相当于用户名，标识一个域中的主机、服务等等，principal的格式为：*Name[/Instance]@REALM*，Name标识用域名，
Instance为角色，为可选项， Realm为域名，例如：

1.  *john@EXAMPLE.COM*，表示EXAMPLE.COM中的用户john 
1.  *alice/admin@EXAMPLE.COM*，表示EXAMPLE.COM域中的管理员alice
1.  *pluto/host@EXAMPLE.COM*，表示EXAMPLE.COM域中的主机pluto
1.  *ssh/service@EXAMPLE.COM*，表示EXAMPLE.COM域中的ssh服务  

### Ticket

用户访问域中某项服务的凭据，**ticket被服务提供方的密码加密**，主要包含以下内容：
*   The requesting user's principal ;
*   The principal of the service it is intended for;
*   The IP address of the client machine from which the ticket can be used. 
*   The date and time (in timestamp format) when the tickets validity commences;
*   The ticket's maximum lifetime
*   The session key (this has a fundamental role which is described below);

Kerberos协议中使用principal来标识用户，使用ticket来作为用户的凭据

--------

## 认证过程

![](./media/kerberos_architecture.gif)

### Kerberos中的角色

1.  Key Distribution Center (KDC)

    KDC是kerberos认证中依赖的第三方可信节点，负责管理和维护一个域中所有的认证信息：所有的principal及对应的
    密码，为域中的用户分发ticket等等，KDC包括下面三大组件：

    1.  Database

        存储域内所有的principal及对应密码

    1.  Authentication Server (AS)

        处理client的初始化认证请求，发放ticket granting ticket（TGT）

    1.  Ticket Granting Server (TGS)

        为用户发放对应服务的ticket， 即凭据

1.  Client

    申请访问某项服务的用户

1.  Application Server

    服务提供方

### Authentication Steps

1.  Authentication Server Request (AS_REQ)

    client向AS发送AS_REQ报文，携带要访问的服务的principal：我是xxx，我想访问xxx

1.  Authentication Server Reply (AS_REP)

    AS向client回复AS_REP报文，报文中含有TGT，和sessionkey<TGS>, 并且sessionkey<TGS>是被client的密码加密的

1.  Ticket Granting Server Request (TGS_REQ)

    client用自己的密码先将sessionkey<TGS>解密出来，构造一个Authenticator，用sessionkey<TGS>加密Authenticator， 
    再加上TGT，向TGS发送TGS_REQ报文

1.  Ticket Granting Server Replay (TGS_REP)

    TGS首先用自己的密码解密TGT，做如下检验：
    
    1.  根据TGT中的时间戳，判断TGT是否过期， 如果过期，则认证失败，
    1.  判断TGT中的client ip是否等于TGS_REQ中的ip，如果不相等，认证失败
    1.  ...

    如果以上校验通过， 从TGT中获取seesionkey<TGS>, 然后用seesionkey<TGS>解密Authenticator, 做如下检验：
    
    1.  根据Authenticator的时间戳，判断Authenticator是否过期，如果过期，则认证失败
    1.  判断authenticator中的client principal是否等于TGT中的client principal，如果不相等，则认证失败
    1.  判断authenticator中的client ip是否等于TGT中的client ip，如果不相等，则认证失败
    1.  ...

    如果以上校验通过，TGS向client回复TGS_REP报文，其中包含要访问的服务的ticket，和sessionkey<ApplicationServer>,
    并且是被seesionkey<TGS>加密的

1.  Application Request (AP_REQ)

    client用sessionkey<TGS>解密，获得sessionkey<ApplicationServer>, 构造authenticator，authenticator用
    sessionkey<ApplicationServer>加密，再加上ticket，向application server发送AP_REQ报文

1.  Application Reply (AP_REP)

    Application server首先用自己的密码解密ticket，做如下检验：
    
    1.  根据ticket中的时间戳，判断ticket是否过期， 如果过期，则认证失败，
    1.  判断ticket中的client ip是否等于AP_REQ中的ip，如果不相等，认证失败
    1.  ...

    如果以上校验通过， 从ticket中获取seesionkey<ApplicationServer>, 然后用seesionkey<ApplicationServer>解密Authenticator, 做如下检验：
    
    1.  根据Authenticator的时间戳，判断Authenticator是否过期，如果过期，则认证失败
    1.  判断authenticator中的client principal是否等于ticket中的client principal，如果不相等，则认证失败
    1.  判断authenticator中的client ip是否等于ticket中的client ip，如果不相等，则认证失败
    1.  ...

    如果以上校验通过，application server向client回复AP_REP报文，其中包含: application server对client认证
    成功、sessionkey<ApplicationServer>。报文被sessionkey<ApplicationServer>加密

    client收到AP_REP报文后，用sessionkey<ApplicationServer>解密报文，查看认证结果是否成功，比较报文中的sessionkey<ApplicationServer>
    和自己的sessionkey<ApplicationServer>是否一致，确认无误后，整个认证成功，通过AP_REP，client也完成了
    对application server的认证

### 几个解释

1.  什么是TGT？有什么用？

    TGT(Ticket Granting Ticket)，叫做票据授权票，由KDC发放给client，client用TGT向KDC申请票，由于要访问
    某项服务，就要向KDC申请该服务的票，如果一个用户要访问多个服务，那就要多次申请票，每次申请票的时候，都要
    输入用户密码，或者当用户的票过期的时候，需要重新申请票，就要重新输入密码，这样很不方便，为了实现SSO，
    Kerberos设计了TGT，使用有效的TGT，就能申请服务的票。

1.  authenticator有什么用？

    用户只要拥有服务的票，就能访问服务，并且票的生存期一般有数天，假如票被盗取，就会造成安全风险，为了解决这个
    问题，Kerberos使用一个client和application server之间的session key作为加密key，加密client的信息：principal，
    ip地址等等，得到一个authenticator，向application server认证时，必须同时提供票和authenticator（相当于双因子
    认证），并且authenticator的生存期更短，默认为2分钟，application server同时保存最近2分钟收到的authenticator，
    这样authenticator的副本也是无效的，authenticator能让Kerberos更安全。

--------

## 实现

1.  [MIT实现](https://github.com/krb5/krb5)

1.  [heimdal实现](https://github.com/heimdal/heimdal)

--------

## 如何应用

直接使用Kerberos API相当繁琐，实际开发中，会将Kerberos作为SASL框架下的一种认证机制，在
[SASL](https://github.com/zhangchenwhu/zhangchen/blob/master/authentication_authorization/sasl/sasl.md)
框架下开发，包括客户端和服务端。

### 使用Kerberos的项目

1.  hbase
1.  hdoop
1.  hive
1.  spark
1.  ....

--------

## 参考

1.  [http://kerberos.org/software/tutorial.html](http://kerberos.org/software/tutorial.html)
1.  [https://web.mit.edu/kerberos/]
1.  [https://tools.ietf.org/html/rfc4120](https://tools.ietf.org/html/rfc4120)
1.  [https://www.rfc-editor.org/rfc/rfc1964.txt](https://www.rfc-editor.org/rfc/rfc1964.txt)