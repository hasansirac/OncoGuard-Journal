# OncoGuard-AI — Dergi Sürümü Durum Kartı

**Ana proje:** https://github.com/hasansirac/OncoGuard-AI (public, `conference-v1.0` etiketi)
**Bu depo:** dergi çalışması, private

**Son güncelleme:** 13 Eylül 2026
**Bu dosyanın amacı:** Projeye aradan zaman geçtikten sonra dönen herkesin (sen, hoca,
ya da yeni bir sohbetteki asistan) 60 saniyede nerede olduğumuzu anlaması.

---

## Tek cümlede

Konferans sürümü (IDAP'26) kabul edildi ve dondurulacak; dergi sürümü için riskleri tek
tek literatüre dayandıran yeni bir zamansal veri seti (`dataset-v3`) sıfırdan kuruluyor.

---

## Belgeler

| Dosya | Ne işe yarar |
|---|---|
| `PROGRAM.md` | Kilitli çalışma programı, v3.2. 14 eksen, kapsam kararları, kabul kriterleri, kaynaklar |
| `STATUS.md` | Bu dosya. Nerede olduğumuz |
| `spec/` | Risk başına klinik spesifikasyonlar (henüz yazılmadı) |

Program belgesi günlük çalışırken açılmaz; referanstır. Günlük iş `spec/` ve kod tarafında.

---

## Şu an neredeyiz

| Eksen | Durum |
|---|---|
| 0 — Konferans sürümünü dondurma | **Yapılmadı** |
| 1 — v2.1 bilimsel denetimi + rule baseline | Kısmen: kod denetimi yapıldı (aşağıda), rule baseline çalıştırılmadı |
| 2 — Ortak zamansal protokol | Başlanmadı |
| 3 — Risk spesifikasyonları | Başlanmadı. İlk risk: febril nötropeni |
| 4-14 | Başlanmadı |

**Sıradaki iş:** Eksen 0 (git etiketi + checksum + `journal-v3` dalı), sonra Eksen 1'de
kural motorunu mevcut v2.1 verisinde çalıştırıp rule-vs-XGBoost sayısını almak.

---

## Kilitli kapsam kararları

- Yaş: 18-90 yetişkin. Yaşa koşullu tanı dağılımı. Eklenen tanılar: testis germ hücreli
  tümör (BEP), Hodgkin lenfoma (ABVD), osteosarkom (MAP), Ewing sarkomu (VDC/IE)
- Pediatri kapsam dışı, ayrı modül olarak sonraki çalışmaya bırakıldı
- Dokuz bağımsız risk + bir overall özet katmanı. Overall onuncu model değil
- Kaşeksi kapsamda; hedef prekaşeksiden kaşeksiye 30 günlük **evre geçişi**. Refrakter
  kaşeksi kapsam dışı
- Malnütrisyon ve kaşeksi ayrı hedefler, ayrı mekanizmalar, örtüşmeleri normal
- Dört renk (Green/Yellow/Orange/Red) **korunuyor**, ama artık eğitim etiketi değil:
  model skoru üzerinde validation'da dondurulan üç kesim noktası
- Etiket yalnızca gizli süreçten ve `t0` **sonrasındaki** olaydan türer
- Feature yalnızca `t0` ve öncesinden, risk maskesiyle sınırlı
- Eğitim ve servis tek kod kaynağı; ikinci kopya yasak
- ML'in kural üzerine ek değeri deneylerden önce dondurulmuş ölçütlerle test edilir;
  geçemeyen risk kural tabanlı çalışır
- v2.1 sonuçları `historical conference results` olarak ayrı sunulur, v3 ile doğrudan
  performans deltasına sokulmaz

---

## Eksen 1 kod denetiminde bulunanlar

Bunlar v2.1 kaynak kodunda ölçülerek doğrulandı. Dergi sürümünde düzeltilecek sorunların
listesi budur.

| # | Bulgu | Kanıt |
|---|---|---|
| 1 | Etiket = `0.7 × gizli şiddet + 0.3 × gözlenebilir ipucu` | `make_labels`. Makale bu oranı hiç söylemiyor; söylenirse macro-F1'in neden ~0.70'te tavan yaptığı açıklanır |
| 2 | Beş riskte ipucu terimi tek bir model sütununun kendisi | Renal ↔ `CreatinineGrade` ρ=0.85; Hepatik ↔ `ASTGrade` ρ=0.82; TedaviGecikme ↔ `ANCGrade` ρ=0.71 |
| 3 | irAE metriği sert kapıyla şişmiş | Tedavi immüno değilse etiket deterministik 0. Satırların %76.2'si böyle, etiketlerin %90'ı Green. 0.963 accuracy büyük ölçüde bir one-hot sütunu okumak |
| 4 | 30 günlük kaşeksi feature'ı hiç 30 günden hesaplanmamış | Generator hasta başına 7-14 gün üretiyor (`n_days = rng.integers(7, 15)`); `_window()` pencere log sayısından büyükse listenin tamamını döndürüyor. `WeightLossPct30` ↔ `WeightLossPct7` korelasyonu 0.943, kayıtların %35.3'ünde birebir aynı |
| 5 | On model aynı 87 feature'ı kullanıyor | `feature_columns.json`. 3 günlük model 30 günlük feature'a erişiyor; serviste eksik olanlar eğitim medyanıyla dolduruluyor |
| 6 | Eğitim ve servis feature kodu ayrışmış | İki kopya arasında 497 satır fark; servis kopyasında `_calendar_window`, `_parse_log_date`, `_sort_unique_by_log_date` var, eğitimde yok. `main.py` docstring'i "aynı kod" diyor |
| 7 | Enfeksiyon ve FN aynı sinyalden sürülüyor | İkisinin de ipucu `FeverAndLowANCFlag` |
| 8 | Veri gerçek zaman serisi değil | Her satır bir hasta-döngüsü anlık görüntüsü, `CycleDay` yalnızca 5-13 arası, feature'lar önceden özetlenmiş |

**Veri seti künyesi:** 14.998 satır × 87 kolon, 10 etiket, yaş 35-81, 12 senaryo, 7 kanser
tipi, 6 tedavi tipi.

---

## Henüz kapanmamış konular

- Eksen 2'deki sayısal parametreler: toplam hasta, çizelge uzunluğu, hasta başına `t0`,
  risk başına minimum olay. Formal örneklem hesabıyla (Riley yöntemi) belirlenecek
- Kaşeksi için ayrı/daha uzun kohort gerekip gerekmediği. 90 günlük çizelgede geçerli
  `t0` aralığı yalnızca 30-60. günler
- Senaryo holdout birimi: senaryo ailesi uygulanabilir değil, hasta arketipi veya
  kombinasyon olmalı
- Osteosarkom ve Ewing için erişkin kaynaklı kılavuz (mevcut PDQ özetleri pediatrik
  editöryel kurul kaynaklı)
- Onkolog erişimi (Eksen 12B) ve etik kurul gerekliliği
- Sistem adı değişikliği kararı

---

## Yeni bir sohbete başlarken

Şunu paylaş, yeterli olur:

1. Repo bağlantısı: `https://github.com/hasansirac/OncoGuard-AI.git`
2. `PROGRAM.md` ve bu dosya
3. Tek cümle: "Programı v3.2'de kilitledik, şu eksendeyiz, şunu yapacağız."

Dergi çalışması ayrı ve private bir depoda tutulur: `OncoGuard-Journal`. Ana proje deposuna
dergi çalışmasıyla ilgili hiçbir dosya eklenmez.
