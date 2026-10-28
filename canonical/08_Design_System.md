# سیستم طراحی Atlas — Design System

> سند: `08_Design_System.md`
> نسخه: **1.2**
> تاریخ: ۲۰۲۶/۱۰/۲۹
> وضعیت: **Design Token Draft / Validation Required**
> ترکیب‌شده از: `archive/v01_03_UI_Design_Guide.md` + `archive/v01_04_Component_Library.md`
> مرجع: `07_UI_Component_Blueprint.md` (معماری تعامل)
> هماهنگ با: `09_Validation_Gates.md`, `10_Technical_Implementation.md`
> 
> ⚠️ **توجه مهم — وضعیت تصمیم‌ها:**
> تمام مقادیر این سند در حال حاضر **Candidate** هستند و تا پایان Validation
> (طبق `09_Validation_Gates.md` و `10_Technical_Implementation.md`) قطعی نیستند.
> 
> ### سیستم وضعیت‌دهی توکن‌ها
> 
> | وضعیت | معنی |
> |--------|------|
> | **Approved** | تصویب‌شده و قابل استفاده به‌عنوان مرجع اجرایی |
> | **Candidate** | پیشنهاد فعلی، اما هنوز نهایی نشده |
> | **Validation Required** | پذیرش نهایی وابسته به آزمون مشخص |
> | **Superseded** | جایگزین‌شده با تصمیم جدید |
> 
> **وضعیت فعلی این سند:** تمام توکن‌ها در وضعیت **Candidate** هستند.
> پس از Prototype و تأیید روی دستگاه واقعی، وضعیت هر توکن جداگانه تعیین می‌شود.

---

## ۱. زبان طراحی

- **Material 3** (Material You)
- گوشه‌های گرد
- سایه‌های نرم
- آیکون‌های مینیمال
- طراحی یکپارچه در تمام صفحات

---

## ۲. پالت رنگی

### رنگ‌های اصلی (Primary)

| Token | مقدار | کاربرد |
|-------|--------|--------|
| Primary | `#6750A4` (بنفش) | دکمه‌ها، لینک‌ها، Indicator |
| On Primary | `#FFFFFF` | متن روی Primary |
| Primary Container | `#EADDFF` | پس‌زمینه highlight |
| On Primary Container | `#21005E` | متن روی Container |

### رنگ‌های ثانویه (Secondary)

| Token | مقدار | کاربرد |
|-------|--------|--------|
| Secondary | `#625B71` | المان‌های کمکی |
| On Secondary | `#FFFFFF` | متن روی Secondary |
| Secondary Container | `#E8DEF8` | پس‌زمینه ثانویه |
| On Secondary Container | `#1D192B` | متن روی Container |

### رنگ‌های سوم (Tertiary)

| Token | مقدار | کاربرد |
|-------|--------|--------|
| Tertiary | `#7D5260` | تأکید خاص |
| On Tertiary | `#FFFFFF` | متن روی Tertiary |
| Tertiary Container | `#FFD8E4` | پس‌زمینه تأکیدی |
| On Tertiary Container | `#31111D` | متن روی Container |

### رنگ خطا (Error)

| Token | مقدار | کاربرد |
|-------|--------|--------|
| Error | `#B3261E` | پیام خطا، دکمه Danger |
| On Error | `#FFFFFF` | متن روی Error |
| Error Container | `#F9DEDC` | پس‌زمینه خطا |
| On Error Container | `#410E0B` | متن روی Container |

### رنگ‌های پس‌زمینه (Background / Surface)

> ⚠️ **وضعیت:** تمام مقادیر تم تیره و OLED در وضعیت **Candidate** هستند.
> پس از Prototype باید روی دستگاه‌های واقعی با تم تیره/OLED فعال آزمایش شوند.

| Token | تم روشن | تم تیره | تم OLED |
|-------|---------|---------|---------|
| Background | `#FFFFFF` | `#121212` | `#000000` |
| Surface | `#FFFFFF` | `#1E1E1E` | `#0A0A0A` |
| On Background | `#1C1B1F` | `#E6E1E5` | `#FFFFFF` |
| On Surface | `#1C1B1F` | `#E6E1E5` | `#FFFFFF` |

### رنگ‌های ترمینال

