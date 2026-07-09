package com.fincore.process_status_service.producer;

import java.util.Set;
import java.util.stream.Collectors;


import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

import com.fincore.process_status_service.dto.SingleReportTriggerDTO;

import jakarta.validation.ConstraintViolation;
import jakarta.validation.Validator;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Service
@RequiredArgsConstructor
@Slf4j
public class SingleReportProducer {

    private final KafkaTemplate<String, Object> kafkaTemplate;
    private final Validator validator;

    private static final String TOPIC = "report-builder.single-mode.trigger";

    public void triggerReport(SingleReportTriggerDTO dto) {

        Set<ConstraintViolation<SingleReportTriggerDTO>> violations = validator.validate(dto);

        if (!violations.isEmpty()) {

            String error = violations.stream()
                    .map(ConstraintViolation::getMessage)
                    .collect(Collectors.joining(", "));

            log.error("Validation Failed for trigger event: {}", error);
            return;
        }

        log.info("Producing single mode trigger event: {}", dto);

        kafkaTemplate.send(TOPIC, dto);
    }

}
