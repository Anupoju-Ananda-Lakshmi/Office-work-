spring.application.name=ProcessStatusService
spring.profiles.active=local
server.port=9006

# --- Kafka Consumer Configuration ---
# We connect to Kafka on the EXTERNAL listener
spring.kafka.consumer.group-id=Airflow_ETL
spring.kafka.consumer.auto-offset-reset=earliest

# --- Debezium JSON Deserialization ---
# This tells Spring Kafka to parse the incoming JSON into our Java objects (DTOs)
#spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
#spring.kafka.consumer.value-deserializer=org.springframework.kafka.support.serializer.JsonDeserializer
#spring.kafka.consumer.properties.spring.json.trusted.packages=*
#spring.kafka.consumer.properties.spring.json.use.type.headers=false
#spring.kafka.consumer.properties.spring.json.value.default.type=com.fincore.process_status_service.dto.AirflowDagEvent



# LOGIN SERVICE KEY
jwt.secret=bWV0aGlvbnlsdGhyZW9ueWx0aHJlb255bGdsdXRhbWlueWxhbGFueWw=

# --- Redis Configuration ---
spring.data.redis.host=10.0.17.242
spring.data.redis.port=6379
spring.cache.type=redis




# Airflow API Base URL
#airflow.base-url=http://airflow-api-server.cbops.svc.cluster.local:8080

# Airflow API Version
airflow.api-version=/api/v2

# Authentication (use environment variables in production)
airflow.username=admin
airflow.password=admin

# HTTP Client Timeouts (in milliseconds)
http.connect-timeout=5000
http.read-timeout=10000

# Logging Levels
logging.level.com.example.process=DEBUG
logging.level.org.springframework.web.reactive.function.client=INFO

# DB Timeouts: Configuration of connection pooling timeouts in application.properties.
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.maximum-pool-size=30


# --- Actuator Base Config ---
# Enable the endpoints, but control exposure in specific profile files
management.endpoints.web.base-path=/actuator
management.endpoint.health.probes.enabled=true
management.endpoints.web.exposure.include=health, info, prometheus
management.endpoint.health.show-details=always
management.metrics.tags.application=${spring.application.name}
management.info.env.enabled=true

spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializer
#
## Optional: Ensures type information is handled correctly in the DLT
#spring.kafka.producer.properties.spring.json.add.type.headers=true


spring.kafka.consumer.key-deserializer=org.springframework.kafka.support.serializer.ErrorHandlingDeserializer
spring.kafka.consumer.value-deserializer=org.springframework.kafka.support.serializer.ErrorHandlingDeserializer
spring.kafka.consumer.properties.spring.deserializer.key.delegate.class=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.properties.spring.deserializer.value.delegate.class=org.springframework.kafka.support.serializer.JsonDeserializer
spring.kafka.consumer.properties.spring.json.trusted.packages=*
spring.kafka.consumer.properties.spring.json.use.type.headers=false
spring.kafka.consumer.enable-auto-commit=false
spring.kafka.listener.ack-mode = manual

# ==============================================================
# JACKSON CONFIGURATION
# ==============================================================
#spring.jackson.date-format=dd/MM/yyyy
spring.jackson.time-zone=Asia/Kolkata
# Ensure dates are Strings (e.g. "2023-10-01 13:30"), not timestamps (169000...)
spring.jackson.serialization.write-dates-as-timestamps=false


# Time in milliseconds
#30 Seconds
app.scheduling.fixedRate.in.ms=300000
app.scheduling.initialDelay.in.ms=600000


#NiFi
nifi.username=fincore
nifi.password=Password#1234
nifi.base-url=https://10.177.177.93:443/nifi-api

reconciliation.queued-timeout-minutes=30



#
##new fix to handle large payload in network gzip
#server.compression.enabled=true
#server.compression.mime-types=application/json,text/event-stream
#server.compression.min-response-size=1024


i have checked in common utilities there are no kafka configuarations
