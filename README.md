# 🗺️ Project Atlas — نقشه راه مستندات

> نسخه: 3.0 | تاریخ: ۲۰۲۶/۱۰/۲۸ | وضعیت: بازسازی‌شده و ممیزی‌شده

---

## درباره Atlas

Atlas یک **Native Android Graphical Shell / UX Layer** برای اکوسیستم Termux/Linux است.
قدرت command line را بدون مجبور کردن کاربر به تعامل مستقیم با syntax فرمان‌ها، در قالب تجربه‌ای بصری و روان ارائه می‌کند.

**توسعه‌دهنده اصلی:** مهدی
**بازبین AI:** پری

---

## ساختار مستندات

```
Project Atlas/
│
├── 📂 canonical/    ← مرجع معتبر فعلی (13 سند)
├── 📂 archive/      ← اسناد منسوخ و بایگانی‌شده (v01)
├── README.md        ← همین فایل
├── 00_Structure_Overview.md
└── FINAL_REPORT.md
```

> **توضیح:** پوشه `canonical/` شامل **مرجع معتبر فعلی** برای هر موضوع است.
> این به معنای نهایی بودن تمام تصمیمات نیست — هر سند وضعیت بلوغ مستقل خود را دارد.

---

## 📂 canonical — اسناد مرجع

### جدول وضعیت کامل

| # | فایل | موضوع | **نقش** | **بلوغ تصمیم** |
|---|------|--------|----------|-----------------|
| 01 | `01_Core_Philosophy.md` | فلسفه بنیادی و هویت Atlas | 📘 Source of Truth | Draft |
| 02 | `02_Capability_Model.md` | مدل Capability و پایه Feature admission | 📘 مدل پایه | Decision Candidate |
| 03 | `03_Action_Architecture.md` | معماری Action-centered | 📘 معماری | Decision Candidate |
| 04 | `04_Termux_Integration.md` | مرز فنی یکپارچه‌سازی با Termux | 📘 قرارداد فنی | **Validation Required** |
| 05 | `05_Project_Workspace.md` | مدل Project و Workspace | 📘 مدل معنایی | Decision Candidate |
| 06 | `06_Lite_Blueprint.md` | بلوپرینت Atlas Lite | 📘 Blueprint محصول | Decision Candidate |
| 07 | `07_UI_Component_Blueprint.md` | معماری UI و Interaction | 📘 قرارداد UX | Contract Candidate |
| 08 | `08_Design_System.md` | سیستم طراحی بصری | 🎨 Design Tokens | **Draft / Validation Required** |
| 09 | `09_Validation_Gates.md` | گیت‌های اعتبارسنجی Prototype | 🚦 قرارداد اعتبارسنجی | Active Contract |
| 10 | `10_Technical_Implementation.md` | قرارداد پیاده‌سازی فنی | 🔧 قرارداد فنی | Active — دارای تصمیمات OPEN |
| 11 | `11_Roadmap_Sprints.md` | نقشه راه و Sprint breakdown | 🗺️ مسیر اجرا | Execution Roadmap |
| 12 | `12_Atlas_Pro_Vision.md` | چشم‌انداز Atlas Pro | 🔮 Future Vision | خارج از scope Lite |
| 13 | `13_Changelog.md` | تاریخچه تغییرات | 📝 Record | — |

### راهنمای نقش‌ها

| نماد | نقش | توضیح |
|------|------|--------|
| 📘 | Source of Truth / مدل پایه | مرجع اصلی که سایر اسناد به آن وابسته‌اند |
| 🎨 | Design Tokens | مقادیر بصری — تا Validation قطعی نیستند |
| 🚦 | Validation Contract | تعیین می‌کند چه چیزی قبل از کد باید آزمایش شود |
| 🔧 | Technical Contract | قرارداد فنی پیاده‌سازی |
| 🗺️ | Execution Roadmap | ترتیب و زمان‌بندی اجرا |
| 🔮 | Future Vision | چشم‌انداز آینده — در scope فعلی اجرا نمی‌شود |
| 📝 | Record | سابقه تاریخی تغییرات |

### راهنمای بلوغ تصمیم

| وضعیت | معنی |
|--------|------|
| **Draft** | در حال نگارش — قابل تغییر |
| **Decision Candidate** | تصمیم پیشنهادی — نیاز به تصویب |
| **Validation Required** | نیاز به آزمایش روی دستگاه واقعی |
| **Contract Candidate** | قرارداد پیشنهادی — نیاز به بازبینی |
| **Active Contract** | قرارداد فعال — دارای تصمیمات باز مشخص |
| **Execution Roadmap** | نقشه اجرایی — قابل اجرا پس از عبور از Validation Gates |

