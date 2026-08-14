# ShifoTop Clinic OS — UI/UX Master Prompt

**Maqsad:** bu fayl AI dizayn/kod generatoriga (Claude Code, Figma MCP, Figma Make, v0) to'g'ridan-to'g'ri beriladigan **bitta manba** (single source of truth). Ichida: rollar, ekranlar xaritasi, har bir ekran vazifasi, komponentlar kutubxonasi va dizayn tizimi tokenlari.
**Manba hujjatlar:** `shifotop-clinic-os-spec.md` (§3 RBAC, §4–8 modullar), `roadmap.md` (fazalar).
**Versiya:** 1.0 · 2026-08-13

---

## 0. Prompt'dan qanday foydalanish

| Vosita | Nima berish kerak |
|---|---|
| **Claude Code (frontend qurish)** | Butun faylni `docs/design/ui-spec.md` sifatida repo'ga qo'y, keyin: *"`docs/design/ui-spec.md` ni o'qi. S-10 ekranini Next.js 14 + Tailwind + shadcn/ui'da qur. §7 tokenlaridan chetga chiqma."* |
| **Figma MCP (`use_figma`)** | §7 tokenlarini avval **Figma Variables** sifatida yarat, keyin ekranlarni §5 wireframe'lari bo'yicha qur |
| **Figma Make / v0** | §1 + §7 + kerakli bitta ekran bo'limini nusxala (butun faylni bermang — kontekst suyuladi) |

**Qat'iy qoida:** har bir ekran **§7 tokenlaridan** quriladi. Komponent ichida xom `#hex` yoki `px` qiymat yozilmaydi.

---

## 1. Mahsulot konteksti

ShifoTop Clinic OS — ShifoTop marketplace ustiga qurilgan **klinika operatsion tizimi**. Bemorning eshikdan kirganidan to qayta aloqa qilingunicha bo'lgan yo'lini bitta tizimda ushlab turadi.

**Bu marketing sayti emas.** Bu — kuniga 8 soat, xuddi shu ekranga qaraydigan operatorning ish quroli. Shuning uchun dizayn mezoni: *chiroylimi* emas, **tez o'qiladimi, xato qilib bo'ladimi, charchatadimi**.

