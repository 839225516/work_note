HAProxy配置文档（精简版）
============================

2. 配置HAProxy
-----------------------

2.1 配置文件的格式
^^^^^^^^^^^^^^^^^^^^^^^^^

HAProxy的配置过程包含3个主要的参数来源：

- 来自命令行的参数，始终是优先的
- 配置文件的“global”部分，设置进程全局范围有效的参数
- 配置文件的代理部分，以“defaults”、“listen”、“frontend”和“backend”的形式出现

配置文件的语法是：一行一行的配置信息，每行以本手册涉及的关键词开始；跟随一个或多个可选参数，以空格分隔。如果空格必须出现在字符串中，则空格必须前置一个反斜杠（\\）进行转义。反斜杠自己如果要出现在字符串中，也需要写两遍来转义。

2.2 时间格式
^^^^^^^^^^^^^^^^^

某些参数值表示时间，如超时时间。这些值通常以毫秒为单位表示（除非显式声明为其他单位），也可以在数值后添加单位后缀来声明使用其他单位。谨记这一点非常重要，我们后面不会对关键词的时间参数重复解释。支持的单位有：

- us：微秒。1微秒 = 1/1000000秒
- ms：毫秒。1毫秒 = 1/1000秒，默认单位
- s：秒。1秒 = 1000毫秒
- m：分钟。1分钟 = 60秒 = 60000毫秒
- h：小时。1小时 = 60分钟 = 3600秒 = 3600000毫秒
- d：天。1天 = 24小时 = 1440分钟 = 86400秒 = 86400000毫秒

2.3 示例
^^^^^^^^^^^^^^

::

    # 简单配置一个HTTP代理监听80端口，将请求转发到一个后端“servers”，
    # 该后端仅有一个服务器“server1”监听着127.0.0.1:8000
    global
        daemon
        maxconn 256

    defaults
        mode http
        timeout connect 5000ms
        timeout client 50000ms
        timeout server 50000ms

    frontend http-in
        bind *:80
        default_backend servers

    backend servers
        server server1 127.0.0.1:8000 maxconn 32

    # 以一个listen块来定义与上例相同的配置。更简短但
    # 表达能力弱一些，特别是在HTTP模式下
    global
        daemon
        maxconn 256

    defaults
        mode http
        timeout connect 5000ms
        timeout client 50000ms
        timeout server 50000ms

    listen http-in
        bind *:80
        server server1 127.0.0.1:8000 maxconn 32


假设haproxy命令在$PATH定义的查找路径中，可以这样在shell中测试上述配置：

::

    $ sudo haproxy -f configuration.conf -c


3. 全局参数
---------------

“global”部分的参数是进程全局范围有效的，通常也和操作系统相关。这些参数通常设置一次就一劳永逸了。其中某些参数具有等价的命令行选项。

“global”部分支持以下关键词：

- 进程管理与安全

  - chroot
  - daemon
  - gid
  - group
  - log
  - log-send-hostname
  - nbproc
  - pidfile
  - uid
  - ulimit-n
  - user
  - stats
  - node
  - description

- 性能调优

  - maxconn
  - maxpipes
  - noepoll
  - nokqueue
  - nopoll
  - nosepoll
  - nosplice
  - spread-checks
  - tune.bufsize
  - tune.chksize
  - tune.maxaccept
  - tune.maxpollevents
  - tune.maxrewrite
  - tune.rcvbuf.client
  - tune.rcvbuf.server
  - tune.sndbuf.client
  - tune.sndbuf.server

- 调试

  - debug
  - quiet

3.1 进程管理与安全
^^^^^^^^^^^^^^^^^^^^^^^^^^

**chroot <jail dir>**

将当前目录修改为 ``<jail dir>`` ，并在失去权限之前在该目录下执行一次chroot()。这能够在某些不明漏洞被攻击的情况下提高安全级别，因为这会使得骇客很难攻击系统。但这仅在进程以超级用户权限启动时才有效。一定要确保<jail dir>目录为空且任何人都不可写。

**daemon**

使进程fork为守护进程。生产运营环境（operation）下的推荐模式，等价于命令行的“-D”选项。也可以通过命令行“-db”选项来禁用。

**log <address> <facility> [max level [min level]]**

添加一个全局syslog服务器。可以定义多达两个的全局服务器。它们会收到进程启动和退出时的日志，还有配置有“log global”的代理的所有日志。

<address>可以以下两者之一：

- 一个IPv4地址，可选跟随一个冒号和一个UDP端口。如果未指定端口，则默认使用514（标准syslog端口）。
- 一个UNIX域套接字（socket）的文件系统路径，留意chroot（确保在chroot中可以访问该路径）和uid/gid（确保该路径相应地可写）。

<facility>必须是24个标准syslog设备（facilities）之一：

::

    kern    user    mail    daemon  auth    syslog  lpr     news
    uucp    cron    auth2   ftp     ntp     audit   alert   cron2
    local0  local1  local2  local3  local4  local5  local6  local7

可以指定一个可选的级别来过滤输出的信息。默认情况下会发送所有信息。如果指定了一个最大级别，那么仅有严重性至少同于该级别的信息才会被发送。也可以指定一个可选的最小级别，如果设置了，则以比该级别更严重的级别发送的日志会被覆盖到该级别。这样能避免一些默认syslog配置将“emerg”信息发送到所有终端。八个级别如下所示：

::

    emerg   alert   crit    err     warning     notice  info    debug

**log-send-hostname [<string>]**

设置syslog头部的主机名称（hostname）字段。如果设置了可选的“string”参数，那么头部主机名称字段会被设置为string内容，否则使用系统的主机名。
