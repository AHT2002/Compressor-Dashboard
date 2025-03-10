### **گزارش کار**  
**تاریخ:** 1403/12/12  
**نام:** امیرمحمد شریفی  

---

#### **عنوان پروژه:**  
تشخیص اختلالات (Anomaly Detection) در داده‌های فشرده‌ساز با استفاده از روش‌های مختلف یادگیری ماشین و یادگیری عمیق

---

### **خلاصه فعالیت‌ها:**

در این پروژه، به بررسی و پیاده‌سازی روش‌های مختلف تشخیص اختلالات در داده‌های سری زمانی یک فشرده‌ساز پرداخته شد. داده‌ها شامل ۱۵ ویژگی مختلف مانند فشار ورودی (`Pressure_In`)، دما ورودی (`Temperature_In`)، نرخ جریان (`Flow_Rate`) و غیره بوده و حجم داده‌ها ۱۰۰,۰۰۰ ردیف است. هدف اصلی پروژه، استفاده از روش‌های مختلف چون Isolation Forest، Local Outlier Factor (LOF)، DBSCAN، Autoencoder، و روش‌های آماری برای تشخیص نقاط دور از عادی بود.

---

### **جزئیات فعالیت‌ها:**

#### **۱. پیش‌پردازش داده‌ها:**
- داده‌های اولیه از فایل `extended_compressor_data_with_timestamp.csv` بارگذاری شد.
- ستون‌های `Timestamp` و `Time` به عنوان شاخص‌های زمانی تنظیم شدند.
- ویژگی‌های عددی انتخاب شدند و به صورت استاندارد شده (StandardScaler) تبدیل شدند.
- ستون وضعیت (`Status`) که شامل مقادیر "Imbalance"، "Normal" و "Fault" بود، به مقادیر عددی {1, 0, 2} تبدیل شد.

---

#### **۲. استفاده از روش Isolation Forest:**
- مدل Isolation Forest با پارامتر `n_estimators=200` و `contamination=0.01` آموزش دید.
- نقاط دور از عادی به صورت `{1: Anomaly, 0: Normal}` تشخیص داده شدند.
- نتایج در ستون `Anomaly_IForest` ذخیره شد.

---

#### **۳. استفاده از روش Local Outlier Factor (LOF):**
- مدل LOF با پارامتر `n_neighbors=20` و `contamination=0.01` پیاده‌سازی شد.
- نقاط دور از عادی به صورت `{1: Anomaly, 0: Normal}` مشخص شدند.
- نتایج در ستون `Anomaly_LOF` ذخیره شد.

---

#### **۴. استفاده از روش DBSCAN:**
- مدل DBSCAN با پارامتر `eps=1.2` و `min_samples=15` پیاده‌سازی شد.
- نقاط دور از عادی به صورت `{1: Anomaly, 0: Normal}` مشخص شدند.
- نتایج در ستون `Anomaly_DBSCAN` ذخیره شد.

---

#### **۵. استفاده از مدل Autoencoder (PyTorch):**
- یک مدل Autoencoder با استفاده از PyTorch پیاده‌سازی شد.
- معماری مدل شامل لایه‌های Encoder و Decoder بود که اندازه لایه‌های مخفی به ترتیب 64 → 32 → 16 و 16 → 32 → 64 بود.
- مدل برای ۱۰۰ اپوکه آموزش دید و خطای بازسازی محاسبه شد.
- آستانه اقتباسی (Adaptive Threshold) بر اساس MAD (Median Absolute Deviation) با ضریب حساسیت `k=3.5` تعیین شد.
- نتایج در ستون `Anomaly_Autoencoder` ذخیره شد.

---

#### **۶. ترکیب نتایج با Weighted Voting:**
- نتایج چهار مدل (Isolation Forest، LOF، DBSCAN، و Autoencoder) با وزن‌های {0.3, 0.3, 0.2, 0.2} ترکیب شد.
- نقاطی که حداقل دو مدل آن‌ها را به عنوان اختلال شناسایی کرده بودند، به عنوان نهایی اختلال در نظر گرفته شدند.
- نتایج نهایی در ستون `Final_Anomaly` ذخیره شد.

---

#### **۷. تحلیل نتایج:**
- تعداد نقاط دور از عادی در داده‌ها به طور نهایی ۴۶۴ ردیف (حدود ۰.۴٪ از کل داده‌ها) شناسایی شد.
- توزیع نتایج نهایی بر اساس ستون `Status` به شرح زیر است:
  - `False` (عادی):  
    - Imbalance: ۴۳٬۰۸۶  
    - Normal: ۳۹٬۲۹۴  
    - Fault: ۱۶٬۸۴۱  
  - `True` (دور از عادی):  
    - Imbalance: ۳۲۴  
    - Normal: ۲۹۵  
    - Fault: ۱۶۰  