---

## 🔗 زنجیره وابستگی اسناد

```
01_Core_Philosophy (هویت — Source of Truth)
    ↓
02_Capability_Model (توانمندی‌ها — مدل پایه)
    ↓
03_Action_Architecture (معماری عملیات)
    ↓
04_Termux_Integration (مرز اجرا — Validation Required)
    ↓
05_Project_Workspace (مدل پروژه)
    ↓
06_Lite_Blueprint (محصول Lite)
07_UI_Component_Blueprint (قرارداد UX)
08_Design_System (توکن‌های طراحی — Draft)
    ↓
09_Validation_Gates (قرارداد اعتبارسنجی)
10_Technical_Implementation (قرارداد پیاده‌سازی)
    ↓
11_Roadmap_Sprints (نقشه راه اجرا)

12_Atlas_Pro_Vision (آینده — خارج از scope فعلی)
13_Changelog (سابقه تاریخی)
```

---

## 📖 ترتیب خواندن پیشنهادی

### برای درک هویت پروژه:
`01` → `02` → `12`

### برای شروع توسعه (مسیر کامل):
```
01 (فلسفه)
  → 02 (Capability)
    → 03 (Action)
      → 04 (Termux — Validation Required ⚠️)
        → 05 (Workspace)
          → 06 (Lite Blueprint)
            → 07 (UX Contract)
              → 08 (Design System — Draft ⚠️)
                → 09 (Validation Gates 🚦)
                  → 10 (Implementation Contract 🔧)
                    → 11 (Roadmap/Sprints 🗺️)
```

> ⚠️ **توجه مهم:** مسیر توسعه از Validation Gates (`09`) و Implementation Contract (`10`) عبور می‌کند.
> هیچ Sprint‌ای نباید بدون رعایت Gateهای متناظر شروع شود.
> مقادیر Design System (`08`) تا پایان Prototype قطعی نیستند.

### برای پیاده‌سازی UI:
`07` → `08 (Draft)` → `06`

### برای درک معماری کامل:
`01` → `02` → `03` → `04` → `05` → `06` → `07` → `09` → `10`

---

## 📂 archive — اسناد منسوخ (نسل v01)

اسناد نسل اولیه که توسط اسناد canonical جایگزین شده‌اند.
این اسناد مرجع تاریخی هستند و نباید به‌عنوان منبع حقیقت برای پیاده‌سازی استفاده شوند.

| فایل | دلیل بایگانی | جایگزین در canonical |
|------|----------------|---------------------|
| `v01_00_Project_Vision.md` | refining شده در `01` | 01 |
| `v01_01_Blueprint_Lite.md` | جایگزین شده با `06` | 06 |
| `v01_03_UI_Design_Guide.md` | ادغام شده در `08` | 08 |
| `v01_04_Component_Library.md` | ادغام شده در `08` | 08 |
| `v01_05_Roadmap.md` | جایگزین شده با `11` | 11 |
| `v01_07_TODO.md` | جایگزین شده با `11` | 11 |
| `v01_08_Architecture.md` | توزیع شده در `01`-`05` | 01-05 |
| `v01_09_API_Integration.md` | جایگزین شده با `04` | 04 |
| `v01_11_Core_Philosophy_Audit.md` | سابقه ممیزی | — |
| `v01_20_Documentation_Audit.md` | سابقه بازسازی | — |

---

## ⚠️ یادداشت‌های مهم

1. **Canonical ≠ نهایی**: اسناد canonical «مرجع معتبر فعلی» هستند، نه لزوماً تصمیمات تصویب‌شده. هر سند وضعیت بلوغ خود را دارد.
2. **Validation Gates**: تا وقتی `04_Termux_Integration.md` (Validation Required) و `08_Design_System.md` (Draft) تأیید نشده‌اند، پیاده‌سازی نباید مقادیر را قطعی تلقی کند.
3. **Pro Vision خارج از scope**: `12_Atlas_Pro_Vision.md` چشم‌انداز آینده است و در Sprintهای Lite فعلی اجرا نمی‌شود.
4. **Design Tokens**: `08_Design_System.md` مقادیر پیشنهادی دارد که پس از Prototype باید روی دستگاه واقعی تأیید شوند (طبق Gate در `09`).
5. **Archive**: اسناد archive با پیشوند `v01_` نشان‌دهنده نسخه اولیه هستند. برای کار فعال، فقط از `canonical/` استفاده کنید.

---

*پایان نقشه راه مستندات — نسخه 3.0*
