# ShifoTop Clinic OS — UI prototip

Klinikalar uchun ish jarayonini tartibga soluvchi tizimning interaktiv UI prototipi.
Bemor eshikdan kirganidan to qayta ko'rikka qaytgunicha bo'lgan butun yo'l — bitta tizimda.

**Jonli demo:** https://ARTCODERSGRUP.github.io/shifotop-clinic-os/

---

## Ichida nima bor

Bitta `index.html` fayl — build talab qilmaydi, tashqi bog'liqlik yo'q (shriftlardan tashqari).
Ochilgach chapdagi navigatordan **27 ta ekran/holat** ni ko'rish mumkin.

### Ekranlar

| Guruh | Ekranlar |
|---|---|
| Boshlanish | Oqim xaritasi · S-00 Kirish |
| Registratura | S-10 Navbat boshqaruvi · S-11a/b Yangi navbat (2 qadam) · S-12 Navbat kartochkasi · S-10c Foydalanuvchi menyusi · S-13 Bemorlar bazasi · S-14 Bemor kartasi |
| Qo'ng'iroqlar | S-15 Qayta aloqa · S-16 Natija belgilash · S-17 Qayta ko'rik tasdiqlash · S-17b Vaqt tanlash |
| Shifokor | S-20 Mening navbatim · S-20b Qayta chaqirish · S-21 Qabul yozuvi (xizmat, narx, qayta ko'rik sanasi) |
| Kassa | S-30 Hisoblar · S-31 To'lovni qabul qilish · S-32 Smenani yopish |
| Profil | S-43 Mening profilim |
| Kutish zali | S-50 TV navbat ekrani · S-50b Bo'sh holat · S-51 Ekranni ulash |
| Chekli holatlar | Yuklanmoqda · Aloqa uzildi · 403 · Xatolik |

### Rollar

5 ta ichki rol: **Rahbar · Registratura · Shifokor · Kassir · Buxgalter**
2 ta tashqi: bemor (SMS havolasi orqali), ShifoTop platforma admini.

---

## Asosiy oqim

```
Kirish  →  Navbat berish  →  Chek + SMS  →  TV ekran
                                              ↓
                                        Shifokor chaqiradi
                                              ↓
                        Qabul yozuvi: xizmat + narx + qayta ko'rik sanasi
                                    ↓                        ↓
                            Kassa: to'lov + chek     Qayta ko'rik sanasi
                                                             ↓
                                          Sanadan 1 kun oldin: tasdiqlash qo'ng'irog'i
                                                 "Kelasizmi? Nechida?"
                                                             ↓
                                          Vaqt tanlanadi → SMS → oldindan yozuv
                                                             ↓
                                                  O'sha kuni navbatga tushadi
```

Parallel ravishda: davolashdan 7 kun keyin **qayta aloqa qo'ng'irog'i** —
✅ Yaxshi → bemorga izoh so'rovi · ⚠️ Muammo → shifokorga vazifa.

---

## Interaktivlik

Prototip statik rasm emas — quyidagilar haqiqatan ishlaydi:

- Yuqoridagi tab'lar (Navbat / Bemorlar / Qo'ng'iroqlar) — haqiqiy o'tish
- `+ Yangi navbat` yoki **`F2`** → drawer ochiladi, 1↔2 qadam almashadi
- Navbat kartasi ustiga hover → chaqirish / kelmadi / chek tugmalari
- Kartani bosish → holat tarixi timeline bilan drawer
- Foydalanuvchi menyusi → rol almashtirgich (Registratura ↔ Kassir)
- `● Jonli` belgisini bosish → Jonli → Qayta ulanmoqda → Aloqa yo'q
- Pastdagi panel → Oddiy / Yuklanmoqda / Bo'sh / Aloqa yo'q variantlari
- **`Esc`** — har qanday oynani yopish

---

## Dizayn tizimi

To'liq spetsifikatsiya: [`ui-spec.md`](./ui-spec.md)

| Element | Qiymat |
|---|---|
| Uslub | Toza operatsion oq — oq sirtlar, 1px chegara, soya minimal |
| Primary | `#1668E3` |
| Tipografiya | Manrope (display/TV) · Inter (UI) · JetBrains Mono (kod, pul, vaqt) |
| Grid | 4pt baza, 8pt ritm |
| Viewport | Operator 1440×900 (min 1280) · TV 1920×1080 |
| Kontrast | Operator ≥ 4.5:1 · TV ≥ 7:1 |

**Signature komponent:** `TicketChip` — navbat raqami 24px dan 240px gacha bir xil mantiqda
o'sadi va butun zanjirda qayta ishlatiladi: ekran → chek → TV → SMS.

---

## Ishga tushirish

Build kerak emas:

```bash
git clone https://github.com/ARTCODERSGRUP/shifotop-clinic-os.git
cd shifotop-clinic-os
python3 -m http.server 8080
# → http://localhost:8080
```

Yoki `index.html` ni brauzerda to'g'ridan-to'g'ri oching.

---

## Holat

Bu — **Faza 1 (MVP) UI prototipi**. Backend yozilmagan, ma'lumotlar demo.
Keyingi qadam: ekranlarni Next.js 14 + Tailwind + shadcn/ui komponentlariga ko'chirish.

## Litsenziya

Proprietary — ArtCodersGrup / ShifoTop.
