# راهنمای طراحی رابط کاربری
> نسخه: ۱.۰ | تاریخ: ۲۰۲۶/۰۸/۰۶

---

## زبان طراحی

- Material 3
- گوشه‌های گرد
- سایه‌های نرم
- آیکون‌های مینیمال

---

## پالت رنگی

### رنگ‌های اصلی
- Primary: #6750A4 (بنفش)
- On Primary: #FFFFFF
- Primary Container: #EADDFF
- On Primary Container: #21005E

### رنگ‌های ثانویه
- Secondary: #625B71
- On Secondary: #FFFFFF
- Secondary Container: #E8DEF8
- On Secondary Container: #1D192B

### رنگ‌های سوم
- Tertiary: #7D5260
- On Tertiary: #FFFFFF
- Tertiary Container: #FFD8E4
- On Tertiary Container: #31111D

### رنگ‌های خطا
- Error: #B3261E
- On Error: #FFFFFF
- Error Container: #F9DEDC
- On Error Container: #410E0B

### رنگ‌های پس‌زمینه
- Background: #1C1B1F (تیره)
- On Background: #E6E1E5
- Surface: #1C1B1F (تیره)
- On Surface: #E6E1E5

### تم تیره
- Background: #121212
- Surface: #1E1E1E
- Primary: #BB86FC
- Text: #FFFFFF

### تم OLED
- Background: #000000
- Surface: #0A0A0A
- Primary: #BB86FC
- Text: #FFFFFF

---

## Elevation

### سطوح Elevation
- سطح ۰: ۰dp (پس‌زمینه)
- سطح ۱: ۱dp (کارت‌ها)
- سطح ۲: ۳dp (دکمه‌ها)
- سطح ۳: ۶dp (دیالوگ‌ها)
- سطح ۴: ۸dp (برگه‌های پایین)
- سطح ۵: ۱۲dp (FAB)

### Elevation در تم تیره
- سطح بالاتر = سطح روشن‌تر
- سطح ۰: #121212
- سطح ۱: #1E1E1E
- سطح ۲: #232323
- سطح ۳: #282828
- سطح ۴: #2D2D2D
- سطح ۵: #333333

---

## سیستم Grid

### مشخصات Grid
- Grid: سیستم ۴dp پایه
- موبایل: ۴ ستون
- تبلت: ۸ ستون
- دسکتاپ: ۱۲ ستون
- Gutter: ۱۶dp
- حاشیه: ۱۶dp (موبایل)، ۲۴dp (تبلت)

### عرض ستون
- موبایل: (عرض صفحه - ۳۲dp) / ۴
- تبلت: (عرض صفحه - ۴۸dp) / ۸
- دسکتاپ: (عرض صفحه - ۴۸dp) / ۱۲

---

## Breakpoints

### کلاس‌های اندازه پنجره
- Compact: کمتر از ۶۰۰dp (موبایل)
- Medium: ۶۰۰dp تا ۸۴۰dp (تبلت کوچک)
- Expanded: بیشتر از ۸۴۰dp (تبلت بزرگ/دسکتاپ)

### لایوت تطبیقی
- Compact: تک ستونه
- Medium: دو ستونه
- Expanded: سه ستونه

---

## هدف‌های لمسی

### حداقل اندازه‌ها
- حداقل هدف لمسی: ۴۸dp × ۴۸dp
- بین المان‌ها: ۸dp فاصله
- FAB: ۵۶dp × ۵۶dp
- Bottom Navigation: ۴۸dp × ۴۸dp
- دکمه‌های آیکون: ۴۰dp × ۴۰dp
- دکمه‌های متنی: ارتفاع ۴۰dp

### دسترسی‌پذیری
- پشتیبانی از حالت کنتراست بالا
- پشتیبانی از Screen Reader
- پشتیبانی از متن بزرگ

---

## Border Radius

### شعاع کامپوننت‌ها
- کارت‌ها: ۱۶dp
- دکمه‌ها: ۱۲dp
- دیالوگ‌ها: ۲۰dp
- برگه‌های پایین: ۲۸dp
- FAB: ۱۶dp
- فیلدهای متنی: ۱۲dp