| نوع حباب | پس‌زمینه | متن | کاربرد |
|----------|---------|------|--------|
| دستور (Command) | `#1E3A1E` (سبز تیره) | `#4CAF50` (سبز) | ورودی کاربر |
| خروجی (Output) | `#2D2D2D` (خاکستری) | `#FFFFFF` (سفید) | خروجی عادی |
| خطا (Error) | `#3E1E1E` (قرمز تیره) | `#F44336` (قرمز) | پیام خطا |
| موفقیت (Success) | `#1E3A1E` (سبز تیره) | `#4CAF50` (سبز) | تأیید عملیات |

---

## ۳. Elevation (سایه و ارتفاع)

### سطوح Elevation

| سطح | ارتفاع | کاربرد |
|-----|--------|--------|
| ۰ | 0dp | پس‌زمینه |
| ۱ | 1dp | کارت‌ها |
| ۲ | 3dp | دکمه‌ها |
| ۳ | 6dp | دیالوگ‌ها |
| ۴ | 8dp | Bottom Sheets |
| ۵ | 12dp | FAB |

### Elevation در تم تیره (رنگ Surface)

> ⚠️ **وضعیت:** مقادیر زیر **Candidate** هستند.
> بر اساس اسناد تاریخی (`archive/v01_03_UI_Design_Guide.md`) استخراج شده‌اند
> و باید روی دستگاه با تم تیره فعال آزمایش شوند.

| سطح | مقدار |
|-----|--------|
| ۰ | `#121212` |
| ۱ | `#1E1E1E` |
| ۲ | `#232323` |
| ۳ | `#282828` |
| ۴ | `#2D2D2D` |
| ۵ | `#333333` |

---

## ۴. سیستم Grid

| مشخصه | مقدار |
|--------|--------|
| پایه Grid | 4dp |
| موبایل | ۴ ستون |
| تبلت | ۸ ستون |
| دسکتاپ | ۱۲ ستون |
| Gutter | 16dp |
| حاشیه موبایل | 16dp |
| حاشیه تبلت | 24dp |
| حاشیه دسکتاپ | 24dp |

### فرمول عرض ستون

```
عرض ستون = (عرض صفحه − (۲ × حاشیه) − ((تعداد ستون − ۱) × Gutter)) ÷ تعداد ستون
```

مثال برای موبایل با عرض 360dp:
```
عرض ستون = (360 − 32 − 48) ÷ 4 = 70dp
```

### Breakpoints (Window Size Classes)

| کلاس | محدوده | لایوت | حاشیه |
|------|---------|--------|--------|
| Compact | < 600dp | تک ستونه (موبایل) | 16dp |
| Medium | 600–840dp | دو ستونه (تبلت کوچک) | 24dp |
| Expanded | > 840dp | سه ستونه (تبلت/دسکتاپ) | 24dp |

---

## ۵. تایپوگرافی

### مقیاس Type (Material 3)

| Style | اندازه | Weight | کاربرد |
|-------|--------|--------|--------|
| Display Large | 57sp | Regular | عناوین بزرگ |
| Display Medium | 45sp | Regular | عناوین |
| Display Small | 36sp | Regular | عناوین کوچک |
| Headline Large | 32sp | Regular | سرفصل صفحه |
| Headline Medium | 28sp | Regular | سرفصل بخش |
| Headline Small | 24sp | Regular | زیرسرفصل |
| Title Large | 22sp | Regular | عنوان کارت |
| Title Medium | 16sp | w500 | عنوان میانی |
| Title Small | 14sp | w500 | عنوان کوچک |
| Body Large | 16sp | Regular | متن اصلی |
| Body Medium | 14sp | Regular | متن عادی |
| Body Small | 12sp | Regular | متن کوچک |
| Label Large | 14sp | w500 | برچسب دکمه |
| Label Medium | 12sp | w500 | برچسب میانی |
| Label Small | 11sp | w500 | برچسب کوچک |

### خانواده فونت‌ها

| نقش | فونت |
|------|------|
| اصلی (UI) | Roboto (سیستم) |
| Monospace (ترمینال) | Fira Code (پیش‌فرض) |
| Display | Roboto Flex |

### فونت‌های جایگزین ترمینال

- Fira Code (پیش‌فرض)
- JetBrains Mono
- Source Code Pro
- Monospace (سیستم)

