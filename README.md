# Agricult1
#include <ESP8266WiFi.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";
const char* server = "your_server_address";

WiFiClient client;

void setup() {
  Serial.begin(115200);
  dht.begin();
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi connected");
}

void loop() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  int soilMoisture = analogRead(A0);

  if (client.connect(server, 80)) {
    String postData = "temp=" + String(t) + "&humidity=" + String(h) + "&soilMoisture=" + String(soilMoisture);
    client.println("POST /data HTTP/1.1");
    client.println("Host: your_server_address");
    client.println("Content-Type: application/x-www-form-urlencoded");
    client.println("Content-Length: " + String(postData.length()));
    client.println();
    client.print(postData);
    client.stop();
  }
  delay(60000); // Send data every minute
}
const express = require('express');
const bodyParser = require('body-parser');
const app = express();

app.use(bodyParser.urlencoded({ extended: true }));

app.post('/data', (req, res) => {
  const temp = req.body.temp;
  const humidity = req.body.humidity;
  const soilMoisture = req.body.soilMoisture;
  console.log(`Temperature: ${temp}, Humidity: ${humidity}, Soil Moisture: ${soilMoisture}`);
  res.send('Data received');
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet } from 'react-native';

const App = () => {
  const [data, setData] = useState({ temp: '', humidity: '', soilMoisture: '' });

  useEffect(() => {
    fetch('http://your_server_address:3000/data')
      .then(response => response.json())
      .then(data => setData(data))
      .catch(error => console.error('Error fetching data:', error));
  }, []);

  return (
    <View style={styles.container}>
      <Text>Temperature: {data.temp}</Text>
      <Text>Humidity: {data.humidity}</Text>
      <Text>Soil Moisture: {data.soilMoisture}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
});

export default App;
node server.js
