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

## พรีวิวเอกสาร

- เปิดไฟล์แต่ละไฟล์ใน VS Code แล้วกด `Open Preview` (หรือใช้ตัวช่วยแสดง Markdown preview) เพื่อดูผลลัพธ์

## ติดตามปัญหา / ถาม-ตอบ

- หากพบปัญหา ให้สร้าง Issue ใน GitHub: https://github.com/alfaXphoori/STEM/issues

---

ไฟล์นี้สร้างโดยอัตโนมัติเพื่อรวมลิงก์เอกสารหลักของโปรเจกต์ หากต้องการให้เพิ่มรายละเอียดหรือเรียงลำดับใหม่ แจ้งผมได้เลย
		adc_sum += analogRead(PH_PIN);

		delay(10);
