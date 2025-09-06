 ## Project: Smart Light Control with ESP32 + Firebase 
 Components Needed

ESP32 board

LED / Light (or just use ESP32 built-in LED on pin 2)

Wi-Fi connection

Firebase Realtime Database



 ## How It Works

ESP32 connects to Wi-Fi

ESP32 uses your Wi-Fi credentials (SSID + Password) to go online.

ESP32 connects to Firebase

You set up a Firebase Realtime Database.

ESP32 reads a value (true or false) stored in the database.

Control the Light

If Firebase has "Light": true → ESP32 turns ON LED.

If Firebase has "Light": false → ESP32 turns OFF LED.

Dashboard / Mobile App (Optional)

You (or any user) can toggle the light status from Firebase.

ESP32 will instantly follow the command.



## Code Explanation (ESP32 Arduino Code)

#define WIFI_SSID "Your_WiFi_Name"
#define WIFI_PASSWORD "Your_WiFi_Password"


👉 Replace with your Wi-Fi details. ESP32 needs internet to talk with Firebase.

#define FIREBASE_HOST "your-project-id.firebaseio.com"
#define FIREBASE_AUTH "your-database-secret"


👉 From Firebase console:

project-id = your Firebase project.

database-secret = key to access Firebase.

#define LIGHT_PIN 2


👉 ESP32 pin 2 controls LED (you can connect a relay/light here).


if (Firebase.getBool(fbdo, "/SmartHome/Light")) {
    bool lightStatus = fbdo.boolData();
    digitalWrite(LIGHT_PIN, lightStatus ? HIGH : LOW);
}



👉 ESP32 keeps checking Firebase:

If /SmartHome/Light = true → light ON.

If /SmartHome/Light = false → light OFF.



With this, you can expand your repo with:

Fan/Relay Control

DHT11 Weather Monitor

Motion Detection

Agriculture Automation (Soil + Pump)


## code 
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

