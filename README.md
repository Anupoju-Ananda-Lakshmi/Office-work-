@Override
public void handleAirflowEvent(AirflowDagEvent dagEvent) {

    List<String> userRoles = repository.fetchRoles();

    Recipients recipients = new Recipients();
    recipients.setRoles(userRoles);

    List<AttachmentRef> attachments = List.of(
            createAttachment(BAL_COMP_ATTACHMENT_TYPE, BAL_COMP_FILE_NAME),
            createAttachment(CIBA_ATTACHMENT_TYPE, CIBA_FILE_NAME),
            createAttachment(SUSPENSE_ATTACHMENT_TYPE, SUSPENSE_FILE_NAME)
    );

    CommunicationEvent commEvent = new CommunicationEvent();

    commEvent.setProducerService(PRODUCER_SERVICE);
    commEvent.setEventType(EVENT_TYPE);
    commEvent.setPriority(PRIORITY);
    commEvent.setChannels(CHANNELS);
    commEvent.setRecipients(recipients);
    commEvent.setTemplateId(TEMPLATE_ID);
    commEvent.setAttachments(attachments);

    // Build payload from Airflow event when ready
    // commEvent.setPayload(...);

    // Publish event
    communicationProducer.publish(commEvent);

    // Notify dashboard
    sendProcessStatusUpdates();
}
