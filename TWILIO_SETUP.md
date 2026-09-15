# إعداد Twilio لإرسال تأكيدات الحجز

يستخدم التطبيق Twilio من الخادم فقط. لا تضع أي مفتاح في ملفات `app/` أو في كود الواجهة أو في مستودع Git.

## المتغيرات المطلوبة

أضف القيم السرية إلى متغيرات بيئة الخادم:

```env
TWILIO_ACCOUNT_SID=ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_AUTH_TOKEN=ضع_القيمة_السرية_هنا
TWILIO_MESSAGING_SERVICE_SID=MGxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

يمكن استخدام رقم إرسال بدل Messaging Service:

```env
TWILIO_FROM_NUMBER=+15551234567
```

استخدم واحدًا فقط: `TWILIO_MESSAGING_SERVICE_SID` أو `TWILIO_FROM_NUMBER`. يفضّل استخدام Messaging Service عند التوسع.

## الحصول على البيانات

1. افتح [Twilio Console](https://console.twilio.com/) وأنشئ حسابًا أو سجّل الدخول.
2. من لوحة الحساب انسخ **Account SID**.
3. من بيانات الاعتماد اعرض **Auth Token** وانسخه إلى مدير الأسرار في بيئة الاستضافة.
4. افتح **Messaging** وأنشئ Messaging Service أو احصل على رقم Twilio يدعم SMS.
5. اربط رقم الإرسال بالـ Messaging Service، أو استخدم الرقم في `TWILIO_FROM_NUMBER`.
6. في الحساب التجريبي، يجب التحقق من رقم المستلم في Verified Caller IDs قبل الإرسال.

## سلوك الواجهة

يظهر زر **تأكيد SMS** في بطاقة كل حجز. عند الضغط عليه تُرسل البيانات إلى إجراء tRPC محمي بتسجيل الدخول، ثم يتصل الخادم بعنوان Twilio التالي:

```text
POST https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Messages.json
```

تُرسل المصادقة باستخدام Account SID وAuth Token عبر Basic Auth، ويُرسل الهاتف بصيغة E.164 مثل `+9665XXXXXXXX`.

## الأمان

- لا تسجل `TWILIO_AUTH_TOKEN` في السجلات.
- لا ترسل المفتاح داخل المحادثة أو ضمن ملفات المشروع.
- غيّر Auth Token فورًا إذا ظهر في مستودع أو سجل.
- اترك نقطة الإرسال محمية بالمصادقة، وأضف حدًا للمعدل قبل النشر العام.
- تأكد من متطلبات Sender ID والتسجيل المحلي في الدولة المستهدفة؛ قد تختلف متطلبات Twilio حسب البلد.
