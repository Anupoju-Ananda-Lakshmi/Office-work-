my kafka topic : 

/*
 * KAFKA EVENT PAYLOAD (Before AES Encryption):
 * {
 * "eventId": "evt-uuid-1234",
 * "eventType": "LOGIN_OTP",
 * "txId": "tx-456",
 * "environment": "UAT",
 * "channels": ["SMS", "EMAIL"],
 * "templateId": "AUTH_LOGIN_OTP_V1",
 * "recipients": {
 * "mobile": "+919999999999",
 * "email": "user@sbi.co.in"
 * },
 * "templateData": {
 * "otpPlaintext": "847291",
 * "expiryMins": "5",
 * "userName": "Pratik"
 * },
 * "timestamp": "2026-06-12T10:00:00Z"
 * }
 */ 

request dto:
public class EmailRequest {

	@NotNull(message = "Recipient mail is mandatory")
	@Email(message = "Invalid Email Format")
	private String recipientMail;
	
	@Email(message = "Invalid Email Format")
	private String ccToEmail;

	@NotNull(message = "templateID is mandatory")
	private int templateID;

	@NotNull(message = "Place holders are mandatory")
	private Map<String,String> placeHolders;
	
	@NotNull(message = "Place holders are mandatory")
	private Map<String,String> eventType;
	

}
