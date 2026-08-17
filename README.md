# NIVARO

منصة تجارة إلكترونية عربية/إنجليزية للأثاث والديكور، مبنية كـ monorepo قابل للنشر المستقل.

## المعمارية

- `apps/storefront`: متجر Next.js 15 على المنفذ 3000، متجاوب ويدعم RTL/LTR والسلة والمفضلة والبحث وSEO.
- `apps/admin`: لوحة إدارة مستقلة على المنفذ 3001 للتقارير والمنتجات والطلبات والتقييمات والعملاء والهوية.
- `apps/api`: API بـ NestJS على المنفذ 4000 مع Prisma/PostgreSQL، حجز المخزون، الطلبات، والتحقق المشفر من Paymob webhooks.
- `apps/api/prisma`: مخطط البيانات الكامل وبيانات seed لعشرين منتجًا، خمس صور لكل منتج، تصنيفات، كوبون، وهوية افتراضية.

## التشغيل المحلي

1. انسخ `.env.example` إلى `.env` وغيّر `AUTH_SECRET`.
2. شغّل الخدمات: `docker compose up -d postgres redis`.
3. ثبّت الحزم: `npm install`.
4. جهّز قاعدة البيانات: `npm run db:generate && npm run db:migrate && npm run db:seed`.
5. شغّل التطبيقات: `npm run dev`.

الحساب الإداري التجريبي بعد seed هو `admin@nivaro.local` وكلمة المرور `ChangeMe123!`. يجب تغييرها قبل أي نشر.

## تكاملات الإنتاج

أضف مفاتيح Google OAuth وCloudinary وPaymob Sandbox وموفر البريد إلى `.env`. لا تُجمع بيانات البطاقة داخل NIVARO؛ الدفع بالبطاقة يجب أن يفتح Paymob hosted iframe، والـ webhook يُرفض إذا لم يطابق HMAC. الدفع عند الاستلام مدعوم مباشرة.

## أوامر الجودة والنشر

- `npm run typecheck` فحص TypeScript.
- `npm test` الاختبارات.
- `npm run build` بناء التطبيقات الثلاثة.
- `docker compose up --build` تشغيل الحزمة كاملة.

أنشئ migrations في التطوير عبر `npm run db:migrate`. في الإنتاج استخدم `npx prisma migrate deploy -w @nivaro/api` قبل تشغيل API. لكل تطبيق Dockerfile مستقل ويمكن ربطه بنطاق منفصل.

## الأمان

تتضمن البنية تحقق DTO صارم، Helmet، CORS محدد، Prisma parameterization، HMAC ثابت الزمن لـPaymob، متغيرات بيئة للأسرار، نماذج RBAC وسجل تدقيق. قبل الإطلاق أضف rate limiting موزعًا عبر Redis، قم بتدوير المفاتيح، فعّل HTTPS، واضبط CORS على النطاقات النهائية فقط.
