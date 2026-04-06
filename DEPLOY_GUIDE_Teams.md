# 🚀 SMILE UAT — คู่มือ Deploy บน Microsoft Teams

## ไฟล์ที่เตรียมไว้ใน SMILE_UAT_TeamsApp.zip

| ไฟล์ | รายละเอียด |
|------|-----------|
| `manifest.json` | ไฟล์กำหนดค่า Teams App หลัก |
| `icon-color.png` | App icon สี 192×192 px |
| `icon-outline.png` | App icon ขาวดำ 32×32 px |
| `tc_progress_overdue.html` | Tab 1 — TC Progress Overdue Monitor (+ Teams SDK) |
| `uat_issue_monitor.html` | Tab 2 — UAT Issue Monitor (+ Teams SDK) |

---

## ขั้นตอนที่ 1 — Host HTML Files บน Web Server

> **สำคัญ:** Teams บังคับใช้ **HTTPS เท่านั้น**

### ตัวเลือก A: GitHub Pages (ฟรี, ง่ายที่สุด)
1. สร้าง Repository ใหม่บน GitHub (ตั้งเป็น Public หรือ Private + Pages)
2. Upload `tc_progress_overdue.html` และ `uat_issue_monitor.html` เข้าไป
3. ไปที่ Settings → Pages → Source: `main branch`
4. GitHub จะให้ URL เช่น `https://username.github.io/repo-name/`

### ตัวเลือก B: SharePoint (แนะนำสำหรับองค์กร)
1. ไปที่ SharePoint site ขององค์กร
2. อัพโหลดไฟล์ HTML ทั้ง 2 ไปที่ Document Library
3. คลิก 3 จุดที่ไฟล์ → Share → Copy Link (ใช้ลิงก์นี้เป็น contentUrl)

### ตัวเลือก C: Azure Static Web Apps (ฟรี tier)
1. ไปที่ portal.azure.com → สร้าง Static Web App ใหม่
2. เชื่อมกับ GitHub repo หรือ upload ไฟล์โดยตรง
3. Azure จะให้ URL อัตโนมัติ เช่น `https://app-name.azurestaticapps.net`

---

## ขั้นตอนที่ 2 — แก้ไข manifest.json

หลังจากได้ URL ของ Web Server แล้ว ให้เปิด `manifest.json` และแทนที่ **ทุก** `YOUR_HOST_URL` ด้วย URL จริง:

**ตัวอย่าง:** (สมมติ URL คือ `https://username.github.io/smile-uat`)
```json
"contentUrl": "https://username.github.io/smile-uat/tc_progress_overdue.html",
"websiteUrl": "https://username.github.io/smile-uat/tc_progress_overdue.html",
...
"validDomains": ["username.github.io"]
```

จากนั้น zip ไฟล์ทั้ง 5 ใหม่อีกครั้ง (หรือใช้ Teams Developer Portal)

---

## ขั้นตอนที่ 3 — Upload ขึ้น Microsoft Teams

### วิธีที่ 1: Sideload (สำหรับทดสอบหรือทีมเล็ก)
> ต้องได้รับอนุญาต "Allow custom app uploads" จาก Teams Admin ก่อน

1. เปิด Microsoft Teams
2. คลิก **Apps** (ไอคอนซ้ายมือล่าง)
3. คลิก **Manage your apps** → **Upload an app**
4. เลือก **Upload a custom app**
5. เลือกไฟล์ `SMILE_UAT_TeamsApp.zip`
6. คลิก **Add** → App จะปรากฏใน Apps ของคุณ

### วิธีที่ 2: Teams Admin Center (Deploy ทั้งองค์กร)
> สำหรับ Teams Admin เท่านั้น

1. ไปที่ https://admin.teams.microsoft.com
2. **Teams apps** → **Manage apps**
3. คลิก **Upload new app**
4. Upload `SMILE_UAT_TeamsApp.zip`
5. ตั้งค่า Permission Policy เพื่อกำหนดว่าใครใช้ได้บ้าง

---

## ขั้นตอนที่ 4 — เพิ่ม Tab เข้า Channel

1. ไปที่ Channel ที่ต้องการ
2. คลิก **+** (เพิ่ม Tab) ที่แถบ Tabs
3. ค้นหา "SMILE UAT" → เลือก App
4. เลือก Tab ที่ต้องการ (TC Progress หรือ Issue Monitor)
5. คลิก **Save**

---

## โครงสร้าง App ใน Teams

```
SMILE UAT App
├── 📋 TC Progress     → tc_progress_overdue.html
│   - ติดตาม Test Case ที่เลยกำหนด Start Date
│   - Upload Excel → วิเคราะห์ทันที
│
└── 🐛 Issue Monitor   → uat_issue_monitor.html
    - ติดตาม UAT Issues & Processing Time
    - Chart วิเคราะห์ + Export PDF
```

---

## หมายเหตุสำคัญ

- **ไฟล์ HTML ทั้งคู่รองรับทั้ง Dark Mode และ Light Mode** ของ Teams อัตโนมัติ
- **ข้อมูลไม่ถูกส่งออก** — ทุกอย่างประมวลผลใน Browser (ไม่มี backend)
- **Microsoft Teams SDK v2.22.0** ถูก inject เรียบร้อยแล้วในทั้ง 2 ไฟล์
- หากต้องการ update ไฟล์ HTML ให้ upload ไฟล์ใหม่ขึ้น Web Server โดยไม่ต้อง re-deploy Teams App

---

*Prepared: April 2026 | SMILE UAT Team*
