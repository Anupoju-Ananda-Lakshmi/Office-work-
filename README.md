@Slf4j
public class EmailTemplateUtil {

	public static String buildEmailBody(String templateName, Map<String, String> values) throws IOException {
        String template = EmailTemplateUtil.loadEmailTemplate(templateName);
        StrSubstitutor substitutor = new StrSubstitutor(values);
        return substitutor.replace(template);
    }

    public static String loadEmailTemplate(String fileName) throws IOException {

        String path = "email-templates/" + fileName;

        try (InputStream is = EmailTemplateUtil.class.getClassLoader().getResourceAsStream(path)) {
            if (is == null) {
                throw new IOException("File not found: " + path);
            }
            return new String(is.readAllBytes(), StandardCharsets.UTF_8);
        }

    }

}


