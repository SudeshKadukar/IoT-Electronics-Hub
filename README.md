#include <WiFi.h>
#include <FirebaseESP32.h>

// WiFi credentials
#define WIFI_SSID "Your_WiFi_Name"
#define WIFI_PASSWORD "Your_WiFi_Password"

// Firebase credentials
#define FIREBASE_HOST "your-project-id.firebaseio.com"
#define FIREBASE_AUTH "your-database-secret"

// Define light pin
#define LIGHT_PIN 2  // Built-in LED (GPIO 2)

FirebaseData fbdo;

void setup() {
  Serial.begin(115200);
  pinMode(LIGHT_PIN, OUTPUT);

  // Connect to Wi-Fi
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  Serial.print("Connecting to Wi-Fi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnected to Wi-Fi");

  // Connect to Firebase
  Firebase.begin(FIREBASE_HOST, FIREBASE_AUTH);
  Firebase.reconnectWiFi(true);
}

void loop() {
  // Read value from Firebase
  if (Firebase.getBool(fbdo, "/SmartHome/Light")) {
    bool lightStatus = fbdo.boolData();
    digitalWrite(LIGHT_PIN, lightStatus ? HIGH : LOW);
    Serial.printf("Light is %s\n", lightStatus ? "ON" : "OFF");
  }
  delay(1000);
}