**Foydalanish muhiti (dizaynga bevosita ta'sir qiladi):**

| Omil | Haqiqat | Dizaynga ta'siri |
|---|---|---|
| Qurilma | Klinikada eski Windows PC, 1366×768 monitor keng tarqalgan | Min qo'llab-quvvatlanadigan kenglik **1280px**, 1366×768'da ham skrollsiz ishlashi shart |
| Yorug'lik | Registratura yorug', deraza yonida | **Light theme majburiy**, kontrast ≥ 4.5:1, glossy/glass effektlar yo'q |
| Tezlik | Bemor qarshisida turibdi, operator shoshiladi | Har bir asosiy amal ≤ 2 klik, klaviatura shortcut'lari majburiy |
| Internet | Uzilib turishi mumkin | Ulanish holati **doim ko'rinib turadi**, optimistik UI + idempotent qayta yuborish |
| Foydalanuvchi | 35–55 yosh administrator, texnik emas | Ikonka + matn (faqat ikonka emas), atamalar sodda o'zbek tilida |

---

## 2. Rollar — jami 5 ta ichki + 2 ta tashqi

### 2.1 Ichki rollar (klinika xodimlari)

| # | Rol | UI'dagi nomi | Asosiy ekrani | Kunlik ish rejimi |
|---|---|---|---|---|
| 1 | `admin` | Rahbar / Administrator | S-40 Dashboard | Kuniga 10–20 daqiqa, hisobot ko'radi |
| 2 | `reception` | Registratura | S-10 Navbat | **Kun bo'yi ochiq**, eng yuqori yuk |
| 3 | `doctor` | Shifokor | S-20 Mening navbatim | Qabullar orasida, 30–60 s sessiyalar |
| 4 | `cashier` | Kassir | S-30 To'lovlar | Kun bo'yi ochiq, ikkinchi monitor |
| 5 | `accountant` | Buxgalter | S-42 Moliyaviy hisobot | Haftada 1–2 marta |

### 2.2 Tashqi (klinika xodimi emas)

| # | Rol | Kirish usuli | Qamrov |
|---|---|---|---|
| 6 | `patient` | Autentifikatsiyasiz, SMS'dagi havola orqali | S-70, S-71 (Faza 3) |
| 7 | `platform_admin` | ShifoTop platformasi tomonidan | **Bu spec'dan tashqarida** |

### 2.3 RBAC → UI qoidalari

Spec §3 matritsasi UI'da quyidagicha amalga oshiriladi:

- **Ruxsat yo'q element ko'rsatilmaydi** (disabled qilinmaydi) — "bor, lekin senga emas" hissi ishonchsizlik tug'diradi
- Shifokorning "faqat o'ziniki" cheklovi **backend query darajasida**, frontend routing bilan emas
- Sidebar navigatsiyasi rolga qarab dinamik quriladi; bitta menyu 4 tadan ortiq punktga ega bo'lmasin (Hick qonuni)
- Rol bir nechta bo'lsa (masalan `reception` + `cashier`) — TopBar'da rol almashtirgich (segmented control), sahifa qayta yuklanmaydi

---

## 3. Klient sirtlari (surfaces) — 6 ta

| Kod | Sirt | Texnologiya | Viewport | Kirish | Faza |
|---|---|---|---|---|---|
| **A** | Operator Web (reception / doctor / cashier / admin) | Next.js 14 + Tailwind + shadcn/ui | 1280×720 → 1920×1080 | Login | 1 |
| **B** | TV Display (kutish zali) | Next.js, kiosk rejim, `EventSource` | 1920×1080 fixed | Pairing kodi, loginsiz | 1 |
| **C** | Chek (termik printer) | 58mm / 80mm ESC-POS shabloni | 384px / 576px raster | — | 1 |
| **D** | SMS shabloni | Matn, 1 segment (70 belgi, kirill/lotin) | — | — | 1 |
| **E** | Xodim check-in | Operator Web ichidagi modul + webcam | 1280×720 | Login | 4 |
| **F** | Bemor web-view | Next.js public route | 390×844 mobile-first | Havola | 3 |

> **C va D — bu ham dizayn.** Chek va SMS bemor qo'lidagi yagona artefakt. Ular ham §7 terminologiyasiga bo'ysunadi.

---

## 4. Ekranlar xaritasi — jami 33 ta

**Belgilar:** 🔴 Faza 1 (MVP, majburiy) · 🟡 Faza 2 · 🟢 Faza 3–4

### 4.1 Umumiy (3)

| ID | Ekran nomi | Vazifasi | Rol | Faza |
|---|---|---|---|---|
| S-00 | Kirish | Telefon + parol (yoki SMS OTP). Klinika avtomatik aniqlanadi | barcha | 🔴 |
| S-01 | Klinika tanlash | Bir user bir nechta filialga bog'langan bo'lsa | barcha | 🟡 |
| S-02 | Holat ekranlari | 403 / 404 / Aloqa yo'q / Texnik ish | barcha | 🔴 |

### 4.2 Registratura (7)

| ID | Ekran nomi | Vazifasi | Faza |
|---|---|---|---|
| **S-10** | **Navbat boshqaruvi** | Bosh ekran. Shifokorlar bo'yicha vertikal kolonkalar, real-time. Kun bo'yi ochiq turadi | 🔴 |
| S-11 | Yangi navbat berish | Drawer, 2 qadam: telefon qidiruv → shifokor/xizmat tanlash → chek + SMS | 🔴 |
| S-12 | Navbat kartochkasi | Drawer: bemor ma'lumoti, holat tarixi, amallar (chaqirish / bekor / chekni qayta chop etish) | 🔴 |
| S-13 | Bemorlar bazasi | Qidiruv (telefon/F.I.Sh), ro'yxat, tashrif tarixi bilan | 🔴 |
| S-14 | Bemor kartasi | Profil + barcha tashriflar timeline'i + to'lov holati | 🟡 |
| S-15 | Bugungi qo'ng'iroqlar | M3 follow-up navbati. "Qo'ng'iroq qilish → natija belgilash" konveyeri | 🟡 |
| S-16 | Qo'ng'iroq natijasi | Modal: ✅ Yaxshi / ⚠️ Muammo / ☎️ Javob yo'q + izoh | 🟡 |

### 4.3 Shifokor (5)

| ID | Ekran nomi | Vazifasi | Faza |
|---|---|---|---|
| **S-20** | **Mening navbatim** | Bitta katta "Keyingi bemor" tugmasi + hozirgi bemor + kutayotganlar ro'yxati | 🔴 |
| S-21 | Qabul yozuvi | Shikoyat / tashxis / retsept / xizmatlar / follow-up ☑ — bitta ekranda, saqlash = yakunlash | 🟡 |
| S-22 | Mening bemorlarim | Faqat o'z tashriflari tarixi, qidiruv bilan | 🟡 |
| S-23 | Vazifalar | Follow-up'dan ⚠️ eskalatsiya qilingan holatlar | 🟡 |
| S-24 | Mening statistikam | Qabullar soni, o'rtacha davomiylik, follow-up natijasi | 🟡 |

### 4.4 Kassa (4)

| ID | Ekran nomi | Vazifasi | Faza |
|---|---|---|---|
| S-30 | To'lanmagan hisoblar | Kassa bosh ekrani, `pending` invoyslar ro'yxati real-time | 🟡 |
| S-31 | To'lov qabul qilish | Modal: summa, usul (naqd/karta/ShifoTop onlayn), qaytim kalkulyatori | 🟡 |
| S-32 | Smena | Smena ochish/yopish + yopilish hisoboti (kutilgan / haqiqiy / farq) | 🟡 |
| S-33 | To'lovlar jurnali | Append-only ledger. O'chirish yo'q — faqat `refund` teskari yozuvi | 🟡 |

### 4.5 Rahbar / Buxgalter (7)

| ID | Ekran nomi | Vazifasi | Faza |
|---|---|---|---|
| S-40 | Dashboard | 6 ta KPI karta + kunlik grafik + diqqat talab qiladigan holatlar | 🟡 |
| S-41 | Shifokorlar samaradorligi | Jadval: qabullar, daromad, o'rtacha vaqt, follow-up ijobiy ulushi | 🟡 |
| S-42 | Moliyaviy hisobot | Davr bo'yicha daromad, to'lov usullari kesimi, eksport (xlsx/pdf) | 🟡 |
| S-43 | Xodimlar va rollar | Xodim qo'shish, rol berish, faolsizlantirish. RBAC matritsasi vizual | 🔴 (minimal) |
| S-44 | Xizmatlar va narxlar | Narx ro'yxati. Narx o'zgarishi eski hisoblarga ta'sir qilmasligi vizual tushuntiriladi | 🟡 |
| S-45 | Klinika sozlamalari | Ish vaqti, shifokor prefikslari va ranglari, SMS shabloni, printer, TV | 🔴 (minimal) |
| S-46 | Amallar jurnali (audit) | Kim, qachon, nima qildi. Filtr + eksport | 🟡 |

### 4.6 TV Display (2)

| ID | Ekran nomi | Vazifasi | Faza |
|---|---|---|---|
| **S-50** | **Navbat ekrani** | Kutish zali. "Hozir chaqirilmoqda" + "Keyingi navbat". 3–6 metr masofadan o'qiladi | 🔴 |
| S-51 | Ekranni ulash | 6 xonali pairing kodi, ulangach avtomatik S-50'ga o'tadi | 🔴 |

### 4.7 Xodim davomati — M5 (3)

| ID | Ekran nomi | Vazifasi | Faza |
|---|---|---|---|
| S-60 | Check-in | "Men keldim" / "Ketdim" + webcam bitta kadr | 🟢 |
| S-61 | Mening davomatim | Xodimning shaxsiy oylik jadvali | 🟢 |
| S-62 | Davomat hisoboti | HR/admin uchun: kechikishlar, rasm bilan tasdiq | 🟢 |

### 4.8 Bemor (2)

| ID | Ekran nomi | Vazifasi | Faza |
|---|---|---|---|
| S-70 | Navbat holati | SMS havolasi → "Sizniki: K-14 · Hozir: K-9 · ~24 daqiqa" | 🟢 |
| S-71 | Izoh qoldirish | Follow-up ✅ dan keyin → ShifoTop reytingiga izoh | 🟢 |

### 4.9 Faza 1 MVP qamrovi — 10 ta ekran

`S-00 · S-02 · S-10 · S-11 · S-12 · S-13 · S-20 · S-43(minimal) · S-45(minimal) · S-50 · S-51`
Bularga qo'shimcha: **C** (chek shabloni) va **D** (SMS shabloni).

---

## 5. Kalit ekranlar spetsifikatsiyasi

> Har bir ekran quyidagi formatda: **Muammo → Yechim → UX qonuniyati → Accessibility**

### S-10 · Navbat boshqaruvi (registratura bosh ekrani)

**Muammo:** administrator kun bo'yi shu ekranga qaraydi. 3–6 shifokor navbati bir vaqtda ketadi. "Kim oxirgi, kim hozir kirdi, kim kelmadi" — bir qarashda ko'rinishi kerak. Skroll = xato.

**Yechim:**

```
┌──────────────────────────────────────────────────────────────────────────┐
│ ShifoTop OS   [Navbat] Bemorlar  Qo'ng'iroqlar     ● Jonli   AA ▾  ⚙     │ 64px TopBar
├──────────────────────────────────────────────────────────────────────────┤
│  Bugun · 13-avgust, payshanba          Kutmoqda 12 · Qabulda 3 · Kelmadi 1│ 48px
│                                                    [ + Yangi navbat  F2 ] │
├────────────────┬────────────────┬────────────────┬───────────────────────┤
│ ▌K  Karimov A. │ ▌S  Saidova N. │ ▌T  To'rayev B.│ ▌U  Usmonova D.       │ kolonka
│   Kardiolog    │   Stomatolog   │   Terapevt     │   UZI           ⏸ Pauza│ header 72px
│   4 kutmoqda   │   3 kutmoqda   │   5 kutmoqda   │   0 kutmoqda          │
├────────────────┼────────────────┼────────────────┼───────────────────────┤
│ QABULDA        │ CHAQIRILDI     │ QABULDA        │                       │
│ ┌────────────┐ │ ┌────────────┐ │ ┌────────────┐ │                       │
│ │ K-09  12:04│ │ │ S-07  ◉◉◉  │ │ │ T-11  09:12│ │      Navbat bo'sh     │
│ │ Aliyev A.  │ │ │ Yusupov M. │ │ │ Qodirov S. │ │   [Navbat berish]     │
│ └────────────┘ │ └────────────┘ │ └────────────┘ │                       │
│                │                │                │                       │
│ KUTMOQDA       │ KUTMOQDA       │ KUTMOQDA       │                       │
│ K-10 Rahimova  │ S-08 Nazarov   │ T-12 Ergashev  │                       │
│ K-11 Sobirov   │ S-09 Olimova   │ T-13 Yo'ldosh  │                       │
│ K-12 Karimova  │ S-10 Ismoilov  │ T-14 Sattorov  │                       │
│ K-13 Toshev    │                │ T-15 Nabiyev   │                       │
└────────────────┴────────────────┴────────────────┴───────────────────────┘
```

- **Kolonka = shifokor.** Har biriga doimiy rang (`--doctor-1..8`) — bu rang TV'da, chekda, kassada ham bir xil qoladi
- Kolonka soni 4 tadan oshsa — gorizontal skroll emas, **kolonka kengligi qisqaradi** (min 240px), 7 tadan oshsa "Shifokorni tanlang" filtri paydo bo'ladi (Miller qonuni: 7±2)
- **Chaqirilgan bemor** kartasi 3 marta pulsatsiya qiladi (`◉◉◉`) va 600ms davomida `--st-blue-600` fonga o'tadi — Von Restorff effekti
- Yuqori o'ngda **`● Jonli`** ulanish indikatori: yashil (SSE ochiq) / sariq "Qayta ulanmoqda" / qizil "Aloqa yo'q · oxirgi yangilanish 14:32"
- Karta ustiga hover → o'ng tomonda 3 ta ikonka tugma paydo bo'ladi (chaqirish / kelmadi / bekor). Hover'siz ham kontekst menyu (o'ng tugma) ishlaydi

