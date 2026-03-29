# Electrs Docker (สำหรับมือใหม่)

Electrs เป็นโปรแกรมที่ช่วยให้ Bitcoin Wallet (เช่น Electrum, BlueWallet, Alby) เชื่อมต่อกับ Bitcoin Node ของคุณได้รวดเร็วขึ้น คู่มือนี้จะช่วยคุณติดตั้ง Electrs ผ่าน Docker

## สิ่งที่ต้องเตรียม
1. **Docker**: ดูวิธีได้ที่ [การติดตั้ง Docker](../install.md)
2. **Bitcoin Node**: ต้อง Sync เสร็จสมบูรณ์แล้ว (ควรใช้พื้นที่เก็บข้อมูลเดียวกันหรือเชื่อมต่อถึงกันได้)

---

## ขั้นตอนที่ 1: การ Build Docker Image (สร้างโปรแกรม)

เนื่องจาก Electrs ควรถูกสร้าง (Compile) ให้เหมาะกับสถาปัตยกรรมของเครื่องคุณ เราจะเริ่มด้วยการสร้าง Docker Image เองก่อน:

1. เข้าไปที่โฟลเดอร์ `build`:
   ```bash
   cd build
   ```
2. รันสคริปต์สำหรับสร้าง Image:
   ```bash
   chmod +x build.sh
   ./build.sh
   ```
   *(ขั้นตอนนี้อาจใช้เวลาสักพัก ขึ้นอยู่กับความเร็วเครื่องของคุณ)*
3. ตรวจสอบว่า Image ถูกสร้างสำเร็จ:
   ```bash
   docker images
   ```
   ควรเห็นรายการชื่อ `local/electrs` ปรากฏขึ้น

### การทำงานของ Dockerfile (ข้อมูลเพิ่มเติม)
หากคุณสงสัยว่าในขั้นตอนการ Build เกิดอะไรขึ้นบ้าง สามารถดูได้ที่ไฟล์ [Dockerfile](build/Dockerfile):
1. **ดาวน์โหลด Source Code**: ดึงโค้ดล่าสุดจาก [GitHub ของ romanz/electrs](https://github.com/romanz/electrs)
2. **Compile โปรแกรม**: ใช้ภาษา Rust ในการสร้างไฟล์โปรแกรม (Binary) ที่เหมาะสมกับเครื่องของคุณที่สุด
3. **สร้าง Image ที่ปลอดภัย**: นำไฟล์โปรแกรมที่สร้างเสร็จแล้ว ไปใส่ไว้ในสภาพแวดล้อมที่สะอาดและปลอดภัย (Debian Slim) โดยแยกส่วนที่ใช้ Build ออกไปเพื่อให้ Image มีขนาดเล็กที่สุด

---

## ขั้นตอนที่ 2: การรัน Electrs Container

หลังจากสร้าง Image สำเร็จแล้ว เราจะมาตั้งค่าการทำงาน:

1. เข้าไปที่โฟลเดอร์ `run`:
   ```bash
   cd ../run
   ```
2. ตั้งค่าไฟล์ [.env](run/.env) เพื่อเชื่อมต่อกับ Bitcoin Node:
   * **ELECTRS_HOST_DB_DIR**: โฟลเดอร์ที่จะเก็บ Database ของ Electrs (แนะนำเป็น SSD)
   * **ELECTRS_HOST_DAEMON_DIR**: Path ของโฟลเดอร์ข้อมูลของ Bitcoin Node ที่ Sync ไว้
   * **ELECTRS_DAEMON_RPC_ADDR**: ไอพีของ Bitcoin RPC (ปกติคือ `127.0.0.1:8332`)

3. เริ่มรัน Electrs:
   ```bash
   docker compose up -d
   ```

---

## การตรวจสอบสถานะ

### ดูการทำงานของ Electrs
```bash
docker logs -f electrs
```
*ช่วงแรก Electrs จะต้องทำการ "Indexing" ข้อมูลจาก Bitcoin Node ซึ่งอาจใช้เวลาหลายชั่วโมงถึงหลายวัน ขึ้นอยู่กับความเร็วของ Disk*

### เช็คพอร์ตการเชื่อมต่อ
ตรวจสอบว่า Electrum Server เปิดรอรับการเชื่อมต่อ (Port 50001) หรือยัง:
```bash
docker ps
```

---

## คำสั่งที่ใช้บ่อย
* **หยุดการทำงาน**: `docker compose down` (รันในโฟลเดอร์ `run`)
* **เริ่มการทำงานใหม่**: `docker compose restart`
* **อัปเดตเวอร์ชัน**: แก้ไขค่า `VERSION` ใน `build/build.sh` แล้วกลับไปทำขั้นตอนที่ 1 และ 2 ใหม่

---
[กลับไปหน้าหลัก](../../README.md)
