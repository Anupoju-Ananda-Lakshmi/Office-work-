kafka event dto :

@Data
public class AirflowDagEvent {

	private String eventType;
	private String dagId;
	private String dagRunId;
	private String logicalDate;
	private String status;
	private String timestamp;
	private String processRunId;
	private String etlDate;
	private String runId;
}


kafka consumer file : 

package com.fincore.process_status_service.consumer;

import com.fincore.process_status_service.dto.AirflowDagEvent;
import com.fincore.process_status_service.service.ProcessStatusService;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
@Slf4j
public class AirflowEventConsumer {


    private  final ProcessStatusService processStatusService;


    @KafkaListener(
            topics = "airflow-events",
            groupId = "Airflow_ETL",
            properties = {"spring.json.value.default.type=com.fincore.process_status_service.dto.AirflowDagEvent"}
    )
    public void consume(AirflowDagEvent dagEvent)  {

        log.info("Received Airflow event: {}", dagEvent);
        processStatusService.sendProcessStatusUpdates();
    }
}


processStatusService file :

 void sendProcessStatusUpdates();


processStatusServiceImpl file :

   public void sendProcessStatusUpdates() {
        log.info("Sending Process Update");
        CompletableFuture.runAsync(() -> {
            Iterator<SseEmitter> emitters = processStatusEmitters.iterator();

            // Only generate data once per broadcast to save DB hits
            Map<String, Object> data = null;
            if(emitters.hasNext()){
                data = getProcessData(null, null);
            }

            while (emitters.hasNext()) {
                SseEmitter emitter = emitters.next();
                try {
                    emitter.send(SseEmitter.event().data(data));
                } catch (IOException e) {
                    log.warn("Client Disconnected, removing SSE emitter");
                    emitter.complete();
                    emitters.remove();
                }
            }
        });
    }
