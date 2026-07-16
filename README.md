#
#
# @author:Sampath [v1017860]
#
#

spring.datasource.url=jdbc:oracle:thin:@10.177.103.192:1523/fincorepdb1
#spring.datasource.url=jdbc:oracle:thin:@10.177.179.46:1523/fincorepdb1
spring.datasource.username=fincore
spring.datasource.password=Password#1234
spring.datasource.driver-class-name=oracle.jdbc.OracleDriver

#spring.kafka.consumer.bootstrap-servers=localhost:29092
#pring.kafka.consumer.bootstrap-servers=10.0.19.100:9092
#spring.kafka.producer.bootstrap-servers=10.0.19.100:9092
spring.kafka.bootstrap-servers=localhost:9092

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






