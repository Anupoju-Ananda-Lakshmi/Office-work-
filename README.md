@Override
	public void handleAirflowEvent(AirflowDagEvent dagEvent) {

		List<String> userRoles = rolePermissionsRepository.fetchRoles();

		Recipients recipients = new Recipients();
		recipients.setRoles(userRoles);
		String etlDate = dagEvent.getEtlDate();
		List<AttachmentRef> attachments = List.of(
				createAttachment(Constants.BAL_COMP_ATTACHEMENT_TYPE, Constants.BAL_COMP_FILE_NAME, etlDate),
				createAttachment(Constants.CIBA_ATTACHEMENT_TYPE, Constants.CIBA_FILE_NAME, etlDate),
				createAttachment(Constants.SUSPENSE_ATTACHEMENT_TYPE, Constants.SUSPENSE_FILE_NAME, etlDate));

		Map<String, Object> payload = new HashMap<>();

		List<ProcessStatusProjection> summaryList = processRunStageRepository.fetchSummary(dagEvent.getProcessRunId(),
				dagEvent.getEtlDate());
		
		Map<String,String> titlesMap = new HashMap<>();
		titlesMap.put("balanceCompareTitle", "Balance Compare");
		titlesMap.put("cibaTitle" , "CIBA");
		titlesMap.put("suspenseTitle" , "Suspense");
		
		Map<String, String> balCompSummaryMap = new HashMap<>();
		Map<String, String> intrSummaryMap = new HashMap<>();
		Map<String, String> cntrSummaryMap = new HashMap<>();

		List<String> formattedBalCompHeaders = new ArrayList<>();
		List<String> formattedIntrHeaders = new ArrayList<>();
		List<String> formattedCntrHeaders = new ArrayList<>();

		for (ProcessStatusProjection summary : summaryList) {
			try {
				switch (summary.getStageId()) {

				case Constants.DIFFERENCE_SUMMARY:
					balCompSummaryMap = objMap.readValue(summary.getSummary(),
							new TypeReference<Map<String, String>>() {
							});
					formattedBalCompHeaders = formatHeaders((List<String>) balCompSummaryMap.keySet());
					break;

				case Constants.CIBA_SUMMARY:
					intrSummaryMap = objMap.readValue(summary.getSummary(), new TypeReference<Map<String, String>>() {
					});
					formattedIntrHeaders = formatHeaders((List<String>) intrSummaryMap.keySet());
					break;

				case Constants.SUSPENSE_SUMMARY:
					cntrSummaryMap = objMap.readValue(summary.getSummary(), new TypeReference<Map<String, String>>() {
					});
					formattedCntrHeaders = formatHeaders((List<String>) cntrSummaryMap.keySet());
					break;

				default:
					break;
				}
			} catch (JsonProcessingException e) {
				throw new RuntimeException("Error parsing summary JSON", e);
			}
		}

		payload.put("processDate", etlDate);
		payload.put("titles", titlesMap);
		payload.put("balanceCompareHeaders", formattedBalCompHeaders);
		payload.put("balanceCompareRows", balCompSummaryMap.get("summary"));
		payload.put("cibaHeaders", formattedIntrHeaders);
		payload.put("cibaRows", intrSummaryMap.get("summary"));
		payload.put("suspenseHeaders", formattedCntrHeaders);
		payload.put("suspenseRows", cntrSummaryMap.get("summary"));
		payload.put("startTime", null);
		payload.put("endTime", null);
		payload.put("timeTaken", null);
		payload.put("STATUS", dagEvent.getStatus());

		CommunicationEvent commEvent = new CommunicationEvent();

		commEvent.setEventId("evt-" + UUID.randomUUID().toString());
		commEvent.setTraceId("trc-" + UUID.randomUUID().toString());
		commEvent.setCorrelationId("batch-" + etlDate);
		commEvent.setProducerService(Constants.PRODUCER_SERVICE);
		commEvent.setEventType(Constants.EVENT_TYPE);
		commEvent.setPriority(Constants.PRIORITY);
		commEvent.setChannels(Constants.CHANNELS);
		commEvent.setRecipients(recipients);
		commEvent.setTemplateId(Constants.TEMPLATE_ID);
		commEvent.setAttachments(attachments);
		commEvent.setPayload(payload);

		// commEvent.setDeepLinks(null);
		// commEvent.setMetaData(null);

		communicationProducer.produceEvent(commEvent);

	}