---

## فاصله‌گذاری

### مقیاس فاصله
- کوچک: ۸dp
- متوسط: ۱۶dp
- بزرگ: ۲۴dp
- خیلی بزرگ: ۳۲dp

### فاصله کامپوننت‌ها
- بین کارت‌ها: ۸dp
- بین بخش‌ها: ۲۴dp
- حاشیه صفحه: ۱۶dp (موبایل)، ۲۴dp (تبلت)
- پدینگ محتوا: ۱۶dp

---

## تایپوگرافی

### مقیاس نوع
- Display Large: 57sp
- Display Medium: 45sp
- Display Small: 36sp
- Headline Large: 32sp
- Headline Medium: 28sp
- Headline Small: 24sp
- Title Large: 22sp
- Title Medium: 16sp (w500)
- Title Small: 14sp (w500)
- Body Large: 16sp
- Body Medium: 14sp
- Body Small: 12sp
- Label Large: 14sp (w500)
- Label Medium: 12sp (w500)
- Label Small: 11sp (w500)

### خانواده فونت‌ها
- اصلی: Roboto (سیستم)
- Monospace: Fira Code (ترمینال)
- Display: Roboto Flex

---

## آیکون‌ها

### مشخصات آیکون
- کتابخانه: Material Symbols Rounded
- اندازه: ۲۴dp (پیش‌فرض)
- اندازه کوچک: ۱۸dp
- اندازه بزرگ: ۳۲dp
- رنگ: مطابق با رنگ متن

### استفاده از آیکون
- ناوبری: ۲۴dp
- نوار ابزار: ۲۴dp
- دکمه‌ها: ۲۰dp
- FAB: ۲۴dp
- نوار وضعیت: ۱۶dp

---

## ناوبری

### Bottom Navigation
- حداکثر ۵ آیتم
- ارتفاع: ۸۰dp
- آیکون: ۲۴dp
- برچسب: Caption 12sp
- رنگ فعال: Primary
- رنگ غیرفعال: خاکستری

### کشوی ناوبری
- عرض: ۲۸۰dp (موبایل)، ۳۶۰dp (تبلت)
- آیتم‌ها: آیکون + متن
- جداساز بین بخش‌ها
- هدر با اطلاعات کاربر

### نوار ابزار
- ارتفاع: ۵۶dp
- آیکون‌ها: ۲۴dp
- عنوان: Title 20sp
- Elevation: سطح ۲

### Tab‌ها
- ارتفاع: ۴۸dp
- Indicator: Primary
- متن: Label Large
- فعال: Primary
- غیرفعال: خاکستری
- قابل اسکرول: در صورت نیاز

---

## انیمیشن‌ها

### مدت زمان
- سریع: ۱۵۰ms
- معمولی: ۲۵۰ms
- آهسته: ۳۵۰ms

### اصول
- همیشه روان
- هرگز حواس‌پرت نکن
- Easing یکپارچه
- حرکت هدفمند

### Easing
- Standard: CubicBezier(0.2, 0.0, 0, 1.0)
- Decelerate: CubicBezier(0, 0, 0, 1)
- Accelerate: CubicBezier(0.3, 0, 1, 1)

---

## راهنمای تم تیره

### تطبیقات رنگی
- پس‌زمینه: #121212
- سطح: #1E1E1E
- Primary: #BB86FC
- متن: #FFFFFF

### Elevation در حالت تیره
- ارتفاع بالاتر = سطح روشن‌تر
- سطح ۰: #121212
- سطح ۱: #1E1E1E
- سطح ۲: #232323

### الزامات کنتراست
- کنتراست متن: حداقل ۴.۵:۱
- کنتراست متن بزرگ: حداقل ۳:۱
- المان‌های رابط کاربری: حداقل ۳:۱

---

*پایان راهنمای طراحی رابط کاربری — نسخه ۱.۰*
