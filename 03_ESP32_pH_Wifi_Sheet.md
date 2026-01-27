
# ESP32-C3 pH Monitoring with WiFi



<div align="center">
  <img src="https://img.shields.io/badge/Platform-ESP32--C3-blue" alt="Platform">
  <img src="https://img.shields.io/badge/Interface-WiFi-green" alt="WiFi">
  <img src="https://img.shields.io/badge/Language-Arduino%20C++-brightgreen" alt="Arduino C++">
</div>

---

## 📝 ขั้นตอนการใช้งาน


### 1. เข้า Google Sheets
ไปที่ https://workspace.google.com/products/sheets
<div align="center" style="margin: 2em 0;">
  <img src="Src/0301_gosheet.png" alt="เข้า Google Sheets" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>เข้า Google Sheets</em></div>
</div>

### 2. สร้าง Sheet ใหม่
คลิก “+” เพื่อสร้าง Google Sheet เปล่า
<div align="center" style="margin: 2em 0;">
  <img src="Src/0302_create_sheet.png" alt="สร้าง Sheet ใหม่" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>สร้าง Sheet ใหม่</em></div>
</div>

### 3. เปลี่ยนชื่อ Sheet และเข้า App Script
เปลี่ยนชื่อ Sheet ตามต้องการ แล้วไปที่ Extensions > Apps Script
<div align="center" style="margin: 2em 0;">
  <img src="Src/0303_rename_appscript.png" alt="เข้า App Script" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>เข้า App Script</em></div>
</div>

### 4. เปลี่ยนชื่อ Project และวางโค้ด
เปลี่ยนชื่อ Project (เช่น “pH Logger”) ลบโค้ดเดิมออก แล้ววางโค้ดนี้ลงไป:
<div align="center" style="margin: 2em 0;">
  <img src="Src/0304_rename_addcode.png" alt="วางโค้ดใน App Script" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>วางโค้ดใน App Script</em></div>
</div>

```javascript
function doGet(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  if (e.parameter.action == "read") {
    var rows = sheet.getLastRow();
    var startRow = Math.max(1, rows - 49); 
    var data = sheet.getRange(startRow, 1, rows - startRow + 1, 3).getValues();
    return ContentService.createTextOutput(JSON.stringify(data))
      .setMimeType(ContentService.MimeType.JSON);
  }
  var ph = e.parameter.ph;
  var voltage = e.parameter.voltage;
  if (ph && voltage) {
    var date = new Date();
    sheet.appendRow([date, ph, voltage]);
    return ContentService.createTextOutput("Success");
  }
  return ContentService.createTextOutput("No Data");
}
```

### 5. กดปุ่ม “Deploy” > “New deployment”
<div align="center" style="margin: 2em 0;">
  <img src="Src/0305_newDeploy.png" alt="New deployment" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>New deployment</em></div>
</div>

### 6. ตรง “Select type” เลือก “Web app”
<div align="center" style="margin: 2em 0;">
  <img src="Src/0306_chooseWebapp.png" alt="เลือก Web app" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>เลือก Web app</em></div>
</div>

### 7. ตั้งค่าตามนี้
- Description: pH Logger
- Execute as: Me (ฉัน)
- Who has access: Anyone (ทุกคน)  
<div align="center" style="margin: 2em 0;">
  <img src="Src/0307_set_Webapp.png" alt="ตั้งค่า Web app" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>ตั้งค่า Web app</em></div>
</div>

### 8. กด Deploy
### 9. Authorize access (ให้สิทธิ์การเข้าถึง)
<div align="center" style="margin: 2em 0;">
  <img src="Src/0308_Auth_Webapp.png" alt="Authorize access" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>Authorize access</em></div>
</div>

### 10. กด Advance และ “Go to unsafe”
<div align="center" style="margin: 2em 0;">
  <img src="Src/0309_Adv_unsafe.png" alt="Go to unsafe" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>Go to unsafe</em></div>
</div>

### 11. Copy Deployment ID มาใส่ในตัวแปร GOOGLE_SCRIPT_ID ในโค้ด Arduino
<div align="center" style="margin: 2em 0;">
  <img src="Src/0310_copyID.png" alt="Copy Deployment ID" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>Copy Deployment ID</em></div>
</div>

### 12. ข้อมูลจะถูกส่งไปเก็บไว้ใน Google Sheet เรียบร้อย
<div align="center" style="margin: 2em 0;">
  <img src="Src/0311_updateSheet.png" alt="ข้อมูลถูกส่งเข้า Google Sheet" width="800" style="box-shadow:0 4px 24px rgba(0,0,0,0.15);border-radius:16px;"/>
  <div style="font-size:1.1em;color:#555;margin-top:0.5em;"><em>ข้อมูลถูกส่งเข้า Google Sheet</em></div>
</div>

---

