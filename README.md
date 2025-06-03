# Weather-Monitoring
In this project, it detects the moisture content and temperature of the surrounding environment. Using sensors and data connected via internet, it is an efficient way of monitoring weather.
#include <SoftwareSerial.h>
#include "dht.h"
dht DHT;
int a;
String temp,humid;
SoftwareSerial esp8266(3,2);
#define SSID "Sridharv 2.4G"
#define PASS "Ekansh18*"
String sendAT(String command, const int timeout)
{
  String response="";
  esp8266.print(command);
  long int time=millis();
  while((time+timeout)>millis())
  {
    while(esp8266.available())
    {
      char c=esp8266.read();
      response +=c;
    }
  }
  Serial.print(response);
  return response;
}
void setup() {
  // put your setup code here, to run once:
Serial.begin(9600);
esp8266.begin(9600);
Serial.println("weather monitor");
sendAT("AT+RST\r\n",2000);
sendAT("AT\r\n",1000);
sendAT("AT+CWMODE=1\r\n",1000);
sendAT("AT+CWJAP=\""SSID"\",\""PASS"\"\r\n",2000);
while(!esp8266.find("OK"))
{

}
sendAT("AT+CIFSR\r\n",1000);
sendAT("AT+CIPMUX=0\r\n",2000);
}
void loop() {
  // put your main code here, to run repeatedly:
int a=DHT.read11(8);
temp=DHT.temperature;
humid=DHT.humidity;
Serial.println(temp);
Serial.println(DHT.temperature);
Serial.println(humid);
Serial.println(DHT.humidity);
updateTS(temp,humid);
delay(2000);
}
void updateTS(String T, String H)
{
  Serial.println("uploding data");
  sendAT("AT+CIPSTART=\"TCP\",\"api.thingspeak.com\",80\r\n",1000);
  delay(2000);
  String cmdlen;
  String cmd = "GET https://api.thingspeak.com/update?api_key=5D5G2LKOCW01OB1U&field1=0\r\n";
  cmdlen=cmd.length();
  sendAT("AT+CIPSEND="+cmdlen+"\r\n",2000);
  esp8266.print(cmd);
  Serial.println("");
  sendAT("AT+CIPCLOSE\r\n",2000);
  Serial.println("");
  delay(1000);
}
