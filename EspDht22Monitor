// ThingSpeak Temp/Humidity IoT Monitor
// ESP-8266 (ESP-01)
// DHT22 Sensor (Connect VCC to 5V line)
// 5V to 3.3V Buck Converter  (Power for ESP-8266)
// Modified from: https://eryk.io/2016/04/temperature-logging-to-thingspeak-using-esp8266-and-dht22/
// Modified to use DHT 22, Farenheit degrees


#include <DHT.h>
#include <ESP8266WiFi.h>
#include <WiFiManager.h>  

// Thingspeak API key
String apiKey = "****************";       // Enter Your ThingSpeak API Write Key
 
const char* server = "api.thingspeak.com";
#define DHTPIN 2                             // DHT22 sensor pin (ESP-01 pin 2)
 
DHT dht(DHTPIN, DHT22,11);

WiFiClient client;

long previousMillis = 0;        // last time of update
long delayInt = 15;              // Time to wait between readings (minutes) 
long interval = delayInt * 60000;   // Loop interval
 
void setup() {
  Serial.begin(115200);
  delay(10);
  dht.begin();

  WiFiManager wifiManager;
  wifiManager.autoConnect("EspTempSensor");
  // Initial update on startup
  updateFields();
}
 
void loop() {
  unsigned long currentMillis = millis(); 
  
  if(currentMillis - previousMillis > interval) {
    previousMillis = currentMillis;   
    
    updateFields();  
  }  // End of time interval
}

void updateFields(){
  float h = dht.readHumidity();
  float t = dht.readTemperature(true);
  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  if (client.connect(server,80)) {
    String postStr = apiKey;
           postStr +="&field1=";
           postStr += String(t);
           postStr +="&field2=";
           postStr += String(h);
           postStr += "\r\n\r\n";
 
     client.print("POST /update HTTP/1.1\n");
     client.print("Host: api.thingspeak.com\n");
     client.print("Connection: close\n");
     client.print("X-THINGSPEAKAPIKEY: "+apiKey+"\n");
     client.print("Content-Type: application/x-www-form-urlencoded\n");
     client.print("Content-Length: ");
     client.print(postStr.length());
     client.print("\n\n");
     client.print(postStr);
 
     Serial.print("Temperature: ");
     Serial.print(t);
     Serial.print(" F, Humidity: ");
     Serial.print(h);
     Serial.println("%. Send to Thingspeak.");
  }
  client.stop();
 
  Serial.println("Waiting...");
}