### اندازه فونت ترمینال

- محدوده: 12sp تا 24sp
- پیش‌فرض: 14sp
- دکمه‌های سریع: کوچک / متوسط / بزرگ

---

## ۶. Border Radius (شعاع گردی)

| کامپوننت | شعاع |
|-----------|------|
| کارت‌ها | 16dp |
| دکمه‌ها | 12dp |
| دیالوگ‌ها | 20dp |
| Bottom Sheets | 28dp |
| FAB | 16dp |
| فیلدهای متنی | 12dp |
| نوار جستجو | 28dp |
| حباب ترمینال | 8dp |

---

## ۷. فاصله‌گذاری (Spacing)

| مقیاس | مقدار |
|--------|--------|
| xs | 4dp |
| sm | 8dp |
| md | 16dp |
| lg | 24dp |
| xl | 32dp |

### فاصله بین کامپوننت‌ها

| مورد | مقدار |
|------|--------|
| بین کارت‌ها | 8dp |
| بین بخش‌ها | 24dp |
| حاشیه صفحه | 16dp (موبایل) / 24dp (تبلت) |
| پدینگ محتوا | 16dp |

---

## ۸. هدف‌های لمسی (Touch Targets)

> ⚠️ **توضیح مهم:** اندازه‌های ظاهری (Visual Size) ممکن است کوچک‌تر از حداقل هدف لمسی (Touch Target) باشند.
> در این حالت، ناحیه قابل لمس (Hit Area) به حداقل 48dp گسترش می‌یابد، حتی اگر المان بصری کوچک‌تر باشد.

| عنصر | اندازه ظاهری | Hit Area | توضیح |
|------|--------------|----------|--------|
| دکمه / آیتم لیست | — | 48dp × 48dp | حداقل هدف لمسی Material Design |
| دکمه آیکون | 40dp × 40dp | 48dp × 48dp | Hit Area 4dp در هر طرف گسترش می‌یابد |
| دکمه متنی | ارتفاع 40dp | ارتفاع 48dp | Hit Area 4dp بالا/پایین گسترش می‌یابد |
| FAB | 56dp × 56dp | 56dp × 56dp | بزرگ‌تر از حداقل، بدون نیاز به گسترش |
| Bottom Navigation | 48dp × 48dp | 48dp × 48dp | منطبق با حداقل |
| فاصله بین عناصر | 8dp | — | فاصله بصری بین المان‌ها |

### دسترسی‌پذیری (Accessibility Requirements)

> ⚠️ **وضعیت:** الزامات زیر در حال حاضر **Candidate** هستند.
> در Sprint مربوط به Accessibility (طبق `09_Validation_Gates.md`) باید به
> معیارهای قابل آزمون تبدیل و روی دستگاه واقعی تأیید شوند.

#### معیارهای کنتراست (Contrast Ratios)

| معیار | حداقل نسبت | روش آزمون | وضعیت |
|--------|-----------|-----------|--------|
| متن عادی (≤ 18sp) | `4.5:1` | ابزار automated contrast checker | Candidate |
| متن بزرگ (> 18sp) | `3:1` | ابزار automated contrast checker | Candidate |
| المان‌های رابط کاربری | `3:1` | ابزار automated contrast checker | Candidate |

#### معیارهای عملکردی (Functional Requirements)

| الزام | معیار پذیرش | روش آزمون | وضعیت |
|--------|-------------|-----------|--------|
| TalkBack | تمام المان‌های تعاملی label مناسب دارند | تست دستی با TalkBack فعال | Candidate |
| Large Text | UI تا 200% scaling بدون overlap کار می‌کند | تست با System Font Size | Candidate |
| Focus Order | ترتیب فوکوس منطقی و قابل پیش‌بینی است | تست با D-pad / Keyboard | Candidate |
| High Contrast Mode | UI در حالت High Contrast سیستم قابل استفاده است | تست با System High Contrast | Candidate |
| Touch Targets | تمام المان‌های تعاملی ≥ 48dp | اندازه‌گیری با Layout Inspector | Candidate |

#### Gate مربوطه

این الزامات باید در **G8 — Android UX / Accessibility Gate** (طبق `09_Validation_Gates.md`) آزمون شوند.

---

## ۹. آیکون‌ها

