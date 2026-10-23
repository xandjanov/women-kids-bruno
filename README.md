# WK API — Bruno kolleksiyasi

Bu papkani Bruno'ga import qilib, API'ni to'liq sinab ko'rish mumkin.

## Import qilish

1. Bruno'ni ochib: **Collection → Open Collection**
2. Shu papkani tanlang: `D:\xampp\Work\Go\wk\bruno`
3. Yuqoridagi environment ro'yxatidan **Local** ni tanlang
4. `01-Staff / Login` so'rovini yuboring — token avtomatik saqlanadi

Login muvaffaqiyatli bo'lsa `token` va `staffId` env o'zgaruvchilariga yoziladi,
qolgan hamma so'rov shu tokenni o'zi oladi (collection darajasida Bearer auth).

## Boshlang'ich hisob

| | |
|---|---|
| username | `superadmin` |
| password | `01-Staff / Login` so‘rovining body’sida |
| role | `superadmin` |

Parolni almashtirish:
`01-Staff / Staff Update` → `{"password": "YangiParol@123"}`

## Papkalar

| Papka | Mazmuni |
|---|---|
| `01-Staff` | login, profile, logout, sessiyalar + staff CRUD (superadmin) |
| `03-Pages` | statik sahifalar (`key` majburiy) |
| `04-TrialBenefit` | sinov imtiyozlari — 4 tilda text sarlavha, options, CRUD/public/status/sort-order (JSON) |
| `05-Branch` | filiallar — rasm bilan, manzil/zal 4 tilda (create multipart) |
| `06-Metric` | ko'rsatkichlar — `coaches` / `about_studio` guruhlari, `?group=` filtri (create ham JSON) |
| `07-Social` | ijtimoiy tarmoqlar — 4 tilda sarlavha va qisqa matn, icon/options/link, CRUD/public/status/sort-order (JSON) |
| `08-Menu` | daraxt menyu, `cat_id`, sort-order |
| `09-Feature` | xususiyatlar — `header` / `footer` guruhlari, `?group=` filtri (create ham JSON) |
| `10-Feedback` | ochiq murojaat + `branch_id`/`branch_title`; **delete faqat superadmin** |
| `11-Media` | fayl yuklash, is-main, o'chirish, til, fayllar tartibi |
| `12-Common` | status va yozuvlar tartibi |
| `13-TrialOffer` | sinov takliflari — `adults`/`children` 4 tilda (create JSON) |
| `14-Useful` | foydali havolalar — rasm/svg bilan (create multipart) |
| `15-Advantage` | afzalliklar — sarlavha va qisqa matn 4 tilda, icon, CRUD/public/status/sort-order (JSON) |
| `16-OnboardingStep` | boshlash bosqichlari — sarlavha va qisqa matn 4 tilda, CRUD/public/status/sort-order (JSON) |
| `17-CoacheCategory` | murabbiy kategoriyalari — 4 tilda sarlavha, CRUD/public/status (JSON, `sort_order` YO'Q) |
| `18-Coache` | murabbiylar — `women` / `kids` / `women_and_kids` guruhlari, `?group=` filtri, rasm bilan (create multipart) |
| `19-Direction` | yo'nalishlar — `group` + `week` (0..7, bir nechta kun) filtri, rasm bilan (create multipart) |
| `20-User` | oddiy foydalanuvchilar — telefon + SMS kod bilan register/login, profil |

Har bir modelning hamma so'rovi (admin ham, public ham) o'z papkasida.

## Muhim qoidalar

**create = multipart/form-data, update = JSON.**
Istisno: `04-TrialBenefit`, `06-Metric`, `07-Social`, `09-Feature`,
`13-TrialOffer`, `15-Advantage`, `16-OnboardingStep` va `17-CoacheCategory` —
ular fayl yuklamaydi, shuning uchun create ham JSON.
`05-Branch`, `14-Useful`, `18-Coache` va `19-Direction` da `Files` bor — ularning create'i multipart.

**Til tanlash** — `Accept-Language` header: `oz` | `ru` | `uz` | `en` (standart `oz`).
Public so'rovlar shu header bo'yicha kerakli tildagi matnni qaytaradi.
Xato/muvaffaqiyat xabarlari ham shu tilda keladi.

**Rollar** (yuqoridan pastga):

| Role | Ruxsat |
|---|---|
| `superadmin` | hammasi + staff boshqaruvi (`/api/admin/staffs/*`) |
| `admin` | kontent CRUD (pages, trial-benefits, advantages, onboarding-steps, coaches, coache-categories, directions, branches, metrics, socials, menu, features, trial-offers, usefuls) + feedback list/detail |
| `redactor` | faqat `/api/auth/*` (profile, logout, sessiyalar) |

**Feedback o'chirish — faqat `superadmin`.** `admin` roli `/api/feedback/list` va
`/detail/:id` ni ko'radi, lekin `DELETE /api/feedback/delete/:id` ga `403` oladi.
Boshqa hamma kontent modulida `admin` va `superadmin` huquqi bir xil.

**O'ziga ta'sir qiluvchi amallar bloklangan** — o'z rolini/statusini o'zgartirish
yoki o'zini o'chirish `403` qaytaradi.

**sort-order kalitlari adashtirmaslik uchun:**

| So'rov | Kalit |
|---|---|
| `11-Media / Media Files Sort Order` | `order` |
| `12-Common / Sort Order` | `sort_order` |
| `08-Menu / Menu Sort Order` | `sort_order` |
| `04-TrialBenefit / TrialBenefit Sort Order` | `sort_order` |
| `15-Advantage / Advantage Sort Order` | `sort_order` |
| `16-OnboardingStep / OnboardingStep Sort Order` | `sort_order` |
| `06-Metric / Metric Sort Order` | `sort_order` |
| `09-Feature / Feature Sort Order` | `sort_order` |
| `18-Coache / Coache Sort Order` | `sort_order` |

`17-CoacheCategory` da sort-order so'rovi YO'Q — `coache_categories` jadvalida
`sort_order` ustuni mavjud emas, shuning uchun
`PATCH /api/v1/coache-categories/sort-order` `404` qaytaradi.

## Guruhli (grouped) modellar

Uch modelda `group` ustuni bor. Guruh nomlarini **kodda qidirish shart emas** —
har bir papkada `... Groups` so'rovi bor (serverdan ro'yxatni oladi), va
`List` / `Public List` so'rovlarining `params:query` bo'limida hamma qiymat
tayyor qator bo'lib turadi: **faqat bittasi yoqilgan**, qolganlari `~` bilan
o'chirilgan. Bruno'da kerakligining checkbox'ini belgilab, oldingisini
o'chirib qo'ying — boshqa hech narsa o'zgartirmaysiz.

