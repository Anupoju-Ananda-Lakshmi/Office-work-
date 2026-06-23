package com.fincore.CommunicationService.client;

import java.io.BufferedReader; 
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.Map;

import org.json.JSONArray;
import org.json.JSONObject;

import com.fincore.CommunicationService.exception.CommunicationExceptions.VendorDeliveryException;

// Required imports for SSL bypass
import javax.net.ssl.*;
import java.security.cert.X509Certificate;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class EmailGatewayClient {

	private static final String URL = "url";
	private static final String API_KEY = "api_key";
	private static final String SENDER_ID = "senderId";
	private static final String TO_EMAIL = "toEmail";
	private static final String CC_TO_EMAIL = "ccToEmail";
	private static final String EMAIL_BODY = "emailBody";
	private static final String SUBJECT = "subject";

	public static int sendEmail(Map<String, Object> emailRequest) {

		HttpURLConnection emailApiConnection = null;
		log.info("Connection created");
		try {
			// Email Request Values
			String apiUrl = emailRequest.get(URL).toString();
			String apiKey = emailRequest.get(API_KEY).toString();
			String senderEmail = emailRequest.get(SENDER_ID).toString();
			String recipientMail = emailRequest.get(TO_EMAIL).toString();
			String ccToEmail = emailRequest.get(CC_TO_EMAIL).toString();
			String emailBody = emailRequest.get(EMAIL_BODY).toString();
			String subject = emailRequest.get(SUBJECT).toString();

			// Open Connection
			URL url = new URL(apiUrl);
			emailApiConnection = (HttpURLConnection) url.openConnection();
			log.info("Connection opened");
			emailApiConnection.setRequestMethod("POST");
			emailApiConnection.setDoOutput(true);

			// ARCHITECTURE MANDATE: Reduced timeouts from 10s to 3s to prevent Kafka thread
			// starvation
			emailApiConnection.setConnectTimeout(3000);
			emailApiConnection.setReadTimeout(3000);

			// --- BYPASS SSL VERIFICATION FOR THIS CONNECTION ---
			if (emailApiConnection instanceof HttpsURLConnection) {
				HttpsURLConnection httpsConnection = (HttpsURLConnection) emailApiConnection;
				try {
					TrustManager[] trustAllCerts = new TrustManager[] { new X509TrustManager() {
						public X509Certificate[] getAcceptedIssuers() {
							return null;
						}

						public void checkClientTrusted(X509Certificate[] certs, String authType) {
						}

						public void checkServerTrusted(X509Certificate[] certs, String authType) {
						}
					} };
					SSLContext sc = SSLContext.getInstance("TLS");
					sc.init(null, trustAllCerts, new java.security.SecureRandom());
					httpsConnection.setSSLSocketFactory(sc.getSocketFactory());
					httpsConnection.setHostnameVerifier((hostname, session) -> true);
				} catch (Exception e) {
					log.error("Failed to set SSL bypass: ", e);
				}
			}
			// -------------------------------------------------------------

			// Request headers
			emailApiConnection.setRequestProperty("Content-Type", "application/json");
			emailApiConnection.setRequestProperty("Accept", "application/json");
			emailApiConnection.setRequestProperty("api_key", apiKey);
			emailApiConnection.setDoOutput(true);

			log.info("Done Setting properties");

			// Sender Details
			JSONObject senderJson = new JSONObject();
			senderJson.put("email", senderEmail);
			senderJson.put("name", "FINCORE");

			// Email Content
			JSONObject emailContentJson = new JSONObject();
			emailContentJson.put("type", "html");
			emailContentJson.put("value", emailBody);

			JSONArray contentArray = new JSONArray();
			contentArray.put(emailContentJson);

			// Recipient Details
			JSONObject recipientJson = new JSONObject();
			recipientJson.put("email", recipientMail);

			JSONArray recipientArray = new JSONArray();
			recipientArray.put(recipientJson);

			// Custom Attributes
			JSONObject attributesJson = new JSONObject();
			attributesJson.put("LEAD", "Andy Dwyer");
			attributesJson.put("BAND", "Mouse Rat");

			// Personalization
			JSONObject personalizationsJson = new JSONObject();
			personalizationsJson.put("to", recipientArray);
			personalizationsJson.put("attributes", attributesJson);

			// CC details
			if (emailRequest.containsKey("ccToEmail")) {
				JSONObject ccRecipientJson = new JSONObject();
				ccRecipientJson.put("email", ccToEmail);

				JSONArray ccArray = new JSONArray();
				ccArray.put(ccRecipientJson);
				personalizationsJson.put("cc", ccArray);
			}

			personalizationsJson.put("token_to", "8693839845");
			personalizationsJson.put("token_cc", "MSGID657243");
			personalizationsJson.put("token_bcc", "MSGID657244");

			JSONArray personalizationsJsonArray = new JSONArray();
			personalizationsJsonArray.put(personalizationsJson);

			// Tracking Settings
			JSONObject trackingSettingsJson = new JSONObject();
			trackingSettingsJson.put("open_track", true);
			trackingSettingsJson.put("click_track", true);
			trackingSettingsJson.put("unsubscribe_track", true);

			log.info("building email request json");

			// Final Request JSON
			JSONObject emailRequestJson = new JSONObject();
			emailRequestJson.put("from", senderJson);
			emailRequestJson.put("subject", subject);
			emailRequestJson.put("content", contentArray);
			emailRequestJson.put("personalizations", personalizationsJsonArray);
			emailRequestJson.put("settings", trackingSettingsJson);

			String finalRequestBody = emailRequestJson.toString();

			log.info("finalRequestBody   " + finalRequestBody);

			// Send Request
			log.info("sending request");
			try (OutputStream outputStream = emailApiConnection.getOutputStream()) {
				byte[] requestBytes = finalRequestBody.getBytes(StandardCharsets.UTF_8);
				outputStream.write(requestBytes);
				log.info("Request sent");
			}
			// Read response
			int responseCode = emailApiConnection.getResponseCode();
			log.info("responseCode" + responseCode);
			InputStream responseStream = (responseCode >= 200 && responseCode < 300)
					? emailApiConnection.getInputStream()
					: emailApiConnection.getErrorStream();

			if (responseStream != null) {
				try (BufferedReader reader = new BufferedReader(
						new InputStreamReader(responseStream, StandardCharsets.UTF_8))) {

					StringBuilder responseBody = new StringBuilder();
					String line;

					while ((line = reader.readLine()) != null) {
						responseBody.append(line).append("\n");
					}

					log.info("Server Response: " + responseBody);
				}
			} else {
				log.info("No response body received. HTTP " + responseCode);
			}

			return responseCode;

		} catch (VendorDeliveryException e) {
			log.error("Error occurred while sending Email " + e);
		} finally {
			if (emailApiConnection != null) {
				emailApiConnection.disconnect();
			}
		}
		return 0;
	}
}
