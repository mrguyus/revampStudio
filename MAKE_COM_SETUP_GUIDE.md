# Make.com Setup Guide — ส่งใบเสนอราคาผ่าน Gmail

## ข้อมูลที่เว็บส่งมาให้ Make.com

Webhook จะส่ง JSON payload มาพร้อมข้อมูลครบดังนี้:

```json
{
  "quote_number": "QT-20260218-472",
  "date": "18/02/2026",
  "customer_name": "สมชาย ใจดี",
  "company": "บริษัท ABC จำกัด",
  "phone": "086-123-4567",
  "customer_email": "somchai@example.com",
  "owner_email": "hello@revamp.studio",
  "website_url": "https://example.com",
  "budget": "20,000 - 50,000 บาท",
  "message": "ต้องการระบบ ตะกร้าสินค้า",
  
  "items": [
    {"name": "Custom Design (18,000)", "price": 18000, "price_str": "฿18,000"},
    {"name": "1-5 หน้า (รวมแล้ว)", "price": 0, "price_str": "รวมแล้ว"}
  ],
  
  "items_html": "<tr>...</tr><tr>...</tr>",  // ← HTML table rows สำเร็จรูป
  "items_text": "• Custom Design — ฿18,000\n• 1-5 หน้า — รวมแล้ว",
  
  "total": 18000,
  "total_str": "฿18,000"
}
```

---

## ขั้นตอนตั้งค่า Make.com (2 modules เท่านั้น!)

### Step 1 — สร้าง Webhook

1. เปิด Make.com → **Create a new scenario**
2. กด **+** → ค้นหา **"Webhooks"** → เลือก **"Custom webhook"**
3. กด **Add** → ตั้งชื่อ `revamp-quotation` → **Save**
4. **Copy Webhook URL** ที่ได้
5. กด **"Run once"** ค้างไว้
6. ไปที่เว็บ → กรอกฟอร์มทดสอบ 1 ครั้ง → Make.com จะจับ data structure

---

### Step 2 — ตั้งค่า Gmail App Password

> ต้องทำครั้งเดียวก่อนเชื่อม Gmail

1. ไป **myaccount.google.com** → **Security**
2. เปิด **2-Step Verification** (ถ้ายังไม่ได้เปิด)
3. กลับมาที่ Security → ค้นหา **"App passwords"**
4. App: **Mail** / Device: **Other** → พิมพ์ `Make.com` → **Generate**
5. **Copy รหัส 16 ตัวอักษร** ไว้ (เห็นครั้งเดียว)

---

### Step 3 — เพิ่ม Gmail Module

1. กด **+** ต่อจาก Webhook
2. ค้นหา **"Gmail"** → เลือก **"Send an Email"**
3. กด **Add** → **Create a connection**
   - Email address: อีเมล Gmail ของคุณ
   - Password: App Password 16 ตัวที่ copy ไว้
4. กด **Save**

---

### Step 4 — ตั้งค่า Gmail Module (สำคัญ!)

**To:**
```
{{12.customer_email}}
```

**CC:**
```
{{12.owner_email}}
```

**Subject:**
```
[ใบเสนอราคา] {{12.quote_number}} — Revamp Studio
```

**Content Type:** เลือก **HTML**

**Content (HTML Body):** Copy ทั้งหมดนี้ลงไป