| Papka | `group` qiymatlari | Groups endpoint |
|---|---|---|
| `06-Metric` | `coaches`, `about_studio` | `GET /api/metrics/groups` |
| `09-Feature` | `header`, `footer` | `GET /api/features/groups` |
| `18-Coache` | `women`, `kids`, `women_and_kids` | `GET /api/coaches/groups` |
| `19-Direction` | `women`, `kids`, `women_and_kids` | `GET /api/directions/groups` |

Qoidalar uchchalasida bir xil:

- **create** da `group` **majburiy** — yuborilmasa `400` (validation),
  noto'g'ri qiymat bo'lsa `400` + ruxsat etilgan guruhlar ro'yxati bilan
  tarjimali xato
- **update** da `group` ixtiyoriy, lekin yuborilsa ruxsat etilganlardan biri
  bo'lishi shart
- **list / public list** da `?group=` filtri ixtiyoriy; butunlay o'chirsangiz
  hamma guruh chiqadi, noto'g'ri qiymat `400` qaytaradi
- filtr `total` ga ham ta'sir qiladi, shuning uchun sahifalash to'g'ri ishlaydi
- qiymat **katta-kichik harfga sezgir emas** (`Women` ham `women` deb qabul
  qilinadi), bo'sh joylar kesiladi
- `sort_order` global — tartiblash guruhga bog'liq emas

`18-Coache` da bundan tashqari `coache_category_id` bor (`17-CoacheCategory`
dagi yozuvga ishora). Mavjud bo'lmagan ID yuborilsa `400` + "kategoriya
topilmadi", `0` yuborilsa kategoriya olib tashlanadi. Murabbiy yaratishdan
oldin `17-CoacheCategory / CoacheCategory List` ni yuborsangiz,
`coacheCategoryId` env o'zgaruvchisi avtomatik to'ldiriladi.

## OnboardingStep

`16-OnboardingStep` ichida 8 ta so'rov bor: Create, List, Detail, Update,
Delete, Public List, Status va Sort Order. Create/Update JSON orqali yuboriladi.
List birinchi yozuv IDsi bilan `onboardingStepId`ni yangilaydi; boshqa yozuv
uchun Local environmentdagi qiymatni almashtiring.

