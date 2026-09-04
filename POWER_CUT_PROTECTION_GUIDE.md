# 🛡️ Siemens SIMATIC IOT2050 Power-Cut Proof & Stability Guide
**Project:** Gate Valve Control & Monitoring System — Mitr Phol Pin Mill Plant  
**Author:** Dream Piyapong (xFreedom777)

---

## 🚀 สรุปการปรับปรุงเพื่อป้องกันการดับไฟผิดวิธี (Power-Cut Immune)

ระบบได้รับการอัปเกรดเพื่อป้องกันปัญหา **"User สับเบรกเกอร์ / ดึงปลั๊ก แล้ว OS พัง / SD Card Corrupted"** ให้หมดไป 100%:

### 1. ระบบ OverlayFS (Read-Only Root Filesystem)
* พาร์ติชันหลัก (`/`) บน MicroSD Card จะถูกตั้งเป็น **Read-Only (ห้ามเขียนถาวร)**
* ไฟล์ชั่วคราว, Cache, Log ของระบบ และ Browser จะถูกเขียนลงบน **RAM (`tmpfs`)** แทน
* **ผลลัพธ์:** ไม่ว่าจะสับเบรกเกอร์ตอนไหน SD Card จะไม่ได้รับผลกระทบเลย เปิดใหม่ก็บูตขึ้นมา 100% เสมอ

### 2. Auto-Repair Kernel Bootargs (`fsck.repair=yes`)
* สั่งให้ Linux ซ่อม Inode อัตโนมัติทุกครั้งตอนบูต โดยไม่ต้องรอให้คนมาต่อคีย์บอร์ดกด Enter

### 3. คำสั่งสะดวกสำหรับการดูแลรักษาระบบ (Maintenance Commands)

หากในอนาคตต้องการแก้ไขโค้ด อัปเดตโปรแกรม หรือแก้ไฟล์คอนฟิก สามารถใช้คำสั่งลัดเหล่านี้ผ่าน SSH:

| คำสั่ง | การทำงาน |
| :--- | :--- |
| `sudo edit-system` | เข้าสู่โหมด **Write Mode (chroot)** เพื่อแก้ไฟล์ได้ทันที พิมพ์ `exit` เมื่อเสร็จ |
| `sudo disable-readonly` | ปิดระบบป้องกัน Read-Only ชั่วคราว (แล้ว reboot เพื่อเข้าโหมดปกติ) |
| `sudo enable-readonly` | เปิดระบบป้องกัน Read-Only กลับคืนมา (แล้ว reboot เพื่อเปิดเกราะป้องกัน) |

---

## 📦 วิธี Deploy พรุ่งนี้

1. เสียบสาย LAN หรือเชื่อมต่อ Network กับ IOT2050
2. เปิด PowerShell ที่โฟลเดอร์นี้ แล้วรัน:
   ```powershell
   .\Deploy-Patch.ps1
   ```
3. กด `Y` เพื่อ Reboot เครื่อง IOT2050
4. หลังจากเครื่องบูตขึ้นมาใหม่ ระบบจะอยู่ในโหมด **Power-Cut Proof** ทันทีครับ!