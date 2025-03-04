مشروع موقع Flask حديث مع نظام مصادقة شبيه بـ GitHub

📌 مقدمة

في هذا الدليل، سننشئ مشروع موقع ويب باستخدام Flask، إطار العمل الخفيف والمرن بلغة Python، مع واجهة أمامية حديثة مستوحاة من تجربة موقع GitHub من حيث التصميم والتجاوب. سيركز المشروع على بناء نظام مصادقة آمن للمستخدمين، يتضمن ميزات تسجيل الدخول والتسجيل، مع تخزين آمن لكلمات المرور.

سنستخدم Flask Blueprints لتنظيم الكود بشكل معياري، مما يسهل تطوير التطبيق وتحديثه. كما سنتبع أفضل الممارسات في هيكلية المشروع، بحيث:

نضع القوالب في مجلد templates/ والملفات الثابتة في static/.

نعتمد على Flask-SQLAlchemy لإدارة قاعدة البيانات بشكل آمن.

نستخدم Flask-WTF لحماية النماذج من هجمات CSRF.

نطبق تقنيات حماية متقدمة لمنع XSS (Cross-Site Scripting) وSQL Injection.


بالنسبة للواجهة الأمامية، سنستخدم Bootstrap للحصول على تصميم حديث ومتجاوب، يشبه الأسلوب البسيط والأنيق لموقع GitHub. بالإضافة إلى ذلك، سنضيف ميزات أمان متقدمة مثل حماية الجلسات، واستخدام bcrypt لتشفير كلمات المرور، ومنع هجمات حقن SQL.

سنتناول في هذا الدليل:

1. هيكلة المشروع وفق أفضل الممارسات.


2. تنفيذ المصادقة (تسجيل، تسجيل دخول، تسجيل خروج) مع تخزين آمن لكلمات المرور.


3. إنشاء لوحة تحكم بسيطة لعرض بيانات المستخدم بعد تسجيل الدخول.


4. تحسينات الأمان لضمان حماية التطبيق.


5. شرح طريقة التشغيل والاعتمادات المطلوبة في ملف README.



هذا المشروع مفتوح المصدر، وجميع الحقوق محفوظة. عند استخدامه، يجب ذكر المصدر وفقًا لسياسات المصادر المفتوحة. 🚀


---

📂 هيكل المشروع (Project Structure)

لتنظيم المشروع وفق أفضل الممارسات في Flask، سنعتمد على البنية التالية للمجلدات والملفات:

my_flask_app/                # المجلد الرئيسي للمشروع
├── run.py                   # ملف تشغيل التطبيق الرئيسي
├── project/                 # الحزمة الرئيسية لمشروع Flask
│   ├── __init__.py          # تهيئة التطبيق وإعداد الإعدادات
│   ├── models.py            # تعريف نموذج قاعدة البيانات (مثل نموذج User)
│   ├── auth/                # Blueprint خاص بالمصادقة (تسجيل الدخول والتسجيل)
│   │   ├── __init__.py      # تهيئة Blueprint للمصادقة
│   │   └── routes.py        # المسارات (Routes) المتعلقة بالمصادقة
│   ├── main/                # Blueprint للصفحات الرئيسية (الرئيسية ولوحة التحكم)
│   │   ├── __init__.py      # تهيئة Blueprint الرئيسي
│   │   └── routes.py        # المسارات العامة (الرئيسية، لوحة التحكم، تسجيل الخروج)
│   ├── templates/           # مجلد القوالب HTML
│   │   ├── base.html        # قالب أساسي مشترك
│   │   ├── home.html        # صفحة الرئيسية Home
│   │   ├── login.html       # صفحة تسجيل الدخول
│   │   ├── register.html    # صفحة التسجيل
│   │   └── dashboard.html   # صفحة لوحة التحكم Dashboard
│   └── static/              # مجلد الملفات الثابتة (صور، CSS، JS)
│       ├── style.css        # ملف CSS مخصص (إن وجد، للتخصيصات الإضافية)
│       └── shield.png       # شعار أو صورة رمز للموقع (اختياري)

📌 ملاحظات حول هذه البنية:

قسمنا المشروع إلى Blueprints:

auth/ يتعامل مع المصادقة (تسجيل الدخول والتسجيل).

main/ يتعامل مع الصفحات العامة مثل الرئيسية ولوحة التحكم.


وضعنا القوالب في مجلد templates/، مع إمكانية فصلها داخل auth/templates/auth/ أو main/templates/main/ عند الحاجة.

يتم تخزين الملفات الثابتة (CSS، JS، صور) في static/.

ملف run.py هو نقطة تشغيل التطبيق.



---

🚀 تشغيل المشروع

1️⃣ تثبيت المتطلبات

قبل تشغيل المشروع، تأكد من تثبيت المتطلبات باستخدام:

pip install -r requirements.txt

أو تثبيتها يدويًا:

pip install Flask Flask-SQLAlchemy Flask-WTF

2️⃣ تشغيل التطبيق

انتقل إلى مجلد المشروع، ثم شغل التطبيق باستخدام:

python run.py

أو باستخدام Flask CLI:

export FLASK_APP=run.py
flask run

بعد التشغيل، توجه إلى http://localhost:5000 في المتصفح لرؤية الموقع يعمل.


---

🛡️ ميزات الأمان (Security Features)

✅ الحماية من CSRF

تم تفعيل حماية CSRF تلقائيًا عبر Flask-WTF، حيث يتم تضمين توكن CSRF في جميع النماذج لحمايتها من الهجمات غير المصرح بها.

✅ الحماية من XSS

يتم التعامل مع المدخلات عبر محرك القوالب Jinja2 الذي يقوم بترميز المحتوى تلقائيًا لمنع حقن JavaScript.

✅ الحماية من SQL Injection

استخدام ORM SQLAlchemy يضمن تنفيذ استعلامات قاعدة البيانات بشكل آمن تمامًا دون التعرض لهجمات SQL Injection.

✅ تخزين آمن لكلمات المرور

نستخدم generate_password_hash لحفظ كلمات المرور في قاعدة البيانات بشكل مشفر، مع التحقق منها عبر check_password_hash عند تسجيل الدخول.


---

📜 ملف README والمصادر المفتوحة

📌 هذا المشروع مفتوح المصدر وجميع الحقوق محفوظة.
عند استخدامه أو التعديل عليه، يرجى ذكر المصدر وفقًا لسياسات المصادر المفتوحة.


---

💡 تحسينات مستقبلية

💡 يمكن تطوير المشروع ليشمل:
✅ إعادة تعيين كلمة المرور عبر البريد الإلكتروني.
✅ استخدام Flask-Login لإدارة جلسات المستخدمين بشكل أكثر أمانًا.
✅ إضافة Dashboard متقدمة تعرض إحصائيات وبيانات مخصصة لكل مستخدم.
✅ إضافة دعم OAuth للسماح بتسجيل الدخول باستخدام GitHub أو Google.

🔹 استمتع بالتطوير مع Flask! 🚀

