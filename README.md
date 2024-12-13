<dependencies>
    <!-- Apache HttpClient -->
    <dependency>
        <groupId>org.apache.httpcomponents.client5</groupId>
        <artifactId>httpclient5</artifactId>
        <version>5.4</version>
    </dependency>
    
    <!-- JSON library (Gson) -->
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.10.1</version>
    </dependency>
</dependencies>



import org.apache.hc.client5.http.classic.methods.HttpPost;
import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.CloseableHttpResponse;
import org.apache.hc.client5.http.impl.sync.HttpClients;
import org.apache.hc.core5.http.io.entity.StringEntity;
import org.apache.hc.core5.http.ContentType;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

import java.io.IOException;

public class ApiPoster {

    public static void main(String[] args) {
        try {
            // Step 1: Get the token from the token API
            String token = getToken("https://token-api.example.com/getToken", "your-client-id", "your-client-secret");

            // Step 2: Use the token to post JSON data to the target API
            if (token != null) {
                postJsonWithToken("https://target-api.example.com/endpoint", token);
            } else {
                System.out.println("Failed to retrieve token.");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static String getToken(String tokenApiUrl, String clientId, String clientSecret) throws IOException {
        // Create JSON payload for token request
        JsonObject json = new JsonObject();
        json.addProperty("clientId", clientId);
        json.addProperty("clientSecret", clientSecret);

        // Initialize HttpClient
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            HttpPost postRequest = new HttpPost(tokenApiUrl);
            postRequest.setEntity(new StringEntity(json.toString(), ContentType.APPLICATION_JSON));

            // Execute request
            try (CloseableHttpResponse response = httpClient.execute(postRequest)) {
                int statusCode = response.getCode();
                if (statusCode == 200) {
                    String responseBody = new String(response.getEntity().getContent().readAllBytes());
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
        // Create JSON payload for main API
        JsonObject json = new JsonObject();
        json.addProperty("key1", "value1");
        json.addProperty("key2", "value2");

        // Initialize HttpClient
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            HttpPost postRequest = new HttpPost(apiUrl);
            postRequest.setHeader("Authorization", "Bearer " + token);
            postRequest.setEntity(new StringEntity(json.toString(), ContentType.APPLICATION_JSON));

            // Execute request
            try (CloseableHttpResponse response = httpClient.execute(postRequest)) {
                int statusCode = response.getCode();
                if (statusCode == 200) {
                    String responseBody = new String(response.getEntity().getContent().readAllBytes());
                    System.out.println("API Response: " + responseBody);
                } else {
                    System.err.println("API call failed with status: " + statusCode);
                }
            }
        }
    }
}
