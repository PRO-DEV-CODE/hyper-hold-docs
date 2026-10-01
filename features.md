# ฟีเจอร์หลัก

มีท่าอุ้มทั้งหมด 10 แบบ แต่ละแบบตั้ง anim และ attach แยกกันระหว่างคนอุ้ม (source) กับคนถูกอุ้ม (target) ใน `config/config.anim.lua`

## ท่าอุ้มทั้งหมด

| รหัส | ชื่อในเมนู | Anim คนอุ้ม | Anim คนถูกอุ้ม | Bone ที่ติด |
| --- | --- | --- | --- | --- |
| `carry` | อุ้มศพ | `missfinale_c2mcs_1` / `fin_c2_mcs_1_camman` | `nm` / `firemans_carry` | 0 |
| `carry1` | อุ้ม | `anim@heists@box_carry@` / `idle` | `amb@code_human_in_car_idles@generic@ps@base` / `base` | 9816 |
| `carry2` | ขี่หลัง | `anim@arena@celeb@flat@paired@no_props@` / `piggyback_c_player_a` | `anim@arena@celeb@flat@paired@no_props@` / `piggyback_c_player_b` | 0 |
| `carry3` | แบก | — (เดินปกติ) | `nm` / `firemans_carry` | 0 |
| `carry4` | อุ้มแบบคิมิโน้โตะ | `penguin@custom@v1` / `penguin_carry_m` | `penguin@custom@v1` / `penguin_carry_f` | 9816 |
| `carry5` | [ถ่ายรูป] อุ้มเจ้าสาว (เดินไม่ได้) | `penguin@custom@v1` / `penguin_carry_m` | `penguin@custom@v1` / `penguin_carry_f` | 9816 |
| `carry6` | [ถ่ายรูป] Hug | `penguin@custom@v2` / `penguin_duo2_m` | `penguin@custom@v2` / `penguin_duo2_f` | 9816 |
| `carry7` | [ถ่ายรูป] มายฮันนี่ | `penguin@custom@v2` / `penguin_duo1_m` | `penguin@custom@v2` / `penguin_duo1_f` | 9816 |
| `carry8` | [ถ่ายรูป] Kiss | `penguin@custom@v2` / `penguin_pose_couple_m_v2` | `penguin@custom@v2` / `penguin_pose_couple_f_v2` | 9816 |
| `hostage` | (ไม่อยู่ในเมนู) | `anim@gangops@hostage@` / `perp_idle` | `anim@gangops@hostage@` / `victim_idle` | 0 |

{% hint style="warning" %}
ท่า `penguin@custom@v1` และ `penguin@custom@v2` เป็น anim คัสตอม ต้องมี resource ที่ stream anim ชุดนี้อยู่ในเซิร์ฟ ไม่งั้นท่า carry4–carry8 จะไม่เล่น
{% endhint %}

{% hint style="warning" %}
ท่า `hostage` มี anim, option และ Discord log ครบ แต่ไม่อยู่ใน `Config.Hold_Type_List` ผู้เล่นจึงเลือกท่านี้จากเมนูไม่ได้ ถ้าต้องการใช้ ให้เพิ่ม `{ name = "hostage", label = "จับตัวประกัน" }` ลงในรายการ
{% endhint %}

## ขั้นตอนการอุ้ม

1. คนอุ้มเลือกท่า แล้วฝั่ง client เช็คว่าไม่ได้อยู่บนรถ ไม่อยู่ในโซนห้าม มีผู้เล่นในระยะ 3 เมตร สถานะเป็น/ตายตรงกับท่า และถืออาวุธที่กำหนด (ถ้าท่านั้นบังคับ)
2. Server ตัดสินว่าต้องรอยอมรับหรือไม่ ถ้าต้องรอ จะส่งเมนูยืนยันให้อีกฝ่ายและแจ้งคนอุ้มว่า "ส่งคำขอไปยังผู้เล่น [ID]"
3. เมื่ออีกฝ่ายยอมรับ หรือไม่ต้องรอ server จะส่ง action ไปทั้งสองฝั่ง
4. ทั้งสองฝั่งรอดีเลย์สุ่ม 100–500 ms แล้วเล่น anim และคนถูกอุ้มถูก attach เข้ากับคนอุ้ม
5. ระหว่างอุ้ม ระบบเช็คทุก 2 วินาทีว่ายังอยู่ห่างกันไม่เกิน 10 เมตร

