# face2face
##基于netty的异步非阻塞聊天服务器。<br>
face2face是一个异步非阻塞的聊天服务器，底层由netty网络库驱动。服务器将业务拆分，每个服务单独设置一个进程，服务之间通过socket进行连接。每个消息被传送到对应的服务后，根据用户名分发到对应工作线程处理。<br>
业务逻辑的可扩展：所有业务逻辑的处理都采用了统一注册回调的方式，如果要增加新的业务逻辑，只需在很少几个地方进行注册，并在对应的服务上编写对应的处理函数即可。<br>

实现基本功能后的目标是将每个服务设计为可水平扩展的集群，并且重点放在架构上的优化而不是逻辑功能上。<br>
auth服务：负责登录认证。<br> 
gate服务：负责客户端接入，也是服务器和客户端通信的媒介。<br> 
logic服务：负责处理各种业务逻辑。<br>

###quicksStart:
windows:<br>
1、使用intellij maven方式导入该工程，执行mvn clean compile<br>
2、启动本地redis服务:使用windows cmd命令行进入到thirdparty模块resources目录下的redis文件夹，执行redis-server.exe redis.windows.conf<br>
3、按照提示，设置好auth logic gate正确的配置文件路径，依次启动auth、logic、gate服务<br>
<br>
注册和登录功能，测试方法如下(建议跟踪断点)：<br>
1、启动client模块中的测试用客户端，按照提示依次任意输入2个用户名，如name1 name2，服务便会自动执行注册，登录的流程。<br>
<br>
单聊功能测试方法：<br>
1、启动第一个client模块中的测试用客户端1，按照提示依次输入用户名test，aaa<br>
2、再启动第二个客户端2，依次输入aaa，test<br>
3、在启动的客户端1输入任意文字，之后便能在第客户端2查看到客户端1发送的文字<br>

###下一步目标：
1、将gate网关服务进行水平扩展，并在最外层加入nginx服务器进行负载均衡，对每个客户端连接进行分发。<br>
2、为应对业务逻辑将变得越来越复杂的情况(CPU密集型)，需要将logic逻辑服进行水平扩展拆分，初步想法是根据userid得出一个hash值，然后将这个用户分配到对应启动的logic服务上。<br>
3、增加Sync协同服务，为保证整个系统能够知道gate服、logic服的扩展或者减少，需要通过Sync协同服务同步机器信息，同时协同服务必须提供一种机制动态判断服务的可用性。<br>
