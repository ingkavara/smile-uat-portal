# 🚀 SMILE UAT — Deploy บน SharePoint + Microsoft Teams
## คู่มือ Step-by-Step (สำหรับองค์กรที่มี SharePoint)

---

## ทำไมต้อง Host บน SharePoint ก่อน?

> Teams ไม่สามารถเปิดไฟล์ `.html` โดยตรงจากเครื่องได้
> ต้องมี **URL แบบ HTTPS** ที่ทุกคนเข้าถึงได้ก่อน
> SharePoint ขององค์กรเป็นตัวเลือกที่ดีที่สุดเพราะทุกคนมีสิทธิ์อยู่แล้ว

---

## STEP 1 — Upload ไฟล์ HTML ขึ้น SharePoint

### 1.1 ไปที่ SharePoint Site ขององค์กร
เปิด Browser → ไปที่:
```
https://[ชื่อองค์กร].sharepoint.com/sites/[ชื่อ Site]
```
*ตัวอย่าง: `https://mycompany.sharepoint.com/sites/UAT`*

### 1.2 เปิด Document Library ที่เหมาะสม
แนะนำใช้ **SiteAssets** หรือ **Documents** folder

- คลิก **Documents** (หรือ **Site Contents** → **SiteAssets**)

### 1.3 Upload ไฟล์ทั้ง 2
- คลิก **Upload** → **Files**
- เลือกไฟล์ทั้งสอง:
  - `tc_progress_overdue.html`
  - `uat_issue_monitor.html`

### 1.4 คัดลอก URL ของแต่ละไฟล์
1. คลิกขวาที่ไฟล์ → **Copy link** → เลือก **People in your organization**
2. หรือ คลิกที่ไฟล์ → ดูที่ Address Bar ของ Browser

URL ควรมีหน้าตาแบบนี้:
```
https://mycompany.sharepoint.com/sites/UAT/SiteAssets/tc_progress_overdue.html
https://mycompany.sharepoint.com/sites/UAT/SiteAssets/uat_issue_monitor.html
```

---

## STEP 2 — แก้ไข manifest.json

เปิดไฟล์ `manifest.json` (อยู่ใน zip) และแทนที่ URL:

**ก่อน (ตัวอย่าง):**
```json
"contentUrl": "https://YOUR_HOST_URL/tc_progress_overdue.html"
```

**หลัง (ใส่ URL จริงของ SharePoint):**
```json
"contentUrl": "https://mycompany.sharepoint.com/sites/UAT/SiteAssets/tc_progress_overdue.html"
```

### ต้องแก้ไขทั้งหมด 7 จุดใน manifest.json:

| จุดที่ | Field | ไฟล์ที่เกี่ยวข้อง |
|--------|-------|------------------|
| 1 | `developer.websiteUrl` | URL หลักของ SharePoint Site |
| 2 | `developer.privacyUrl` | URL หลักของ SharePoint Site |
| 3 | `developer.termsOfUseUrl` | URL หลักของ SharePoint Site |
| 4 | `staticTabs[0].contentUrl` | tc_progress_overdue.html |
| 5 | `staticTabs[0].websiteUrl` | tc_progress_overdue.html |
| 6 | `staticTabs[1].contentUrl` | uat_issue_monitor.html |
| 7 | `staticTabs[1].websiteUrl` | uat_issue_monitor.html |
| 8 | `validDomains[0]` | `mycompany.sharepoint.com` เท่านั้น (ไม่มี https://) |

### ตัวอย่าง validDomains ที่ถูกต้อง:
```json
"validDomains": ["mycompany.sharepoint.com"]
```

---

## STEP 3 — Re-pack ไฟล์ zip ใหม่

หลังแก้ manifest.json แล้ว ให้ zip ไฟล์เหล่านี้รวมกันใหม่:
```
manifest.json
icon-color.png
icon-outline.png
tc_progress_overdue.html   ← (ไม่ต้องใส่ก็ได้ เพราะ host บน SharePoint แล้ว)
uat_issue_monitor.html     ← (ไม่ต้องใส่ก็ได้ เพราะ host บน SharePoint แล้ว)
```

> **หมายเหตุ:** zip สำหรับ Teams App ต้องมีแค่ `manifest.json` + 2 icon เท่านั้น
> ไฟล์ HTML ให้ใช้ผ่าน URL ของ SharePoint

ตั้งชื่อว่า: `SMILE_UAT_TeamsApp_FINAL.zip`

---

## STEP 4 — Upload ขึ้น Microsoft Teams

### วิธีที่ 1: ทดสอบด้วยตัวเองก่อน (Sideload)
1. เปิด Teams → คลิก **Apps** (แถบซ้าย)
2. คลิก **Manage your apps** (ล่างซ้าย)
3. คลิก **Upload an app** → **Upload a custom app**
4. เลือก `SMILE_UAT_TeamsApp_FINAL.zip`
5. คลิก **Add** → เลือก **Add to a team** หรือ **Add for me**

### วิธีที่ 2: Deploy ให้ทั้งทีม (Teams Admin)
1. ไปที่ https://admin.teams.microsoft.com
2. **Teams apps** → **Manage apps** → **Upload new app**
3. Upload `SMILE_UAT_TeamsApp_FINAL.zip`
4. ตั้ง Permission Policy ให้ทีม UAT

---

## STEP 5 — เพิ่ม Tab ใน Channel ของทีม

1. ไปที่ Channel ของทีม UAT
2. คลิก **+** (เครื่องหมายบวก) ที่แถบ Tabs
3. ค้นหา **"SMILE UAT"**
4. เลือก Tab ที่ต้องการ → คลิก **Save**

---

## วิธีทางเลือก — "Website Tab" (ไม่ต้อง deploy App)

ถ้าไม่ต้องการ deploy Custom App ให้ใช้ **Website Tab** แทน:

1. ไปที่ Channel → คลิก **+**
2. ค้นหา **"Website"** (built-in Teams Tab)
3. ใส่ URL ของไฟล์ใน SharePoint โดยตรง
4. ตั้งชื่อ Tab เช่น "TC Progress" หรือ "Issue Monitor"
5. คลิก **Save**

> **ข้อดี:** ง่าย ไม่ต้อง deploy App
> **ข้อเสีย:** ผู้ใช้ต้อง Login SharePoint ก่อน (Teams จะถาม)

---

## แก้ไขปัญหาที่พบบ่อย

| ปัญหา | สาเหตุ | วิธีแก้ |
|-------|--------|---------|
| `Refused to display in frame` | SharePoint บล็อก iframe | ดู Note ด้านล่าง |
| `App cannot be loaded` | URL ใน manifest ผิด | ตรวจสอบ URL และ validDomains |
| หน้าขาว ไม่โหลด | Teams SDK ไม่ทำงาน | refresh หน้า |
| Login loop | Session หมดอายุ | Login SharePoint ใหม่ |

### Note: SharePoint X-Frame-Options
บางครั้ง SharePoint Admin ตั้งค่าบล็อก iframe
แก้ไขโดย: **SharePoint Admin Center** → Site Settings → HTML Field Security → อนุญาต iframe

---

*Prepared: April 2026 | SMILE UAT Team*
