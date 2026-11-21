# متابعة دورات المزارع (Supabase + GitHub Pages)

```
web/index.html, web/config.js   الواجهة (تعمل على الجوال والتابلت والكمبيوتر)
supabase/schema.sql             الجداول + التوليد التلقائي للأيام والمهام + الصلاحيات
supabase/seed.sql               بيانات ملف Production_Orders.xlsx (21 مزرعة، 202 بيت وأمر)
.github/workflows/pages.yml     نشر الواجهة تلقائيًا على GitHub Pages
```

## 1) Supabase
1. أنشئ مشروعًا جديدًا في supabase.com.
2. من **SQL Editor** شغّل محتوى `supabase/schema.sql` كاملًا، ثم `supabase/seed.sql`.
3. من **Authentication → Users → Add user** أضف المستخدمين الستة (بريد + كلمة مرور، وفعّل Auto Confirm).
   ومن **Authentication → Sign In / Providers** عطّل **Allow new users to sign up** حتى لا يسجل غيركم.
4. اجعل نفسك مديرًا (غيّر البريد):
   ```sql
   update profiles set role='admin' where id=(select id from auth.users where email='you@example.com');
   ```
5. لربط مشرف بمزرعته فقط (المدير يرى كل المزارع):
   ```sql
   insert into user_farms(user_id,farm_id)
   select u.id,f.id from auth.users u, farms f
   where u.email='sup1@example.com' and f.name in ('ghorary','Kawary');
   ```
6. من **Project Settings → API** انسخ **Project URL** و**anon public key** وضعهما في `web/config.js`.
   لا تضع مفتاح `service_role` أبدًا في الواجهة.

## 2) GitHub
1. أنشئ مستودعًا وارفع محتويات هذا المجلد كما هي (فرع `main`).
2. من **Settings → Pages** اجعل **Source = GitHub Actions**.
3. بعد أول push يعمل النشر تلقائيًا، ويظهر الرابط في تبويب **Actions**.

## ملاحظات
- أوامر الإنتاج الجديدة تُنشأ من تبويب "إنشاء دورة جديدة" (للمدير)، وتتولّد أيامها ومهامها تلقائيًا بالـ trigger.
- آخر سطر في `seed.sql` يعلّم مهام الدورة القديمة المنتهية "غير منطبق" كسجل تاريخي؛ احذفه إن لم تُرد ذلك.
- تعديل تاريخ بداية أو نهاية أمر موجود لا يعيد توليد أيامه؛ احذف الأمر وأنشئه من جديد.
- وقت الحفظ والتعديل يسجلهما الخادم نفسه، فلا يمكن تغييرهما من المتصفح.
