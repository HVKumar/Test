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



import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class OracleDBConnection {
    public static Connection getConnection() throws SQLException {
        String dbUrl = "jdbc:oracle:thin:@your-db-host:1521:your-service-name";
        String dbUser = "your-username";
        String dbPassword = "your-password";

        // Load Oracle JDBC Driver (optional for newer versions)
        try {
            Class.forName("oracle.jdbc.driver.OracleDriver");
        } catch (ClassNotFoundException e) {
            System.err.println("Oracle JDBC Driver not found. Add it to your classpath.");
            e.printStackTrace();
        }

        return DriverManager.getConnection(dbUrl, dbUser, dbPassword);
    }
}


import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class FetchDetailsFromDB {

    public static void fetchDetails(String keyFromApiResponse) {
        String query = "SELECT column1, column2 FROM your_table WHERE some_column = ?";

        try (Connection conn = OracleDBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {

            // Set the value from API response
            stmt.setString(1, keyFromApiResponse);

            // Execute the query
            ResultSet rs = stmt.executeQuery();

            // Process the results
            while (rs.next()) {
                String column1 = rs.getString("column1");
                String column2 = rs.getString("column2");
                System.out.println("Column1: " + column1 + ", Column2: " + column2);
            }

        } catch (Exception e) {
            System.err.println("Database operation failed.");
            e.printStackTrace();
        }
    }
}


public static void main(String[] args) {
    // Example API response
    String apiResponseKey = "some-value-from-api";

    // Fetch details from Oracle DB
    FetchDetailsFromDB.fetchDetails(apiResponseKey);
}


SELECT name, age FROM employees WHERE id = 12345;




package test;

import javax.swing.*;
import java.awt.*;
import java.util.ArrayList;
import java.util.List;

public class ApiPostGuiWithTabs {
    public static void main(String[] args) {
        // Create the main frame
        JFrame frame = new JFrame("API Post GUI with Tabs");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(1200, 800);
        frame.setResizable(true); // Allow resizing
        frame.setLayout(new BorderLayout());

        // Lists to keep track of components
        List<ButtonGroup> radioGroups = new ArrayList<>();
        List<JCheckBox> checkBoxes = new ArrayList<>();
        List<JComboBox<String>> comboBoxes = new ArrayList<>();

        // Tab 1 Content
        JPanel tab1Panel = new JPanel();
        tab1Panel.setLayout(new GridLayout(3, 1)); // 3 sections: input options, payload, output

        // Input Options Panel
        JPanel inputPanel = new JPanel();
        inputPanel.setBorder(BorderFactory.createTitledBorder("Input Options"));
        inputPanel.setLayout(new GridLayout(1, 3)); // 3 columns

        // Column 1: Radio Buttons with Labels (without DES row)
        JPanel column1 = new JPanel(new GridLayout(4, 1)); // 4 rows (removed DES row)
        column1.setBorder(BorderFactory.createTitledBorder("Column 1"));
        column1.setAlignmentX(Component.LEFT_ALIGNMENT);

        column1.add(createRowWithLabelAndRadioButtons("In/Out", radioGroups, "OutPut", "InPut"));
        column1.add(createRowWithLabelAndRadioButtons("FD", radioGroups, "Ma", "Op"));
        column1.add(createRowWithLabelAndRadioButtons("MSG", radioGroups, "ABC", "DEFG", "HIJK", "LMNO"));

        // Column 2: Editable Drop-downs (added DES row and moved Default/Custom to bottom)
        JPanel column2 = new JPanel(new GridLayout(4, 1)); // 4 rows (added DES row and moved Json/XML to bottom)
        column2.setBorder(BorderFactory.createTitledBorder("Column 2"));
        column2.setAlignmentX(Component.LEFT_ALIGNMENT);

        column2.add(createRowWithLabelAndRadioButtons("DES", radioGroups, "SIM-123456789", "TC-987654321"));
        column2.add(createRowWithLabelsAndDropDowns("D:", "DR:", comboBoxes));
        column2.add(createRowWithLabelsAndDropDowns("C:", "CR:", comboBoxes));
        column2.add(createRowWithLabelAndRadioButtons("Json/XML", radioGroups, "Default", "Custom"));

        // Column 3: First row - EVN, Second row - Check In, Third row - Send To, Fourth row - Buttons
        JPanel column3 = new JPanel(new GridLayout(4, 1)); // 4 rows
        column3.setBorder(BorderFactory.createTitledBorder("Column 3"));
        column3.setAlignmentX(Component.LEFT_ALIGNMENT);

        column3.add(createRowWithLabelAndRadioButtons("EVN", radioGroups, "ev1", "ev2", "ev3", "ev4"));

        JPanel checkInPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        checkInPanel.add(new JLabel("Check In"));
        JCheckBox gCheckBox = new JCheckBox("G");
        JCheckBox eCheckBox = new JCheckBox("E");
        JCheckBox tCheckBox = new JCheckBox("T");
        checkBoxes.add(gCheckBox);
        checkBoxes.add(eCheckBox);
        checkBoxes.add(tCheckBox);
        checkInPanel.add(gCheckBox);
        checkInPanel.add(eCheckBox);
        checkInPanel.add(tCheckBox);
        column3.add(checkInPanel);

        column3.add(createRowWithLabelAndRadioButtons("Send To", radioGroups, "C", "GP"));

        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.LEFT)); // Align buttons to the left
        JButton resetButton = new JButton("Reset");
        JButton submitButton = new JButton("Submit");
        buttonPanel.add(resetButton);
        buttonPanel.add(submitButton);
        column3.add(buttonPanel);

        inputPanel.add(column1);
        inputPanel.add(column2);
        inputPanel.add(column3);

        // Payload Panel
        JPanel payloadPanel = new JPanel(new GridLayout(1, 2)); // 2 columns
        payloadPanel.setBorder(BorderFactory.createTitledBorder("Payload"));

        JTextArea requestArea = new JTextArea();
        requestArea.setBorder(BorderFactory.createTitledBorder("Request"));
        JScrollPane requestScrollPane = new JScrollPane(requestArea);

        JTextArea responseArea = new JTextArea();
        responseArea.setBorder(BorderFactory.createTitledBorder("Response"));
        JScrollPane responseScrollPane = new JScrollPane(responseArea);

        payloadPanel.add(requestScrollPane);
        payloadPanel.add(responseScrollPane);

        // Output Panel
        JTextArea outputArea = new JTextArea();
        outputArea.setBorder(BorderFactory.createTitledBorder("Output"));
        JScrollPane outputScrollPane = new JScrollPane(outputArea);

        tab1Panel.add(inputPanel);
        tab1Panel.add(payloadPanel);
        tab1Panel.add(outputScrollPane);

        // Tab 2 Content
        JPanel tab2Panel = new JPanel();
        tab2Panel.setBorder(BorderFactory.createTitledBorder("Tab 2: Data for Tab 1"));
        JTextArea tab2Area = new JTextArea("Data related to Tab 1 goes here.");
        tab2Area.setEditable(false);
        tab2Panel.setLayout(new BorderLayout());
        tab2Panel.add(new JScrollPane(tab2Area), BorderLayout.CENTER);

        // Add tabs to the frame
        JTabbedPane tabbedPane = new JTabbedPane();
        tabbedPane.addTab("Input & Payload", tab1Panel);
        tabbedPane.addTab("Tab 2 Data", tab2Panel);

        frame.add(tabbedPane, BorderLayout.CENTER);

        // Reset Button Action
        resetButton.addActionListener(e -> {
            for (ButtonGroup group : radioGroups) {
                group.clearSelection();
                group.getElements().nextElement().setSelected(true); // Select the first button in each group
            }
            for (JCheckBox checkBox : checkBoxes) {
                checkBox.setSelected(false); // Deselect all checkboxes
            }
            for (JComboBox<String> comboBox : comboBoxes) {
                comboBox.setSelectedIndex(0); // Select the first option in dropdowns
            }
        });

        // Display the frame
        frame.setVisible(true);
    }

    // Helper methods
    private static JPanel createRowWithLabelAndRadioButtons(String label, List<ButtonGroup> radioGroups, String... buttonLabels) {
        JPanel panel = new JPanel(new FlowLayout(FlowLayout.LEFT)); // Align components to the left
        panel.add(new JLabel(label));
        ButtonGroup group = new ButtonGroup();
        radioGroups.add(group); // Track the group
        for (String labelText : buttonLabels) {
            JRadioButton radioButton = new JRadioButton(labelText);
            group.add(radioButton);
            panel.add(radioButton);
        }
        return panel;
    }

    private static JPanel createRowWithLabelsAndDropDowns(String label1, String label2, List<JComboBox<String>> comboBoxes) {
        JPanel panel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        panel.add(new JLabel(label1));
        JComboBox<String> dropDown1 = new JComboBox<>(new String[]{"Option 1", "Option 2", "Option 3"});
        dropDown1.setEditable(true);
        comboBoxes.add(dropDown1);
        panel.add(dropDown1);
        panel.add(new JLabel(label2));
        JComboBox<String> dropDown2 = new JComboBox<>(new String[]{"Option A", "Option B", "Option C"});
        dropDown2.setEditable(true);
        comboBoxes.add(dropDown2);
        panel.add(dropDown2);
        return panel;
    }
}