| مشخصه | مقدار |
|--------|--------|
| کتابخانه | Material Symbols Rounded |
| اندازه پیش‌فرض | 24dp |
| اندازه کوچک | 18dp |
| اندازه بزرگ | 32dp |
| **قانون رنگ** | **رنگ آیکون از رنگ متن (On Surface / On Background) پیروی می‌کند** |

### کاربرد آیکون‌ها

| محل | اندازه |
|------|--------|
| ناوبری | 24dp |
| نوار ابزار | 24dp |
| دکمه‌ها | 20dp |
| FAB | 24dp |
| نوار وضعیت | 16dp |

---

## ۱۰. ناوبری

### Bottom Navigation

| مشخصه | مقدار |
|--------|--------|
| حداکثر آیتم‌ها | ۵ |
| ارتفاع | 80dp |
| آیکون | 24dp |
| برچسب | Caption 12sp |
| رنگ فعال | Primary |
| رنگ غیرفعال | خاکستری |

### Navigation Drawer

| مشخصه | موبایل | تبلت |
|--------|--------|------|
| عرض | 280dp | 360dp |
| ارتفاع آیتم | 56dp | 56dp |
| آیکون | 24dp | 24dp |
| متن | Body 16sp | Body 16sp |
| Divider | 1dp | 1dp |
| Separator بین بخش‌ها | 1dp | 1dp |

### نوار ابزار (Toolbar)

| مشخصه | مقدار |
|--------|--------|
| ارتفاع | 56dp |
| آیکون‌ها | 24dp |
| عنوان | Title 20sp |
| Elevation | سطح ۲ |

### Tab‌ها

| مشخصه | مقدار |
|--------|--------|
| ارتفاع | 48dp |
| Indicator | Primary |
| متن | Label Large |
| رنگ متن فعال | Primary |
| رنگ متن غیرفعال | On Surface با opacity 60% |
| رنگ پس‌زمینه فعال | Surface |
| رنگ پس‌زمینه غیرفعال | Surface |
| قابل اسکرول | در صورت نیاز |

---

## ۱۱. انیمیشن‌ها

| نوع | مدت | کاربرد |
|------|------|--------|
| سریع | 150ms | تغییرات کوچک (hover, press) |
| معمولی | 250ms | Transition‌های استاندارد |
| آهسته | 350ms | تغییرات بزرگ (page transition) |

### اصول

- همیشه روان (smooth)
- هرگز حواس‌پرت‌کن نباشد
- Easing یکپارچه
- حرکت هدفمند

### Easing Curves

| نوع | منحنی |
|------|--------|
| Standard | CubicBezier(0.2, 0.0, 0, 1.0) |
| Decelerate | CubicBezier(0, 0, 0, 1) |
| Accelerate | CubicBezier(0.3, 0, 1, 1) |

---

## ۱۲. کامپوننت‌ها

### کارت‌ها

#### کارت پروژه

| مشخصه | مقدار |
|--------|--------|
| آیکون | 64dp × 64dp |
| نام | Title 16sp |
| مسیر | Caption 13sp |
| آخرین دسترسی | Caption 13sp |
| دکمه عمل | Icon Button (40dp) |
| شعاع | 16dp |
| پدینگ | 16dp |
| Elevation | سطح ۱ |

#### کارت دستور اخیر

| مشخصه | مقدار |
|--------|--------|
| آیکون ترمینال | 32dp × 32dp |
| دستور | Body 16sp |
| خروجی | Caption 13sp |
| تاریخ اجرا | Caption 13sp |
| شعاع | 16dp |
| پدینگ | 16dp |
| Elevation | سطح ۱ |

#### کارت حافظه

| مشخصه | مقدار |
|--------|--------|
| نوار پیشرفت | ارتفاع 8dp |
| درصد | Body 14sp |
| فضای آزاد | Caption 13sp |
| شعاع | 16dp |
| پدینگ | 16dp |
| Elevation | سطح ۱ |

#### کارت وضعیت

| مشخصه | مقدار |
|--------|--------|
| آیکون وضعیت | 24dp × 24dp |
| عنوان | Title 16sp |
| توضیحات | Body 14sp |
| شعاع | 16dp |
| پدینگ | 16dp |
| Elevation | سطح ۱ |

---

### دکمه‌ها

#### دکمه اصلی (Primary)

