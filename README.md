import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.entity.StringEntity;
import org.apache.http.util.EntityUtils;

import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

import java.io.IOException;

public class ApiPoster {

    public static void main(String[] args) {
        try {
            // Step 1: Get the token from the token API
            String token = getToken(
                    "https://token-api.example.com/getToken", 
                    "your-client-id", 
                    "your-client-secret"
            );

            // Step 2: Use the token to post data
            if (token != null) {
                postJsonWithToken(
                        "https://target-api.example.com/endpoint", 
                        token
                );
            } else {
                System.out.println("Failed to retrieve token.");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static String getToken(String tokenApiUrl, String clientId, String clientSecret) throws IOException {
        // Create JSON payload for token request
        JsonObject payload = new JsonObject();
        payload.addProperty("clientId", clientId);
        payload.addProperty("clientSecret", clientSecret);

        // Initialize HttpClient
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            HttpPost postRequest = new HttpPost(tokenApiUrl);
            postRequest.setEntity(new StringEntity(payload.toString(), "UTF-8"));
            postRequest.setHeader("Content-Type", "application/json");

            // Execute request and process response
            try (CloseableHttpResponse response = httpClient.execute(postRequest)) {
                int statusCode = response.getStatusLine().getStatusCode();
                if (statusCode == 200) {
                    // Extract response body
                    String responseBody = EntityUtils.toString(response.getEntity(), "UTF-8");
                    JsonObject responseJson = JsonParser.parseString(responseBody).getAsJsonObject();
                    return responseJson.get("token").getAsString();
                } else {
                    System.err.println("Token API call failed with status: " + statusCode);
                    return null;
                }
            }
        }
    }

    private static void postJsonWithToken(String apiUrl, String token) throws IOException {
        // Create JSON payload for the main API request
        JsonObject payload = new JsonObject();
        payload.addProperty("key1", "value1");
        payload.addProperty("key2", "value2");

        // Initialize HttpClient
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            HttpPost postRequest = new HttpPost(apiUrl);
            postRequest.setHeader("Authorization", "Bearer " + token);
            postRequest.setEntity(new StringEntity(payload.toString(), "UTF-8"));
            postRequest.setHeader("Content-Type", "application/json");

            // Execute request and process response
            try (CloseableHttpResponse response = httpClient.execute(postRequest)) {
                int statusCode = response.getStatusLine().getStatusCode();
                if (statusCode == 200) {
                    // Extract response body
                    String responseBody = EntityUtils.toString(response.getEntity(), "UTF-8");
                    System.out.println("API Response: " + responseBody);
                } else {
                    System.err.println("Main API call failed with status: " + statusCode);
                }
            }
        }
    }
}
