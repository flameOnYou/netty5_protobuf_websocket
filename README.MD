### Serialization_ProtoBuf包下为客户端和服务端的nettyDemo
	#### Client/ReqClient为客户端启动程序
	#### Server/ReqServer为服务端启动程序
	#### ProtoBuf包下为Protobuf的传输协议文件
### Websocket包下为websocket服务端
	#### NettyServer为启动程序
### wsHtml放在tomcat8下的webapp中即可
	#### Client.html为纯websocket聊天demo(不包含protobuf方式)
	#### PFClient.html为以protobuf向服务端发送消息
	#### 后端的区分协议在Websocket.MyWebSocketServerHandler。java
```java
	if (frame instanceof TextWebSocketFrame) {
			System.out.println("文本消息 处理模式");
			// 返回应答消息
			String request = ((TextWebSocketFrame) frame).text();
			System.out.println("服务端收到：" + request);
			if (logger.isLoggable(Level.FINE)) {
				logger
						.fine(String.format("%s received %s", ctx.channel(),
								request));
			}
			TextWebSocketFrame tws = new TextWebSocketFrame(new Date().toString()
					+ ctx.channel().id() + "：" + request);
			// 群发
			Global.group.writeAndFlush(tws);
			// 返回【谁发的发给谁】
			// ctx.channel().writeAndFlush(tws);
			
		} //二进制帧处理,将帧的内容往下传
        else if (frame instanceof BinaryWebSocketFrame) {
            System.out.println("正在处理二进制帧");
            BinaryWebSocketFrame binaryWebSocketFrame = (BinaryWebSocketFrame) frame;
            byte[] by = new byte[frame.content().readableBytes()];
            binaryWebSocketFrame.content().readBytes(by);
            ByteBuf bytebuf = Unpooled.buffer();
            bytebuf.writeBytes(by);
//            PersonProtos.Person result = PersonProtos.Person.parseFrom(by);  
            //解析协议
            System.out.println("收到消息");
            WSMessageProtoBuf.WSMessage msgs = WSMessageProtoBuf.WSMessage.parseFrom(by);
            System.out.println(msgs);
        }
