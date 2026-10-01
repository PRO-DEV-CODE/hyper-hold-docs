# เทคโนโลยีและโครงสร้าง

โค้ดทั้งหมดเป็น Lua 5.4 (fx_version cerulean) มี 11 ไฟล์ รวม 2,006 บรรทัด ไม่มี HTML/JS และไม่มี export

## ลำดับการทำงาน

1. คนอุ้มกด F9 แล้วเลือกท่า หรือกด G ก็จะยิง client event ภายใน `Hyper_Hold:client:Hold_Request_Target_ToServer`
2. ฝั่ง client เช็ครถ โซน ระยะ สถานะเป็น/ตาย และอาวุธ แล้วส่ง `Hyper_Hold:server:Hold_Request_Target`
3. Server ตัดสินว่าต้องรอยืนยันหรือไม่
    1. ถ้าต้องรอ: เก็บคำขอไว้ใน `invitesByTarget` / `inviteBySource` แล้วส่ง `Hold_Invate_FromSource` ไปหาคนถูกอุ้มพร้อมตั้ง timeout 10 วินาที
    2. ถ้าไม่ต้องรอ: เรียก `Hyper_Hold:server:Hold_Action` ทันที
4. คนถูกอุ้มตอบผ่าน `Hold_InvateReceive_FromTarget` แล้ว server ล้างคำขอและเรียก `Hold_Action`
5. `Hold_Action` ส่ง `Hold_Action_Source_Receive` และ `Hold_Action_Target_Receive` ไปทั้งสองฝั่งพร้อมดีเลย์ และส่ง Discord log
6. ทั้งสองฝั่งเล่น anim และ attach เข้าด้วยกัน
7. ตอนปล่อย ระบบส่ง anim ตอนยกเลิก (ถ้ามี) ผ่าน `TaskAnim:RequestSourceAndTarget` แล้วส่ง `Hold_Clear` ไปให้อีกฝ่าย

## ไฟล์

| ไฟล์ | บรรทัด | หน้าที่ |
| --- | --- | --- |
| `core/client/main.lua` | 816 | state machine สองฝั่ง เมนู คำเชิญ anim/attach และ thread 6 ตัว |
| `config/config.anim.lua` | 469 | anim + attach ของ 10 ท่า |
| `core/server/main.lua` | 256 | ตัวประสาน request / invite / action / clear / kill และ Discord webhook |
| `config/functions/config.functions.lua` | 133 | `C.HA`, `C.xHO`, `C.xHJ`, `HTL_FIND` |
| `core/shared/common.lua` | 107 | `GetName`, `RegisEvent`, `GetDelay`, ตาราง keycode |
| `config/config.shared.lua` | 80 | ค่าหลัก |
| `config/config.discord.lua` | 60 | webhook 5 ช่อง |
| `fxmanifest.lua` | 40 | โหลดสคริปต์, Hyper_Check, Code Vault |
| `config/config.language.lua` | 32 | ข้อความ 22 คีย์ |
| `config/framework/esx/shared.lua` | 9 | bridge ESX |
| `core/shared/version.lua` | 4 | เวอร์ชัน |
| `stream/*.gfx` | — | ฟอนต์ 11 ไฟล์ (ใช้จริงเฉพาะ sarabun) |

## Thread ฝั่ง client

| Thread | รอบ | หน้าที่ |
| --- | --- | --- |
| `Thread_KeyOpenMenu` | 5 ms | ฟังปุ่ม F9 |
| `Thread_KeyCarryCorpse` | 5 ms | ฟังปุ่ม G |
| `Thread_DisabledControl` | ทุกเฟรมตอนอุ้ม / 1 วินาทีตอนว่าง | ปิดปุ่มคนถูกอุ้ม |
| `Thread_HoldOption` | 5 ms ตอนจับตัวประกัน / 1.5 วินาทีตอนว่าง | ข้อความ 3D + ปุ่มฆ่า |
| `Thread_HoldValid` | 2 วินาที | เช็คระยะ 10 เมตร การตาย และการขึ้นรถ |
| `Thread_UpdateCoords` | 100 ms ตอนอุ้มศพ / 1 วินาทีตอนว่าง | ดึงศพตามคนอุ้ม |

## Event

| Event | ชนิด | ทิศทาง |
| --- | --- | --- |
| `Hyper_Hold:server:Hold_Request_Target` | net | client → server |
| `Hyper_Hold:server:Hold_InvateReceive_FromTarget` | net | client → server |
| `Hyper_Hold:server:Hold_Clear` | net | client → server → client |
| `Hyper_Hold:server:Hold_Kill_Receive` | net | client → server → client |
| `Hyper_Hold:server:TaskAnim:RequestSourceAndTarget` | net | client → server → 2 client |
| `Hyper_Hold:server:Hold_Action` | ภายใน server | — |
| `Hyper_Hold:client:Hold_MenuOpen` | net | เปิดเมนู |
| `Hyper_Hold:client:Hold_Request_Target_ToServer` | ภายใน client | เริ่มอุ้ม |
| `Hyper_Hold:client:Hold_Invate_FromSource` | net | server → client |
| `Hyper_Hold:client:Hold_Action_Source_Receive` / `_Target_Receive` | net | server → client |
| `Hyper_Hold:client:Hold_Clear` | net | server → client |
| `Hyper_Hold:client:Hold_Kill_Receive` | net | server → client |
| `Hyper_Hold:client:TaskAnim:Receive` | net | server → client |

## ความปลอดภัย

- โค้ดฝั่ง client (`core/client/main.lua`) ส่งผ่าน Hyper_Check Code Vault ตอน runtime จึงไม่ถูก cache ไว้ในเครื่องผู้เล่น
- Hyper_Check (IVM) ยกเว้น ped ที่ถูก attach อยู่ เพื่อไม่ให้คนถูกอุ้มโดนจับว่าวาร์ป
- server เก็บคำขอไว้ฝั่งตัวเอง ทำให้แต่ละคนมีคำขอค้างได้ครั้งละ 1 รายการ

{% hint style="warning" %}
**จุดที่ server ยังเชื่อ client**

- `Hold_Request_Target` ไม่เช็คระยะระหว่างสองคนที่ฝั่ง server และเชื่อค่า `targetIsDead` จาก client ถ้าส่ง `true` จะข้ามการยืนยันได้
- `Hold_Clear` และ `TaskAnim:RequestSourceAndTarget` ส่งต่อไปยัง player id ใดก็ได้ที่ client ระบุ
- `Hold_Kill_Receive` ให้ client ของตัวประกันเป็นคนตรวจว่าผู้ส่งคือคนที่จับอยู่จริง server ไม่ได้ตรวจเอง
- ไม่มี rate limit กับทุก net event
{% endhint %}
