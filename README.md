@Component
@RequiredArgsConstructor
public class KafkaConsumer {

    private final EmailService emailService;

    @KafkaListener(
            topics = "communication-topic",
            groupId = "communication-group"
    )
    public void consume(CommunicationEvent event) {

        System.out.println(event);

    }
}

kafka listener 




@Data
public class Recipients {

    private String mobile;

    private String email;
}


@Data
public class TemplateData {

    private String otpPlaintext;

    private String expiryMins;

    private String userName;
}


@Data
public class CommunicationEvent {

    private String eventId;

    private String eventType;

    private String txId;

    private String environment;

    private List<String> channels;

    private String templateId;

    private Recipients recipients;

    private TemplateData templateData;

    private String timestamp;
}



