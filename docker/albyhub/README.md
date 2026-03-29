# Alby Hub Docker (สำหรับมือใหม่)

Alby Hub เป็นกระเป๋าเงิน (Wallet) และ Lightning Node ที่ใช้งานง่ายบนเบราว์เซอร์ คู่มือนี้จะช่วยให้คุณติดตั้ง Alby Hub ด้วย Docker ได้อย่างรวดเร็ว

## สิ่งที่ต้องเตรียม
1. **Docker**: หากยังไม่ได้ติดตั้ง ดูวิธีได้ที่ [การติดตั้ง Docker](../install.md)
2. **Bitcoin Node**: Alby Hub จำเป็นต้องเชื่อมต่อกับ Bitcoin Node (สามารถใช้โหนดของตัวเองหรือบริการภายนอกก็ได้)

---

## ขั้นตอนการติดตั้ง

### 1. เตรียม Folder สำหรับเก็บข้อมูล
สร้างโฟลเดอร์สำหรับเก็บข้อมูลของ Alby Hub:
```bash
mkdir -p /mnt/alby-hub-data
```

### 2. ตั้งค่าไฟล์ .env
แก้ไขไฟล์ [.env](.env) เพื่อกำหนดค่าพื้นฐาน:
* **UID** และ **GID**: ID ของผู้ใช้ในเครื่อง (ปกติคือ `1000`)
* **HOST_ALBY_HUB_DATA_DIR**: ระบุ Path ของโฟลเดอร์ที่สร้างในขั้นตอนที่ 1
* **VERSION**: เวอร์ชันของ Alby Hub ที่ต้องการรัน

### 3. เลือกวิธีการเชื่อมต่อ Bitcoin Node
เปิดไฟล์ [docker-compose.yml](docker-compose.yml) และดูในส่วน `environment:` คุณสามารถเลือกเชื่อมต่อได้หลายวิธี:

* **วิธีที่ 1: ใช้ค่าเริ่มต้นของ Alby (ง่ายที่สุด)**
  ไม่ต้องแก้อะไร Alby Hub จะเชื่อมต่อไปยังบริการของ Alby โดยอัตโนมัติ

* **วิธีที่ 2: ใช้ Electrum Server ของตัวเอง**
  ระบุที่อยู่ของ Electrum Server:
  ```yaml
  LDK_ELECTRUM_SERVER: "electrum.example.com:50001"
  ```

* **วิธีที่ 3: ใช้ Bitcoin Node ของตัวเอง (RPC User/Pass)**
  แก้ไขค่าเหล่านี้ให้ตรงกับ Node ของคุณ:
  ```yaml
  LDK_BITCOIND_RPC_HOST: "127.0.0.1"
  LDK_BITCOIND_RPC_PORT: 8332
  LDK_BITCOIND_RPC_USER: "your_user"
  LDK_BITCOIND_RPC_PASSWORD: "your_password"
  ```

* **วิธีที่ 4: ใช้ Bitcoin Node ของตัวเอง (Cookie Auth)**
  หากต้องการใช้ cookie file ในการยืนยันตัวตน:
  ```yaml
  LDK_BITCOIND_RPC_HOST: "127.0.0.1"
  LDK_BITCOIND_RPC_PORT: 8332
  LDK_BITCOIND_RPC_USER: "__cookie__"
  LDK_BITCOIND_RPC_PASSWORD: "{ค่าจากไฟล์ .cookie ใน bitcoin data directory}"
  ```

### 4. เริ่มรัน Alby Hub
ใช้คำสั่ง:
```bash
docker compose up -d
```
เมื่อรันเสร็จแล้ว คุณสามารถเข้าใช้งาน Alby Hub ผ่านเบราว์เซอร์ที่ `http://localhost:8080` (หรือไอพีของเครื่องคุณ)

---

## การตรวจสอบสถานะ

### เช็คว่าทำงานอยู่หรือไม่
```bash
docker ps
```

### ดู Logs การทำงาน
```bash
docker logs -f albyhub
```

---

## คำสั่งที่ใช้บ่อย
* **หยุดการทำงาน**: `docker compose down`
* **เริ่มการทำงานใหม่**: `docker compose restart`
* **อัปเดตเวอร์ชัน**: แก้ไข `VERSION` ใน `.env` แล้วรัน `docker compose up -d` อีกครั้ง

---
[กลับไปหน้าหลัก](../../README.md)
