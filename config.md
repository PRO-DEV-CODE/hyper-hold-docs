# การตั้งค่า (Config)

ค่าทั้งหมดอยู่ในโฟลเดอร์ `config/` และใช้ร่วมกันทั้ง client และ server แก้แล้วต้องสั่ง `restart Hyper_Hold`

| ไฟล์ | เนื้อหา |
| --- | --- |
| `config.shared.lua` | ปุ่ม เมนู ระยะ การยืนยัน อาชีพ โซน ฟอนต์ |
| `config.anim.lua` | anim + attach ของแต่ละท่า (`Config.Hold_Action`) |
| `config.language.lua` | ข้อความภาษาไทย 22 คีย์ (`Config.T`) |
| `config.discord.lua` | webhook (`Config.DC`) |
| `framework/esx/shared.lua` | ชื่อ event ของ ESX (`getSharedObject`, `onPlayerSpawn`, `onPlayerDeath`) |

## config.shared.lua

| ค่า | ค่าตอนนี้ | ความหมาย |
| --- | --- | --- |
| `Config.Key_OpenMenu` | `Keys["F9"]` (56) | ปุ่มเปิดเมนูอุ้ม ตั้งเป็น `nil` เพื่อปิด |
| `Config.Key_CarryCorpse` | `Keys["G"]` (47) | ปุ่มอุ้ม/วางศพ ตั้งเป็น `nil` เพื่อปิด |
| `Config.MenuAlign` | `"top-right"` | ตำแหน่งเมนูอุ้ม |
| `Config.Hold_Type_List` | 9 รายการ | ท่าในเมนู `{ name, label }` โดย `name` ต้องตรงกับคีย์ใน `Config.Hold_Action` |
| `Config.Hold_Request_Distance` | `3.0` | ระยะค้นหาผู้เล่น (เมตร) |
| `Config.Hold_Target_IsCancel` | `true` | ค่ากลาง: คนถูกอุ้มยกเลิกเองได้ |
| `Config.Hold_Target_IsDead` | `true` | ค่ากลาง: อุ้มคนตายได้ |
| `Config.NeedInvate` | `true` | ต้องให้อีกฝ่ายยอมรับก่อน |
| `Config.NeedInvate_Timeout` | `10 * 1000` | เวลารอยอมรับ (ms) |
| `Config.NeedInvate_MenuAlign` | `"center"` | ตำแหน่งเมนูยืนยัน |
| `Config.Delay_After_Action` | `{ 100, 500 }` | ดีเลย์สุ่มก่อนเริ่มอุ้ม (ms) |
| `Config.Hold_DisabledControl` | target: 7 ปุ่ม | ปุ่มที่ปิดระหว่างอุ้ม แยก `source` / `target` |
| `Config.Hold_Option` | `hostage`, `carry` | ค่าเฉพาะท่า |
| `Config.Hold_Job` | police, ambulance, gouvernment | ค่าเฉพาะอาชีพ |
| `Config.Zone_BlackList` | 2 จุด | โซนห้ามอุ้ม `{ enabled, x, y, z, distance, job_allow }` |
| `Config.Text_Font` | `"sarabun"` | ฟอนต์ข้อความ 3D |
| `Config.Text_Size` | `0.75` | ขนาดข้อความ 3D |

{% hint style="info" %}
ปุ่มใช้ตาราง `Keys` ใน `core/shared/common.lua` เช่น `Keys["F9"]` = 56 และ `Keys["G"]` = 47 ถ้าปุ่มที่ต้องการไม่อยู่ในตาราง ใส่ keycode ของ GTA ตรง ๆ ได้
{% endhint %}

## ลำดับความสำคัญของค่า

| ค่า | ลำดับ (ตัวหลังชนะ) |
| --- | --- |
| ข้ามการยืนยัน (`Bypass_NeedInvate`) | ไม่ข้าม → `Hold_Job` → `Hold_Option` |
| อุ้มคนตายได้ (`Request_Target_IsDead`) | `Hold_Target_IsDead` → `Hold_Job` → `Hold_Option` |
| คนถูกอุ้มยกเลิกเองได้ (`Target_IsCancel`) | ต้องผ่านทั้ง `Hold_Target_IsCancel`, `Hold_Option` และ `Hold_Job` |

ตัวอย่างเช่น ถ้าอาชีพ police อุ้มด้วยท่า `carry1` จะข้ามการยืนยันได้เพราะ `Hold_Job.police`
แต่ถ้าเพิ่ม `Hold_Option.carry1 = { Bypass_NeedInvate = false }` ค่าของท่าจะชนะ และ police ต้องรอให้อีกฝ่ายยอมรับ

## Config.Hold_Option

