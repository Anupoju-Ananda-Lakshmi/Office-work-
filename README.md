@KafkaListener(topics = "communication-topic")
public void consume(CommunicationEvent event) {

    EmailRequest request = new EmailRequest();

    request.setRecipientMail(
            event.getRecipients().getEmail());

    Map<String,String> placeholders =
            new HashMap<>();

    placeholders.put(
            "otp",
            event.getTemplateData().getOtpPlaintext());

    placeholders.put(
            "expiryMins",
            event.getTemplateData().getExpiryMins());

    placeholders.put(
            "userName",
            event.getTemplateData().getUserName());

    request.setPlaceHolders(placeholders);

    emailService.sendEmail(request);
}
