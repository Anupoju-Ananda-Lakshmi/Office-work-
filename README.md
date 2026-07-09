package com.fincore.process_status_service.consumer;

import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

import com.fincore.process_status_service.dto.AirflowDagEvent;
import com.fincore.process_status_service.service.ProcessStatusService;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Component
@RequiredArgsConstructor
@Slf4j
public class AirflowEventConsumer {

    private final ProcessStatusService processStatusService;

    @KafkaListener(
            topics = "airflow-events",
            groupId = "Airflow_ETL",
            properties = {
                "spring.json.value.default.type=com.fincore.process_status_service.dto.AirflowDagEvent"
            }
    )
    public void consume(AirflowDagEvent dagEvent) {

        log.info("Received Airflow event : {}", dagEvent);

        processStatusService.handleAirflowEvent(dagEvent);
    }
}
