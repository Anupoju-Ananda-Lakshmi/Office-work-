package com.fincore.CommunicationService.client;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.Map;

import org.json.JSONObject;

import com.fincore.CommunicationService.exception.CommunicationExceptions.VendorDeliveryException;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class EmailGatewayClient {

    private static final String URL = "url";
    private static final String API_KEY = "api_key";
    private static final String SENDER_ID = "senderId";

    public String sendEmail(Map<String, String> configMap,
                            JSONObject requestBody) {

        HttpURLConnection connection = null;

        try {

            // Validation
            if (configMap == null) {
                throw new VendorDeliveryException("Vendor configuration is null");
            }

            if (configMap.get(URL) == null || configMap.get(URL).isBlank()) {
                throw new VendorDeliveryException("Vendor URL is missing");
            }

            if (configMap.get(API_KEY) == null || configMap.get(API_KEY).isBlank()) {
                throw new VendorDeliveryException("Vendor API key is missing");
            }

            URL url = new URL(configMap.get(URL));

            connection = (HttpURLConnection) url.openConnection();

            connection.setRequestMethod("POST");
            connection.setRequestProperty("Content-Type", "application/json");
            connection.setRequestProperty("api-key", configMap.get(API_KEY));
            connection.setDoOutput(true);

            // Send request body
            try (OutputStream os = connection.getOutputStream()) {

                byte[] input =
                        requestBody.toString().getBytes(StandardCharsets.UTF_8);

                os.write(input, 0, input.length);
            }

            int responseCode = connection.getResponseCode();

            log.info("Vendor Response Code : {}", responseCode);

            // Validate vendor response
            if (responseCode != HttpURLConnection.HTTP_OK
                    && responseCode != HttpURLConnection.HTTP_CREATED
                    && responseCode != HttpURLConnection.HTTP_ACCEPTED) {

                throw new VendorDeliveryException(
                        "Vendor API failed with response code : "
                                + responseCode);
            }

            InputStream is = connection.getInputStream();

            StringBuilder response = new StringBuilder();

            try (BufferedReader br =
                         new BufferedReader(new InputStreamReader(is))) {

                String line;

                while ((line = br.readLine()) != null) {
                    response.append(line);
                }
            }

            log.info("Vendor Response : {}", response);

            return response.toString();

        } catch (VendorDeliveryException e) {

            log.error("Vendor Delivery Exception : {}", e.getMessage());

            throw e;

        } catch (Exception e) {

            log.error("Exception while calling Email Vendor API", e);

            throw new VendorDeliveryException(
                    "Error occurred while calling Email Vendor API", e);

        } finally {

            if (connection != null) {

                connection.disconnect();

                log.info("HTTP connection disconnected successfully.");
            }
        }
    }
}
