public void consume(AirflowDagEvent dagEvent) {

		log.info("Received Airflow event : {}", dagEvent);
		
		int status = Integer.parseInt(dagEvent.getStatus());
		if (status == Constants.WAITING_FOR_APPROVAL) {
			processStatusService.handleAirflowEvent(dagEvent);
		}

		processStatusService.sendProcessStatusUpdates();
	}