```html
<!DOCTYPE html>
<html>
<head><meta charset="UTF-8"></head>
<body style="margin:0;padding:0;background:#f5f2eb;font-family:Arial,sans-serif">
<table width="100%" cellpadding="0" cellspacing="0" style="background:#f5f2eb;padding:32px 0">
<tr><td align="center">
<table width="600" cellpadding="0" cellspacing="0" style="background:#ffffff;border:1px solid #d4d0c8">

  <!-- HEADER -->
  <tr>
    <td style="background:#0a0a0f;padding:24px 32px;border-bottom:3px solid #e63c2f">
      <span style="font-size:22px;font-weight:900;color:#ffffff;letter-spacing:-0.5px">
        REVAMP<span style="color:#e63c2f">.</span>STUDIO
      </span>
      <span style="display:block;font-size:11px;color:#888;margin-top:4px;letter-spacing:2px">
        WEB DESIGN & REVAMP SERVICE
      </span>
    </td>
  </tr>

  <!-- TITLE BAR -->
  <tr>
    <td style="background:#e63c2f;padding:12px 32px">
      <span style="font-size:13px;font-weight:700;color:#fff;letter-spacing:1px">
        📄 ใบเสนอราคา / QUOTATION
      </span>
    </td>
  </tr>

  <!-- BODY -->
  <tr>
    <td style="padding:28px 32px">

      <!-- Greeting -->
      <p style="font-size:15px;color:#0a0a0f;margin:0 0 16px">
        สวัสดีคุณ <strong>{{12.customer_name}}</strong>,
      </p>
      <p style="font-size:14px;color:#555;margin:0 0 24px;line-height:1.7">
        ขอบคุณที่สนใจบริการของเราค่ะ 🙏<br>
        ด้านล่างคือสรุปรายการที่คุณเลือกไว้
      </p>

      <!-- Quote Meta -->
      <table width="100%" cellpadding="0" cellspacing="0"
             style="background:#f5f2eb;border-left:3px solid #e63c2f;margin-bottom:24px">
        <tr>
          <td style="padding:12px 16px;font-size:12px;color:#666">
            เลขที่ใบเสนอราคา:
            <strong style="color:#0a0a0f">{{12.quote_number}}</strong>
            &nbsp;&nbsp;|&nbsp;&nbsp;
            วันที่: <strong style="color:#0a0a0f">{{12.date}}</strong>
          </td>
        </tr>
      </table>

      <!-- Items Table -->
      <table width="100%" cellpadding="0" cellspacing="0"
             style="border:1px solid #d4d0c8;margin-bottom:4px">
        
        <!-- Table Header -->
        <tr style="background:#0a0a0f">
          <td style="padding:10px 14px;font-size:12px;font-weight:700;
                     color:#fff;letter-spacing:1px">
            รายการ / ITEM
          </td>
          <td style="padding:10px 14px;font-size:12px;font-weight:700;
                     color:#fff;letter-spacing:1px;text-align:right;width:140px">
            ราคา / PRICE
          </td>
        </tr>

        <!-- ═══ ITEMS LOOP ═══ -->
        <!-- วาง items_html ตรงนี้ -->
        {{12.items_html}}

        <!-- Total Row -->
        <tr style="background:#e63c2f">
          <td style="padding:13px 14px;font-size:14px;font-weight:700;color:#fff">
            รวมทั้งหมด (โดยประมาณ)
          </td>
          <td style="padding:13px 14px;font-size:16px;font-weight:900;
                     color:#fff;text-align:right">
            {{12.total_str}}
          </td>
        </tr>
      </table>

      <!-- Note -->
      <p style="font-size:11px;color:#999;margin:8px 0 24px;font-style:italic">
        * ราคาข้างต้นเป็นราคาประมาณการ ราคาจริงขึ้นอยู่กับ scope งานหลังพูดคุยรายละเอียด
        ใบเสนอราคานี้มีอายุ 30 วัน
        * มัดจำ 30% ก่อนเริ่มงาน 60% หลังงานเสร็จ
      </p>

      <!-- Customer Info Box -->
      <table width="100%" cellpadding="0" cellspacing="0"
             style="background:#f9f8f5;border:1px solid #d4d0c8;margin-bottom:24px">
        <tr>
          <td style="padding:14px 16px">
            <span style="font-size:11px;font-weight:700;color:#888;
                         letter-spacing:1.5px;text-transform:uppercase;display:block;
                         margin-bottom:8px">
              ข้อมูลผู้ติดต่อ
            </span>
            <span style="font-size:13px;color:#333;display:block">
              👤 {{12.customer_name}}
              {{#if 12.company}}
                {{#ifne 12.company "-"}}
                  &nbsp;|&nbsp; 🏢 {{12.company}}
                {{/ifne}}
              {{/if}}
            </span>
            <span style="font-size:13px;color:#333;display:block;margin-top:4px">
              📞 {{12.phone}}
              &nbsp;&nbsp;✉️ {{12.customer_email}}
            </span>
            {{#if 12.website_url}}
              {{#ifne 12.website_url "-"}}
                <span style="font-size:12px;color:#555;display:block;margin-top:4px">
                  🌐 {{12.website_url}}
                </span>
              {{/ifne}}
            {{/if}}
            {{#if 12.budget}}
              {{#ifne 12.budget "-"}}
                <span style="font-size:12px;color:#888;display:block;margin-top:4px">
                  💰 งบประมาณ: {{12.budget}}
                </span>
              {{/ifne}}
            {{/if}}
            {{#if 12.message}}
              {{#ifne 12.message "-"}}
                <span style="font-size:12px;color:#555;display:block;margin-top:8px;
                             padding-top:8px;border-top:1px dashed #d4d0c8">
                  💬 {{12.message}}
                </span>
              {{/ifne}}
            {{/if}}
          </td>
        </tr>
      </table>

      <!-- CTA -->
      <p style="font-size:14px;color:#333;line-height:1.7;margin:0 0 24px">
        ทีมงานจะติดต่อกลับภายใน <strong>24 ชั่วโมง</strong> ค่ะ<br>
        หรือสามารถติดต่อโดยตรงได้ที่ช่องทางด้านล่าง
      </p>

    </td>
  </tr>

  <!-- FOOTER -->
  <tr>
    <td style="background:#0a0a0f;padding:20px 32px;border-top:3px solid #e63c2f">
      <span style="font-size:13px;font-weight:700;color:#fff;display:block;
                   margin-bottom:6px">
        REVAMP<span style="color:#e63c2f">.</span>STUDIO
      </span>
      <span style="font-size:11px;color:#888;display:block;line-height:1.8">
        📞 094-117-7741 &nbsp;|&nbsp;
        ✉️ mr.guyus@gmail.com &nbsp;|&nbsp;
        💬 @842zfdlr (Line)
      </span>
    </td>
  </tr>

</table>
</td></tr>
</table>
</body>
</html>
```

