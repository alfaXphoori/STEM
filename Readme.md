# ESP32-C3 pH Monitoring via Serial

## Source Code
# STEM — ESP32-C3 pH Monitoring

รวมเอกสารและคู่มือสำหรับโปรเจกต์วัดค่า pH ด้วยบอร์ด ESP32-C3 (Serial / WiFi)

## เอกสารในโปรเจกต์

- [00_Install_ArduinoIDE.md](00_Install_ArduinoIDE.md) — คู่มือการติดตั้ง Arduino IDE และการเพิ่มบอร์ด ESP32
- [01_ESP32_pH.md](01_ESP32_pH.md) — การอ่านค่า pH ผ่าน Serial (โค้ดตัวอย่าง + สายต่อวงจร)
- [02_ESP32_pH_Wifi.md](02_ESP32_pH_Wifi.md) — อ่านค่า pH พร้อมเชื่อมต่อ WiFi (แสดงผลผ่าน Serial)
- [03_ESP32_pH_Wifi_Sheet.md](03_ESP32_pH_Wifi_Sheet.md) — ตั้งค่า Google Sheets และ Apps Script เพื่อรับข้อมูลจากบอร์ด
- [04_Sheet_Dashboard.md](04_Sheet_Dashboard.md) — สร้าง Dashboard (index.html) สำหรับแสดงกราฟและสถานะจาก Google Sheets
- `Src/` — โฟลเดอร์รูปภาพที่ใช้ในเอกสาร (เช่น 01_ESP32_pH.png, 02_ESP32_pH_Wifi.png)

## สรุปการใช้งานด่วน (Quick Start)

1. ทำตาม [00_Install_ArduinoIDE.md](00_Install_ArduinoIDE.md) เพื่อติดตั้ง Arduino IDE และบอร์ด ESP32
2. หากต้องการทดสอบแบบ Serial ให้เปิดและอัปโหลดโค้ดจาก [01_ESP32_pH.md](01_ESP32_pH.md)
3. หากต้องการส่งข้อมูลไปยัง Google Sheets ให้ใช้โค้ดจาก [02_ESP32_pH_Wifi.md](02_ESP32_pH_Wifi.md) และทำตามขั้นตอนใน [03_ESP32_pH_Wifi_Sheet.md](03_ESP32_pH_Wifi_Sheet.md)
4. เมื่อข้อมูลเข้าที่ Google Sheets แล้ว ให้เปิด [04_Sheet_Dashboard.md](04_Sheet_Dashboard.md) เพื่อสร้างหรือใช้ `index.html` ในการแสดง Dashboard

## คำแนะนำการปรับเทียบ (Calibration)

- ทุกโปรเจกต์ pH ควรทำการ Calibrate โดยใช้ buffer solution (pH 1, 7, 14) และปรับค่าตัวแปร Calibration ในไฟล์โค้ดตามคำแนะนำในเอกสาร

## Prompts for Gemini (ใช้ขอโค้ดจากโมเดล)

ต่อไปนี้เป็น 4 prompt พร้อมคำอธิบายที่สามารถคัดลอกไปให้โมเดล (Gemini) เพื่อขอโค้ดและไฟล์ที่ต้องการได้โดยตรง — แต่ละ prompt ขอให้ส่งผลลัพธ์เป็นไฟล์เดียวที่พร้อมคัดลอกวาง (`.ino`, `Code.gs`, `index.html`) และมีคอมเมนต์ภาษาไทยในโค้ด

### 1) Arduino (Serial pH)
```
ภาษา: ไทย

ช่วยเขียนโค้ด Arduino C++ สำหรับบอร์ด ESP32-C3 ที่อ่านสัญญาณจากเซนเซอร์ pH ต่อเข้าขาอนาล็อก (A1 หรือ GPIO ที่แมปแล้ว) แล้ว:
- ตั้งค่า ADC เป็น 12-bit, attenuation 11dB, ค่า Vref = 3.3V
- อ่านค่าแบบ oversampling (เช่น 20 ครั้ง) แล้วคำนวณค่าเฉลี่ย
- แปลง ADC → Voltage โดยใช้ ADC_RESOLUTION = 4095
- แปลง Voltage → pH ด้วยการทำ piecewise linear calibration (ตัวแปร `V_PH1`, `V_PH7`, `V_PH14` ให้ผู้ใช้แก้ได้)
- จำกัดค่า pH ให้อยู่ในช่วง 0–14
- แสดงผลใน Serial Monitor (115200) เป็นบรรทัด: Status | Raw ADC | Voltage (3 ตำแหน่ง) | pH (2 ตำแหน่ง)
- ใส่คำอธิบายสั้นๆ ในคอมเมนต์เกี่ยวกับการปรับเทียบ (calibration) และการแม็ปขา A1 เป็น GPIO ตัวเลข
- ส่งออกเป็นไฟล์ `.ino` พร้อมคำแนะนำสั้นๆ วิธีอัปโหลดใน Arduino IDE
```

