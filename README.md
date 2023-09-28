# WIFI-JAMMER
#Jam any wifi using Arduino 

cpp
#include <ESP8266WiFi.h>
#include <WiFiClient.h>
#include <ESP8266WebServer.h>
#include <ESP8266HTTPClient.h>
#include <WiFiUdp.h>
#include <functional>

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";

ESP8266WebServer server(80);

void setup() {
 Serial.begin(115200);
 WiFi.begin(ssid, password);

 while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
 }

 Serial.println("");
 Serial.println("WiFi connected");
 Serial.println("IP address: ");
 Serial.println(WiFi.localIP());

 server.on("/", []() {
    server.send(200, "text/html", "<h1>WiFi Jammer</h1><p><a href=\"/jam\"><button>Jam WiFi</button></a></p>");
 });

 server.on("/jam", []() {
    server.send(200, "text/html", "<h1>WiFi Jamming...</h1><p><a href=\"/stop\"><button>Stop Jamming</button></a></p>");
    WiFi.disconnect();
    WiFi.mode(WIFI_AP);
    WiFi.softAP("Free WiFi", "");
 });

 server.on("/stop", []() {
    server.send(200, "text/html", "<h1>WiFi Jamming Stopped</h1><p><a href=\"/jam\"><button>Jam WiFi</button></a></p>");
    WiFi.disconnect();
    WiFi.mode(WIFI_STA);
    WiFi.begin(ssid, password);
 });

 server.begin();
}

void loop() {
 server.handleClient();
}
