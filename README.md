
---

### ** شناسایی نقاط محوری (Pivot Points)**
- **ورودی مرتبط**:  
  ```mql5
  input int InpCheckBarsM5 = 10;    // تعداد کندلهای بررسی برای M5
  input int InpCheckBarsOther = 2;  // تعداد کندلهای بررسی برای سایر تایمفریمها
  ```
- **مکانیزم**:  

### متغیر InpCheckBarsM5

توضیح: تعداد کندلهایی که برای شناسایی نقاط محوری در تایمفریم M5 بررسی میشوند.

مقدار پیشفرض: 10

معنی: برای تشخیص یک سقف یا کف محلی، الگوریتم 10 کندل قبل و بعد از کندل فعلی را بررسی میکند.

### متغیر InpCheckBarsOther

توضیح: تعداد کندلهایی که برای شناسایی نقاط محوری در تایمفریمهای دیگر (مثل M15) بررسی میشوند.

مقدار پیشفرض: 2

معنی: در تایمفریمهای بالاتر، به جای بررسی 10 کندل، فقط 2 کندل قبل و بعد بررسی میشود. این کار به دلیل این است که در تایمفریمهای بالاتر، حرکات قیمتی بزرگتر هستند و نیازی به بررسی تعداد زیادی کندل نیست.

---

### **۲. شناسایی الگوی "کف → سقف → کف"**

  الگوی معتبر باید شامل **سه نقطه محوری** به ترتیب زیر باشد:
  1. **کف اولیه** (`PIVOT_BOTTOM`)
  2. **سقف** (`PIVOT_TOP`)
  3. **کف ثانویه** (`PIVOT_BOTTOM`)

---

### **۳. اعتبارسنجی روند صعودی قوی (IsStrongImpulseLeg)**
این مرحله از پارامترهای زیر استفاده میکند تا مطمئن شود روند صعودی **به اندازه کافی قوی** است:

- **ورودی مرتبط**:  
  ```mql5
  input double InpMinMove = 5.0;       // حداقل حرکت قیمتی (پیپ)
  input int InpMaxConsecutiveDownBars = 7; // حداکثر کندلهای نزولی متوالی
  input double InpPullbackLimit = 0.5; // حداکثر درصد اصلاح مجاز (50%)
  input double InpMinUpBarsPercent = 0.25; // حداقل 25% کندلهای صعودی
  ```
- **اجرای شرایط**:
  1. **حداقل حرکت قیمتی**:  
     تغییر قیمت از کف اولیه به سقف باید حداقل `5 پیپ` باشد.
  2. **کندلهای نزولی متوالی**:  
     تعداد کندلهای نزولی متوالی در روند نباید از `7` کندل تجاوز کند.
  3. **حداکثر اصلاح مجاز**:  
     بیشترین اصلاح قیمتی در طول روند نباید از `50%` حرکت صعودی اولیه فراتر رود.
  4. **حداقل کندلهای صعودی**:  
     حداقل `25%` از کندلهای روند باید صعودی باشند.

#### **ب. استفاده از ATR برای سنجش قدرت روند**
- **ورودی مرتبط**:  
  ```mql5
  input int InpAtrPeriod = 14;          // دوره محاسبه ATR
  input double InpSignificantMovePercent = 0.3; // 30% حرکت نسبت به ATR
  ```
- **اجرای شرایط**:
  - حرکت صعودی باید حداقل **۲ برابر ATR** باشد.
  - میانگین اندازه بدنه کندلهای صعودی باید حداقل **۳۰% ATR** باشد.


### **۴. بررسی اصلاح قیمت (IsValidRetracement)**
- **ورودی مرتبط**:  
  ```mql5
  input double InpTargetRetracement = 50.0;  // هدف اصلاح 50%
  input double InpRetracementTolerance = 10.0; // تلرانس ±10%
  ```
- **اجرای شرایط**:
  - اصلاح قیمت از سقف به کف ثانویه باید بین **۴۰٪ تا ۶۰٪** (۵۰٪ ±۱۰٪) باشد.

---

### **۵. ترکیب تمام شرایط**
فقط در صورتی یک لگ صعودی بهعنوان **معتبر** شناسایی میشود که:
1. الگوی "کف → سقف → کف" وجود داشته باشد.
2. روند صعودی اولیه **قوی** باشد (بر اساس پارامترهای بالا).
3. اصلاح قیمت در محدوده **هدف تعیینشده** قرار گیرد.

---

### **مثال عملی**
فرض کنید:
- کف اولیه در قیمت `1.0000`
- سقف در `1.0050` (۵۰ پیپ صعود)
- کف ثانویه در `1.0030` (۲۰ پیپ اصلاح)

**محاسبات**:
- درصد اصلاح:  
  `(50 - 20) / 50 = 60%` → خارج از محدوده هدف (۵۰٪ ±۱۰٪) → **رد میشود**.

---
