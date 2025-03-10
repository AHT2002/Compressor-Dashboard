# **گزارش کار پروژه پردازش داده‌های کمپرسور**

## **۱. بارگذاری و پیش‌پردازش داده‌ها**
- فایل **filter_data.csv** بارگذاری شد و داده‌ها در یک دیتافریم Pandas ذخیره شدند.
- داده‌ها شامل ۱۰۰,۰۰۰ نمونه با ۲۲ ویژگی ازجمله فشار، دما، نرخ جریان، مصرف انرژی و ارتعاش بودند.
- برچسب‌های داده شامل سه کلاس مختلف برای تشخیص شرایط عملکرد کمپرسور بودند:
  - **کلاس ۰:** وضعیت عادی
  - **کلاس ۱ و ۲:** وضعیت‌های ناهنجار
- نرمال‌سازی داده‌ها با **StandardScaler** انجام شد.
- داده‌ها به دو مجموعه‌ی **آموزشی (۸۰٪)** و **آزمایشی (۲۰٪)** تقسیم شدند.

## **۲. پیاده‌سازی مدل Autoencoder برای تشخیص ناهنجاری‌ها**
- یک **Autoencoder** با معماری فشرده‌سازی و بازسازی داده‌ها تعریف شد:
  - **۳ لایه‌ی فشرده‌سازی** (۱۴، ۱۰ و ۸ نورون)
  - **۳ لایه‌ی بازسازی** (۱۰، ۱۴ و ۱۷ نورون)
- مدل با **Adam** بهینه‌سازی و با **Mean Squared Error (MSE)** آموزش داده شد.
- نتایج آموزش مدل:
  - کاهش تدریجی خطای بازسازی (از ۰.۳۸۸ به ۰.۰۲۵۷)
  - تشخیص آستانه ناهنجاری با درصد **۸۰٪** و شناسایی **۴۰۰۰ نمونه ناهنجار**
  - دقت مدل در تشخیص ناهنجاری‌ها **۴۸٪** (بازیابی پایین برای کلاس‌های ناهنجار)

## **۳. پیاده‌سازی مدل شبکه عصبی برای طبقه‌بندی داده‌ها**
- یک مدل شبکه عصبی با معماری زیر طراحی شد:
  - **۳ لایه‌ی مخفی** با تعداد نورون‌های ۶۴، ۳۲ و ۱۶
  - **لایه‌ی خروجی** با ۳ نورون (برای سه کلاس)
  - **تابع فعال‌سازی ReLU** برای لایه‌های مخفی و **Softmax** برای خروجی
- مدل با **Sparse Categorical Crossentropy** آموزش داده شد.
- نتایج آموزش مدل:
  - دقت **۹۸.۹٪** در مجموعه آزمایشی
  - ماتریس درهم‌ریختگی نشان‌دهنده‌ی طبقه‌بندی صحیح اکثر نمونه‌ها
  - ذخیره‌ی مدل برای استفاده‌های بعدی

## **۴. تبدیل مدل به فرمت‌های مختلف**
- مدل ذخیره‌شده به **ONNX** تبدیل شد.
- ابزارهای موردنیاز شامل **tf2onnx، onnx و onnx2pytorch** نصب و استفاده شدند.
- مدل به فرمت **PyTorch** تبدیل شد.

## **۵. پیاده‌سازی API با Flask برای تشخیص ناهنجاری‌ها**
### **۵.۱. ایجاد API اولیه برای پیش‌بینی ناهنجاری‌ها**
- یک **Flask API** برای دریافت داده‌های جدید و پیش‌بینی ناهنجاری‌ها با استفاده از مدل **ONNX** پیاده‌سازی شد.
- این API داده‌های ورودی را پردازش کرده و خروجی را به‌صورت JSON برمی‌گرداند.
- ساختار کلی:
  - بارگذاری مدل **ONNX** با **onnxruntime**
  - دریافت داده‌ها از کاربر از طریق متد **POST**
  - اجرای مدل برای پیش‌بینی ناهنجاری‌ها و ارسال پاسخ

### **۵.۲. بهینه‌سازی API با دریافت داده‌ها از فایل**
- برای ساده‌سازی پردازش داده‌ها، API قابلیت خواندن داده‌ها از یک فایل پایتون را اضافه کرد.
- یک تابع برای بارگذاری داده‌ها از **data.py** ایجاد شد که شامل متغیر `features` بود.
- درخواست‌های API اکنون داده‌ها را مستقیماً از فایل می‌خوانند.

### **۵.۳. اجرای پردازش داده‌ها در پس‌زمینه با استفاده از Threading**
- برای بهینه‌سازی عملکرد API، پردازش داده‌ها در یک **Thread** مجزا اجرا شد.
- هر ۳ ثانیه، داده‌های جدید از فایل بارگذاری و پردازش می‌شوند.
- نتایج پردازش در یک متغیر **global** ذخیره می‌شود و API آخرین نتایج را از آن دریافت می‌کند.

## **۶. نمایش نتایج در یک داشبورد وب**
- یک صفحه وب با استفاده از **HTML، CSS و JavaScript** طراحی شد تا نتایج تشخیص ناهنجاری را نمایش دهد.
- این صفحه با استفاده از **Chart.js** نمودارهایی برای نمایش خروجی مدل ایجاد می‌کند.
- ویژگی‌های داشبورد:
  - طراحی مدرن و واکنش‌گرا
  - دریافت نتایج از API هر ۳ ثانیه و به‌روزرسانی نمودارها
  - نمایش خطا در صورت عدم دریافت داده

## **۷. جمع‌بندی**
- مدل **Autoencoder** برای تشخیص ناهنجاری‌ها عملکرد متوسطی داشت و نیاز به بهینه‌سازی بیشتر دارد.
- مدل **شبکه عصبی عمیق** توانست با دقت بالا داده‌ها را طبقه‌بندی کند.
- پیاده‌سازی **Flask API** امکان تشخیص ناهنجاری‌ها را از طریق یک سرویس وب فراهم کرد.
- تبدیل مدل به **ONNX و PyTorch** موفقیت‌آمیز بود و امکان استفاده در محیط‌های مختلف را فراهم کرد.
- نمایش گرافیکی نتایج در داشبورد وب، امکان تحلیل بهتر داده‌ها را فراهم کرد.

