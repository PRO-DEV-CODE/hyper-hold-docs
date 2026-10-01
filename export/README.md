# การเชื่อมต่อ (Export)

Hyper_Hold ไม่มี `exports` ให้ resource อื่นเรียก ผลสแกนทั้งเซิร์ฟพบ 0 export และไม่มี resource ไหนเรียก event ของ Hyper_Hold
จุดเข้าที่ใช้จากภายนอกได้คือ client event 2 ตัว ส่วน Hyper_Hold เองเรียก export ของ resource อื่น 1 ตัว

## ถ้าจะทำเมนู / ระบบ → ใช้อะไร

| ถ้าจะทำ | ใช้ | ฝั่ง | ตัวอย่างในเซิร์ฟ |
| --- | --- | --- | --- |
| เปิดเมนูอุ้มจาก radial menu / F-menu | `TriggerEvent("Hyper_Hold:client:Hold_MenuOpen")` | client | ยังไม่มี |
| อุ้มท่าที่ระบุจาก target (ox_target / qtarget) | `TriggerEvent("Hyper_Hold:client:Hold_Request_Target_ToServer", "carry2")` | client | ยังไม่มี |
| ปุ่มอุ้มศพในเมนูหมอ | `TriggerEvent("Hyper_Hold:client:Hold_Request_Target_ToServer", "carry")` | client | ยังไม่มี (ตอนนี้ใช้ปุ่ม G) |
| สั่งเปิดเมนูอุ้มจาก server | `TriggerClientEvent("Hyper_Hold:client:Hold_MenuOpen", playerId)` | server | ยังไม่มี |

## Hold_MenuOpen

```lua
-- client
TriggerEvent("Hyper_Hold:client:Hold_MenuOpen")
```

ทำงานเหมือนกด F9 ถ้ายังไม่ได้อุ้มจะเปิดเมนูท่า ถ้าอุ้มอยู่จะเปิดเมนู "ยกเลิก [ID]"
และจะไม่ทำงานถ้ากำลังส่งคำขอ กำลังยกเลิก หรือมีคำเชิญค้างอยู่ เป็น net event จึงสั่งจาก server ได้

## Hold_Request_Target_ToServer

```lua
-- client: อุ้มคนที่ใกล้ที่สุดในระยะ 3 เมตรด้วยท่าที่ระบุ
TriggerEvent("Hyper_Hold:client:Hold_Request_Target_ToServer", holdType)
```

| พารามิเตอร์ | ชนิด | หมายเหตุ |
| --- | --- | --- |
| `holdType` | string | คีย์ใน `Config.Hold_Action` คือ `carry`, `carry1`–`carry8` หรือ `hostage` |

- เลือกเป้าหมายด้วย `ESX.Game.GetClosestPlayer()` จึงเลือกคนเองไม่ได้
- ถ้ากำลังอุ้มอยู่ การเรียกซ้ำจะเป็นการ**ยกเลิก**การอุ้ม (ปุ่ม G ใช้วิธีนี้วางศพ)
- เช็คเงื่อนไขครบเหมือนเลือกจากเมนู (รถ โซน ระยะ เป็น/ตาย อาวุธ)
- ใช้ท่า `hostage` ผ่าน event นี้ได้ แม้ท่านี้จะไม่อยู่ในเมนู

{% hint style="warning" %}
event นี้เป็น local event (ไม่ใช่ net event) จึงต้องเรียกจาก client ของคนอุ้มเท่านั้น ใช้ `TriggerClientEvent` จาก server ไม่ได้
{% endhint %}

## Export ที่ Hyper_Hold เรียก

| Resource | Export | ใช้ทำอะไร |
| --- | --- | --- |
| Hyper_Notifyall | `sendNotify` (client) + `sendNotify` พร้อม `source` (server) | แจ้งเตือนทุกข้อความ ตำแหน่ง `bottomCenter` เวลา 1.5 วินาที |
| esx_menu_default | `ESX.UI.Menu.Open("default", ...)` | เมนูอุ้ม เมนูยืนยัน และเมนูยกเลิก |

```lua
-- core/client/main.lua
exports["Hyper_Notifyall"]:sendNotify({
    text = { title = "แจ้งเตือน", message = tostring(message) or "" },
    type = kind or "info",
    time = time or 1500,
    layout = layout or "bottomCenter",
})
```

## Resource ที่เกี่ยวข้อง

| Resource | ความสัมพันธ์ |
| --- | --- |
| Hyper_Check | Hyper_Hold โหลด `protected.lua` + Code Vault และ IVM ของ Hyper_Check ยกเว้น ped ที่ถูกอุ้ม |
| Hyper_Notifyall | แสดงแจ้งเตือนทั้งหมด |
| esx_menu_default | วาดเมนูและธีมสี |

{% hint style="info" %}
ไม่ต้องเพิ่มอะไรใน `hyper_vault_export` เพราะ resource นี้ไม่มี export ฝั่ง client
{% endhint %}