**UX qonuniyati:**
| Qonun | Qo'llanishi |
|---|---|
| Miller (7±2) | Bir ekranda maks 7 kolonka, undan ortig'i — filtr |
| Von Restorff | Chaqirilgan bemor yagona to'q rangli element |
| Fitts | `+ Yangi navbat` — 180×44px, doimiy joyda, `F2` bilan dublikat |
| Jakob | Kanban ustunlari — allaqachon tanish pattern, o'rgatish shart emas |
| Doherty | Chaqirish tugmasi bosilishi bilan ≤100ms optimistik holat o'zgaradi, server javobi keyin keladi |
| Hick | Ekranda faqat **1 ta** primary tugma (`+ Yangi navbat`) |

**Accessibility:**
- Holat faqat rang bilan emas, **matn yorlig'i bilan** ham beriladi (`QABULDA`, `KUTMOQDA`) — daltonizm
- Kolonka ranglari oq fonda ≥ 4.5:1
- Tab tartibi: TopBar → primary tugma → kolonkalar chapdan o'ngga → karta ichida
- `aria-live="polite"` — yangi bemor qo'shilganda skrinrider e'lon qiladi
- Butun ekran klaviaturadan boshqariladi: `1..9` = kolonka, `↑↓` = karta, `Enter` = chaqirish