| مشخصه | مقدار |
|--------|--------|
| پس‌زمینه | Primary (`#6750A4`) |
| متن | سفید (`#FFFFFF`) |
| ارتفاع | 40dp |
| پدینگ افقی | 24dp |
| شعاع | 12dp |
| فونت | Label Large (14sp, w500) |
| حالت‌ها | فعال، غیرفعال، فشرده |

#### دکمه ثانویه (Secondary / Outlined)

| مشخصه | مقدار |
|--------|--------|
| پس‌زمینه | شفاف |
| حاشیه | 1dp Primary |
| متن | Primary (`#6750A4`) |
| ارتفاع | 40dp |
| پدینگ افقی | 24dp |
| شعاع | 12dp |
| فونت | Label Large (14sp, w500) |

#### دکمه خطر (Danger / Destructive)

| مشخصه | مقدار |
|--------|--------|
| پس‌زمینه | Error (`#B3261E`) |
| متن | سفید (`#FFFFFF`) |
| ارتفاع | 40dp |
| پدینگ افقی | 24dp |
| شعاع | 12dp |
| فونت | Label Large (14sp, w500) |

#### دکمه آیکون (Icon Button)

| مشخصه | مقدار |
|--------|--------|
| اندازه | 40dp × 40dp |
| آیکون | 24dp |
| پدینگ | 8dp |
| شعاع | 20dp |
| حالت‌ها | فعال، غیرفعال، فشرده |

#### دکمه شناور (FAB)

| مشخصه | مقدار |
|--------|--------|
| اندازه | 56dp × 56dp |
| آیکون | 24dp |
| پس‌زمینه | Primary Container |
| رنگ آیکون | On Primary Container |
| Elevation | سطح ۳ |
| شعاع | 16dp |

---

### فیلدهای ورودی

#### نوار جستجو (Search Bar)

| مشخصه | مقدار |
|--------|--------|
| آیکون جستجو | 24dp |
| آیکون پاک کردن | 24dp |
| شعاع | 28dp |
| ارتفاع | 48dp |
| پدینگ افقی | 16dp |
| پس‌زمینه | Surface |

#### فیلد دستور (Command Field)

| مشخصه | مقدار |
|--------|--------|
| پرامپت | `"$ "` (سبز) |
| آیکون اجرا | 24dp |
| شعاع | 12dp |
| ارتفاع | 48dp |
| فونت | Fira Code 14sp |
| پس‌زمینه | Surface |

#### فیلد متنی (Text Field)

| مشخصه | مقدار |
|--------|--------|
| برچسب | شناور (Floating) |
| حاشیه | 1dp |
| حاشیه فوکوس | 2dp Primary |
| ارتفاع | 56dp |
| پدینگ | 16dp |
| شعاع | 12dp |

#### کشویی (Dropdown)

| مشخصه | مقدار |
|--------|--------|
| آیکون فلش | 24dp |
| ارتفاع آیتم | 48dp |
| حالت انتخاب | پس‌زمینه Primary |
| شعاع | 12dp |
| حداکثر ارتفاع | 240dp |

---

### نمای ترمینال

| مشخصه | مقدار |
|--------|--------|
| فونت پیش‌فرض | Fira Code |
| اندازه فونت | 14sp |
| پس‌زمینه | `#1E1E1E` |
| متن | `#D4D4D4` |
| Cursor | Block (پیش‌فرض) |
| ارتفاع خط | 1.4 |
| اسکرول | روان |
| **پدینگ حباب** | **عمودی 8dp، افقی 12dp** |

---

### دیالوگ‌ها

#### دیالوگ تأیید (Confirm)

| مشخصه | مقدار |
|--------|--------|
| عنوان | Title 18sp |
| پیام | Body 16sp |
| دکمه‌ها | لغو + تأیید |
| شعاع | 20dp |
| پدینگ | 24dp |
| عرض | 280dp (موبایل) / 400dp (تبلت) |

#### دیالوگ حذف (Destructive)

| مشخصه | مقدار |
|--------|--------|
| آیکون | هشدار 24dp |
| عنوان | "تأیید حذف" |
| دکمه‌ها | لغو + حذف (Danger) |
| شعاع | 20dp |
| پدینگ | 24dp |

#### دیالوگ خطا (Error)

