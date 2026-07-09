package com.fincore.process_status_service.consumer;

import com.fincore.process_status_service.dto.AirflowDagEvent;
import com.fincore.process_status_service.dto.AttachmentRef;
import com.fincore.process_status_service.dto.CommunicationEvent;
import com.fincore.process_status_service.dto.Recipients;
import com.fincore.process_status_service.repository.RolePermissionsRepository;
import com.fincore.process_status_service.service.ProcessStatusService;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

import java.util.ArrayList;
import java.util.List;

import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
@Slf4j
public class AirflowEventConsumer {

	private final ProcessStatusService processStatusService;

	private final RolePermissionsRepository repository;

	private static final String PRODUCER_SERVICE = "PROCESS_STATUS";
	private static final String EVENT_TYPE = "EOD_EVENT";
	private static final String PRIORITY = "NORMAL";
	private static final String TEMPLATE_ID = "SUCCESS_EOD_TEMPLATE";
	private static final List<String> CHANNELS = List.of("SMS", "EMAIL");
	private static final String BAL_COMP_ATTACHEMENT_TYPE = "BAL_COMP_SUMMARY";
	private static final String CIBA_ATTACHEMENT_TYPE = "CIBA_SUMMARY";
	private static final String SUSPENSE_ATTACHEMENT_TYPE = "SUSPENSE_SUMMARY";
	private static final String BAL_COMP_FILE_NAME = "BalCompareReport";
	private static final String CIBA_FILE_NAME = "CIBAReport";
	private static final String SUSPENSE_FILE_NAME = "SuspenseReport";

	@KafkaListener(topics = "airflow-events", groupId = "Airflow_ETL", properties = {
			"spring.json.value.default.type=com.fincore.process_status_service.dto.AirflowDagEvent" })
	public void consume(AirflowDagEvent dagEvent) {

		log.info("Received Airflow event: {}", dagEvent);

		List<String> userRoles = repository.fetchRoles();

		Recipients recipients = new Recipients();
		recipients.setRoles(userRoles);

		AttachmentRef bcAttachmentRef = new AttachmentRef();
		bcAttachmentRef.setAttachmentType(BAL_COMP_ATTACHEMENT_TYPE);
		bcAttachmentRef.setFileName(BAL_COMP_FILE_NAME);

		AttachmentRef cibaAttachmentRef = new AttachmentRef();
		cibaAttachmentRef.setAttachmentType(CIBA_ATTACHEMENT_TYPE);
		cibaAttachmentRef.setFileName(CIBA_FILE_NAME);

		AttachmentRef suspAttachmentRef = new AttachmentRef();
		suspAttachmentRef.setAttachmentType(SUSPENSE_ATTACHEMENT_TYPE);
		suspAttachmentRef.setFileName(SUSPENSE_FILE_NAME);

		List<AttachmentRef> list = new ArrayList<>();
		list.add(bcAttachmentRef);
		list.add(cibaAttachmentRef);
		list.add(suspAttachmentRef);

		CommunicationEvent commEvent = new CommunicationEvent();

		commEvent.setProducerService(PRODUCER_SERVICE);
		commEvent.setEventType(EVENT_TYPE);
		commEvent.setPriority(PRIORITY);
		commEvent.setChannels(CHANNELS);
		commEvent.setRecipients(recipients);
		commEvent.setTemplateId(TEMPLATE_ID);

		// commEvent.setPayload(null);

		commEvent.setAttachments(list);

		// commEvent.setDeepLinks(null);
		// commEvent.setMetaData(null);
		processStatusService.sendProcessStatusUpdates();
	}
}
