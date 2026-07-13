@Override
    public void handleAirflowEvent(AirflowDagEvent dagEvent) {

        List<String> userRoles = rolePermissionsRepository.fetchRoles();

        Recipients recipients = new Recipients();
        recipients.setRoles(userRoles);
        String etlDate = dagEvent.getEtlDate();
        List<AttachmentRef> attachments = List.of(
                createAttachment(Constants.BAL_COMP_ATTACHEMENT_TYPE, Constants.BAL_COMP_FILE_NAME,etlDate),
                createAttachment(Constants.CIBA_ATTACHEMENT_TYPE, Constants.CIBA_FILE_NAME,etlDate),
                createAttachment(Constants.SUSPENSE_ATTACHEMENT_TYPE, Constants. SUSPENSE_FILE_NAME,etlDate)
        );
        
        Map<String,Object> payload = new HashMap<>();
        
        List<ProcessStatusProjection> summaryList = processRunStageRepository.fetchSummary(dagEvent.getProcessRunId(),dagEvent.getEtlDate());
        
        ObjectMapper objMap = new ObjectMapper();
        
         Map<String,String> balCompSummaryMap = new HashMap<>();
         Map<String,String> intrSummaryMap = new HashMap<>();
         Map<String,String> cntrSummaryMap = new HashMap<>();
        
        List<String> balCompHeaders = new ArrayList<>();
        List<String> intrHeaders = new ArrayList<>();
        List<String> cntrHeaders = new ArrayList<>();
        
        for(ProcessStatusProjection list : summaryList) {
        	try {
        	if(list.getStageId().equalsIgnoreCase("difference_summary")) {
        		balCompSummaryMap = objMap.readValue(list.getSummary(), new TypeReference<Map<String,String>>() {
				});
        		balCompHeaders.addAll(balCompSummaryMap.keySet());
        	}else if(list.getStageId().equalsIgnoreCase("intr_summary_calculated")) {
        		intrSummaryMap = objMap.readValue(list.getSummary(), new TypeReference<Map<String,String>>() {
				});
        		intrHeaders.addAll(intrSummaryMap.keySet());
        	}else if(list.getStageId().equalsIgnoreCase("cntr_summary_calculated")) {
        		cntrSummaryMap = objMap.readValue(list.getSummary(), new TypeReference<Map<String,String>>() {
				});
        		cntrHeaders.addAll(cntrSummaryMap.keySet());
        	}
        } catch(JsonProcessingException e ) {
        	throw new RuntimeException("Error parsing summary Json",e);
        }
        	} 
       
        
        //payload.put("userName",);
        payload.put("processDate",etlDate);
        payload.put("balanceCompareTitle","Balance Compare");
        payload.put("balanceCompareHeaders",balCompHeaders);
        payload.put("balanceCompareRows",balCompSummaryMap);
        payload.put("cibaTitle","CIBA");
        payload.put("cibaHeaders",intrHeaders);
        payload.put("cibaRows",intrSummaryMap);
        payload.put("suspenseTitle","Suspense");
        payload.put("suspenseHeaders",cntrHeaders);
        payload.put("suspenseRows",cntrSummaryMap);
        payload.put("startTime",null);
        payload.put("endTime",null);
        payload.put("timeTaken",null);
        payload.put("STATUS",dagEvent.getStatus());

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
