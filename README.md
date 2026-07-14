public static List<String> formatHeaders(List<String> headers) {

        List<String> formattedHeaders = new ArrayList<>();

        for (String header : headers) {
            formattedHeaders.add(toTitleCase(header));
        }

        return formattedHeaders;
    }
	
    public static String toTitleCase(String text) {

        String[] words = text.replace("_", " ").toLowerCase().split(" ");
        StringBuilder result = new StringBuilder();

        for (String word : words) {
            result.append(Character.toUpperCase(word.charAt(0)))
                  .append(word.substring(1))
                  .append(" ");
        }

        return result.toString().trim();
    }
