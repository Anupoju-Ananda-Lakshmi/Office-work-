@KafkaListener(topics = "airflow-events", groupId = "Airflow_ETL", properties = {
			"spring.json.value.default.type=com.fincore.process_status_service.dto.AirflowDagEvent" })


@KafkaListener(topics = "nwsa-variance-report.started", groupId = "Report-Batch", properties = "spring.json.value.default.type=com.fincore.process_status_service.dto.NwsaVarianceStartedDTO")


@KafkaListener(topics = "nwsa-variance-report.completed", groupId = "Report-Batch", properties = "spring.json.value.default.type=com.fincore.process_status_service.dto.NwsaVarianceCompletedDTO")


@KafkaListener(topics = "allfiletodatalake", groupId = "pyspark-kafka-group", containerFactory = "manualAckKafkaListenerContainerFactory", properties = {"spring.json.value.default.type=com.fincore.process_status_service.dto.FileEvent"} )


@KafkaListener(topics = "report-builder.batch-mode.completed", groupId = "Report-Batch", properties = "spring.json.value.default.type=com.fincore.process_status_service.dto.ReportBatchCompletedDTO")
