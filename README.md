for (ProcessStatusProjection summary : summaryList) {
    try {
        switch (summary.getStageId()) {

            case Constants.DIFFERENCE_SUMMARY:
                balCompSummaryMap = objectMapper.readValue(
                        summary.getSummary(),
                        new TypeReference<Map<String, String>>() {});
                balCompHeaders.addAll(balCompSummaryMap.keySet());
                break;

            case Constants.INTR_SUMMARY_CALCULATED:
                intrSummaryMap = objectMapper.readValue(
                        summary.getSummary(),
                        new TypeReference<Map<String, String>>() {});
                intrHeaders.addAll(intrSummaryMap.keySet());
                break;

            case Constants.CNTR_SUMMARY_CALCULATED:
                cntrSummaryMap = objectMapper.readValue(
                        summary.getSummary(),
                        new TypeReference<Map<String, String>>() {});
                cntrHeaders.addAll(cntrSummaryMap.keySet());
                break;

            default:
                break;
        }
    } catch (JsonProcessingException e) {
        throw new RuntimeException("Error parsing summary JSON", e);
    }
}

