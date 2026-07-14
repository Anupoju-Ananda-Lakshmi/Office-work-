public static List<String> formatHeaders(List<String> headers) {
    return headers.stream()
            .map(header -> toTitleCase(header))
            .toList();
}


Map<String, Map<String, String>> summaryData = new HashMap<>();
Map<String, List<String>> headerData = new HashMap<>();

for (ProcessStatusProjection summary : summaryList) {
    try {
        Map<String, String> map = objMap.readValue(
                summary.getSummary(),
                new TypeReference<Map<String, String>>() {}
        );

        summaryData.put(summary.getStageId(), map);
        headerData.put(summary.getStageId(),
                formatHeaders(new ArrayList<>(map.keySet())));

    } catch (JsonProcessingException e) {
        throw new RuntimeException("Error parsing summary JSON", e);
    }
}


payload.put("processDate", etlDate);
payload.put("titles", titlesMap);

payload.put("balanceCompareHeaders",
        headerData.get(Constants.DIFFERENCE_SUMMARY));
payload.put("balanceCompareRows",
        summaryData.get(Constants.DIFFERENCE_SUMMARY).get("summary"));

payload.put("cibaHeaders",
        headerData.get(Constants.CIBA_SUMMARY));
payload.put("cibaRows",
        summaryData.get(Constants.CIBA_SUMMARY).get("summary"));

payload.put("suspenseHeaders",
        headerData.get(Constants.SUSPENSE_SUMMARY));
payload.put("suspenseRows",
        summaryData.get(Constants.SUSPENSE_SUMMARY).get("summary"));

payload.put("startTime", null);
payload.put("endTime", null);
payload.put("timeTaken", null);
payload.put("STATUS", dagEvent.getStatus());




public static String toTitleCase(String text) {
    return Arrays.stream(text.replace("_", " ")
                    .toLowerCase()
                    .split(" "))
            .map(word -> Character.toUpperCase(word.charAt(0)) + word.substring(1))
            .collect(Collectors.joining(" "));
}
