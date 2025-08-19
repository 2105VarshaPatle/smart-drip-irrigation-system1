#define BLYNK_TEMPLATE_ID "TMPL3U35_-eLU"
#define BLYNK_TEMPLATE_NAME " Soil Drip Irrigation System"
#define BLYNK_PRINT Serial

#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

// Blynk authentication token
char auth[] = "yA7sqwqgv0O-2Y4n8B-vY894aN-GeGwX";
char ssid[] = "Galaxy F23 5G 6ECB"; // Enter your WIFI name
char pass[] = "21052004"; // Enter your WIFI password

BlynkTimer timer;

#define sensor A0 // Soil moisture sensor pin
#define relayPin D3 // Relay pin

void setup() {
  Serial.begin(9600);
  pinMode(relayPin, OUTPUT);
  digitalWrite(relayPin, HIGH); // Initialize relay as OFF

  Blynk.begin(auth, ssid, pass); // Initialize Blynk
  timer.setInterval(2000L, soilMoistureSensor); // Call sensor function every 2 seconds
}

BLYNK_WRITE(V1) { 
  if (param.asInt() == 1) { 
    digitalWrite(relayPin, LOW); // Turn on water pump
  } else { 
    digitalWrite(relayPin, HIGH); // Turn off water pump
  }
}

// Get the soil moisture values
void soilMoistureSensor() {
  int value = analogRead(sensor);
  value = map(value, 0, 1024, 0, 100);
  value = (value - 100) * -1;
  Blynk.virtualWrite(V0, value); // Send moisture level to Blynk
  Serial.print("Moisture Value: ");
  Serial.println(value); // Print moisture level for debugging

  if (value < 30) { // Adjust threshold as needed
    digitalWrite(relayPin, LOW); // Turn on relay (water pump)
  } else {
    digitalWrite(relayPin, HIGH); // Turn off relay (water pump)
  }
}

void loop() {
  Blynk.run(); // Run the Blynk library 
  timer.run(); // Run the Blynk timer
}
