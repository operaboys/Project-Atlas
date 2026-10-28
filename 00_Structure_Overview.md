# 🏗️ ساختار نهایی Project Atlas

> تاریخ بازسازی: ۲۰۲۶/۱۰/۲۸
> آخرین به‌روزرسانی: ۲۰۲۶/۱۰/۲۹
> وضعیت: ✅ **کامل — ممیزی‌شده — راستی‌آزمایی‌شده**

---

## نمای کلی

```
Project Atlas/
│
├── README.md                              ← نقشه راه مستندات (v3.0)
├── 00_Structure_Overview.md               ← همین فایل
│
├── 📂 canonical/                          ← 13 سند مرجع ✅
│   ├── 01_Core_Philosophy.md              ← فلسفه بنیادی (Draft)
│   ├── 02_Capability_Model.md             ← مدل Capability (Decision Candidate)
│   ├── 03_Action_Architecture.md          ← معماری Action (Decision Candidate)
│   ├── 04_Termux_Integration.md           ← مرز Termux (Validation Required)
│   ├── 05_Project_Workspace.md            ← مدل Workspace (Decision Candidate)
│   ├── 06_Lite_Blueprint.md              ← بلوپرینت Lite (Decision Candidate)
│   ├── 07_UI_Component_Blueprint.md       ← معماری UX (Contract Candidate)
│   ├── 08_Design_System.md               ← توکن‌های طراحی (Draft / Validation Required)
│   ├── 09_Validation_Gates.md            ← گیت‌های اعتبارسنجی (Active Contract)
│   ├── 10_Technical_Implementation.md     ← قرارداد فنی (Active — تصمیمات OPEN)
│   ├── 11_Roadmap_Sprints.md             ← نقشه راه (Execution Roadmap)
│   ├── 12_Atlas_Pro_Vision.md            ← چشم‌انداز آینده (Future Vision)
│   └── 13_Changelog.md                   ← تاریخچه تغییرات (Record)
│
├── 📂 archive/                            ← 11 فایل بایگانی ✅
│   ├── README.md                          ← توضیح بایگانی
│   ├── v01_00_Project_Vision.md
│   ├── v01_01_Blueprint_Lite.md
│   ├── v01_03_UI_Design_Guide.md
│   ├── v01_04_Component_Library.md
│   ├── v01_05_Roadmap.md
│   ├── v01_07_TODO.md
│   ├── v01_08_Architecture.md
│   ├── v01_09_API_Integration.md
│   ├── v01_11_Core_Philosophy_Audit.md
│   └── v01_20_Documentation_Audit.md
│
└── FINAL_REPORT.md                        ← گزارش نهایی بازسازی
```

---

## آمار (بر اساس ساختار واقعی Dropbox)

| معیار | مقدار |
|--------|--------|
| **فایل‌های ریشه** | ۳ (README, Structure, Final Report) |
| **canonical/** | ۱۳ فایل ✅ |
| **archive/** | ۱۱ فایل (۱۰ سند + ۱ README) ✅ |
| **مجموع کل فایل‌ها** | **۲۷ فایل Markdown** |
| فایل‌های جدید ساخته‌شده | ۳ (README, Design_System, archive/README) |
| وضعیت انتقال مستندات | **۱۰۰٪ تکمیل** ✅ |

### نکته درباره Changelog و Pro Vision

- **`13_Changelog.md`** در `canonical/` باقی مانده است (نه در `records/`)
  - دلیل: سادگی ساختار و جلوگیری از پیچیدگی اضافی
  - README آن را به‌عنوان `📝 Record` طبقه‌بندی کرده است
- **`12_Atlas_Pro_Vision.md`** در `canonical/` باقی مانده است (نه در `future/`)
  - دلیل: ارتباط با معماری کلی و نیاز به مرجع بودن
  - README آن را به‌عنوان `🔮 Future Vision — خارج از scope Lite` طبقه‌بندی کرده است

---

## سوابق اصلاحات

| تاریخ | عمل |
|--------|------|
| ۲۰۲۶/۱۰/۲۸ | بازسازی اولیه — انتقال، بازنام‌گذاری، ایجاد canonical/archive |
| ۲۰۲۶/۱۰/۲۸ | ایجاد `08_Design_System.md` (ترکیب v01_03 + v01_04) |
| ۲۰۲۶/۱۰/۲۸ | ترجمه `v01_20_Documentation_Audit.md` به فارسی/انگلیسی |
| ۲۰۲۶/۱۰/۲۸ | به‌روزرسانی تمام ارجاعات بین‌سندی (هدر + بدنه) |
| ۲۰۲۶/۱۰/۲۸ | ممیزی تطبیقی — اصلاح ناهماهنگی‌های A-01 تا A-05 |
| ۲۰۲۶/۱۰/۲۸ | اصلاح گزارش الحاقی — وضعیت Candidate و Accessibility |
| ۲۰۲۶/۱۰/۲۹ | راستی‌آزمایی Dropbox — اصلاح ارجاع خراب، افزودن جزئیات Design System |

---

---

> **دامنه:** این سند صرفاً وضعیت **سازمان‌دهی و ممیزی مستندات** را گزارش می‌دهد.
> تأیید فنی محصول و آزمون روی دستگاه واقعی نیازمند شواهد اجرایی جداگانه است.

*پایان نمای ساختار*
