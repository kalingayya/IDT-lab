#include <DHT.h>

#define PIR_PIN 2
#define DHT_PIN 3
#define BUZZER_PIN 4
#define RELAY_PIN 5

#define DHTTYPE DHT11

DHT dht(DHT_PIN, DHTTYPE);

void setup() {
  pinMode(PIR_PIN, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(RELAY_PIN, OUTPUT);

  digitalWrite(BUZZER_PIN, LOW);
  digitalWrite(RELAY_PIN, LOW);

  Serial.begin(9600);
  dht.begin();

  Serial.println("Crop Protection System Started");
}

void loop() {
  int motion = digitalRead(PIR_PIN);
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  // Check if DHT reading failed
  if (isnan(temperature) || isnan(humidity)) {
    Serial.println("DHT11 Error");
    delay(2000);
    return;
  }

  if (motion == HIGH) {
    // Animal / intruder detected
    digitalWrite(BUZZER_PIN, HIGH);
    digitalWrite(RELAY_PIN, HIGH);

    Serial.println("⚠️ ALERT: Motion Detected!");
    Serial.print("Temperature: ");
    Serial.print(temperature);
    Serial.print(" °C | Humidity: ");
    Serial.print(humidity);
    Serial.println(" %");
  } 
  else {
    // No motion
    digitalWrite(BUZZER_PIN, LOW);
    digitalWrite(RELAY_PIN, LOW);

    Serial.println("✅ Field Safe");
    Serial.print("Temperature: ");
    Serial.print(temperature);
    Serial.print(" °C | Humidity: ");
    Serial.print(humidity);
    Serial.println(" %");
  }

  delay(2000); //
