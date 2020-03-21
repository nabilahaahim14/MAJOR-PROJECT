# MAJOR-PROJECT
Code for the major project
//
#include "SoftwareSerial.h"

String ssid ="POCO PHONE";
String password="amhashim1";

SoftwareSerial esp(3, 2);// RX, TX

String server = "www.thingspeak.com"; //Your Host 
String uri = "AZT8P3IM6PY1WN9A"; // Your URI

 
int YELLOW_BULB=6;

void setup() {


  pinMode(YELLOW_BULB, OUTPUT);
  
 
  digitalWrite(YELLOW_BULB, HIGH);
  
  esp.begin(9600);
  
  Serial.begin(9600);

  connectWifi();
  
  httpget();
  
  delay(1000);

}
void connectWifi() {

String cmd = "AT+CWJAP=\"" +ssid+"\",\"" + password + "\"";

esp.println(cmd);

delay(4000);

if(esp.find("OK")) {

Serial.println("Connected!");

}
else {

Serial.println("Cannot connect to wifi ! Connecting again..."); }
connectWifi();

}
/////////////////////////////GET METHOD///////////////////////////////
void httpget() {
esp.println("AT+CIPSTART=\"TCP\",\"" + server + "\",80");//start a TCP connection.

if( esp.find("OK")) {

Serial.println("TCP connection ready");

} delay(1000);

String getRequest =
"GET " + uri + " https://api.thingspeak.com/update?api_key=AZT8P3IM6PY1WN9A&field1=0" +
"Host: " + server + "https://ifttt.com/my_applets" +
"Accept: " + "/" + "\r\n" +
"Content-Type: application/json" +
"\r\n";

String sendCmd = "AT+CIPSEND=";

esp.print(sendCmd);

esp.println(getRequest.length() );

delay(500);

if(esp.find(">")) { 
  Serial.println("Sending.."); 
  esp.print(getRequest);
  
if( esp.find("SEND OK")) { 
  
Serial.println("Packet sent");

while (esp.available()) {

String response = esp.readString();

int YELLOW_BULB_ON = response.indexOf("YELLOW_BULB>TRUE")>0?1:0;


if(YELLOW_BULB_ON==1)
{
  digitalWrite(YELLOW_BULB, LOW);
}
else
{
  digitalWrite(YELLOW_BULB, HIGH);
}
}
esp.println("AT+CIPCLOSE");

}
}
}

void loop() {
  httpget();
}
