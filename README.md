# صالون جودة الأظافر — نسخة Firebase

## ما يعمل بعد الإعداد والنشر
- تسجيل الدخول عبر Firebase Authentication (بريد وكلمة مرور).
- قراءة البيانات من `salons/{salonId}` في Firestore حسب الصلاحية.
- حجز مبدئي بحالة «بانتظار الموافقة» عبر Cloud Function.
- يمكن وصول طلبين متعارضين إلى المالكة، لكن لا يمكن اعتماد حجزين متداخلين للموظفة نفسها.
- حظر نطاق زمني لموظفة أو لجميع الموظفات، ومنع الطلبات الجديدة ضمن الحظر.
- قبول/رفض/إلغاء من المالكة فقط.
- شاشات الخدمات والموظفات والعميلات والكاشير والمصروفات والمخزون والنقاط والأرشفة الشهرية.

## إعداد المشروع
1. افتح Firebase Console للمشروع `nailqualitysalon`.
2. Authentication > Sign-in method: فعّل Email/Password، وأنشئ حساب المالكة.
3. ثبّت Node.js 20 وFirebase CLI على الكمبيوتر، ثم فك ZIP وافتح Terminal داخل مجلد المشروع.
4. نفّذ: `npm install -g firebase-tools` ثم `firebase login` ثم `firebase use nailqualitysalon`.
5. أعط حساب المالكة claim موثوقًا من جهاز الإدارة بعد إعداد Application Default Credentials أو مفتاح خدمة آمن خارج GitHub:
   `npm install firebase-admin`
   `node scripts/set-user-role.js owner@example.com owner quality-nails`
   كرر للموظفات والعميلات مع role المناسب. لا تضع مفاتيح service account في المستودع.
6. جهز مجلد `salons/quality-nails` والبيانات الفرعية `services` و`staff` و`clients` و`bookings` و`blocks` و`invoices` و`expenses` و`inventory` و`loyaltyLedger`. معرّف مستند الموظفة ينبغي أن يطابق UID في Authentication حتى يظهر جدولها.
7. انشر القواعد والدوال: `cd functions && npm install && cd ..` ثم `firebase deploy --only firestore:rules,functions`.
8. انشر الاستضافة إن رغبت: `firebase deploy --only hosting`، أو ارفع `index.html` إلى GitHub Pages بعد إتمام الإعداد.

## ملاحظات مهمة
- Firebase web config ظاهر بطبيعته في الواجهة؛ لا تعتبر `apiKey` كلمة مرور. الأمان الحقيقي من Authentication وFirestore Rules وCloud Functions.
- هذه النسخة متصلة فعليًا بFirebase في الأجزاء المذكورة، لكنها ليست نظام فوترة إلكترونية معتمدًا من هيئة الزكاة والضريبة والجمارك.
- طلبات الحجز تُنشأ عبر Cloud Function. الحسابات تحتاج Custom Claims. لا تنشر الخدمة للعميلات قبل إنشاء بيانات الخدمات والموظفات واختبار صلاحيات مالكة وموظفة وعميلة منفصلة.
- واجهة طلب الحجز متاحة حاليًا داخل التطبيق للحسابات المسجلة؛ تسجيل عميلات ذاتي وإنشاء حسابات Authentication وإشعارات push يحتاج إضافة تدفق تسجيل/تفعيل موثوق قبل فتح الموقع للعامة.
- أرشفة الفواتير تعمل فقط للفواتير التي لها `createdAt` من نوع Firestore Timestamp.
- بيانات المشروع موجودة في `firebaseConfig` داخل index.html كما زوّدني المستخدم. لا تضع كلمة مرور أو مفاتيح Admin SDK في HTML أو GitHub.