---

### S-11 · Yangi navbat berish (drawer)

**Muammo:** bemor qarshingizda turibdi. Bu jarayon **15 soniyada** tugashi kerak, aks holda navbat registratura oldida to'planadi.

**Yechim:** o'ng tomondan 480px drawer, 2 qadam:

```
┌─ Yangi navbat ───────────────────── ✕ ┐
│  1 ── Bemor        2 ── Shifokor      │  progress 2 qadam
│                                        │
│  Telefon raqami                        │
│  ┌────────────────────────────────┐   │
│  │ +998 (90) 123-45-67            │   │  autofocus, mask
│  └────────────────────────────────┘   │
│                                        │
│  ✓ Topildi: Aliyev Anvar, 34 y.       │  200ms debounce, live
│    Oxirgi tashrif: 12-iyul, Dr.Karimov│
│                                        │
│  ○ Yangi bemor sifatida qo'shish       │
└────────────────────────────────────────┘
```

- Telefon topilsa — bemor karta ko'rinadi, **qadam 2 ga avtomatik o'tmaydi** (xato profil tanlanishining oldini olish uchun tasdiq talab qilinadi)
- Topilmasa — inline forma (F.I.Sh + tug'ilgan yil), modal ustiga modal ochilmaydi
- Qadam 2: shifokor kartalari grid (rang + prefiks + kutayotganlar soni + taxminiy vaqt)
- Yakuniy tugma: **`Navbat berish · K-14`** — raqam tugma matnida oldindan ko'rsatiladi (natijani oldindan ko'rsatish = ishonch)
- Bosilgach: tugma `loading` → chek chop etiladi → drawer yopiladi → S-10'da yangi karta 400ms `slide-in` bilan paydo bo'ladi
- **Idempotency:** tugma bosilgandan keyin 3 soniya disabled; qayta bosish bir xil `Idempotency-Key` bilan ketadi → ikkinchi chek chiqmaydi

**UX qonuniyati:** Progressive disclosure (2 qadam, bir vaqtda 1 ta qaror) · Doherty (debounce 200ms, skeleton 400ms dan keyin) · Postel qonuni (telefon `+998901234567`, `901234567`, `90 123 45 67` — hammasi qabul qilinadi)

**Accessibility:** har bir maydonda ko'rinadigan `<label>` (placeholder yorliq o'rnini bosmaydi) · xato maydon **yonida** chiqadi · `Esc` = yopish (tasdiq bilan, agar ma'lumot kiritilgan bo'lsa) · `Enter` = keyingi qadam

---

### S-20 · Mening navbatim (shifokor)

**Muammo:** shifokor bu ekranga kuniga 40 marta, har safar 5 soniyaga qaraydi. Unga dashboard kerak emas — unga **bitta tugma** kerak.

**Yechim:**

```
┌────────────────────────────────────────────────────────────┐
│  Dr. Karimov A. · Kardiolog                    ● Jonli  ▾  │
├────────────────────────────────────────────────────────────┤
│                                                             │
│              ┌──────────────────────────┐                  │
│              │         K-09             │  128px raqam     │
│              │      Aliyev Anvar        │                  │
│              │   34 yosh · 12:04 dan    │                  │
│              └──────────────────────────┘                  │
│                                                             │
│   [  Qabulni yakunlash  ]   [ Kelmadi ]                    │
│                                                             │
│   ─────────────────────────────────────────                │
│   Keyingi: K-10 Rahimova M.                                │
│                                                             │
│   ╔═══════════════════════════════════════╗                │
│   ║      KEYINGI BEMORNI CHAQIRISH        ║  72px, Space   │
│   ╚═══════════════════════════════════════╝                │
│                                                             │
│   Kutmoqda (4): K-10 · K-11 · K-12 · K-13                  │
└────────────────────────────────────────────────────────────┘
```