`title_oz/ru/uz/en` maksimum 255 belgi, `short_content_oz/ru/uz/en` text.
Update faqat yuborilgan maydonlarni o'zgartiradi; bo'sh satr, false va 0 saqlanadi.
Public List faqat faol yozuvlarni `sort_order ASC, id ASC` tartibida,
`Accept-Language`ga mos `title` va `short_content` bilan qaytaradi.

Admin URL: `/api/onboarding-steps`; public URL: `/public/onboarding-steps/list`.
Global status/sort-order uchun `table` qiymati `onboarding-steps`,
bazadagi jadval esa `onboarding_steps`.

`20261020170000_create_onboarding_steps_table.go` migratsiyasi qo'shilgan;
model AutoMigrate ro'yxatiga ham kiritilgan.

## TrialBenefit va Advantage

Har bir papkada 8 ta so'rov bor: Create, List, Detail, Update, Delete,
Public List, Status va Sort Order. Create/Update JSON orqali yuboriladi.
List birinchi yozuv IDsi bilan `trialBenefitId` yoki `advantageId`ni yangilaydi;
boshqa yozuv ustida ishlash uchun Local environmentdagi shu qiymatni almashtiring.

`TrialBenefit` sarlavhalari text, 255 belgi cheklovi olib tashlangan.
`Advantage` sarlavhalari va qisqa matnlari ham text; `options` (TrialBenefit)
va `icon` (Advantage) maksimum 255 belgi.

Ikkala modulda create paytida `status: false` saqlanadi; status yuborilmasa
standart true. Public List faqat faol yozuvlarni qaytaradi.
Status va Sort Order so'rovlari umumiy `/api/v1/:table/...` funksiyalariga boradi.

Migratsiyalar: `20261020160000_create_advantages_table.go` yangi jadvalni yaratadi,
`20261020160001_alter_trial_benefits_titles_to_text.go` mavjud sinov imtiyozlari
sarlavhalarini textga o'tkazadi. `go run . db migrate` orqali qo'llanadi;
serverdagi AutoMigrate ham modellarga moslaydi. Textdan varchar(255)ga rollback
qilishda 255 belgidan uzun ma'lumot bo'lsa PostgreSQL xato qaytaradi.

## Coache va CoacheCategory

