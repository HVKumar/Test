import okhttp3.*;
import com.google.gson.*;
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

        OkHttpClient client = new OkHttpClient();
        RequestBody body = RequestBody.create(json.toString(), MediaType.get("application/json"));

        Request request = new Request.Builder()
                .url(tokenApiUrl)
                .post(body)
                .build();

        try (Response response = client.newCall(request).execute()) {
            if (response.isSuccessful()) {
                String responseBody = response.body().string();
                JsonObject responseJson = JsonParser.parseString(responseBody).getAsJsonObject();
                return responseJson.get("token").getAsString();
            } else {
                System.err.println("Token API call failed: " + response.code());
                return null;
            }
        }
    }

    private static void postJsonWithToken(String apiUrl, String token) throws IOException {
        // Create JSON payload for main API
        JsonObject json = new JsonObject();
        json.addProperty("key1", "value1");
        json.addProperty("key2", "value2");

        OkHttpClient client = new OkHttpClient();
        RequestBody body = RequestBody.create(json.toString(), MediaType.get("application/json"));

        Request request = new Request.Builder()
                .url(apiUrl)
                .addHeader("Authorization", "Bearer " + token)
                .post(body)
                .build();

        try (Response response = client.newCall(request).execute()) {
            if (response.isSuccessful()) {
                System.out.println("API Response: " + response.body().string());
            } else {
                System.err.println("API call failed: " + response.code());
            }
        }
    }
}# Test
