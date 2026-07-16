im using Spring boot tool suite dashboard to run the application 

1)@KafkaListener(topics = "airflow-events", groupId = "Airflow_ETL", properties = {
			"spring.json.value.default.type=com.fincore.process_status_service.dto.AirflowDagEvent" })
	public void consume(AirflowDagEvent dagEvent) {
  
2) @KafkaListener(topics = "nwsa-variance-report.started", groupId = "Report-Batch", properties = "spring.json.value.default.type=com.fincore.process_status_service.dto.NwsaVarianceStartedDTO")
    public void consumeStarted(NwsaVarianceStartedDTO dto) {

i have added the log you mentioned in application.properties but i didnt get any logs what should i search for 


bootstrap.servers , BOOTSTRAP_SERVERS not result but spring.kafka.bootstrap-servers i got only one result which is in local properties file 


  