`18-Coache` ichida 10 ta so'rov: Groups, Create, List, Detail, Update, Delete,
Public List, Status, Sort Order, Media Upload.
`17-CoacheCategory` ichida 7 ta: Create, List, Detail, Update, Delete,
Public List, Status (sort-order yo'q).

Ishlash tartibi: avval `CoacheCategory Create` → `CoacheCategory List`
(bu `coacheCategoryId` ni yozadi) → `Coache Create` (u shu ID ni ishlatadi) →
`Coache List` (bu `coacheId` ni yozadi).

`Coache` create **multipart** (`Files` bor), update esa **JSON**.
Sarlavha va qisqa matnlar `text` — 255 belgi cheklovi yo'q.
Rasm cheklovi: jpg/jpeg/png/webp, har biri max 5MB, 100 tagacha.
Public List fayllarni `Accept-Language` bo'yicha filtrlaydi (`lang` mos yoki `all`),
admin List esa faqat asosiy rasmni (`is_main`) qaytaradi.
Delete yozuv bilan birga fayllarni diskdan ham o'chiradi.

`coache_categories` jadvalida `sort_order` ustuni yo'q — ro'yxat `id ASC`
tartibida qaytadi va tartiblash endpointi ishlamaydi.

Migratsiyalar: `20261020180000_create_coache_categories_table.go` va
`20261020180001_create_coaches_table.go`. Ikkala model AutoMigrate ro'yxatiga
ham kiritilgan; `coaches` media va yuklash xaritalariga ham qo'shilgan.

Ustun qo'shilishi migratsiya talab qilmaydi — AutoMigrate entity struct'iga
qarab ustunni o'zi qo'shadi (masalan `metrics.group`, `socials.short_content_*`).

## Direction

`19-Direction` — yo'nalishlar. Ikki filtr bilan ishlaydi: `group` (yuqoridagi
guruhli modellar qoidasi bo'yicha) va **`week`** — hafta kunlari.

| | |
|---|---|
| qiymatlar | `0` dan `7` gacha butun sonlar |
| saqlanishi | bitta yozuvda bir nechta kun: `[1, 3, 5]` (`jsonb` ustun) |
| tartib | server tartiblaydi, takrorini olib tashlaydi: `4,2,2` → `[2, 4]` |
| bo'sh | `[]` ham to'g'ri qiymat |

Yuborish formati endpointga qarab farq qiladi:

| Joy | Ko'rinish |
|---|---|
| `create` (multipart) | `week: 5` + `week: 6` (takrorlangan) yoki `week: 5,6` |
| `update` (JSON) | `"week": [5, 6]`; `[]` bo'shatadi, yuborilmasa tegilmaydi |
| `list` / `public list` | `?week=5&week=6` yoki `?week=5,6` |

**Filtr mantiqi** — `week` kesishma bo'yicha ishlaydi: yozuvning kunlari ichida
so'ralgan kunlardan kamida bittasi bo'lsa, yozuv natijaga tushadi. Ya'ni
`[0,1,3]` kunlari bor yozuv `?week=3,6` so'roviga ham javob beradi.
`group` bilan birga ishlatilsa ikkisi `VA` bog'lanadi:
`?group=women&week=5` = ayollar yo'nalishi VA juma kuni bor.

0..7 dan tashqari son, raqam bo'lmagan qiymat yoki manfiy son `400` +
tarjimali xato qaytaradi.

Model `files` ham saqlaydi (create multipart, rasm 5MB gacha), `age_*`
maydonlari esa to'rt tilda yosh chegarasini ko'rsatadi ("18+", "5-12").
Migratsiya: `20261021120000_create_directions_table.go`.

## User (oddiy foydalanuvchilar)

`20-User` — sayt foydalanuvchilari. Bu **staff'dan butunlay alohida tizim**:
o'z jadvali (`users`), o'z sessiyalari (`user_sessions`) va o'z tokeni bor.
Staff tokeni user endpointlariga kirmaydi, user tokeni admin endpointlariga
kirmaydi — ikkisi ham `401` qaytaradi.

Parol yo'q. Kirish ham, ro'yxatdan o'tish ham **4 xonali SMS kod** bilan:

| Qadam | So'rov | Natija |
|---|---|---|
| 1 | `User Register` (`full_name` + `phone`) | telefonga kod ketadi, foydalanuvchi hali yaratilmaydi |
| 2 | `User Register Verify` (`phone` + `code`) | foydalanuvchi yaratiladi va token beriladi (`201`) |
| 1 | `User Login` (`full_name` + `phone`) | juftlik tekshiriladi, kod ketadi |
| 2 | `User Login Verify` (`phone` + `code`) | token beriladi (`200`) |
| — | `User Profile` | `{{userToken}}` bilan o'z profili |
| — | `User Logout` | shu qurilmadagi sessiya o'chadi |

**SMS provayder hali ulanmagan.** Shuning uchun kod doim `1234` va `register`/`login`
javobida `code` maydoni bo'lib qaytadi — `Register`/`Login` so'rovlarining script'i
uni `userCode` ga yozib qo'yadi, ya'ni `Verify` so'rovini hech narsa kiritmasdan
yuborish mumkin. Eskiz ulangach kod tasodifiy bo'ladi va javobdan yo'qoladi;
so'rovlar va maydonlar o'zgarmaydi.

Kod qoidalari: 2 daqiqa yashaydi, 5 marta xato kiritilsa o'ladi, yangi kod
so'rash uchun 60 sekund kutish kerak (`429`).

Telefon formati erkin — `+998 90 123 45 67`, `901234567`, `998-90-123-45-67`
hammasi `998901234567` ga keltiriladi.

`User Profile` va `User Logout` kolleksiya darajasidagi staff tokenini emas,
`{{userToken}}` ni ishlatadi — ularda `Authorization` header'i qo'lda yozilgan.

## Fayl yuklash va `_samples/`

`_samples/sample.png` — haqiqiy 1×1 PNG, 70 bayt. **O'chirmang.**

Nega kerak: Bruno'da multipart fayl qatori haqiqiy fayl yo'lini talab qiladi.
Agar kolleksiya ichida fayl bo'lmasa, har marta qo'lda brauzerdan rasm tanlash
kerak bo'ladi va `11-Media / Media Upload` so'rovi umuman ishlamaydi (unda fayl
majburiy). Namuna fayl kolleksiya bilan birga ko'chadi, shuning uchun boshqa
kompyuterda import qilsangiz ham so'rovlar darhol ishlaydi.

| So'rov | Fayl qatori |
|---|---|
| `11-Media / Media Upload` | **yoqilgan** — fayl majburiy |
| `18-Coache / Coache Media Upload` | **yoqilgan** — fayl majburiy |
| `03-Pages`, `05-Branch`, `08-Menu`, `14-Useful`, `18-Coache` create | `~` bilan o'chirilgan |

`create` so'rovlarida fayl ixtiyoriy, shuning uchun o'chirilgan holatda qoldirdim —
so'rov fayl bo'lmasa ham ishlaydi. Rasm yuklash uchun Bruno'da `files`
qatorining checkbox'ini belgilang (yoki o'z faylingizni tanlang).

