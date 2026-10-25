گیم‌نت خاقانی — نسخه حرفه‌ای اولیه (PWA + API)
================================================
این بسته شامل رابط وب قابل نصب و یک سرور نمونه با پایگاه داده SQLite است.

پیش‌نیاز سرور:
- Node.js نسخه 22.5 یا جدیدتر
- برای نسخه فعلی نیازی به npm install نیست؛ از node:sqlite داخلی Node استفاده می‌کند.

اجرای محلی:
1) وارد پوشه server شوید.
2) متغیرهای محیطی را تنظیم کنید. در Linux/macOS:
   export ADMIN_USER=admin
   export ADMIN_PASSWORD='یک-رمز-طولانی-و-منحصر-به-فرد'
   export BASE_TICKET_PRICE=80000
   node server.js
3) در مرورگر آدرس http://localhost:8080 را باز کنید.
4) فایل پایگاه داده در server/data/gamenet.sqlite ساخته می‌شود.

توجه مهم:
- سرور API اکنون شامل SQLite، عضویت با هش scrypt، ورود مدیر، افزودن/حذف بازی، رزرو با جلوگیری از هم‌پوشانی زمانی، مدیریت وضعیت رزرو و سفارش، و درگاه Zarinpal به‌صورت قابل پیکربندی است.
- برای پرداخت واقعی باید دامنه HTTPS، شناسه پذیرنده معتبر زرین‌پال و مقادیر BASE_URL و ZARINPAL_MERCHANT_ID تنظیم شود. هیچ اطلاعات بانکی را داخل کد قرار ندهید.
- UI قبلی هنوز همه صفحات را به API متصل نکرده است؛ API باید به فرم‌ها و فهرست‌های UI وصل شود تا کاربران نهایی از پایگاه داده مرکزی استفاده کنند. نسخه فعلی UI ذخیره‌سازی محلی خود را نیز دارد.
- ثبت‌نام API در حال حاضر رمز حداقل ۸ نویسه‌ای را هش می‌کند. ورود کاربر در این نمونه پروفایل را برمی‌گرداند؛ برای عرضه عمومی باید نشست کاربر/توکن، محدودسازی تلاش‌ها، بازیابی حساب، CSRF/CORS policy و سیاست حریم خصوصی کامل شود.
- ورود مدیر فقط زمانی کار می‌کند که ADMIN_PASSWORD از متغیر محیطی تنظیم شده باشد؛ رمز پیش‌فرض ناامن تعریف نشده است.
- جلوگیری از رزرو هم‌پوشان در سرور انجام می‌شود، اما برای ظرفیت چند دستگاه از یک نوع، باید شناسه یکتای هر دستگاه (مثلاً PS5-01 و PS5-02) در پنل تعریف شود.
- فایل APK نهایی در این محیط ساخته نشده است. برای APK مستقل باید پروژه را با Android Studio/Capacitor بسته‌بندی و امضا کرد. وب‌اپ پس از میزبانی HTTPS قابل نصب از Chrome است.

API های اصلی:
GET  /api/health
GET  /api/games
POST /api/members/register   {name, phone, password}
POST /api/members/login      {phone, password}
POST /api/admin/login        {username, password}
POST /api/admin/games        {name, platform, category, price}  Authorization: Bearer <token>
DELETE /api/admin/games/:id  Authorization: Bearer <token>
POST /api/reservations       {device, date:"YYYY-MM-DD", time:"HH:MM", duration, people, userId?}
GET  /api/admin/reservations Authorization: Bearer <token>
PATCH /api/admin/reservations/:id/status {status:"pending|confirmed|cancelled|completed"}
POST /api/payments/create    {title, amount}
GET  /api/payments/verify?orderId=...&Authority=...&Status=...