---

### ⚠️ หมายเหตุสำคัญ Make.com

**สำหรับ items_html:**
Make.com จะแสดง field `items_html` เป็น string ปกติ — ใช้ `{{{12.items_html}}}` (3 curly braces) เพื่อให้ render HTML ได้ ไม่ escape

**สำหรับ conditional (if company ไม่ใช่ "-"):**
Make.com ใช้ Handlebars helpers:
- `{{#if variable}}` = if exists
- `{{#ifne variable "-"}}` = if not equal to "-"

---

### Step 5 — เปิดใช้งาน

1. กด **Save** 💾
2. Toggle **Scheduling** → **"Immediately as data arrives"**
3. เปิด switch เป็น **ON** 🟢

---

## Scenario สุดท้าย (แค่ 2 modules!)

```
[Webhook: Custom webhook]
         ↓
         รับ JSON พร้อม items_html
         ↓
[Gmail: Send an Email]
         ↓
         ส่งอีเมลพร้อมตารางรายการ
         → To: customer_email
         → CC: owner_email
```

---

## ทดสอบ

1. กลับไปที่เว็บ → กรอกฟอร์มอีกครั้ง → Submit
2. ตรวจสอบอีเมลที่ `customer_email` และ `owner_email`
3. ควรเห็นตารางรายการครบพร้อมราคา ✅

---

## Troubleshooting

**ถ้า items_html แสดงเป็น text:**
- ใช้ `{{{12.items_html}}}` (3 curly braces) แทน `{{12.items_html}}`

**ถ้า Gmail ส่งไม่ผ่าน:**
- ตรวจสอบ App Password ถูกต้องหรือไม่
- ลองใช้ Brevo SMTP แทน Gmail (ฟรี 300 อีเมล/วัน)

**ถ้า webhook timeout:**
- Make.com free plan limit 1,000 ops/เดือน — ตรวจสอบ quota

---

เสร็จแล้วค่ะ! 🎉
