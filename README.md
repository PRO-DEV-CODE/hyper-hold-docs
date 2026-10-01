# Hyper_Hold

Hyper_Hold คือระบบอุ้ม แบก และจับตัวประกันสำหรับเซิร์ฟ ESX ในเมนูมี 9 ท่า ผู้เล่นกด **F9** เพื่อเปิดเมนูอุ้ม หรือกด **G** เพื่ออุ้มศพได้ทันที

![เมนูอุ้ม](.gitbook/assets/01_hold_menu.png)

| รายการ | ค่า |
| --- | --- |
| เวอร์ชัน | 2.0.0 (Hyper Dev) |
| เฟรมเวิร์ก | ESX (`esx:getSharedObject`) |
| ท่าอุ้ม | 9 ท่าในเมนู + ท่าจับตัวประกัน 1 ท่า (ตั้งค่าไว้ แต่ยังไม่อยู่ในเมนู) |
| ปุ่ม | F9 เปิดเมนู · G อุ้ม/วางศพ · E ฆ่าตัวประกัน (ถ้าเปิดใช้) |
| UI | ใช้เมนู `esx_menu_default` + แจ้งเตือน `Hyper_Notifyall` (ไม่มี NUI ของตัวเอง) |
| ทำงานร่วมกับ | es_extended, esx_menu_default, Hyper_Notifyall, Hyper_Check (protected + Code Vault) |
| ฟอนต์ในเกม | Sarabun (`stream/sarabun.gfx`) สำหรับข้อความ 3D |

## ติดตั้ง

1. วางโฟลเดอร์ `Hyper_Hold` ใน `resources/[Esx]/[Inventory]/`
2. เพิ่ม `ensure Hyper_Hold` ใน `script.cfg` (เซิร์ฟนี้อยู่บรรทัด 63) **หลัง** es_extended, esx_menu_default, Hyper_Notifyall และ Hyper_Check
3. ตั้งปุ่ม ระยะ อาชีพ และโซนห้ามอุ้มใน `config/config.shared.lua` (ดู [การตั้งค่า](config.md))
4. ถ้าต้องการ log ให้ใส่ webhook ใน `config/config.discord.lua` แล้วตั้ง `Enabled = true`

## เริ่มต้นใช้งาน

1. ยืนห่างผู้เล่นอีกคนไม่เกิน 3 เมตร แล้วกด **F9**
2. เลือกท่าในเมนู "หน้าต่างอุ้ม"
3. อีกฝ่ายจะเห็นเมนู "ยืนยันการขอจากผู้เล่น" และต้องกดยอมรับภายใน 10 วินาที
4. ถ้าต้องการปล่อย ให้กด F9 อีกครั้งแล้วเลือก "ยกเลิก [ID]"

{% hint style="info" %}
ภาพหน้าจอในเอกสารนี้ถ่ายจากโค้ด NUI จริงของ esx_menu_default และ Hyper_Notifyall โดยส่งข้อมูลแบบเดียวกับที่ Hyper_Hold ส่งในเกม ส่วน ID ผู้เล่นในภาพเป็นข้อมูลจำลอง
{% endhint %}