- Primary tugma **72px balandlikda**, ekran markazida — Fitts qonuni bo'yicha eng katta nishon
- `Space` klavishi = chaqirish (shifokor sichqonchaga qo'l uzatmaydi)
- Bemor kirmasa: tugma matni `QAYTA CHAQIRISH (2/2)` ga o'zgaradi, 2-marta javob bo'lmasa `Kelmadi` ga aylanadi. **Muhim:** kelmagan bemor navbatdan chiqadi, lekin keyingi bemorlar raqami o'zgarmaydi
- Faza 2'da `Qabulni yakunlash` → S-21 qabul yozuvi ochiladi

**UX qonuniyati:** Fitts (72px nishon) · Hick (ekranda 1 ta asosiy qaror) · Tesler qonuni (murakkablik shifokordan tizimga o'tkazilgan — u navbat mantiqini bilishi shart emas)

**Accessibility:** primary tugma kontrasti ≥ 7:1 · `Space`/`Enter` ikkalasi ham ishlaydi · chaqirilgandan keyin `aria-live` orqali tasdiq · `prefers-reduced-motion` da pulsatsiya statik halqaga almashadi

---

### S-50 · TV Display

**Muammo:** bemor 3–6 metrdan, ba'zan ko'zoynaksiz qaraydi. Ekran devorda, kun bo'yi yonib turadi. U yerda hech kim bilan gaplashish mumkin emas — ekran o'zini o'zi tushuntirishi kerak.

**Yechim:** operator UI'dan **butunlay boshqa vizual rejim** — to'q navy fon, ulkan raqamlar.

```
┌──────────────────────────────────────────────────────────────────────┐
│                                                              64px safe│
│   HOZIR CHAQIRILMOQDA                    │  KEYINGI NAVBAT           │
│                                          │                            │
│  ▌┌────────────────────────────────┐    │   K-10   Kardiolog        │
│   │                                 │    │   S-08   Stomatolog       │
│   │      K-09                       │    │   T-12   Terapevt         │
│   │      ──────                     │    │   K-11   Kardiolog        │
│   │      2-KABINET                  │    │   S-09   Stomatolog       │
│   │      Dr. Karimov A.             │    │                            │
│   └─────────────────────────────────┘    │                            │
│                        240px raqam        │   64px                    │
│  ▌┌────────────────────────────────┐    │                            │
│   │      S-07    ·   5-KABINET     │    │                            │
│   └─────────────────────────────────┘    │                            │
│                                          │                            │
├──────────────────────────────────────────┴────────────────────────────┤
│  ShifoTop            Navbatingiz SMS orqali ham yuborildi     14:32   │
└───────────────────────────────────────────────────────────────────────┘
```

**Texnik parametrlar:**

| Parametr | Qiymat |
|---|---|
| Rezolyutsiya | 1920×1080 fixed, `overflow: hidden`, skroll yo'q |
| Safe area | Har tomondan 64px (eski TV'larda overscan) |
| Fon | `--st-ink-900` `#0B1F3A` — oq emas (kun bo'yi yonadigan ekranda oq fon "bloom" beradi) |
| Matn | `#F2F7FF` (sof `#FFFFFF` emas) · kontrast 14:1 |
| Chaqirilgan raqam | 240px, Manrope ExtraBold, `tabular-nums` |
| Kabinet raqami | 96px — bemorga aslida **eng kerakli ma'lumot** shu |
| Keyingi navbat | 64px, maks 5 ta qator |
| Yangi chaqiruv animatsiyasi | Cross-fade 300ms + 24px pastdan ko'tarilish, `cubic-bezier(0.25, 0.1, 0.25, 1)`, so'ng 3× pulsatsiya (600ms, `cubic-bezier(0.34, 1.56, 0.64, 1)`) |
| Ovoz | 2 notali yumshoq "ding" (E5→A5, 400ms), sozlamalardan o'chiriladi |
| Aloqa uzilsa | Pastki chiziqda kichik sariq belgi: `Yangilanmoqda…` — bemorni qo'rqitmaydigan formulirovka |
| Bo'sh holat | "Navbat bo'sh · Registraturaga murojaat qiling" + klinika logotipi |

**UX qonuniyati:** Signal-to-noise (ekranda faqat 3 tur ma'lumot: kim, qayerga, keyin kim) · Von Restorff (chaqirilgan raqam yagona harakatlanuvchi element) · Serial position (eng muhimi — yuqori chapda)

**Accessibility:** kontrast ≥ 7:1 (WCAG AAA) · animatsiya 3 martadan oshmaydi (fotosensitiv xavf) · raqam + kabinet + shifokor F.I.Sh — uch xil kanal, faqat rangga tayanmaydi

---

## 6. Komponentlar kutubxonasi

### 6.1 Atomlar

| Komponent | Variantlar | Holatlar | Eslatma |
|---|---|---|---|
| `Button` | primary · secondary · ghost · danger | default · hover · active · focus-visible · loading · disabled | Balandliklar: sm 32 / md 40 / lg 48 / hero 72px |
| `Input` | text · phone · number · search | default · focus · error · disabled · readonly | Telefon uchun `+998 (__) ___-__-__` mask |
| `Select` / `Combobox` | — | + qidiruv, + bo'sh holat | 8 tadan ortiq element bo'lsa qidiruv majburiy |
| `Textarea` | auto-grow | + belgilar hisoblagichi | Retsept uchun min 6 qator |
| `Checkbox` / `Radio` / `Switch` | — | + indeterminate | Nishon zonasi 44×44px |
| `Badge` | neutral · info · success · warning · danger | — | Ikonka + matn, faqat rang emas |
| `Avatar` | initials · photo | + rang shifokor tokenidan | 24 / 32 / 40px |
| `Icon` | 20 / 24px stroke 1.75 | — | Lucide. **Emoji ikonka sifatida ishlatilmaydi** |
| `Spinner` / `Skeleton` | — | — | >400ms operatsiyada skeleton, spinner emas |
| `Tooltip` | — | 400ms kechikish | Faqat qo'shimcha ma'lumot; hech qachon yagona kanal emas |

### 6.2 Molekulalar

| Komponent | Vazifasi | Muhim detal |
|---|---|---|
| **`TicketChip`** | Navbat raqami: `[K] 14` | **Signature komponent.** Prefiks — shifokor rangidagi kvadrat, raqam — `tabular-nums`. O'lchamlar: sm 24 / md 32 / lg 64 / tv 240px. Butun tizimda bir xil ko'rinadi: ekran, chek, TV, SMS |
| **`ConnectionPill`** | SSE holati | `Jonli` (yashil, sekin pulsatsiya) / `Qayta ulanmoqda` (sariq, spinner) / `Aloqa yo'q · 14:32` (qizil + oxirgi yangilanish vaqti). **Hech qachon yashirilmaydi** |
| `PatientRow` | Bemor qatori | F.I.Sh · telefon · yosh · oxirgi tashrif. Hover'da amallar |
| `QueueCard` | Navbat kartochkasi | TicketChip + F.I.Sh + vaqt + holat yorlig'i. Drag'siz (tasodifiy siljish xavfi) |
| `DoctorPickerCard` | Shifokor tanlash | Rang + prefiks + kutayotganlar soni + taxminiy kutish |
| `StatCard` | KPI | Raqam 32px `tabular-nums` + o'zgarish belgisi (▲▼ + matn) |
| `ServiceRow` | Xizmat + narx | Narx o'ng chekkada, `tabular-nums`, ustunda tekislangan |
| `PaymentMethodCard` | Naqd / Karta / ShifoTop | 3 ta karta, radio emas — 44px+ nishon |
| `EmptyState` | Bo'sh holat | Ikonka + sabab + **amal tugmasi**. "Ma'lumot yo'q" degan yakka matn taqiqlanadi |
| `ConfirmDialog` | Tasdiq | Destruktiv amal — qizil tugma **o'ngda**, `Esc` = bekor |
| `Toast` | Bildirishnoma | 4s, pastki o'ngda. Xato — avtomatik yopilmaydi |
| `ShiftBanner` | Kassa smenasi | Ochiq: yashil chiziq + davomiylik. Yopiq: kassa amallarini bloklaydi |

### 6.3 Organizmlar

| Komponent | Tarkibi |
|---|---|
| `TopBar` | Logotip · asosiy navigatsiya · `ConnectionPill` · sana/vaqt · foydalanuvchi menyusi (rol almashtirgich bilan) |
| `SideNav` | Rolga qarab dinamik, 4 tagacha punkt, ikonka + matn (faqat ikonka emas) |
| **`QueueBoard`** | `QueueColumn[]` — S-10'ning yuragi. Virtualizatsiya 50+ kartada |
| `QueueColumn` | Shifokor sarlavhasi + "Qabulda" bloki + "Kutmoqda" ro'yxati + pauza holati |
| `NewTicketDrawer` | 2 qadamli sehrgar (S-11) |
| `TicketDetailDrawer` | Bemor + holat tarixi timeline + amallar |
| `VisitForm` | Shikoyat · tashxis · retsept · xizmatlar tanlagich · follow-up ☑ (S-21) |
| `InvoicePanel` | Xizmatlar ro'yxati + jami + to'lov tugmasi (S-30/31) |
| `FollowUpList` | Qo'ng'iroq konveyeri: bitta karta, natija tugmalari darhol ko'rinadi (S-15) |
| `TVNowCalling` / `TVUpNext` | Faqat TV sirti uchun, alohida token to'plami |
| `RolePermissionMatrix` | Spec §3 jadvalining interaktiv ko'rinishi (S-43) |
| `AuditTable` | Filtrlanadigan, eksport qilinadigan, o'zgartirilmaydigan jadval |
| `AppendOnlyNotice` | "Bu yozuv o'zgartirilmaydi. Xato bo'lsa — tuzatuvchi yozuv qo'shing" + tugma |

---

## 7. Dizayn tizimi

### 7.1 ⚠️ Token manbai

Quyidagi qiymatlar — **ShifoTop marketplace brendbukidan olinishi kerak**. Agar brendbuk mavjud bo'lsa, `--st-blue-*` va shrift oilalari o'sha yerdan almashtiriladi; qolgan tuzilma (shkala, oraliq, radius, motion) o'zgarmaydi.

### 7.2 Ranglar

```css
/* Brend — ShifoTop asosiy ko'k */
--st-blue-700:  #0B4FA8;   /* bosilgan holat */
--st-blue-600:  #1668E3;   /* PRIMARY */
--st-blue-500:  #3B86F0;   /* hover */
--st-blue-100:  #DBEAFE;   /* tanlangan fon */
--st-blue-50:   #EFF6FF;   /* eng yengil fon */

/* Neytral — matn va tuzilma */
--st-ink-900:   #0B1F3A;   /* asosiy matn · TV foni */
--st-ink-700:   #2B3F5C;   /* sarlavha */
--st-ink-500:   #64748B;   /* ikkilamchi matn */
--st-ink-300:   #CBD5E1;   /* chegara (kuchli) */
--st-ink-200:   #E2E8F0;   /* chegara (asosiy) */
--st-ink-100:   #F1F5F9;   /* zebra qator */
--st-canvas:    #F5F8FC;   /* ilova foni */
--st-surface:   #FFFFFF;   /* karta */

/* Semantik */
--st-success:   #15803D;  --st-success-bg: #DCFCE7;
--st-warning:   #B45309;  --st-warning-bg: #FEF3C7;
--st-danger:    #B91C1C;  --st-danger-bg:  #FEE2E2;
--st-info:      #0369A1;  --st-info-bg:    #E0F2FE;

/* Navbat holatlari (state machine bilan 1:1) */
--q-waiting:     var(--st-ink-500)  on var(--st-ink-100);
--q-called:      #FFFFFF            on var(--st-blue-600);   /* + pulsatsiya */
--q-in-progress: #0E7490            on #CFFAFE;
--q-completed:   var(--st-success)  on var(--st-success-bg);
--q-no-show:     var(--st-warning)  on var(--st-warning-bg);
--q-cancelled:   var(--st-ink-500)  on transparent;          /* + strikethrough */

/* Shifokor ranglari — maks 8, oq fonda ≥4.5:1 tekshirilgan */
--doctor-1: #1668E3;  --doctor-2: #7C3AED;  --doctor-3: #0E7490;  --doctor-4: #B45309;
--doctor-5: #BE185D;  --doctor-6: #15803D;  --doctor-7: #4338CA;  --doctor-8: #A16207;
```

### 7.3 Tipografiya

Uchta rol — bu **ataylab tanlangan** kombinatsiya, bitta universal shrift emas:

| Rol | Shrift | Qayerda |
|---|---|---|
| **Display** | `Manrope` ExtraBold/Bold | TV raqamlari, KPI raqamlari, sahifa sarlavhalari |
| **UI / Body** | `Inter` Regular/Medium/SemiBold | Butun interfeys matni |
| **Data** | `JetBrains Mono` Medium | Navbat kodi, hisob raqami, audit jurnali, pul summalari |

> Uchalasi ham lotin + kirill qo'llab-quvvatlaydi (o'zbek tilining ikkala yozuvi uchun kerak).
> Barcha raqamlarda `font-variant-numeric: tabular-nums` — jadval va navbat ro'yxatida raqamlar "sakramaydi".

```
Display-TV   240px / 0.95 / ExtraBold / -0.02em
Display-XL    96px / 1.0  / ExtraBold
Display-L     64px / 1.05 / Bold
H1            32px / 1.25 / Bold
H2            24px / 1.3  / SemiBold
H3            20px / 1.4  / SemiBold
Body-L        16px / 1.5  / Regular      ← o'qish uchun (forma, izoh)
Body          14px / 1.5  / Regular      ← interfeys standarti
Caption       12px / 1.4  / Medium
Label         11px / 1.45 / SemiBold / +0.04em / UPPERCASE
```

> **Chetlanish izohi:** interfeys bazasi 14px (16px emas) — bu operatsion dashboard normasi, monitor masofasi 50–60 sm. Lekin **matn hech qachon 12px dan kichik bo'lmaydi**, va foydalanuvchi o'qiydigan mazmun (shikoyat, retsept, izoh) 16px da beriladi.

### 7.4 Oraliq, radius, soya

```css
/* 4pt baza, 8pt ritm */
--sp-1: 4px;  --sp-2: 8px;   --sp-3: 12px;  --sp-4: 16px;
--sp-6: 24px; --sp-8: 32px;  --sp-12: 48px; --sp-16: 64px;

--r-input: 10px;  --r-card: 14px;  --r-modal: 18px;
--r-chip: 999px;  --r-tv: 28px;

/* Tuzilma chegara bilan beriladi, soya bilan emas */
--e-1: 0 1px 2px rgba(11,31,58,.06);
--e-2: 0 4px 12px rgba(11,31,58,.08);   /* dropdown, popover */
--e-3: 0 24px 48px rgba(11,31,58,.18);  /* modal, drawer */
--border: 1px solid var(--st-ink-200);
```

**Zichlik (density):**
| Rejim | Qator balandligi | Qayerda |
|---|---|---|
| Compact | 44px | Registratura, kassa (ko'p qator ko'rish kerak) |
| Comfortable | 56px | Shifokor, sozlamalar |

### 7.5 Motion

```css
--ease-standard: cubic-bezier(0.2, 0, 0, 1);        /* asosiy */
--ease-enter:    cubic-bezier(0.25, 0.1, 0.25, 1);  /* kirish */
--ease-overshoot:cubic-bezier(0.34, 1.56, 0.64, 1); /* pulsatsiya, tasdiq */

--dur-fast: 120ms;   /* hover, focus */
--dur-base: 200ms;   /* holat o'zgarishi */
--dur-enter: 240ms;  /* drawer, modal ochilishi */
--dur-exit: 160ms;   /* yopilish — kirishdan tez */
```

| Hodisa | Animatsiya |
|---|---|
| Navbat chaqirildi | `scale 1 → 1.04 → 1`, 600ms, 3×, `--ease-overshoot` + fon rangi o'tishi |
| Yangi ticket qo'shildi | 12px pastdan `slide-in` + fade, 240ms |
| Drawer | O'ngdan 240ms `--ease-enter`, orqa fon `backdrop: rgba(11,31,58,.32)` |
| TV raqam almashishi | Cross-fade 300ms + 24px ko'tarilish |
| Optimistik amal | Darhol (0ms) holat o'zgaradi; server xato qaytarsa — 200ms'da orqaga qaytadi + toast |

**`prefers-reduced-motion: reduce`** — barcha `transform` animatsiyalari o'chiriladi, faqat 120ms `opacity` qoladi. Pulsatsiya statik halqaga almashadi.

### 7.6 Uslub yo'nalishi — bir jumlada

> **Toza operatsion oq (clinical operational)**: oq sirtlar, 1px neytral chegaralar, soya minimal, rang **faqat ma'no tashiganda** ishlatiladi (holat va shifokor identifikatori). Glassmorphism, gradient fon, dekorativ illyustratsiya, yumaloq "friendly" ikonkalar — **yo'q**. Yagona vizual jasorat: `TicketChip` va uning TV'dagi 240px ko'rinishi.

**Taqiqlanadi:**
- Gradient tugmalar, neon accent, dark mode operator UI uchun (faqat TV to'q)
- Emoji ikonka sifatida
- Faqat ikonkali tugmalar (matn yorlig'isiz)
- Placeholder yorliq o'rnida
- 3 tadan ortiq shrift o'lchami bitta kartochkada
- Xom `#hex` yoki `px` komponent ichida — faqat tokenlar

---

## 8. Terminologiya lug'ati (UI matni)

Butun tizimda **bitta atama = bitta so'z**. Tugma nomi va uning natijasi bir xil so'z bilan ataladi.

| Tushuncha | UI matni (o'zbek) | Ishlatilmaydi |
|---|---|---|
| Queue | Navbat | Ochered, Turnaqi |
| Ticket | Navbat raqami / talon | Bilet, tiket |
| Call next | Keyingi bemorni chaqirish | Chaqir, Next |
| Waiting | Kutmoqda | Kutish |
| Called | Chaqirildi | — |
| In progress | Qabulda | Jarayonda |
| Completed | Yakunlandi | Tugadi, Bajarildi |
| No-show | Kelmadi | Yo'q, No-show |
| Cancelled | Bekor qilindi | O'chirildi |
| Walk-in | Joyida ro'yxatdan o'tgan | Oflayn |
| ShifoTop booking | ShifoTop orqali | Onlayn |
| Visit | Qabul yozuvi | Vizit |
| Complaint | Shikoyat | Jalob |
| Prescription | Retsept | Tayinlov |
| Invoice | Hisob | Faktura, invoys |
| Payment | To'lov | Oplata |
| Refund | Qaytarish | Vozvrat |
| Shift | Smena | Navbatchilik |
| Follow-up | Qayta aloqa | Follow-up |
| Escalated | Shifokorga yuborildi | Eskalatsiya |
| Attendance | Davomat | Yo'qlama |
| Audit log | Amallar jurnali | Loglar |

**Matn qoidalari:**
- Tugma — buyruq fe'li: `Navbat berish`, `To'lovni qabul qilish` (`Yuborish`, `OK` emas)
- Xato — nima bo'ldi + nima qilish kerak: *"Telefon raqami noto'g'ri. 9 raqam kiriting: 90 123 45 67"* (`Xatolik yuz berdi` emas)
- Bo'sh holat — taklif: *"Navbat bo'sh. Birinchi bemorni ro'yxatga oling."*
- Kechirim so'ralmaydi, gap sodda bo'ladi, "Iltimos" ishlatilmaydi

---

## 9. Chek va SMS shabloni (C, D sirtlari)

**Chek (58mm, 384px raster):**
```
        ShifoTop
    "Salomatlik" klinikasi
────────────────────────────
        K - 1 4
────────────────────────────
  Kardiolog · Dr. Karimov A.
  2-kabinet
  13.08.2026   09:14
  Oldingizda: 4 kishi
────────────────────────────
 Navbatni TV ekranda kuzating
   shifotop.uz/q/AB12CD
```
Raqam — chekning 40% maydonini egallaydi, qolgan hamma narsa ikkilamchi.

**SMS (1 segment, ≤70 belgi — kirill uzatilsa cheklov qattiq):**
```
ShifoTop: navbatingiz K-14. Hozir: K-9. shifotop.uz/q/AB12CD
```

---

## 10. Sifat darvozasi (Definition of Done)

Ekran tayyor hisoblanadi, agar:

- [ ] 1280×720 da gorizontal skrollsiz ishlaydi
- [ ] Barcha holatlar chizilgan: `loading` · `empty` · `error` · `offline` · `no permission`
- [ ] Klaviaturadan to'liq boshqariladi, `focus-visible` halqasi 2px ko'rinadi
- [ ] Kontrast: matn ≥ 4.5:1, katta matn ≥ 3:1, TV ≥ 7:1
- [ ] Har bir holat rang + matn (yoki ikonka) bilan beriladi
- [ ] Nishon o'lchami ≥ 44×44px, nishonlar orasi ≥ 8px
- [ ] `prefers-reduced-motion` hurmat qilinadi
- [ ] Xom `#hex` / `px` yo'q — faqat §7 tokenlari
- [ ] Barcha matn §8 lug'atiga mos
- [ ] Real-time ekranlarda `ConnectionPill` bor va uzilishni to'g'ri ko'rsatadi

---

## 11. Qisqa prompt shablonlari (nusxa olib ishlatish uchun)

**Ekran qurish:**
```
`docs/design/ui-spec.md` ni o'qi. [S-10 Navbat boshqaruvi] ekranini qur.
Stek: Next.js 14 App Router + Tailwind + shadcn/ui + TypeScript.
Tokenlar: faqat §7. Terminologiya: faqat §8. Wireframe: §5.
Barcha holatlarni chiz: loading / empty / error / offline.
Ma'lumot — mock, lekin tip `zod` sxemasi bilan aniqlangan bo'lsin.
Chiqish: bitta `app/(reception)/queue/page.tsx` + `components/queue/*`.
```

**Figma'da qurish:**
```
`ui-spec.md` §7 tokenlarini Figma Variables sifatida yarat (color, spacing, radius, typography).
Keyin §5 dagi [S-50 TV Display] ni 1920×1080 frame sifatida qur.
Har bir takrorlanuvchi element — Component (TicketChip: sm/md/lg/tv variantlari bilan).
Auto Layout majburiy, absolute positioning faqat TV pastki chizig'i uchun.
```

**Dizayn tekshiruvi:**
```
Ushbu ekranni `ui-spec.md` §10 sifat darvozasi bo'yicha tekshir.
Har bir buzilgan band uchun: nima buzilgan → nega muhim → aniq tuzatish (token nomi bilan).
```
