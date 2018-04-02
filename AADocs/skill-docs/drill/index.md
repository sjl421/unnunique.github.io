# 使用技巧
1,  [drill读取spark保存的parquet文件中timestamp不正确](drill-read-spark-parquet-timestamp-error.md)  


# 知识点 
## 1，中文翻译文档下载
[drill 中文文档](drill.docx)

## 2， 安装
```
安装前提：
(Required) Running Oracle JDK version 7 or version 8 if running Drill 1.6 or later.
(Required) Running a ZooKeeper quorum
(Recommended) Running a Hadoop cluster
(Recommended) Using DNS

在每个节点。下载，解压，配置 drill-overrive.conf
drill.exec:{
  cluster-id: "<mydrillcluster>",
  zk.connect: "<zkhostname1>:<port>,<zkhostname2>:<port>,<zkhostname3>:<port>"
}


在每个节点启动Drillbit
${DRILL_HOME}/bin/drillbit.sh start

Web 控制台
http://<IP address or host name>:8047

```


# 创建错误
## 谨慎之前安装过drill 的节点
```
s102 节点安装失败，之前配置过单节点的配置：
rm -rf  /tmp/drill
rm -rf ${INSTALL_HOM}/drill。
[Error Id: 7f7d1bdc-9a3d-4ddd-86d8-ecb1677cfe4b on s102:31010]
	at org.apache.drill.exec.rpc.AbstractDisposableUserClientConnection.sendResult(AbstractDisposableUserClientConnection.java:85) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.Foreman$ForemanResult.close(Foreman.java:868) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.Foreman.moveToState(Foreman.java:977) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.Foreman.access$2600(Foreman.java:115) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.Foreman$StateSwitch.processEvent(Foreman.java:1027) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.Foreman$StateSwitch.processEvent(Foreman.java:1020) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.common.EventProcessor.processEvents(EventProcessor.java:107) ~[drill-common-1.11.0.jar:1.11.0]
	at org.apache.drill.common.EventProcessor.sendEvent(EventProcessor.java:65) ~[drill-common-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.Foreman$StateSwitch.addEvent(Foreman.java:1022) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.Foreman.addToEventQueue(Foreman.java:1040) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.foreman.QueryManager$1.statusUpdate(QueryManager.java:521) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.rpc.control.WorkEventBus.statusUpdate(WorkEventBus.java:71) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.batch.ControlMessageHandler.handle(ControlMessageHandler.java:94) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.work.batch.ControlMessageHandler.handle(ControlMessageHandler.java:55) ~[drill-java-exec-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.rpc.BasicServer.handle(BasicServer.java:157) ~[drill-rpc-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.rpc.BasicServer.handle(BasicServer.java:53) ~[drill-rpc-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.rpc.RpcBus$InboundHandler.decode(RpcBus.java:274) ~[drill-rpc-1.11.0.jar:1.11.0]
	at org.apache.drill.exec.rpc.RpcBus$InboundHandler.decode(RpcBus.java:244) ~[drill-rpc-1.11.0.jar:1.11.0]
	at io.netty.handler.codec.MessageToMessageDecoder.channelRead(MessageToMessageDecoder.java:89) ~[netty-codec-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:339) ~[netty-transport-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:324) ~[netty-transport-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.handler.timeout.ReadTimeoutHandler.channelRead(ReadTimeoutHandler.java:150) ~[netty-handler-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:339) ~[netty-transport-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:324) ~[netty-transport-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.handler.codec.MessageToMessageDecoder.channelRead(MessageToMessageDecoder.java:103) ~[netty-codec-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:339) ~[netty-transport-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:324) ~[netty-transport-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.handler.codec.ByteToMessageDecoder.channelRead(ByteToMessageDecoder.java:242) ~[netty-codec-4.0.27.Final.jar:4.0.27.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:339) ~[netty-transport-4.0.27.Final.jar:4.0.27.Final]
```