`lang` qatorini yoqsangiz, uning soni fayllar soniga **teng** bo'lishi shart.

`lang` maydonini yuborsangiz, u **fayllar soni bilan teng** bo'lishi shart,
aks holda `400` qaytadi.

## Eslatma: port

`.env` da `APP_PORT=:8000`. XAMPP PHP serveri ham `127.0.0.1:8000` ni egallab
turishi mumkin — bu holda so'rovlar PHP'ning 404 sahifasiga tushadi.
Bunday bo'lsa `.env` dagi portni almashtirib, `environments/Local.bru` dagi
`baseUrl` ni ham moslang.

## Yopiq endpoint

`POST /api/staff/register` — `internal/routes/admin.go` da izohga olingan.
Yangi staff `01-Staff / Staff Create` (superadmin) orqali yaratiladi.

## Parol — bcrypt bo'lishi SHART

`staffs.password` ustunida faqat **bcrypt hash** turishi kerak (60 belgi, `$2a$` bilan
boshlanadi). Bazaga SQL bilan to'g'ridan-to'g'ri ochiq matn parol yozsangiz,
`bcrypt.CompareHashAndPassword` uni hech qachon moslay olmaydi va login doim
`401 Login yoki parol noto'g'ri` qaytaradi — parol to'g'ri bo'lsa ham.

Shuning uchun yangi xodimni **har doim API orqali** yarating:
`01-Staff / Staff Create` (`POST /api/admin/staffs/create`, superadmin) — u parolni
o'zi hash qiladi. Parolni almashtirish ham shunday: `01-Staff / Staff Update`.

## Qurilmalar va sessiyalar (web + mobil bitta backend)

Sessiya **tokenga emas, qurilmaga** bog'langan: `sessions` jadvalida bir staff +
bir qurilma uchun **faqat bitta qator** bo'ladi. O'sha qurilmadan qayta login
qilinsa yangi qator qo'shilmaydi — eski qator o'rniga yangisi yoziladi va
**eski token darhol ishlamay qoladi**.

### Mijoz yuboradigan header'lar

| Header | Qiymat | Kim yuboradi |
|---|---|---|
| `X-Device-Id` | barqaror UUID | mobil app — **shart**; web — tavsiya |
| `X-Device-Name` | `iPhone 15 Pro`, `Samsung Galaxy S23` | mobil app |
| `X-Device-Platform` | `web` \| `android` \| `ios` | mobil app |

`X-Device-Id` ni mijoz **bir marta** yasaydi va doimiy saqlaydi:

| Platforma | Qayerda saqlanadi |
|---|---|
| iOS | Keychain (ilova o'chib qayta o'rnatilsa ham saqlanadi) |
| Android | `SharedPreferences` / `DataStore` |
| Web | `localStorage` |

Qiymat server tomonda xom holda saqlanmaydi — `sha256` hash qilinadi (`device_id`, 64 belgi).

### Nega `User-Agent` yetarli emas

Header yuborilmasa, qurilma `User-Agent` barmoq izi bo'yicha aniqlanadi — brauzer
uchun bu yetarli. Lekin mobil ilovada `User-Agent` ilova yoki OS yangilanganda
o'zgaradi, natijada **o'sha telefon yangi qurilma** sifatida ro'yxatga tushib
qoladi. `X-Device-Id` yuborilsa, IP ham, `User-Agent` ham o'zgarsa sessiya o'sha
qurilmada qoladi. IP qurilmani aniqlashda **ishlatilmaydi** — mobil internetda u
tez-tez o'zgaradi.

### Oqim

```
POST /api/staff/login        -> o'sha qurilmaning eski sessiyasi almashtiriladi
GET  /api/auth/sessions      -> qurilmalar ro'yxati (device_name, platform, last_seen_at)
DELETE /api/auth/sessions/:id -> qurilmani chiqarib yuborish
POST /api/auth/logout        -> faqat joriy qurilmani chiqaradi
```

Qurilma chiqarilgandan keyin uning tokeni `401` qaytaradi — qayta kirish uchun
login qilish shart. Boshqa qurilmalarning sessiyalariga ta'sir qilmaydi.

Parol almashsa yoki staff `status: false` qilinsa — **barcha** qurilmalarning
sessiyalari o'chadi.

`last_seen_at` har so'rovda emas, 5 daqiqada bir marta yangilanadi
(`sessions.TouchInterval`), shuning uchun auth tekshiruviga yuk tushmaydi.