| مشخصه | مقدار |
|--------|--------|
| آیکون | خطا 24dp (قرمز) |
| عنوان | "خطا" |
| دکمه | OK |
| شعاع | 20dp |
| پدینگ | 24dp |

#### دیالوگ درباره (About)

| مشخصه | مقدار |
|--------|--------|
| آیکون اپ | 64dp |
| نام | Title 18sp |
| نسخه | Body 14sp |
| لینک‌ها | Body 14sp (Primary) |
| شعاع | 20dp |
| پدینگ | 24dp |

#### دیالوگ تنظیمات (Settings Dialog)

| مشخصه | مقدار |
|--------|--------|
| عنوان | Title 18sp |
| محتوا | لیست تنظیمات با Toggle و Dropdown |
| Toggle | Material 3 Switch |
| Dropdown | Material 3 Menu |
| شعاع | 20dp |
| پدینگ | 24dp |
| فاصله بین آیتم‌ها | 16dp |
| حداکثر ارتفاع | 70% صفحه |

---

## ۱۳. تم‌های ترمینال

| تم | پس‌زمینه | توضیح |
|----|---------|--------|
| Dark | `#1E1E1E` | پیش‌فرض |
| OLED | `#000000` | برای نمایشگرهای AMOLED |
| Light | `#FFFFFF` | تم روشن |
| Solarized Dark | — | رنگ‌بندی Solarized |
| Solarized Light | — | رنگ‌بندی Solarized |
| Dracula | — | تم محبوب Dracula |
| Nord | — | تم محبوب Nord |

### رنگ‌های ANSI

- پشتیبانی از ۱۶ رنگ استاندارد
- Bold, Italic, Underline
- رنگ‌های پس‌زمینه
- طرح رنگ سفارشی قابل import/export

---

---

## جمع‌بندی وضعیت تصمیم‌ها

> **وضعیت کلی این سند:** تمام توکن‌ها در وضعیت **Candidate** هستند.

| دسته توکن | وضعیت | وابسته به Validation | Gate مربوطه |
|-----------|--------|---------------------|-------------|
| پالت رنگی (Primary, Secondary, Tertiary, Error) | Candidate | ✅ | G7 — Visual Validation |
| رنگ‌های پس‌زمینه (Light, Dark, OLED) | Candidate | ✅ | G7 — Visual Validation |
| رنگ‌های ترمینال | Candidate | ✅ | G4 — Terminal UX |
| Elevation levels | Candidate | ✅ | G7 — Visual Validation |
| سیستم Grid و Breakpoints | Candidate | ✅ | G8 — Android UX |
| تایپوگرافی | Candidate | ✅ | G7 — Visual Validation |
| Border Radius | Candidate | ✅ | G7 — Visual Validation |
| فاصله‌گذاری (Spacing) | Candidate | ✅ | G7 — Visual Validation |
| Touch Targets | Candidate | ✅ | G8 — Android UX / Accessibility |
| الزامات Accessibility | Candidate | ✅ | G8 — Android UX / Accessibility |
| آیکون‌ها | Candidate | ✅ | G7 — Visual Validation |
| ناوبری (Bottom Nav, Drawer, Toolbar) | Candidate | ✅ | G7 + G8 |
| انیمیشن‌ها و Easing | Candidate | ✅ | G7 — Visual Validation |
| کامپوننت‌ها (کارت‌ها، دکمه‌ها، دیالوگ‌ها) | Candidate | ✅ | G7 — Visual Validation |
| تم‌های ترمینال | Candidate | ✅ | G4 — Terminal UX |

### مسیر تبدیل به Approved

```text
Prototype روی دستگاه واقعی
        ↓
آزمون بصری (G7) + آزمون Accessibility (G8)
        ↓
ثبت نتایج در Decision Record
        ↓
به‌روزرسانی وضعیت هر توکن به Approved / Superseded
```

### مرجع‌های وابسته

| سند | رابطه |
|------|--------|
| `07_UI_Component_Blueprint.md` | معماری تعامل — مرجع بالاتر |
| `09_Validation_Gates.md` | تعیین Gateها و معیارهای پذیرش |
| `10_Technical_Implementation.md` | قرارداد پیاده‌سازی — تصمیمات OPEN را ثبت می‌کند |

---

*پایان سیستم طراحی — نسخه 1.2*
