# Vehicle_Gaseous_Fuel_Leak_Detector

#include<ESP8266WiFi.h>
#include<ESP8266HTTPClient.h>
#include<WiFiClient.h>
const char* ssid = "iot";
const char* password = "12345678";
const char* serverName = "http://iotcloud22.in/767/post_value.php";
WiFiClient client;
HTTPClient http;
int gas;
float Gas;
void setup() {
pinMode(A0, INPUT);
pinMode(D7, OUTPUT);
pinMode(D8, OUTPUT);
Serial.begin(9600);
WiFi.begin(ssid, password);
Serial.println("Connecting");
while (WiFi.status() != WL_CONNECTED) {
Serial.print(".");
delay(500);
}
Serial.println("");
Serial.print("Connected to WiFi network with IP Address: ");
Serial.println(WiFi.localIP());
digitalWrite(D7, LOW);
digitalWrite(D8, HIGH);
}
void loop()
{
gas = analogRead(A0);
Gas = gas / 10.25;
Serial.println(Gas);
if (Gas > 50)
{
digitalWrite(D7, HIGH);
digitalWrite(D8, LOW);
sms();
}
else
{
digitalWrite(D7, LOW);
digitalWrite(D8, HIGH);
}
sending_to_db();
}
void sending_to_db()
{
if (WiFi.status() == WL_CONNECTED)
{
http.begin(client, serverName);
http.addHeader("Content-Type", "application/x-www-form-urlencoded");
String httpRequestData = "&value1=" + String(Gas) + "";
// Serial.print("httpRequestData: ");
// Serial.println(httpRequestData);
int httpResponseCode = http.POST(httpRequestData);
if (httpResponseCode > 0) {
Serial.print("HTTP Response code: ");
Serial.println(httpResponseCode);
}
else {
Serial.print("Error code: ");
Serial.println(httpResponseCode);
}
http.end();
}
else {
Serial.println("WiFi Disconnected");
}
delay(1500);
}
void sms()
{
Serial.println("AT\r");
delay(1000);
Serial.println("AT+CMGF=1\r");
delay(1000);
Serial.println("AT+CMGS=\"+918072865332\"\r");
delay(1000);
Serial.println("***EMERGENCY***");
Serial.print("GAS DETECTED");
//Serial.println(phvalue);
delay(1000);
Serial.println((char)26);
delay(100);
}