## การยืนยัน (Invite)

- คนถูกอุ้มเห็นเมนูกลางจอ "ยอมรับ [ID]" / "ยกเลิก" และเมนูปิดเองเมื่อครบ 10 วินาที
- ถ้าคนถูกอุ้มตายอยู่ ระบบกดยอมรับให้อัตโนมัติ
- ท่า `carry` (อุ้มศพ) และอาชีพ police / ambulance / gouvernment ข้ามขั้นตอนนี้

## สิทธิ์ตามอาชีพ

| อาชีพ | ข้ามการยืนยัน | อุ้มคนตายได้ | คนถูกอุ้มยกเลิกเองได้ |
| --- | --- | --- | --- |
| police | ได้ | ได้ | ไม่ได้ |
| ambulance | ได้ | ได้ | ไม่ได้ |
| gouvernment | ได้ | ได้ | ไม่ได้ |
| อาชีพอื่น | ไม่ได้ | ได้ (ค่ากลาง) | ได้ (ค่ากลาง) |

## ข้อจำกัดระหว่างอุ้ม

- คนถูกอุ้มถูกปิดปุ่ม 7 ปุ่ม ได้แก่ keycode 24, 25, 140, 21, 22, 38 และ 73 (ยิง เล็ง ตี วิ่ง กระโดด E และ X)
- อุ้มไม่ได้ถ้าคนอุ้มหรือคนถูกอุ้ม (ที่ยังไม่ตาย) อยู่บนรถ
- การอุ้มถูกยกเลิกเองเมื่อฝ่ายใดฝ่ายหนึ่งตาย เกิดใหม่ ขึ้นรถ ออกจากเกม หรืออยู่ห่างกันเกิน 10 เมตร

## อุ้มศพด้วยปุ่ม G

- กด G ใกล้ศพเพื่ออุ้มทันทีโดยไม่ต้องเปิดเมนู และกด G อีกครั้งเพื่อวาง
- ศพจะถูกย้ายมาที่ตำแหน่งคนอุ้มทุก 100 ms ถ้าหลุดจากการ attach
- ถ้าอีกฝ่ายยังไม่ตาย จะขึ้นแจ้งเตือน "ผู้เล่นยังไม่ตาย ไม่สามารถอุ้มศพได้"

## จับตัวประกัน (hostage)

| ค่า | ตอนนี้ | ความหมาย |
| --- | --- | --- |
| `UseWeapon` | false | บังคับให้ถืออาวุธในรายการ |
| `UseWeaponList` | dagger, bottle, knife, machete, switchblade | อาวุธ 5 ชนิดที่ใช้จับได้ |
| `Kill` | false | ให้กด E ฆ่าตัวประกันได้ |
| `Kill_Alert` | กด [E] ฆ่าตัวประกัน | ข้อความ 3D เหนือหัวคนจับ |
| `Target_IsCancel` | false | ตัวประกันยกเลิกเองไม่ได้ |

ตอนปล่อยตัวประกัน ทั้งสองฝั่งจะเล่นท่าผลักกัน (`reaction@shove`) นาน 1 วินาทีก่อนแยกจากกัน

## โซนห้ามอุ้ม

ตั้งได้หลายจุดใน `Config.Zone_BlackList` ตอนนี้มี 2 จุด เปิดใช้ 1 จุด

| จุด | เปิดใช้ | รัศมี | อาชีพที่ยกเว้น |
| --- | --- | --- | --- |
| 5.88, -1127.95, 28.37 | ไม่ | 50 เมตร | police |
| -1028.93, -2982.6, 13.94 | ใช่ | 200 เมตร | ไม่มี |

## Discord log

มี webhook 5 ช่อง คือ `carry`, `carry1`, `carry2`, `carry3` และ `hostage` ซึ่งตอนนี้ปิดอยู่ทั้งหมด log แสดง identifier และชื่อของทั้งสองฝั่ง ส่วน hostage จะแสดงอาวุธด้วย และส่ง log แยกตอนฆ่าตัวประกันเป็นสีแดง
ท่า carry4–carry8 ไม่มีช่อง log
