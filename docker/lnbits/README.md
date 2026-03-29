# LNbits Docker (สำหรับมือใหม่)

LNbits เป็นระบบกระเป๋าเงิน (Wallet) และระบบบริหารจัดการ Lightning Network ที่ทรงพลัง คุณสามารถติดตั้ง Extension ต่างๆ เพื่อเปลี่ยน LNbits ให้เป็นระบบ POS, Paywall, หรือแม้แต่ระบบจัดการพนักงานได้

## สิ่งที่ต้องเตรียม
1. **Docker**: ดูวิธีได้ที่ [การติดตั้ง Docker](../install.md)
2. **Lightning Backend**: LNbits ต้องเชื่อมต่อกับ Lightning Node (เช่น LND, Core Lightning, Eclair) หรือใช้บริการภายนอกเพื่อเป็นแหล่งเงินทุน (Funding Source)

---

## ขั้นตอนการติดตั้ง

### 1. เตรียม Folder สำหรับเก็บข้อมูล
สร้างโฟลเดอร์สำหรับเก็บข้อมูลของ LNbits:
```bash
mkdir -p data
```

### 2. ดาวน์โหลดและตั้งค่าไฟล์ .env
LNbits แนะนำให้ดาวน์โหลดไฟล์ตัวอย่างจาก Repository หลักโดยตรง:
```bash
curl -sSL https://raw.githubusercontent.com/lnbits/lnbits/main/.env.example -o .env
```
จากนั้นแก้ไขค่าต่างๆ ในไฟล์ `.env` ตามต้องการ (เช่น `LNBITS_SITE_TITLE`) และตรวจสอบให้แน่ใจว่า `LNBITS_ADMIN_UI=true` เพื่อความสะดวกในการตั้งค่า

### 3. เริ่มรัน LNbits
แก้ไขไฟล์ [docker-compose.yml](docker-compose.yml) โดยเปลี่ยนคำว่า `LNBITS_PATH` เป็นตำแหน่งโฟลเดอร์ปัจจุบันของคุณ (เช่น `./`) จากนั้นใช้คำสั่ง:
```bash
docker compose up -d
```
เมื่อรันเสร็จแล้ว คุณสามารถเข้าใช้งาน LNbits ผ่านเบราว์เซอร์ที่ `http://localhost:5000` (หรือไอพีของเครื่องคุณ)

---

## การเข้าใช้งานครั้งแรก (สร้าง SuperUser) 🔑

เมื่อคุณเปิดหน้าเว็บครั้งแรก ระบบจะตรวจพบว่ายังไม่มีผู้ใช้ และจะให้คุณสร้าง **SuperUser** ของระบบ:
1. เข้าไปที่ `http://localhost:5000`
2. ระบบจะแสดงหน้าจอให้คุณตั้งค่า **Username** และ **Password** สำหรับ SuperUser
3. เมื่อสร้างเสร็จแล้ว คุณจะสามารถเข้าสู่หน้า **Admin UI** เพื่อตั้งค่า Funding Source (เช่น เชื่อมต่อกับ Bitcoin Node หรือ LND) ได้ทันที

---

## การเปิดให้เข้าใช้งานจากภายนอก (Cloudflare Tunnel) 🌐
คุณสามารถเพิ่มบริการ Cloudflare Tunnel ลงในไฟล์ `docker-compose.yml` เพื่อสร้าง URL ให้เข้าใช้งานจากที่ไหนก็ได้โดยไม่ต้องเปิดพอร์ต:

เพิ่มส่วนนี้ต่อท้ายใน `services:`:
```yaml
  cloudflared:
    container_name: lnbits-tunnel
    image: cloudflare/cloudflared:latest
    command: tunnel --url http://lnbits:5000
```
หลังจากรัน `docker compose up -d` คุณสามารถดู URL ได้จาก Log:
```bash
docker logs lnbits-tunnel
```
*มองหาข้อความประมาณ `https://something-random.trycloudflare.com` ใน Log*

---

## ข้อแนะนำด้านความปลอดภัย (สำคัญมาก) ⚠️
หลังจากที่คุณสร้าง SuperUser และตั้งค่า Funding Source สำเร็จแล้ว **ควรปิดการสร้าง User ใหม่** เพื่อไม่ให้คนแปลกหน้ามาใช้งานเครื่องของคุณ โดยวิธีที่ง่ายที่สุดคือตั้งค่าผ่านหน้าเว็บ:

1. เข้าไปที่เมนู **Manage Server** ในหน้า Admin
2. ไปที่ส่วน **Security**
3. ปิดตัวเลือก **Allow new accounts** แล้วกด Save

---

## การตรวจสอบสถานะ
* **เช็คสถานะ**: `docker ps`
* **ดู Logs ของ LNbits**: `docker logs -f lnbits`

---

## คำสั่งที่ใช้บ่อย
* **หยุดการทำงาน**: `docker compose down`
* **อัปเดตเวอร์ชัน**: `docker compose pull && docker compose up -d`

---
[กลับไปหน้าหลัก](../../README.md)
