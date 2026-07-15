
	docker inspect <kafka-container>


	docker exec -it <kafka-container> env
	
	
	at org.apache.kafka.clients.consumer.KafkaConsumer.<init>(KafkaConsumer.java:595)
	at org.springframework.kafka.core.DefaultKafkaConsumerFactory$ExtendedKafkaConsumer.<init>(DefaultKafkaConsumerFactory.java:505)
	at org.springframework.kafka.core.DefaultKafkaConsumerFactory.createRawConsumer(DefaultKafkaConsumerFactory.java:484)
	at org.springframework.kafka.core.DefaultKafkaConsumerFactory.createKafkaConsumer(DefaultKafkaConsumerFactory.java:461)
	at org.springframework.kafka.core.DefaultKafkaConsumerFactory.createConsumerWithAdjustedProperties(DefaultKafkaConsumerFactory.java:438)
	at org.springframework.kafka.core.DefaultKafkaConsumerFactory.createKafkaConsumer(DefaultKafkaConsumerFactory.java:405)
	at org.springframework.kafka.core.DefaultKafkaConsumerFactory.createConsumer(DefaultKafkaConsumerFactory.java:366)
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.<init>(KafkaMessageListenerContainer.java:866)
	at org.springframework.kafka.listener.KafkaMessageListenerContainer.doStart(KafkaMessageListenerContainer.java:379)
	at org.springframework.kafka.listener.AbstractMessageListenerContainer.start(AbstractMessageListenerContainer.java:512)
	at org.springframework.kafka.listener.ConcurrentMessageListenerContainer.doStart(ConcurrentMessageListenerContainer.java:255)
	at org.springframework.kafka.listener.AbstractMessageListenerContainer.start(AbstractMessageListenerContainer.java:512)
	at org.springframework.kafka.config.KafkaListenerEndpointRegistry.startIfNecessary(KafkaListenerEndpointRegistry.java:436)
	at org.springframework.kafka.config.KafkaListenerEndpointRegistry.start(KafkaListenerEndpointRegistry.java:382)
	at org.springframework.context.support.DefaultLifecycleProcessor.doStart(DefaultLifecycleProcessor.java:288)
	... 13 common frames omitted
Caused by: org.apache.kafka.common.config.ConfigException: No resolvable bootstrap urls given in bootstrap.servers
	at org.apache.kafka.clients.ClientUtils.parseAndValidateAddresses(ClientUtils.java:103)
	at org.apache.kafka.clients.ClientUtils.parseAndValidateAddresses(ClientUtils.java:62)
	at org.apache.kafka.clients.ClientUtils.parseAndValidateAddresses(ClientUtils.java:58)
	at org.apache.kafka.clients.consumer.internals.LegacyKafkaConsumer.<init>(LegacyKafkaConsumer.java:183)
	... 30 common frames omitted



// Application.properties file 

spring.datasource.url=jdbc:oracle:thin:@10.177.103.192:1523/fincorepdb1
#spring.datasource.url=jdbc:oracle:thin:@10.177.179.46:1523/fincorepdb1
spring.datasource.username=fincore
spring.datasource.password=Password#1234
spring.datasource.driver-class-name=oracle.jdbc.OracleDriver

#spring.kafka.consumer.bootstrap-servers=localhost:29092
spring.kafka.consumer.bootstrap-servers=10.0.19.100:9092
spring.kafka.producer.bootstrap-servers=10.0.19.100:9092

#airflow.base-url=http://localhost:1234/airflow
airflow.base-url=https://fincoreuat.sbi/airflow22
#airflow.base-url=https://fincorest.sbi/airflow


# Time in milliseconds
#30 Seconds
app.scheduling.fixedRate.in.ms=9000
app.scheduling.initialDelay.in.ms=8000

sftp.hostname=10.177.177.112
sftp.username=root
sftp.password=root123
sftp.port=22