### 2) Arduino (WiFi + Serial)
```
ภาษา: ไทย

ช่วยเขียนโค้ด Arduino C++ สำหรับ ESP32-C3 ที่:
- รวมฟังก์ชันทั้งหมดจาก prompt (1) (ADC, smoothing, แปลงเป็น pH)
- เชื่อมต่อ WiFi โดยให้ตัวแปร `ssid` และ `password` แก้ได้ง่าย
- แสดงสถานะการเชื่อมต่อ WiFi บน Serial (ONLINE/OFFLINE)
- เมื่อเชื่อมต่อ ให้ส่งค่า `ph` และ `voltage` เป็น HTTP GET และตัวอย่าง POST ไปยัง Google Apps Script URL (`GOOGLE_SCRIPT_URL`) — ตัวอย่าง GET: `?ph=...&voltage=...`
- ทำ retry/reconnect อัตโนมัติเมื่อ offline
- ให้ค่าหน่วงเวลา (เช่น ส่งข้อมูลทุก 1–10 วินาที) เป็นตัวแปรที่แก้ได้
- ส่งออกเป็นไฟล์ `.ino` พร้อมคำอธิบายการตั้งค่า Google Script URL/ID
```

### 3) Google Apps Script (Sheets endpoint)
```
ภาษา: ไทย

สร้าง Google Apps Script (ไฟล์ `Code.gs`) ที่มี:
- ฟังก์ชัน `doGet(e)` และ `doPost(e)` รองรับทั้ง GET/POST
- เมื่อได้รับพารามิเตอร์ `ph` และ `voltage` ให้ append แถวใหม่ใน active sheet เป็น `[timestamp, ph, voltage]`
- เมื่อเรียก `?action=read` ให้คืนค่า JSON (Array of rows) ของแถวล่าสุด (เช่น last 50 rows)
- ตั้ง MIME type เป็น JSON และตอบข้อความชัดเจน (Success / No Data / Bad Request)
- ใส่ตัวอย่างวิธี Deploy เป็น Web App (Execute as: Me, Who has access: Anyone)
```

### 4) Dashboard `index.html` (Chart + Settings)
```
ภาษา: ไทย

สร้างไฟล์ `index.html` แบบ single-page ที่:
- ใช้ Tailwind (CDN) และ Chart.js (CDN)
- มี UI: header, ปุ่มตั้งค่า (ใส่ Google Apps Script URL), สถานะการเชื่อมต่อ, การ์ดแสดงค่า pH ปัจจุบันและ voltage, และกราฟเส้นประวัติ pH
- ดึงข้อมูลจาก Apps Script URL (`?action=read`) ทุก 10 วินาที และเก็บ `SCRIPT_URL` ใน `localStorage`
- แปลงข้อมูลเป็น labels + dataset สำหรับ Chart.js และแสดงเวลาในท้องถิ่น
- แสดง SweetAlert2 เมื่อเรียกข้อมูลไม่ได้ และเปลี่ยนสถานะเป็น Offline
- ฟังก์ชัน `openSettings()` ให้ผู้ใช้ใส่/บันทึก URL
- คืนโค้ด `index.html` พร้อมคำอธิบายวิธีใช้งาน (เปิดใน Chrome, ใส่ URL, ดูกราฟ)
```

### คำแนะนำการใช้กับ Gemini (meta-prompt)
- ระบุให้โมเดลออกเป็นไฟล์เดียวต่อ prompt (`.ino`, `Code.gs`, `index.html`) และรวมคำอธิบายสั้น ๆ ด้านล่างไฟล์
- ขอให้ output พร้อมคอมเมนต์ภาษาไทยเพื่อให้ทีมอ่านเข้าใจง่าย
- ระบุว่า "พร้อมคัดลอกไปวางในไฟล์" เพื่อให้ผลลัพธ์สะอาดและใช้งานได้ทันที

---

หากต้องการ ผมสามารถแปลงข้อความ prompt เหล่านี้เป็นไฟล์ `.txt` หรือ JSON ที่พร้อมส่งให้ Gemini ให้เลยครับ


