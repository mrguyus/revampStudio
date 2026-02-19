# วิธีตั้งค่า Webhook URL (Hardcode)

## ขั้นตอนการตั้งค่า

### 1. สร้าง Webhook ใน Make.com

1. ไป Make.com → สร้าง Scenario
2. เพิ่ม **Webhooks → Custom webhook**
3. Copy Webhook URL ที่ได้ เช่น:
   ```
   https://hook.eu1.make.com/abc123xyz789
   ```

---

### 2. แก้ไข index.html

เปิดไฟล์ `index.html` → ค้นหาบรรทัดนี้ (ประมาณบรรทัด 605-607):

```javascript
/* ════ CONFIG ════ */
var OWNER_EMAIL  = 'mr.guyus@gmail.com';  // อีเมลเจ้าของ (CC)
var WEBHOOK_URL  = 'YOUR_MAKE_COM_WEBHOOK_URL_HERE';  // ← ใส่ Make.com Webhook URL ตรงนี้
```

**แก้เป็น:**

```javascript
/* ════ CONFIG ════ */
var OWNER_EMAIL  = 'mr.guyus@gmail.com';
var WEBHOOK_URL  = 'https://hook.eu1.make.com/abc123xyz789';  // ← ใส่ URL จริง
```

---

### 3. Commit & Push

```bash
git add index.html
git commit -m "Add webhook URL"
git push
```

Cloudflare Pages จะ auto-deploy ใน 1-2 นาที

---

## ข้อดี

✅ ไม่มีกล่อง "ตั้งค่า Webhook" ในหน้าเว็บ — UI สะอาด  
✅ ลูกค้าใช้งานได้เลย ไม่ต้องตั้งค่าอะไร  
✅ ปลอดภัย — URL อยู่ใน code เท่านั้น

---

## ถ้าต้องการเปลี่ยน Webhook URL

1. แก้ `WEBHOOK_URL` ในไฟล์ `index.html`
2. Commit → Push → Cloudflare auto-deploy

เสร็จใน 2 นาที!

---

## Troubleshooting

**ถ้าส่งไม่ผ่าน:**
- ตรวจสอบ URL ถูกต้องหรือไม่
- ใช้ Make.com Webhook tester
- เปิด Console ดู error (F12 → Console tab)

**ถ้าแก้ code แล้ว Cloudflare ไม่อัปเดต:**
- รอ 2-3 นาที
- Cloudflare Pages → Deployments → ดู deployment ล่าสุด
- Clear browser cache (Ctrl+F5)
