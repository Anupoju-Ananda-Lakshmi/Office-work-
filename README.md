[appuser@cec6a9b48911 ~]$ kafka-topics --bootstrap-server localhost:9092 --list
__consumer_offsets
fincore.auth.otp.priority
fincore.auth.otp.priority-dlq
fincore.auth.otp.priority-retry-0
fincore.auth.otp.priority-retry-1
fincore.auth.otp.priority-retry-2
trigger-events
trigger-events-dlt
trigger-events-retry-2000
trigger-events-retry-4000

it have listed some topics  




[appuser@cec6a9b48911 ~]$ env | grep KAFKA
KAFKA_CONTROLLER_LISTENER_NAMES=CONTROLLER
KAFKA_ADVERTISED_LISTENERS=INTERNAL://kafka:29092,EXTERNAL://10.0.19.100:9092
KAFKA_INTER_BROKER_LISTENER_NAME=INTERNAL
KAFKA_CONTROLLER_QUORUM_VOTERS=1@kafka:9093
KAFKA_LOG_DIRS=/tmp/kraft-storage
KAFKA_TRANSACTION_STATE_LOG_MIN_ISR=1
KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS=0
KAFKA_LISTENERS=INTERNAL://kafka:29092,EXTERNAL://0.0.0.0:9092,CONTROLLER://kafka:9093
KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR=1
KAFKA_AUTO_CREATE_TOPICS_ENABLE=true
KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1
KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT,CONTROLLER:PLAINTEXT
KAFKA_NODE_ID=1
KAFKA_PROCESS_ROLES=broker,controller



   <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>
