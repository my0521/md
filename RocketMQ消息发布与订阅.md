---
title: RocketMQ消息发布与订阅
date: 2019-05-06 20:00:48
categories: 
 - MQ
tags:
 - RocketMQ
 - centOS
---

记录RocketMQ访问远程服务收发消息以及报错问题记录

<!-- more -->

官网 [https://rocketmq.apache.org/docs/simple-example/][1]

## 导入依赖
注：使用的rocketmq-client版本与远程RocketMQ Server一致
``` vbscript-html
<!-- https://mvnrepository.com/artifact/org.apache.rocketmq/rocketmq-client -->
<dependency>
	<groupId>org.apache.rocketmq</groupId>
	<artifactId>rocketmq-client</artifactId>
	<version>4.7.1</version>
</dependency>
```

## 订阅
``` actionscript
import java.io.UnsupportedEncodingException;
import java.util.List;

import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.common.message.MessageExt;
import org.apache.rocketmq.remoting.common.RemotingHelper;

public class SimpleConsumer {
	public static void main(String[] args) throws MQClientException {
		// STEP1: 创建默认Consumer并指定
		DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("GroupA");

		// STEP2: 指定nameServer地址
		consumer.setNamesrvAddr("121.36.64.197:9876");

		// STEP3: 订阅对应主题和tag
		consumer.subscribe("AsyncTopic", "*");

		// STEP4: 注册接收到broker消息后的处理接口
		consumer.registerMessageListener(new MessageListenerConcurrently() {
			@Override
			public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
				try {
					System.out.println(new String(msgs.get(0).getBody(), RemotingHelper.DEFAULT_CHARSET));
				} catch (UnsupportedEncodingException e) {
					e.printStackTrace();
				}
				return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
			}
		});

		// STEP5: 启动consumer (必须在注册完消息监听器之后启动，否则会报错)
		consumer.start();

		System.out.println("Consumer started......");
	}
}
```

## 发布消息
1. 异步消息发送
- 一般用来对方法调用响应时间有较严格要求的情况下，异步调用，立即返回 不同于同步的唯一在于：send方法调用的时候多携带一个回调接口参数，用来异步处理消息发送结果
``` java
	private static void asyncProducer() throws MQClientException, UnsupportedEncodingException, RemotingException,
			MQBrokerException, InterruptedException {
		// STEP1: 创建Producer并且指定组名
		DefaultMQProducer producer = new DefaultMQProducer("GroupA");

		// STEP2: 指定nameServer地址
		producer.setNamesrvAddr("121.36.64.197:9876");

		// STEP3: 启动Producer
		producer.start();
		producer.setRetryTimesWhenSendAsyncFailed(0); // 设置异步发送失败重试次数，默认为2
		Thread.sleep(200);
		int messageCount = 5;
		final CountDownLatch countDownLatch = new CountDownLatch(messageCount);
		for (int i = 0; i < messageCount; i++) {

			try {
				final int index = i;
				Message msg = new Message("AsyncTopic", "TagA", "OrderID188",
						"Hello world".getBytes(RemotingHelper.DEFAULT_CHARSET));
				producer.send(msg, new SendCallback() {
					@Override
					public void onSuccess(SendResult sendResult) {
						countDownLatch.countDown();
						System.out.printf("%-10d OK %s %n", index, sendResult.getMsgId());
					}

					@Override
					public void onException(Throwable e) {
						countDownLatch.countDown();
						System.out.printf("%-10d Exception %s %n", index, e);
						e.printStackTrace();
					}
				});
			} catch (Exception e) {
				e.printStackTrace();
			}

		}
		countDownLatch.await(5, TimeUnit.SECONDS);
		producer.shutdown();
	}
```
2. 同步消息发送
- 一般用来进行通知、短信等重要消息的同步
``` java
	private static void syncProducer() throws MQClientException, UnsupportedEncodingException, RemotingException,
			MQBrokerException, InterruptedException {
		// STEP1: 创建Producer并且指定组名
		DefaultMQProducer syncProducer = new DefaultMQProducer("GroupA");

		// STEP2: 指定nameServer地址
		syncProducer.setNamesrvAddr("121.36.64.197:9876");

		// STEP3: 启动Producer
		syncProducer.start();
		Thread.sleep(200);
		// STEP4: 循环发送消息
		for (int i = 0; i < 3; i++) {
			Message message = new Message("AsyncTopic", "TagA",
					("SyncMsg" + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
			SendResult sendResult = syncProducer.send(message);
			System.out.println(sendResult);
		}

		// STEP5: 关闭Producer
		syncProducer.shutdown();
	}
```
3. 单向模式
- 一般用来对可靠性有一定要求的消息发送，例如日志系统 不同于同步的唯一之处在于：
		 * 调用的是sendOneway方法，且该方法不会给调用者任何返回值
``` java
	private static void oneWayProducer() throws MQClientException, UnsupportedEncodingException, RemotingException,
			MQBrokerException, InterruptedException {
		// STEP1: 创建Producer并且指定组名
		DefaultMQProducer oneWayProducer = new DefaultMQProducer("GroupA");

		// STEP2: 指定nameServer地址
		oneWayProducer.setNamesrvAddr("121.36.64.197:9876");

		// STEP3: 启动Producer
		oneWayProducer.start();
		Thread.sleep(200);
		// STEP4: 循环发送消息
		for (int i = 0; i < 3; i++) {
			Message message = new Message("AsyncTopic", "TagA",
					("OneWayMsg" + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
			oneWayProducer.sendOneway(message);
		}

		// STEP5: 关闭Producer
		oneWayProducer.shutdown();
	}
```

## 报错问题记录
1. 使用DefaultMQProducer生产消息，Debug时正常，Run时报异常
- 异常：`org.apache.rocketmq.client.exception.MQClientException: Send [1] times, still failed, cost [3091]ms, Topic: AsyncTopic, BrokersSent: [broker-a] See http://rocketmq.apache.org/docs/faq/ for further details.org.apache.rocketmq.client.exception.MQClientException: Send [1] times, still failed, cost [3091]ms, Topic: AsyncTopic, BrokersSent: [broker-a]`
- 原因: Debug效率低，Run时效率高，经过分析猜测是发送消息之前Topic: AsyncTopic还未创建成功，导致消息未发送出去，尝试在`producer.start();`之后，添加`Thread.sleep(200);`，重新生产消息，未报此异常


  [1]: https://rocketmq.apache.org/docs/simple-example/

