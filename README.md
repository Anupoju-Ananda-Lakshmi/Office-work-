# ===================================================================
# SPRING DATASOURCE (ORACLE)
# ===================================================================
spring.datasource.url=jdbc:oracle:thin:@10.177.179.85:1523/fincorepdb1
spring.datasource.username=fincore
spring.datasource.password=Password#1234
spring.datasource.driver-class-name=oracle.jdbc.OracleDriver

# ===================================================================
# REDIS CONFIG
# ===================================================================
spring.data.redis.host=redis-service
spring.data.redis.port=6379
spring.cache.type=redis


# ===================================================================
# ENVIRONMENT NAME
# ===================================================================
app.preIpString=UAT


# ===================================================================
# KAFKA CONSUMER CONFIG
# ===================================================================
spring.kafka.consumer.bootstrap-servers=kafka.uat-cbops1.svc.cluster.local:9092
spring.kafka.producer.bootstrap-servers=kafka.uat-cbops1.svc.cluster.local:9092
spring.kafka.consumer.group-id=communication-login-otp-group
# Auto offset: if a new consumer group starts, read from the earliest unread message
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.springframework.kafka.support.serializer.JsonDeserializer
# JSON Deserialization Mappings
spring.kafka.consumer.properties.spring.json.trusted.packages=*
spring.kafka.consumer.properties.spring.json.value.default.type=com.fincore.CommunicationService.dto.KafkaEvent
spring.kafka.listener.ack-mode=manual-immediate
# Shared aes key to decrypt the otp event payload
security.internal.kafka-aes-key: ANLVNly0zpH/6m4+1Afs6Qwowq6zgURNjCe0TEgSXbY=
