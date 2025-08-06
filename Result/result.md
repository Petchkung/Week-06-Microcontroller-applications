
<img width="1426" height="717" alt="สกรีนช็อต 2025-08-06 095416" src="https://github.com/user-attachments/assets/130e61f4-5fc6-4d02-81a6-c76b2d06a0c0" />

ทดลองเพิ่มเติม

<img width="576" height="584" alt="image" src="https://github.com/user-attachments/assets/3c81a846-0334-48e3-aa08-a4e5cc83af08" />

คำถามทบทวน

Docker vs Native Setup: อธิบายข้อดีของการใช้ Docker เปรียบเทียบกับการติดตั้ง ESP-IDF บน host system
Docker
ติดตั้งง่าย ไม่ต้อง config เครื่อง
แยก environment ชัดเจน
ลดปัญหา dependency conflict

Native
เข้าถึงฮาร์ดแวร์ได้ตรง
เร็วกว่านิดหน่อยในบางกรณี
ต้องจัดการ dependency เอง

Build Process: อธิบายขั้นตอนการ build ของ ESP-IDF ใน Docker container ตั้งแต่ source code จนได้ binary
Clone โปรเจกต์ลงใน volume ที่ mount
เข้า container
เรียก idf.py build
CMake สร้าง build system (ผ่าน CMakeLists.txt)

CMake Files: บทบาทของไฟล์ CMakeLists.txt แต่ละไฟล์คืออะไร และทำงานอย่างไรใน Docker environment?
CMakeLists.txt (root): กำหนด project name, target
CMakeLists.txt (component): ระบุ source, dependency, include path

Git Ignore: ไฟล์ .gitignore มีความสำคัญอย่างไรสำหรับ ESP32 project development?
กันไม่ให้ไฟล์ build, binary, config เฉพาะเครื่อง เช่น .pio, build/, .vscode/ ถูก track
ลดขนาด repo และความสับสน
ช่วยให้ project สะอาดและแชร์ได้ง่าย

Container Persistence: ข้อมูลใดบ้างที่จะหายไปเมื่อ restart container และข้อมูลใดที่จะอยู่ต่อ?
ข้อมูลที่หายไป: ทุกอย่างใน container ถ้าไม่ได้ mount (เช่น build output)
ข้อมูลที่อยู่ต่อ: ทุกอย่างใน volume ที่ mount กับ host (เช่น source code)

Development Workflow: เปรียบเทียบ workflow การพัฒนาระหว่างการใช้ Docker กับการทำงานบน native system
Docker
Setup ง่าย, portable, ใช้ข้ามเครื่องได้
ต้อง docker exec เข้าไปทุกครั้ง
ดีสำหรับทีมงาน

Native
เร็วกว่าเล็กน้อย
IDE integration ง่าย
เหมาะกับ dev ประจำเครื่องเดียว

## 💡 บันทึกผลการทดลอง 6_2

**ขั้นตอนที่ 1 (เฉพาะ sensor.c):**
- จำนวนไฟล์ source: 4 ไฟล์
- ขนาด binary: 163041 bytes
- การทำงาน: แสดงข้อมูล sensor ทุก 2 วินาทีใน terminal เช่น Temperature และ Humidity โดยวนลูป และมีการตรวจสอบสถานะ sensor ทุก 3 รอบ

**ขั้นตอนที่ 2 (เพิ่ม display.c):**
- จำนวนไฟล์ source: 6 ไฟล์
- ขนาด binary: 163925 bytes
- การทำงาน: มีการแสดงข้อความ "System Starting..." และแสดงค่าข้อมูล sensor บน display simulation พร้อมทั้งแสดงสถานะ sensor ทุก 3 รอบ

**ขั้นตอนที่ 3 (เพิ่ม led.c):**
- จำนวนไฟล์ source: 8 ไฟล์
- ขนาด binary: 164873 bytes
- การทำงาน: LED จะกะพริบทุก 3 วินาที และแสดงข้อความสถานะของ LED (ON/OFF) บน display ควบคู่กับข้อมูล sensor และการตรวจสอบ sensor status

การเพิ่มไฟล์ source ส่งผลต่อขนาด binary อย่างไร?
การเพิ่มไฟล์ source ใหม่ เช่น sensor.c, display.c, led.c จะทำให้ขนาด binary เพิ่มขึ้นตามจำนวนโค้ดและฟังก์ชันที่ compile เข้ามา
ตัวอย่าง
เพิ่ม sensor.c → เพิ่มประมาณ 50–70 KB
เพิ่ม display.c → เพิ่มอีกประมาณ 60 KB
เพิ่ม led.c → เพิ่มอีกประมาณ 60 KB
รวมทั้งหมด binary อาจเพิ่มจาก ~350 KB เป็น ~470 KB

LED กะพริบทุกกี่วินาที?
LED จะกะพริบ ทุก 3 วินาที

แต่ละโมดูลแสดงข้อมูล file และ line อย่างไร?
โมดูลทั้งหมดใช้ macro __FILE__ และ __LINE__ เพื่อแสดงชื่อไฟล์และบรรทัดที่โค้ดรันอยู่

I (123) SENSOR: 📡 Sensor module ready for operation from file: sensor.c, line: 18
I (126) DISPLAY: 📺 Message: System Starting... from file: display.c, line: 25
I (129) LED: 🔄 LED toggled from file: led.c, line: 42