```lua
Config.Hold_Option = {
    ["hostage"] = {
        Bypass_NeedInvate = false,
        UseWeapon = false,
        UseWeaponList = { "weapon_dagger", "weapon_bottle", "weapon_knife", "weapon_machete", "weapon_switchblade" },
        Kill = false,
        Kill_Key = 38, -- E
        Kill_Alert = "กด ~r~[E]~s~ ฆ่าตัวประกัน",
        Target_IsCancel = false,
        Request_Target_IsDead = false,
    },
    ["carry"] = {
        Bypass_NeedInvate = true,
        Target_IsCancel = false,
        Request_Target_IsDead = true,
    },
}
```

## เพิ่มท่าใหม่

1. เพิ่มคีย์ใน `Config.Hold_Action` (`config.anim.lua`) แยกเป็น `source` / `target` โดยแต่ละฝั่งมี `TaskAnim` และฝั่ง target ใส่ `AttachEntity` ได้
2. เพิ่ม `{ name = "<คีย์>", label = "<ชื่อในเมนู>" }` ลงใน `Config.Hold_Type_List`
3. ถ้าต้องการ log ให้เพิ่ม `Config.DC["<คีย์>"]`
4. ถ้าต้องการให้เล่น anim ตอนปล่อย ให้ใส่ `Cancel_TaskAnim` แบบเดียวกับท่า `hostage`

```lua
["carry9"] = {
    ["source"] = {
        TaskAnim = {
            animDictionary = "...", animationName = "...",
            blendInSpeed = 8.0, blendOutSpeed = -8.0, duration = -1, flag = 49,
            playbackRate = 0, lockX = false, lockY = false, lockZ = false,
        },
    },
    ["target"] = {
        TaskAnim = { --[[ แบบเดียวกับ source ]] },
        AttachEntity = {
            boneIndex = 0, xPos = 0.0, yPos = 0.0, zPos = 0.0, xRot = 0.0, yRot = 0.0, zRot = 0.0,
            p9 = false, useSoftPinning = false, collision = false, isPed = false,
            vertexIndex = 2, fixedRot = false,
        },
    },
},
```

## config.language.lua

| คีย์ | ข้อความ |
| --- | --- |
| `hold_menu_title` | หน้าต่างอุ้ม |
| `player_isin_vehicle` | ผู้เล่นอยู่บนรถ ต้องลงจากรถก่อน |
| `hold_target_notfound` | ไม่พบผู้เล่น |
| `hold_target_invaild` | เกิดข้อผิดพลาด กรุณาลองใหม่อีกครั้ง |
| `hold_target_isdead` | ไม่สามารถ %s ผู้เล่นที่ตายได้ |
| `hold_target_isnotdead` | ผู้เล่นยังไม่ตาย ไม่สามารถอุ้มศพได้ |
| `hold_target_request` | ส่งคำขอไปยังผู้เล่น [ID:%s] |
| `hold_source_cancel` / `hold_target_cancel` | ยกเลิก [ID:%s] |
| `hold_invate_title` | ยืนยันการขอจากผู้เล่น |
| `hold_invate_accept` / `hold_invate_reject` | ยอมรับ [ID:%s] / ยกเลิก |
| `hold_invate_receive_reject_source` | ผู้เล่นยกเลิกคำขอ [ID:%s] |
| `hold_invate_timeout_source` / `hold_invate_timeout_target` | หมดเวลาคำขอ [ID:%s] |
| `hold_action_delay_source` / `hold_action_delay_target` | กรุณารอสักครู่... |
| `hold_action_success_source` / `hold_action_success_target` | %s [ID:%s] (ชื่อท่า + ID) |
| `hold_option_useweapon` | ต้องถืออาวุธก่อน |

ถ้าลบคีย์ไหนออก แจ้งเตือนนั้นจะไม่ขึ้น ส่วนข้อความ "พื้นที่นี้ไม่สามารถ %s ได้" เขียนไว้ในโค้ดโดยตรง ไม่ได้อยู่ใน `Config.T`

## config.discord.lua

```lua
Config.DC = {
    ["carry1"] = {
        Enabled = false, -- true = เปิดใช้
        URL_Webhook = "",
        Action = true, -- ส่ง log ตอนอุ้ม
        Template = {
            color = 0x08F896,
            description = "Source: **%s** | **%s** \n" .. "Target: **%s** | **%s**",
        },
    },
    -- hostage มี Kill = true และ color_kill = 0xFF0000 เพิ่มเข้ามา
}
```

มีทั้งหมด 5 ช่อง คือ `carry` (สี `0xFF6B6B`), `carry1`, `carry2` และ `carry3` (สี `0x08F896`) และ `hostage` (สี `0xF88308` ตอนจับ และ `0xFF0000` ตอนฆ่า)
