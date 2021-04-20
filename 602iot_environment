#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>
#include "DFRobot_DHT11.h"
// WiFi
const char *ssid = "602iot"; // Enter your WiFi name
const char *password = "18wulian";  // Enter WiFi password
// MQTT Broker
const char *mqtt_broker = "red263b0.cn.emqx.cloud";//EMQX CLOUD连接地址
const char *topic = "temp_hum/602iot";
const char *mqtt_username = "602iot";
const char *mqtt_password = "602iot";
const int mqtt_port = 12498;//mqtt端口
// DHT11
#define DHT11_PIN  D3//定义DHT11的引脚
unsigned long previousMillis = 0;

WiFiClient espClient;
PubSubClient client(espClient);
DFRobot_DHT11 DHT;

void setup() {
  // Set software serial baud to 115200;
  Serial.begin(115200);
  // connecting to a WiFi network
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to the WiFi network");
  //connecting to a mqtt broker
  client.setServer(mqtt_broker, mqtt_port);
  while (!client.connected()) {
    String client_id = "602iot_";
    client_id += String(WiFi.macAddress());
    Serial.println("Connecting to public emqx mqtt broker...");
    if (client.connect(client_id.c_str(), mqtt_username, mqtt_password)) {
      Serial.println("Public emqx mqtt broker connected!");
    } else {
      Serial.print("failed with state\n");
      Serial.print(client.state());
      delay(2000);
    }
  }
}

void loop() {
  client.loop();
  unsigned long currentMillis = millis();
  // temperature and humidity data are publish every  5 second
  if (currentMillis - previousMillis >= 5000) {
    previousMillis = currentMillis;
    DHT.read(DHT11_PIN);//DHT11读取环境的温湿度数据
    float temp = DHT.temperature;
    float hum = DHT.humidity;
    // json serialize
    DynamicJsonDocument data(256);
    data["temp"] = temp;
    data["hum"] = hum;
    // publish temperature and humidity
    char json_string[256];
    serializeJson(data, json_string);
    // 数据格式：{"temp":23.5,"hum":55}
    Serial.println(json_string);
    client.publish(topic, json_string, false);
    client.subscribe(topic);
  }
}