---

#### **۸. ترسیم نتایج:**
- **نمودار یک بعدی (1D):** توزیع نمرات اختلال (`Anomaly_Score`) با استفاده از Seaborn ترسیم شد و آستانه ۰.۵ به عنوان معیار تشخیص اختلال مشخص شد.
- **نمودار دو بعدی (2D):** 
  - ترسیم نقاط عادی و دور از عادی بر اساس ویژگی‌های `Power_Consumption` و `Vibration` انجام شد.
  - نقاط عادی با رنگ آبی و نقاط دور از عادی با رنگ قرمز نمایش داده شدند.
- **نمودار Interactive:** با استفاده از Plotly، یک نمودار تعاملی ایجاد شد که اطلاعات اضافی مانند `Status`، `Efficiency`، و `Temperature_In` را نشان می‌داد.

---

#### **۹. ذخیره مدل‌ها:**
- **Autoencoder:** مدل Autoencoder به فرمت ONNX در فایل `autoencoder_model.onnx` ذخیره شد.
- **Isolation Forest، LOF، و DBSCAN:** مدل‌های سنتی با استفاده از کتابخانه Joblib در فایل‌های جداگانه ذخیره شدند:
  - `isolation_forest_model.joblib`
  - `lof_model.joblib`
  - `dbscan_model.joblib`
- **تمام مدل‌ها:** تمام مدل‌ها به همراه مسیر فایل ONNX در یک فایل واحد با نام `all_models.pkl` ذخیره شدند.

---

#### **۱۰. ذخیره داده‌های نهایی:**
- داده‌های نهایی که شامل ستون‌های اختلالات و وضعیت (`Status`) بود، در فایل `final_optimized_anomaly_detected_data.csv` ذخیره شد.

---

### **نتایج و نتیجه‌گیری:**

#### **نتایج:**
- روش Weighted Voting Ensemble به طور موثری قادر بود نقاط دور از عادی را با دقت مناسبی تشخیص دهد.
- تعداد نقاط دور از عادی شناسایی‌شده حدود ۰.۴٪ از کل داده‌ها بود.
- نمودارهای ترسیم‌شده نشان داد که نقاط دور از عادی در مناطقی قرار دارند که از خوشه‌های اصلی جدا هستند.

#### **نتیجه‌گیری:**
- استفاده از روش‌های مختلف تشخیص اختلالات بهبود قابل‌توجهی در دقت نتایج ایجاد کرد.
- ترکیب نتایج با Weighted Voting Ensemble راهکاری مؤثر برای کاهش نرخ خطای تشخیص اختلالات بود.
- مدل Autoencoder به‌طور خاص در تشخیص نقاط دور از عادی با خطای بازسازی بالا عملکرد مناسبی داشت.

---

### **پیشنهادات برای بهبود:**
1. **افزودن ویژگی‌های جدید:** می‌توان ویژگی‌های اضافی مانند میانگین متحرک (Moving Average) یا انحراف معیار نوسانات (`Vibration`) را به مجموعه داده اضافه کرد.
2. **تنظیم پارامترها:** آزمایش پارامترهای مختلف مدل‌ها (مانند `contamination` در Isolation Forest یا `eps` و `min_samples` در DBSCAN) می‌تواند دقت نتایج را بهبود بخشد.
3. **استفاده از RNN/LSTM:** برای داده‌های سری زمانی، استفاده از مدل‌های یادگیری عمیق مانند LSTM یا GRU می‌تواند نتایج بهتری ارائه دهد.
4. **آزمایش روی داده‌های بزرگ‌تر:** پیاده‌سازی روش‌ها روی مجموعه داده‌های بزرگ‌تر می‌تواند عملکرد آن‌ها را بهتر ارزیابی کند.

---

### **پیوست:**
- فایل‌های ذخیره‌شده:
  - `autoencoder_model.onnx`: مدل Autoencoder به فرمت ONNX.
  - `isolation_forest_model.joblib`: مدل Isolation Forest.
  - `lof_model.joblib`: مدل LOF.
  - `dbscan_model.joblib`: مدل DBSCAN.
  - `all_models.pkl`: تمام مدل‌ها در یک فایل واحد.
  - `final_optimized_anomaly_detected_data.csv`: داده‌های نهایی با ستون‌های اختلالات.

---

**پایان گزارش**  