## 💻 ตัวอย่างโค้ด
```cpp
/*
 * Project: ESP32-C3 pH Sensor (Custom Calibration) + WiFi + Google Sheets
 * Author: Alfa (https://github.com/alfaxphoori)
 * Description: อ่านค่า pH, เชื่อมต่อ WiFi และส่งข้อมูลไปยัง Google Sheets
 */

#include <WiFi.h>
#include <HTTPClient.h>       // เพิ่มไลบรารีสำหรับ HTTP Request
#include <WiFiClientSecure.h> // เพิ่มไลบรารีสำหรับ HTTPS (Google ใช้ SSL)

// --- การตั้งค่า WiFi ---
const char* ssid = "YOUR_WIFI_SSID";         // ใส่ชื่อ WiFi ของคุณ
const char* password = "YOUR_WIFI_PASSWORD"; // ใส่รหัสผ่าน WiFi ของคุณ

// --- การตั้งค่า Google Sheets ---
// นำ Deployment ID จาก Google Apps Script มาใส่ตรงนี้ (ดูวิธีทำด้านล่างสุดของไฟล์)
const char* GOOGLE_SCRIPT_ID = "YOUR_DEPLOYMENT_ID_HERE"; 

// ตั้งเวลาส่งข้อมูล (หน่วยมิลลิวินาที)
const unsigned long sendInterval = 10000; // ส่งทุกๆ 10 วินาที
unsigned long lastSendTime = 0;

// --- การตั้งค่า Hardware ---
const int phPin = A1; 

// --- ค่าคงที่ของระบบ ---
const float ESP_ADC_VOLTAGE = 3.3; 
const int ADC_RESOLUTION = 4095;   

// --- ข้อมูล Calibration ---
const float V_PH7  = 2.186; 
const float V_PH1  = 2.537; 
const float V_PH14 = 0.980; 

void setup() {
  Serial.begin(115200);
  analogReadResolution(12);
  analogSetAttenuation(ADC_11db);
  
  Serial.println("---------------------------------------------");
  Serial.println("ESP32-C3 pH Meter & Google Sheets Logger");
  
  // --- เริ่มการเชื่อมต่อ WiFi ---
  Serial.print("Connecting to WiFi: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nWiFi Connected!");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());
  Serial.println("---------------------------------------------");
}

/*
 * ฟังก์ชันคำนวณค่า pH
 */
float getPH(float voltage) {
  float slope;
  if (voltage > V_PH7) { 
    slope = (7.0 - 1.0) / (V_PH7 - V_PH1); 
    return 7.0 + slope * (voltage - V_PH7);
  } else { 
    slope = (14.0 - 7.0) / (V_PH14 - V_PH7); 
    return 7.0 + slope * (voltage - V_PH7);
  }
}

/*
 * ฟังก์ชันส่งข้อมูลไปยัง Google Sheets ผ่าน HTTPS GET
 */
void sendToGoogleSheet(float ph, float voltage) {
  if (WiFi.status() == WL_CONNECTED) {
    WiFiClientSecure client;
    client.setInsecure(); // ข้ามการตรวจสอบ SSL Certificate (เพื่อให้ง่ายต่อ ESP32)
    HTTPClient https;

    // สร้าง URL สำหรับยิงข้อมูล
    // Format: https://script.google.com/macros/s/[ID]/exec?ph=[val]&voltage=[val]
    String url = "https://script.google.com/macros/s/" + String(GOOGLE_SCRIPT_ID) + "/exec";
    url += "?ph=" + String(ph, 2);
    url += "&voltage=" + String(voltage, 3);

    // Serial.println("Sending data to Google Sheets..."); // Debug URL
    
    if (https.begin(client, url)) {
      
      // *** แก้ไข: เพิ่มการติดตาม Redirect เพื่อแก้ปัญหา Code 302 ***
      https.setFollowRedirects(HTTPC_STRICT_FOLLOW_REDIRECTS);

      int httpCode = https.GET(); // ส่ง Request
      
      if (httpCode > 0) {
        // HTTP header has been send and Server response header has been handled
        // *** แก้ไข: เพิ่ม 302 (Found) ในเงื่อนไขความสำเร็จ ***
        if (httpCode == HTTP_CODE_OK || httpCode == HTTP_CODE_MOVED_PERMANENTLY || httpCode == 302) {
          Serial.println(" [Cloud] Google Sheet Updated Successfully!");
        } else {
          Serial.print(" [Cloud] Failed to update. HTTP Code: ");
          Serial.println(httpCode);
        }
      } else {
        Serial.print(" [Cloud] Error: ");
        Serial.println(https.errorToString(httpCode));
      }
      https.end();
    } else {
      Serial.println(" [Cloud] Unable to connect to Google Server");
    }
  } else {
    Serial.println(" [Cloud] WiFi Disconnected");
  }
}

void loop() {
  // 1. อ่านค่า ADC
  unsigned long totalAdc = 0;
  int sampleCount = 30; 

  for(int i = 0; i < sampleCount; i++) {
    totalAdc += analogRead(phPin);
    delay(5); 
  }
  
  float avgAdc = totalAdc / (float)sampleCount;
  float voltage = avgAdc * (ESP_ADC_VOLTAGE / ADC_RESOLUTION);
  float currentPH = getPH(voltage);

  if (currentPH < 0) currentPH = 0;
  if (currentPH > 14) currentPH = 14;

  // 2. แสดงผล Local
  String wifiStatus = (WiFi.status() == WL_CONNECTED) ? "ONLINE " : "OFFLINE";
  Serial.print("Status: ["); Serial.print(wifiStatus);
  Serial.print("] | pH: "); Serial.print(currentPH, 2); 
  Serial.print(" | V: "); Serial.println(voltage, 3);

  // 3. ส่งข้อมูลเข้า Google Sheets (ทุกๆ 10 วินาที ตามตัวแปร sendInterval)
  if (millis() - lastSendTime > sendInterval) {
    if (String(GOOGLE_SCRIPT_ID) != "YOUR_DEPLOYMENT_ID_HERE") {
        sendToGoogleSheet(currentPH, voltage);
    } else {
        Serial.println(" [Warning] Please insert your Google Script ID first!");
    }
    lastSendTime = millis();
  }

  // Check Reconnection
  if (WiFi.status() != WL_CONNECTED) {
    WiFi.disconnect();
    WiFi.reconnect();
  }

  delay(1000); 
}
```

---

