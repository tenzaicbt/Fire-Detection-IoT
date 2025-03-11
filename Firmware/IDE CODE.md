#define BLYNK_PRINT Serial
#define BLYNK_TEMPLATE_ID "your_temp_id"
#define BLYNK_TEMPLATE_NAME "your_temp_name"
#define BLYNK_AUTH_TOKEN "your_auth_code"

#include <Wire.h>
#include <LiquidCrystal_PCF8574.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>

char auth[] = "your_auth_code";  
char ssid[] = "your_ssid";         
char pass[] = "your_wifi_pass";     

#define DHTPIN D4        // DHT11 sensor connected to GPIO2 (D4)
#define DHTTYPE DHT11    // DHT11 Sensor Type
#define BUZZER D6        // Buzzer connected to GPIO12 (D6)
#define LED D7           // LED connected to GPIO13 (D7)

#define FLAME_ANALOG A0  // Analog Output from Flame Sensor
#define FLAME_DIGITAL D5 // Digital Output from Flame Sensor
#define SMOKE_ANALOG A0  // MQ-2 Analog Output (A0)
#define SMOKE_DIGITAL D3 // MQ-2 Digital Output (D3)

#define TEMP_THRESHOLD 50   // Temperature threshold for fire alert (°C)
#define SMOKE_THRESHOLD 400 // Smoke detection threshold

DHT dht(DHTPIN, DHTTYPE);
BlynkTimer timer;
LiquidCrystal_PCF8574 lcd(0x27);

void sendSensorData() {
    float temperature = dht.readTemperature(false);  // Read in Celsius
    float humidity = dht.readHumidity();
    int flameAnalog = analogRead(FLAME_ANALOG);
    int flameDigital = !digitalRead(FLAME_DIGITAL);  // 1 = Fire detected, 0 = No fire
    int smokeAnalog = analogRead(SMOKE_ANALOG);
    int smokeDigital = !digitalRead(SMOKE_DIGITAL);  // 1 = Smoke detected, 0 = No smoke

    if (isnan(temperature) || isnan(humidity)) {
        Serial.println("❌ Failed to read from DHT sensor!");
        return;
    }

    // Debugging prints
    Serial.print("🌡️ Temperature: "); Serial.print(temperature); Serial.println("°C");
    Serial.print("💧 Humidity: "); Serial.print(humidity); Serial.println("%");
    Serial.print("🔥 Flame Analog: "); Serial.print(flameAnalog);
    Serial.print(", Digital: "); Serial.println(flameDigital);
    Serial.print("💨 Smoke Analog: "); Serial.print(smokeAnalog);
    Serial.print(", Digital: "); Serial.println(smokeDigital);

    // Send sensor data to Blynk (converted to String for reliability)
    Blynk.virtualWrite(V0, String(temperature, 2));  
    Blynk.virtualWrite(V1, String(humidity, 2));     
    Blynk.virtualWrite(V2, flameAnalog);
    Blynk.virtualWrite(V3, flameDigital);
    Blynk.virtualWrite(V5, smokeAnalog);
    Blynk.virtualWrite(V6, smokeDigital);

    // Display on LCD
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Temp: "); lcd.print(temperature); lcd.print("C ");
    lcd.print("Hum: "); lcd.print(humidity); lcd.print("%");

    // Fire or smoke detection logic
    if (temperature >= TEMP_THRESHOLD || flameDigital == 1 || smokeAnalog >= SMOKE_THRESHOLD || smokeDigital == 1) {  
        digitalWrite(BUZZER, HIGH);
        digitalWrite(LED, HIGH);
        delay(500);
        digitalWrite(BUZZER, LOW);
        digitalWrite(LED, LOW);
        delay(500);

        Blynk.virtualWrite(V4, 1);  // Fire or Smoke Alert ON
        Serial.println("🚨 Fire or Smoke Detected! Sending Alert...");
        Blynk.logEvent("your_alert_code", "🔥 Fire or Smoke Detected!");

        // Display Alert on LCD
        lcd.setCursor(0, 1);
        lcd.print("🚨 FIRE ALERT! 🚨");
    } else {
        digitalWrite(BUZZER, LOW);
        digitalWrite(LED, LOW);

        // Normal Status on LCD
        lcd.setCursor(0, 1);
        lcd.print("Status: Normal ");
    }
}

// Wi-Fi reconnect function
void checkWiFi() {
    if (WiFi.status() != WL_CONNECTED) {
        Serial.println("⚠️ Wi-Fi lost! Reconnecting...");
        WiFi.begin(ssid, pass);
        int attempts = 0;
        while (WiFi.status() != WL_CONNECTED && attempts < 10) {
            delay(500);
            Serial.print(".");
            attempts++;
        }
        if (WiFi.status() == WL_CONNECTED) {
            Serial.println("\n✅ Reconnected to Wi-Fi!");
        } else {
            Serial.println("\n❌ Wi-Fi reconnect failed! Restarting...");
            ESP.restart();
        }
    }
}

void setup() {
    Serial.begin(115200);
    delay(1000);
    Serial.println("\n🚀 ESP8266 Starting...");

    // Initialize LCD
    lcd.begin(16, 2);
    lcd.setBacklight(255);
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Fire Detector");
    lcd.setCursor(0, 1);
    lcd.print("Initializing...");
    delay(2000);

    pinMode(BUZZER, OUTPUT);
    pinMode(LED, OUTPUT);
    pinMode(SMOKE_DIGITAL, INPUT);
    pinMode(FLAME_DIGITAL, INPUT);
    dht.begin();

    WiFi.begin(ssid, pass);
    Serial.print("🌐 Connecting to Wi-Fi");

    int attempts = 0;
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
        attempts++;
        if (attempts > 20) {
            Serial.println("\n❌ Wi-Fi connection failed! Restarting...");
            ESP.restart();
        }
    }

    Serial.println("\n✅ Connected to Wi-Fi!");
    Serial.print("📡 IP Address: ");
    Serial.println(WiFi.localIP());

    Blynk.begin(auth, ssid, pass);
    Serial.println("✅ Connected to Blynk!");

    timer.setInterval(5000L, sendSensorData);  // Send data every 5 seconds
}

void loop() {
    checkWiFi();
    Blynk.run();
    timer.run();
}
