# OncoGuard AI Dergi Sürümü Nihai Çalışma Programı

**Sürüm:** v3.2 — kilitlenmiş çalışma programı. Kapsam kararları kapalıdır ve bu belge
Eksen 0 ile Eksen 14 boyunca referans alınacaktır. Sayısal parametreler Eksen 2'de kaynak,
veri yeterliliği ve önceden tanımlı analiz kurallarına göre belirlenecektir; bu, kapsamın
yeniden açılması anlamına gelmez.

**Kilitli, değiştirilemez demek değildir; değişikliğin kontrollü olması demektir.**
Belge aşağıdaki gerekçelerden biriyle değiştirilebilir:

- Eksen 1 denetimi veya Eksen 3 literatür çalışması bir kapsam kararını geçersiz kılan bir
  bulgu üretirse
- Örneklem hesabı veya veri yeterliliği analizi bir hedefin desteklenemeyeceğini gösterirse
- Teknik uygulanabilirlik engeli çıkarsa (ölçülemeyen değişken, karşılığı olmayan veri
  kaynağı, gerçekleştirilemeyen zaman bütçesi)
- Güvenlik veya klinik zarar riski tespit edilirse
- Yeniden üretilebilirlik sorunu bir tasarım kararını sürdürülemez kılarsa
- Onkolog değerlendirmesi bir tasarım kararını değiştirmeyi gerektirirse

Her değişiklik için şunlar zorunludur: gerekçe, etkilenen eksenler, geçersiz hale gelen
sonuçlar ve yeni sürüm numarası. Değişiklikler sürüm notunda tutulur; sessiz düzenleme
yapılmaz.

## 1 Amaç ve değişmeyecek ana fikir

Bu program OncoGuard AI projesinin çalışan konferans sürümünü bozmadan daha güçlü,
daha savunulabilir ve onkolog değerlendirmesine hazır bir dergi sürümü geliştirmek
için hazırlanmıştır.

Projenin ana fikri değişmez:

- Doktor, hastanın klinik profilini, tedavi bilgisini ve laboratuvar sonuçlarını girer.
- Hasta, mobil uygulama üzerinden günlük belirtilerini, beslenmesini ve hidrasyonunu kaydeder.
- Sistem yalnızca tahmin anında erişilebilir geçmiş verileri kullanır.
- Akut, subakut ve uzun dönemli riskler kendilerine ait zaman pencerelerinde değerlendirilir.
- Yetersiz veri varsa tahmin zorlanmaz ve monitoring sonucu döndürülür.
- Kural tabanlı güvenlik uyarıları ile makine öğrenmesi tahminleri birbirinden ayrılır.
- Son karar ve klinik değerlendirme doktora aittir.

Bu çalışma mevcut projeyi değiştirmek yerine onun yanında yeni bir sürüm oluşturur.
Veri ve yapay zeka katmanı önemli ölçüde geliştirilecek; mobil uygulama, backend,
doktor paneli ve genel iş akışı mümkün olduğu ölçüde korunacaktır.

## 2 Bilimsel ve klinik kapsam kararları

### 2.1 Hedeflenen sürüm

- Konferans sürümü değişmeden korunacaktır.
- Yeni geliştirme dalı `journal-v3` olacaktır.
- Yeni veri paketi `dataset-v3` olarak adlandırılacaktır.
- Yeni modeller `model-package-v3` altında sürümlenecektir.
- Eski ve yeni model paketleri gerektiğinde ayrı ayrı çalıştırılabilecektir.

### 2.2 Hasta kapsamı

- Ana dergi çalışması yetişkin onkoloji hastalarıyla sınırlandırılacaktır.
- V3 generatorünün çalışma aralığı 18-90 yaş olacaktır. Bu sınır konfigürasyonla
  değiştirilebilir, ancak makale deneylerinde dondurulacaktır.
- Yaşlar eşit veya bağımsız rastgele dağıtılmayacaktır. 18-34, 35-49, 50-64, 65-74,
  75-84 ve 85-90 grupları için kaynaklandırılmış örnekleme dağılımları kullanılacaktır.
- Yaş ile kanser türü ve tedavi rejimi birbirinden bağımsız üretilmeyecektir.
- Özellikle 18-34 yaş grubunda tanı ve tedavi dağılımları ayrıca tanımlanacaktır.
- Yaşa bağlı komorbidite, vücut ölçümü, renal fonksiyon, performans durumu ve tedavi
  toleransı ilişkileri ortak generator içinde tanımlanacaktır.
- Her yaş grubunda yeterli pozitif ve negatif olay bulunduğu otomatik olarak kontrol
  edilecek; sonuçlar yaş gruplarına göre ayrıca raporlanacaktır.

#### Yaşa uygun tanı genişlemesi

18-34 bandı açıldığı için genç yetişkin onkolojisindeki tanılar ve tedaviler veri üretiminde
temsil edilmelidir. Genç yaşta nadir görülen bir tanı imkansız sayılmaz; literatürle uyumlu
çok düşük olasılıkla üretilir. Amaç nadir olayları silmek değil, yaşa koşullu görülme
sıklıklarını ve tedavi bağlamını gerçekçi biçimde temsil etmektir.

**Seçim ölçütü:** Tanı ve rejim dağılımları, modele güçlü sinyal sağlama amacıyla değil,
epidemiyoloji ve klinik uygulanabilirlik esas alınarak oluşturulur. Nadir bir grup eğitimde
olay yeterliliği için bilinçli olarak fazla örneklenirse oranı, gerekçesi ve örnekleme ağırlığı
dataset card içinde açıkça belgelenir. Dokunulmamış test dağılımı hedef popülasyona göre
üretilir; gerekli olduğunda ham ve hedef dağılıma göre ağırlıklandırılmış sonuçlar birlikte
raporlanır.

| Tanı | V3 yetişkin örnekleme yoğunluğu | Temsili tedavi bağlamı |
|---|---|---|
| Testis germ hücreli tümör | 18-35 | BEP (bleomisin, etoposid, sisplatin) |
| Hodgkin lenfoma | 18-34 ve 60 üzeri | ABVD veya güncel evreye ve bağlama uygun alternatif |
| Osteosarkom | 18-30 | MAP (yüksek doz metotreksat, doksorubisin, sisplatin) |
| Ewing sarkomu | 18-30 | VDC/IE (vinkristin, doksorubisin, siklofosfamid; ifosfamid, etoposid ile dönüşümlü) |

Osteosarkom ve Ewing sarkomu **ayrı tanı ve ayrı rejim** olarak tanımlanır; tek bir
"sarkom" satırında birleştirilmeleri klinik olarak hatalıdır ve toksisite profillerini
karıştırır. Tablodaki rejimler evrensel veya her evre için tek standart olarak kabul
edilmez; ülke, kılavuz sürümü, hastalık evresi ve tedavi basamağı risk spesifikasyonunda
ayrıca sürümlenir.

Mevcut listedeki lösemi (genç erişkinde ALL) ve meme kanseri (genç meme kanseri) bu bandı
kısmen zaten karşılamaktadır.

#### Yaş bandı yeterlilik kuralı

Her yaş bandı için minimum örneklem ve minimum pozitif olay sayısı Eksen 2'de sayıyla
belirlenecektir. Bir bant bu eşiği karşılamıyorsa o bant için kararsız bir performans
metriği yorumlanmaz; örneklem ve olay sayısı ile keşifsel sonuçlar açıkça bildirilir. Bu
kural özellikle 85-90 bandı için geçerlidir.

Altmış beş yaş ve üzerindeki hastalarda kronolojik yaş tek başına kırılganlık göstergesi
olarak kullanılmaz. Gerçek sistemde erişilebildiği ölçüde fiziksel işlev, komorbidite,
çoklu ilaç kullanımı, beslenme ve bilişsel/psikososyal kırılganlık alanları kaynakları ve
eksiklik durumlarıyla birlikte temsil edilir. Yaş tek başına bir riski `not_applicable`
yapmaz ve tedaviyi otomatik belirlemez.

- Pediatrik hastalar yetişkin generatorüne yalnızca yaş değeri değiştirilerek eklenmeyecektir.
- Pediatrik sürüm; pediatrik referans aralıkları, tanılar, tedaviler ve uzman değerlendirmesi
  gerektiren ayrı bir gelecek çalışması olacaktır.

### 2.3 Nihai risk mimarisi

Dergi sürümünde dokuz bağımsız klinik risk modülü ve bunların üzerinde bir birleşik genel
kötüleşme özeti bulunacaktır. Böylece sistem dokuz risk sonucu ve bir özet olmak üzere
toplam on kullanıcı çıktısı döndürecektir. Her modülde aday ML modeli araştırılacak;
önceden tanımlı ek değer ve güvenlik ölçütlerini karşılamayan riskte journal-v3 prototipi
kural tabanlı çıktıyı kullanacaktır.

Bağımsız risk modülleri:

1. Febrile neutropenia
2. Infection
3. Dehydration
4. Renal toxicity
5. Hepatic toxicity
6. Immunotherapy related adverse event
7. Malnutrition
8. Treatment delay
9. Cachexia progression

Birleşik çıktı:

10. Overall clinical deterioration

Overall clinical deterioration bağımsız bir hastalık etiketi veya onuncu XGBoost
modeli olarak ele alınmayacaktır. Dokuz riskin sonuçları, klinik bağlam, veri kalitesi
ve güvenlik kurallarından türetilen açık ve test edilebilir bir üst düzey füzyon çıktısı
olacaktır.

#### Overall özetinin rolü ve değerlendirilmesi

**Üründeki rolü:** Overall deterioration doktor ekranındaki birleşik, açıklanabilir ve
çok ufuklu bir **bakım önceliği özeti**dir. Kendi başına eğitilmiş onuncu tahmin modeli,
tek bir hastalık olasılığı veya bağımsız klinik tanı değildir. Sistem dokuz risk tahmini
ve bir özet döndürür.

Üç farklı zaman ölçeği tek bir sahte olasılıkta birleştirilmeyecektir:

| Özet katmanı | İçerik |
|---|---|
| Acil | 72 saatlik akut riskler ve aktif güvenlik uyarıları |
| Yakın dönem | 7 günlük subakut riskler |
| Uzun dönem | 30 günlük kaşeksi ilerleme riski |

Her risk kartı kendi tahmin ufkunu korur. Overall özet, bu katmanlardan hangisinin doktor
incelemesi gerektirdiğini ve hangi veri kalitesi sorununun bulunduğunu açıkça gösterir;
30 günlük kaşeksi çıktısı 7 günlük olasılık gibi ağırlıklandırılmaz.

**Birincil değerlendirme:** Füzyon için tahmin performansından önce mantıksal tutarlılık,
monotonluk, acil uyarıyı bastırmama, `not_applicable` ve `monitoring` davranışı, eksik veri
güvenliği, açıklanabilirlik ve onkologların okunabilirlik/eyleme geçirilebilirlik görüşü
ölçülür. Onkolog değerlendirmesinde yarar göstermeyen veya kafa karıştıran overall özet
sadeleştirilir ya da doktor panelinden kaldırılır.

**İkincil keşifsel değerlendirme:** Generator ayrıca `t0` sonrası 7 gün içinde plansız
hastane yatışı veya acil servis başvurusu olayını üretebilir. Bu olay dokuz etiketin
matematiksel birleşimi değildir; ancak gerçek hayata uygun biçimde enfeksiyon, febril
nötropeni, dehidratasyon, toksisite ve diğer hasta süreçlerinden nedensel olarak
etkilenebilir. Risk süreçleriyle ilişkisini yapay olarak koparmak yasaktır.

Bu ikincil olay için overall modeli eğitilmez. Yalnızca 72 saatlik ve 7 günlük bileşenler,
önceden dondurulmuş füzyon kuralı, basit `en yüksek risk` yaklaşımı ve güvenlik kuralları
dokunulmamış testte karşılaştırılır. Otuz günlük kaşeksi çıktısı bu 7 günlük karşılaştırmada
yalnızca önceden tanımlanmış bağlamsal değişken olarak incelenebilir. Sonuç sentetik iç
tutarlılık analizi olarak sunulur; klinik dış doğrulama veya gerçek hastane kullanım kanıtı
olarak yorumlanmaz.

### 2.4 Kaşeksi modülü

Cachexia projede korunacak, ancak malnütrisyonun ileri seviyesi veya yalnızca 30 günlük
kilo kaybı olarak ele alınmayacaktır. Ayrı bir uzun dönemli risk modülü olarak baştan
kurulacaktır.

#### Klinik dayanak

Fearon ve arkadaşlarının uluslararası konsensüsü (Lancet Oncology, 2011) kaşeksiyi,
konvansiyonel beslenme desteğiyle tam olarak geri döndürülemeyen ve ilerleyici fonksiyonel
bozulmaya yol açan, iskelet kası kaybıyla tanımlanan multifaktöriyel bir sendrom olarak
tanımlar. Patofizyolojisi, azalmış besin alımı ile anormal metabolizmanın değişken
bileşiminden doğan negatif protein ve enerji dengesidir.

Kabul edilen tanı ölçütü:

- Önceki altı ayda yüzde 5'ten fazla istemsiz kilo kaybı, veya
- BMI 20 kg/m² altındaysa önceki altı ayda yüzde 2'den fazla istemsiz kilo kaybı, veya
- Azalmış kas kütlesi varsa önceki altı ayda yüzde 2'den fazla istemsiz kilo kaybı

Sendrom prekaşeksi, kaşeksi ve refrakter kaşeksi evrelerinden geçerek ilerler.

#### Malnütrisyondan ayrımı

Bu ayrım **iki sendromu karşıt göstererek** kurulamaz. GLIM konsensüsü (Cederholm ve ark.,
2019) malnütrisyonu iki fenotipik ve iki etiyolojik ölçüt üzerinden tanımlar; etiyolojik
ölçütler azalmış besin alımı veya asimilasyonun yanında **hastalık yükü ve inflamasyonu**
da içerir. Yani inflamasyon malnütrisyon tanımının da parçasıdır ve iki sendrom aynı
hastada örtüşebilir.

Ayrım bu nedenle hedef, mekanizma vurgusu ve zaman penceresi üzerinden kurulur:

| | Malnütrisyon | Kaşeksi |
|---|---|---|
| Tanı çerçevesi | GLIM (fenotipik + etiyolojik ölçütler) | Fearon 2011 konsensüsü |
| Mekanizma vurgusu | Alım ve asimilasyon açığı baskın; inflamasyon eşlik edebilir | Kas kaybı ve katabolik sürükleyici baskın |
| Beslenme desteğine yanıt | Genellikle daha iyi, ancak garanti değil | Konvansiyonel destekle tam geri döndürülemez |
| Zaman ölçeği | Günler ve haftalar | Haftalar ve aylar |
| Bizim tahmin hedefimiz | 7 günlük risk | 30 günlük evre geçişi |

İki hedefin olay tanımları, zaman pencereleri ve klinik kullanım amaçları ayrıdır; ortak
feature kullanmaları beklenir ve sorun değildir. Örtüşme bir tasarım hatası değil,
modelleme görevinin kendisidir.

#### Kas kaybının ölçülmesi

Fearon çerçevesi azalmış kas kütlesini tek bir yönteme indirgemez. L3 düzeyinde BT tabanlı
iskelet kası indeksi, DEXA ile apendiküler kas indeksi, biyoimpedansla yağsız kütle indeksi
ve antropometrik orta üst kol kas alanı birbirinden farklı ölçüm seçenekleridir. Mevcut v3
veri kaynaklarımızda görüntüleme, DEXA, biyoimpedans ve kaliper ölçümü bulunmadığından bu
yöntemler bu sürümün kapsamı dışındadır; gelecekte hastane entegrasyonuyla eklenmeleri
teknik olarak engellenmez.

Huo ve arkadaşlarının 5769 katılımcılı çok merkezli kohortunda (INSCOC, 2025) Fearon
ölçütü ile altı değiştirilmiş ölçüt karşılaştırılmıştır: orta üst kol kas alanı (MAMA),
yağsız kütle indeksi (FFMI), baldır çevresi (CC), el kavrama gücü (HGS), nötrofil-lenfosit
oranı (NLR) ve azalmış kas kütlesi ölçütünün tamamen çıkarılması. Altı değiştirilmiş
ölçütün tamamı yeterli performans göstermiş, duyarlılıkları yüzde 82.4 ile 90.7 arasında
değişmiştir.

**Kanıt düzeyinin dürüst okunması.** Bu çalışma tanısal ölçütleri ve mortalite ilişkisini
incelemiştir; evde ölçüm güvenilirliğini veya 30 günlük evre geçiş tahminini doğrulamaz.
Ayrıca her antropometrik ölçümü aynı düzeyde desteklemez:

- **Baldır çevresi** altı değiştirilmiş ölçütten biri olarak doğrudan değerlendirilmiştir.
  Kanıt dayanağı en güçlü antropometrik göstergemizdir.
- **MAMA**, orta üst kol çevresinin yanında **triseps deri kıvrımı** ölçümünü gerektirir.
  Deri kıvrımı kaliper ister; hasta evde ölçemez. MAMA bu nedenle kapsam dışıdır.
- **Orta üst kol çevresi tek başına** bu çalışmada ayrı bir ölçüt olarak değerlendirilmemiştir.
  GLIM bağlamındaki hastane çalışmalarında azalmış kas kütlesi göstergesi olarak kullanımı
  desteklenmektedir, ancak kaşeksi tanısı için bu kohortta doğrudan doğrulanmamıştır.
- **Hiçbir çalışma**, bu ölçümlerin hasta tarafından evde kendi kendine yapılmasının
  geçerliliğini göstermemiştir. Bu bizim varsayımımızdır ve öyle işaretlenmelidir.

Buna göre aday alanlar ve veri yeterliliği kuralları:

| Gösterge | Veri kaynağı | Statü | Kanıt dayanağı |
|---|---|---|---|
| Güncel ağırlık, boy ve BMI | Doktor onaylı baseline | **Asgari readiness için zorunlu** | Kilo değişimi ve düşük BMI bileşeni |
| Önceki altı ay içinden en az bir güvenilir ağırlık veya olağan ağırlık | Tarihli hastane kaydı ya da kaynağı işaretlenmiş doktor onaylı beyan | **Asgari readiness için zorunlu** | İstemsiz kilo kaybının hesaplanması |
| İlave 1, 3 ve 6 aylık kilo çıpaları | Doktor onaylı geçmiş ölçüm | **Tercih edilen / opsiyonel** | Trajektoriyi güçlendirir; üçünün birden bulunması şart değildir |
| İştah, besin alımı, aktivite ve performans | Hasta girdisi ve doktor baseline'ı | **Çekirdek aday** | Prekaşeksi ve fonksiyonel bağlam |
| Nötrofil-lenfosit oranı | Laboratuvar | **Çekirdek aday** | Değiştirilmiş tanı ölçütlerinden biri; ileriye dönük değeri ayrıca test edilir |
| CRP ve albümin | Laboratuvar | **Aday / eksik olabilir** | İnflamasyon ve hastalık yükü bağlamı; özgül olmayan göstergelerdir |
| Baldır çevresi | Hasta girdisi, haftalık | **Opsiyonel** | Değiştirilmiş ölçüt olarak doğrudan değerlendirilmiş; ev ölçümü varsayım |
| Orta üst kol çevresi | Hasta girdisi, haftalık | **Opsiyonel / deneysel** | Kaşeksi bağlamında doğrudan doğrulanmamış |
| El kavrama gücü | Doktor onaylı ölçüm | **Opsiyonel** | Dinamometre gerektirir; hasta uygulamasından toplanamaz |
| MAMA, FFMI, ASMI | — | **Kapsam dışı** | Kaliper, biyoimpedans veya görüntüleme gerektirir |

`Readiness için zorunlu` olmak ile `nihai ML feature'ı olmak` birbirinden ayrıdır. Hiçbir
aday değişken, ablation, eksiklik ve dağılım kayması sonuçları görülmeden nihai feature
olarak kabul edilmez. NLR, CRP veya albümin eksikliği tek başına tahmini otomatik olarak
engellemez; risk spesifikasyonunda tanımlanan asgari veri yeterliliği uygulanır.

Opsiyonel işaretli her antropometrik gösterge için şunlar zorunludur:

- Standart ölçüm talimatı (uygulamada gösterilecek, hangi kol, hangi pozisyon, hangi saat)
- Ölçüm hatası senaryosu testi (Eksen 8 doğrulama testlerine dahil)
- Eksik olduğunda modelin çalışmaya devam ettiğinin gösterilmesi
- Bu göstergeler olmadan ve onlarla birlikte ayrı performans raporu

Ev ortamında hasta tarafından yapılan antropometrik ölçümün geçerliliği doğrulanmamış bir
varsayımdır ve makalenin sınırlılık bölümüne bu şekilde yazılacaktır. CRP, albümin ve NLR
tek başına kaşeksi kanıtı veya nedensel katabolizma ölçümü olarak sunulmayacaktır.

#### Tahmin görevi

Hedef "bu hastada bugün kaşeksi var mı" **değildir**. Bu soru, konferans sürümündeki
hatanın tekrarı olur: mevcut durumu aynı kayıttan sınıflamak.

Hedef evre geçişidir: `t0` itibarıyla prekaşeksi durumundaki hasta, sonraki 30 günlük
dönemde kaşeksi evresine geçecek mi. Bu gerçek bir gelecek olayıdır. Otuz günlük ufkun
müdahale açısından uygunluğu literatür ve onkolog görüşüyle Eksen 2'de doğrulanacak;
doğrulanana kadar araştırma protokolü varsayımı olarak işaretlenecektir.

Bu tasarımın yan faydası önemlidir: Fearon ölçütü mevcut durumu **tanımlar**, geçişi
tahmin etmez. Otuz günlük geçiş tahmini bizim araştırma hedefimizdir ve bu ayrım makalede
açıkça belirtilecektir. Makine öğrenmesinin tanısal kural üzerine ek değer sağlayıp
sağlamadığı önceden tanımlı bir araştırma sorusu olarak test edilecektir.

#### Operasyonel tanımlar

Aşağıdakiler `journal/spec/09_cachexia.md` içinde sayı ve eşikle tanımlanmadan modül
kodlanmaz. Fearon ölçütü mevcut kaşeksiyi tanımlar; prekaşeksi ve geçiş olayı bizim
operasyonel tanımlarımızdır ve öyle etiketlenecektir.

| Tanımlanacak | İçermesi gerekenler |
|---|---|
| `t0` uygunluk kriteri | Hangi hasta, hangi günde geçerli bir kaşeksi tahmin noktası oluşturur; asgari aktif kayıt günü; asgari geçmiş kilo çıpası sayısı |
| Prekaşeksi operasyonel tanımı | Kilo kaybı, iştahsızlık ve metabolik değişim göstergeleri; literatür çerçevesi ile generator varsayımlarının açık ayrımı; hangi koşulun zorunlu veya yeterli olduğu |
| Hariç tutma | `t0` anında zaten kaşeksi ölçütünü karşılayan hasta bu modelin hedef popülasyonunda değildir; bu durumda çıktı `not_applicable` olur |
| 30 günlük geçiş olayı | Fearon ölçütünün ilk karşılandığı tarih; ölçütün hangi bileşeninin tetiklediği kaydedilir |
| Birincil model hedefi | `t0` sonrasındaki 30 gün içinde prekaşeksiden kaşeksiye geçiş: evet/hayır; olay tarihi ayrıca korunur |
| Green / Yellow / Orange / Red üretimi | Eğitim etiketi değildir. Validation verisinde dondurulan üç kesim noktasıyla model skorundan yalnızca arayüzde üretilir; eşikler klinik maliyetleri gözetir |
| Eksik takip | Otuz günlük ufuk tamamlanmadan gözlem kaybı sansürlenir; sessizce negatif etiketlenmez |
| Ölüm ve diğer olaylar | Kaşeksi geçişinden önce ölüm rakip olaydır. Plansız yatış ve tedavi kesintisi, protokol gözlemi gerçekten sonlandırmadıkça otomatik rakip olay sayılmaz; tarihli eşlik eden olay veya zamanla değişen bağlam olarak tutulur |
| Refrakter evre | Refrakter kaşeksi ölçütünü karşılayan hasta hedef popülasyonda değildir, `not_applicable` döner |

Birincil sabit ufuk analizinde yalnızca sonucu gözlenebilen örnekler kullanılacak veya
önceden belirlenmiş sansürleme ağırlıkları uygulanacaktır. Olay zamanı ve sansürleme
korunuyorsa ikincil analizde cause-specific Cox, Fine-Gray ya da uygun bir survival modeli;
C-index, zamana bağlı AUC, integrated Brier score ve kümülatif insidans kalibrasyonuyla
değerlendirilecektir. Otuz günlük ufukta ölen bir hastayı "kaşeksiye geçmedi" diye negatif
etiketlemek yasaktır.

#### Dört seviyeli arayüz ölçeği korunur

Mevcut sistem **dört** eylem seviyesi kullanmaktadır: Green, Yellow, Orange, Red. Bu ölçek
README'de ve AI servisinin `LABELS` tanımında yer almakta, doktor panelinde ve mobil
uygulamada gösterilmektedir. Dergi sürümü bu ölçeği **değiştirmez**. Çalışan bir arayüz
sözleşmesini bilimsel gerekçe olmadan daraltmak gereksiz kırılma üretir.

Değişen tek şey seviyelerin **nereden geldiğidir**:

| | Konferans sürümü (v2.1) | Dergi sürümü (v3) |
|---|---|---|
| Dört seviyenin rolü | Modelin eğitim etiketi (4 sınıflı ordinal hedef) | Arayüz eylem seviyesi |
| Nasıl üretilir | Doğrudan sınıflandırma çıktısı | Model skoru üzerinde üç kesim noktası |
| Eşikler nereden gelir | Generator etiket eşikleri | Validation verisinde, klinik maliyetler ve onkolog görüşüyle belirlenip dondurulur |

Green, Yellow, Orange ve Red klinik sonuç sınıfları değil kullanıcı arayüzü eylem
seviyeleridir. Her riskte gelecekteki klinik hedef önce tanımlanır; üç kesim noktası
validation verisi, yanlış negatif ve yanlış pozitif maliyetleri ve onkolog
değerlendirmesiyle belirlenip final test açılmadan dondurulur. Bir riskin gelecekteki olay
şiddeti gerçekten ordinal ise bu ayrı gerekçelendirilir ve o risk için ordinal hedef
kullanılabilir; ancak arayüz rengi hiçbir durumda ordinal etiketin yerine geçmez.

#### Gizli süreç ayrımı

Generator içinde ilişkili fakat ayrı iki gizli süreç yürür:

- **Malnütrisyon latent'i:** azalmış alım veya asimilasyon ile hastalık yükü/inflamasyonun
  değişen katkılarını içerir; beslenme desteğine yanıt hasta ve mekanizmaya göre kısmi ya
  da güçlü olabilir.
- **Kaşeksi latent'i:** süreğen katabolik sürükleyici ve kas kaybı bileşenini vurgular;
  konvansiyonel beslenme desteğiyle tam olarak geri döndürülemez.

Süreçler ortak nedenlerden etkilenebilir ve aynı hastada birlikte bulunabilir; ancak biri
diğerinin etiketi veya model çıktısından türetilmez.

Model bu latentlerin hiçbirini görmez. Gördükleri: kilo trajektorisi, antropometri, alım
verisi, CRP, albümin, nötrofil-lenfosit oranı, aktivite ve performans durumu. Görevi bu
gözlemlerden iki farklı mekanizmayı ayırt etmektir.

Bu, makaleye tek başına bir bulgu üretir: iki örtüşen sendromun gözlemlenebilir verilerden
ayrılabilirliğinin ölçülmesi. Somut sorular:

- Model malnütrisyon ile kaşeksiyi ayırabiliyor mu, yoksa ikisini tek bir beslenme
  bozukluğu olarak mı öğreniyor?
- İnflamatuar belirteçler çıkarıldığında ayrım ne kadar bozuluyor?
- Antropometrik proxy'ler çıkarıldığında ne kadar bozuluyor?
- Fearon eşiklerinin doğrudan uygulanması evre geçişini tahmin edebiliyor mu?

#### Zaman bütçesi

Kaşeksi, zaman çizelgesi üzerinde diğer risklerden daha pahalıdır ve bu Eksen 2'de sayıyla
karara bağlanmalıdır. Aritmetik şudur:

- Geçerli bir kaşeksi `t0` için en az 30 günlük aktif gözlem gerekir, yani `t0`
  ancak 30. günden sonra örneklenebilir.
- Etiket için `t0` sonrasında 30 günlük gelecek üretilmiş olmalıdır.
- Dolayısıyla 90 günlük bir hasta çizelgesinde kaşeksi için kullanılabilir `t0` aralığı
  yalnızca 30 ile 60. günler arasıdır.

Bu kısıt kaşeksinin örnek sayısını diğer risklerin belirgin şekilde altına düşürür. İki
seçenek vardır: ortak çizelgeyi uzatmak, veya kaşeksi için ayrı ve daha uzun bir hasta
alt kohortu üretmek. Karar Eksen 2'de verilecek ve minimum pozitif olay hedefiyle birlikte
yazılacaktır.

#### Diğer kurallar

- Aktif gözlem penceresi en az 30 gün olacaktır. Bu, her değişkenin günlük ölçülmesi
  anlamına gelmez: semptom ve alım girdileri tanımlı günlük sıklıkta, kilo ve antropometri
  daha seyrek, laboratuvarlar klinik takvime uygun günlerde üretilir.
- Generator, geçmiş kilo ve fonksiyon ölçümlerini ayrı tarihli kayıtlar halinde üretecektir.
- Malnutrition ile cachexia aynı etiketten veya birbirinin model sonucundan türetilmeyecektir.
- Ortak feature'lar kullanılabilir, ancak iki hedefin olay tanımları ve mekanizmaları
  ayrı olacaktır.
- Yeterli uzun dönem verisi yoksa model tahmini zorlanmayacak ve `monitoring` veya
  `data_quality_warning` sonucu döndürülecektir.
- Gerçek sistemde her zaman bulunmayan alanlar opsiyonel olarak işaretlenecek ve eksik
  olduklarında modelin davranışı ayrıca test edilecektir.

#### Kapsam dışı: refrakter kaşeksi

Üç evreden yalnızca prekaşeksi ve kaşeksi kapsamdadır. Refrakter kaşeksi; ilerleyici,
tedaviye yanıtsız hastalık, düşük performans ve kısa beklenen yaşam süresi gibi ayrıca
uzman değerlendirmesi gerektiren unsurlar içerir. Mevcut ürün amacı ve veri kaynakları bu
evreyi güvenilir biçimde desteklemediğinden v3 kapsamı dışında tutulur; bu karar palyatif
ve destekleyici bakımın önemini azaltan bir ifade olarak kullanılmaz.

Mevcut konferans cachexia modeli ve 30 günlük feature sonucu arşivde korunacaktır; v3
cachexia modeli bunların devamı sayılmayacak ve yeni zamansal protokolle yeniden eğitilecektir.

### 2.5 Klinik kullanım sınırı

Bu programın tamamlanması sistemi doğrudan gerçek hastalarda kullanılabilecek onaylı
bir tıbbi cihaz haline getirmez. Programın hedefi:

> Teknik ve bilimsel eksikleri giderilmiş, onkologların körleştirilmiş sentetik vakalar
> üzerinde değerlendirebileceği ve izinli bir klinik pilot çalışmaya hazırlanabilecek
> araştırma prototipi.

Gerçek hastada kullanım için ayrıca etik değerlendirme, gerçek klinik veri, prospektif
veya retrospektif klinik doğrulama, güvenlik ve gizlilik incelemesi ile geçerli düzenleyici
süreçler gerekecektir.

### 2.6 Etiket üretim ilkesi ve döngüselliğin kırılması

Konferans sürümünün merkezi bilimsel zaafı, gözlemlenebilir feature'ların ve etiketlerin
aynı üreticiden çıkmasıydı. Denetimde ölçülen yapı şuydu: etiket şiddeti, modelin
göremediği gizli değişkenin yüzde 70'i ile modelin gördüğü gözlenebilir ipucunun yüzde
30'unun ağırlıklı toplamıydı. Beş riskte bu ipucu terimi tek bir model sütununun kendisiydi.

Dergi sürümünde bu yapı **tamamen kaldırılmaktadır**. v3'te geçerli olan tek kural şudur:

> Etiket, yalnızca gizli süreçten ve `t0` sonrasında gerçekleşen tarihli olaydan türetilir.
> Modelin `t0` anında gördüğü hiçbir feature etiket hesabına girmez.

Bunun sonuçları:

- Gözlenebilir ipucu terimi diye bir şey kalmaz; feature-etiket döngüselliği yapısal olarak
  ortadan kalkar, istatistiksel olarak zayıflatılmış olmaz.
- Görev, mevcut durumun sınıflanmasından gelecekteki olayın tahminine döner.
- Performans rakamlarının konferans sürümünden düşük çıkması beklenir ve bu bir gerileme
  değil, görevin gerçek zorluğunun ortaya çıkmasıdır. Delta analizinde bu fark ölçülerek
  raporlanacaktır.

Bu ilke dergi sürümünün en önemli bilimsel iddiasıdır ve makalenin yöntem bölümünde
konferans sürümüyle karşılaştırmalı olarak açıkça anlatılacaktır.

### 2.7 Kural ve makine öğrenmesi arasındaki karar kuralı

Etiketler klinik eşiklerden türediği için bazı risklerde kural tabanlı sınıflayıcının
makine öğrenmesine yetişmesi veya onu geçmesi gerçek bir ihtimaldir. Bu ihtimale karşı
karar, **sonuçlar görülmeden önce** sabitlenir. Aksi halde seçim post-hoc olur ve hakem
tarafından haklı olarak eleştirilir.

Önceden kayıtlı karar çerçevesi:

- Her risk için makine öğrenmesinin kural baseline'ı üzerinde sağlaması beklenen ek değer
  ölçütleri Eksen 7'de, deneyler çalıştırılmadan önce yazılır ve dondurulur. Klinik olarak
  gerekçelendirilemeyen sayısal sınırlar keyfi geçme-kalma eşiği olarak kullanılmaz.
- Karar çerçevesi **yalnızca validation sonuçlarıyla** uygulanır. Final test görülmeden dondurulur;
  final test sonuçlarına bakıp eşik değiştirilmez.
- Karar tek bir metriğe indirgenmez. En az dört boyut birlikte değerlendirilir:
  - Birincil ayırım metriği (macro-F1 veya ordinal eşdeğeri), güven aralığıyla
  - Kalibrasyon (Brier, ECE)
  - Kritik olay kaçırma oranı
  - Yüz izlem dönemi başına yanlış alarm yükü
- Kalibrasyonu kötü veya kritik olay kaçırması yüksek bir model, ayırım metriğinde önde
  olsa bile ek değer göstermiş sayılmaz.
- Önceden belirlenen ek değer ölçütlerini karşılamayan model araştırma sonuçlarında kalır
  ve makalede raporlanır; journal-v3 araştırma prototipi ile onkolog vaka değerlendirmesinde
  o risk için kural tabanlı çıktı tercih edilir. Bu karar gerçek hastada klinik kullanım
  veya üretim onayı anlamına gelmez.
- Bu durum bir başarısızlık olarak değil, ölçülmüş bir bulgu olarak raporlanır.

Bu yaklaşım makalenin zayıflığı değil güçlü yanıdır: hangi risklerde öğrenmenin gerçekten
katkı sağladığını, hangilerinde klinik kuralın yeterli olduğunu gösteren önceden kayıtlı
bir karşılaştırma, "her şey için model eğittik" iddiasından bilimsel olarak üstündür.

## 3 Veri mimarisi

Her risk ayrı dosya ve çalışma paketine sahip olacaktır. Ancak birbirinden kopuk dokuz
farklı hasta evreni üretilmeyecektir. Bütün riskler aynı ortak ve zamansal hasta
altyapısını kullanacaktır.

### 3.1 Tek veri kaynağı

```text
journal/
  dataset_v3/
    common/
      patients.parquet
      treatment_cycles.parquet
      historical_measurements.parquet
      laboratory_results.parquet
      daily_observations.parquet
      clinical_events.parquet
      prediction_index.parquet
    risk_views/
      febrile_neutropenia.csv
      infection.csv
      dehydration.csv
      renal_toxicity.csv
      hepatic_toxicity.csv
      immunotherapy_ae.csv
      malnutrition.csv
      treatment_delay.csv
      cachexia.csv
      overall_deterioration.csv
```

`common` altındaki tablolar tek doğruluk kaynağıdır. Risk dosyaları bu tablolardan
otomatik olarak türetilen analiz görünümleridir. Risk dosyaları elle değiştirilmez.
Ortak `patient_id`, `cycle_id` ve `t0_id` alanları sayesinde bütün dosyalar birbirine
izlenebilir şekilde bağlanır.

### 3.2 Risk çalışma paketi

```text
journal/
  risks/
    febrile_neutropenia/
      clinical_spec.md
      literature_matrix.csv
      feature_registry.csv
      label_definition.yaml
      generator_config.yaml
      rule_baseline.py
      analysis.ipynb
      model_card.md
      tests/
```

Aynı yapı dokuz bağımsız risk için tekrarlanacaktır. Overall clinical deterioration
ayrı bir model paketi yerine `journal/fusion/overall_deterioration/` altında tutulacaktır.

## 4 Geliştirme eksenleri

Bu program zamana göre değil, bağımlılık sırasına göre düzenlenmiştir. Bir eksenin bitti
kriteri karşılanmadan sonraki eksen nihai kabul edilmiş sayılmaz.

## Eksen 0 Mevcut projeyi koruma

### Yapılacaklar

- Mevcut çalışan sürümün commit kimliğini kaydetmek.
- Mevcut sürümü `v1.0-conference` etiketiyle dondurmak.
- Mevcut dataset, model ve sonuç dosyalarının checksum değerlerini almak.
- Konferans veri setini `oncoguard_dataset_v21_conference.csv` adıyla sabitlemek.
- Mevcut sonuçları ve model manifestini konferans arşivine almak.
- Yeni çalışma için `journal-v3` dalını açmak.
- Yeni kodu başlangıçta yalnızca `journal/` altında geliştirmek.
- Mevcut uygulamanın çalıştığını doğrulayan temel smoke testleri kaydetmek.

### Çıktılar

- Dondurulmuş konferans sürümü
- Konferans model ve veri manifesti
- Geri dönüş talimatı
- Ayrı `journal-v3` geliştirme alanı

### Bitti kriteri

`journal-v3` tamamen bırakılsa bile mevcut konferans uygulaması aynı veri, model ve
işlevlerle çalışmaktadır.

## Eksen 1 Mevcut v2.1 bilimsel denetimi ve rule baseline

Yeni generator tasarlanmadan önce mevcut sistem ölçülür. Bu sonuçlar yeni mimarinin
hangi probleme odaklanacağını belirleyecektir.

### Yapılacaklar

- Mevcut kural motorunu v2.1 veri setinin tamamında çalıştırmak.
- Rule only ve XGBoost sonuçlarını aynı değerlendirme kayıtlarında karşılaştırmak.
- Rule plus ML için yalnızca keşif amaçlı ilk karşılaştırmayı yapmak.
- Her etiketin latent severity ve gözlenebilir cue katkısını raporlamak.
- Feature ile etiket arasındaki doğrudan veya neredeyse doğrudan kopyaları bulmak.
- `WeightLossPct7` ve `WeightLossPct30` tekrarını belgelemek.
- Bütün modellerin aynı 87 feature'ı kullanmasının etkisini incelemek.
- 3 ve 7 günlük modellerde dağıtım anı ile eğitim verisi arasındaki farkları çıkarmak.
- irAE sonuçlarını yalnızca immünoterapi alt kohortunda yeniden raporlamak.
- Overall deterioration etiketinin diğer etiketlerden türetilmesini analiz etmek.
- Eğitim, model seçimi ve değerlendirme bölmelerini yeniden doğrulamak.
- Makaledeki her sayıyı kayıtlı model ve sonuç dosyalarıyla eşleştirmek.

### Çıktılar

- `journal/audit/current_system_audit.md`
- `journal/audit/rule_vs_ml_v21.csv`
- `journal/audit/feature_label_dependency.csv`
- `journal/audit/temporal_leakage_report.md`
- `journal/audit/keep_change_remove_matrix.csv`

### Bitti kriteri

Her mevcut risk, feature, etiket, kural ve pencere için korunacak, değiştirilecek veya
kaldırılacak kararı gerekçesiyle verilmiştir. Yeni v3 tasarımı bu sonuçlara göre
başlatılabilir.

## Eksen 2 Ortak zamansal protokolü kilitleme

Dokuz bağımsız risk ve overall fusion için ortak kurallar tek bir belgede tanımlanacaktır.

### Karara bağlanacaklar

- Yetişkin yaş dağılımı ve yaşa uygun tanı listesi
- Kanser türleri ve hematolojik ile solid tümör ayrımı
- Tedavi rejimleri ve tanı tedavi uyumluluk matrisi
- Hasta başına tedavi döngüsü sayısı
- Döngü uzunlukları ve döngüler arası süreklilik
- En az altı aylık seyrek geçmiş ağırlık ve fonksiyon kayıtlarının nasıl temsil edileceği
- Gün 0 ve `t0` tanımı
- Hasta başına üretilebilecek en fazla `t0` sayısı
- Aynı hastanın veri setini domine etmesini önleyen örnekleme veya ağırlıklandırma
- Doktor onaylı baseline verisinin tanımı
- Tarihli ve seyrek laboratuvar ölçümlerinin tanımı
- Günlük hasta bildiriminin tanımı
- Akut, subakut ve uzun dönemli gözlem pencereleri
- Her risk için gelecek tahmin ufku
- Her risk için birincil hedef türü: sabit ufuklu ikili olay, klinik olarak gerekçeli
  ordinal olay veya zaman-aşımı olayı
- Klinik model hedefi ile arayüzdeki Green, Yellow, Orange ve Red eylem seviyelerinin ayrımı
- Overall özetinde 72 saat, 7 gün ve 30 gün ufuklarının ayrı korunma kuralı
- Rastgele ve bilgilendirici eksiklik modelleri
- Ölçüm gürültüsü ve cihaz hatası modeli
- Hasta bazlı ve sızıntısız veri bölme kuralları
- Generator A ve bağımsız stres testi Generator B'nin sınırları

### Sayıyla karara bağlanacaklar

Aşağıdakiler niteliksel ifade olarak değil, sayı olarak protokole yazılacaktır. Bu
sayılar belirlenmeden tek satır veri üretilmez, çünkü sonradan değiştirilmeleri veri
setinin baştan üretilmesini gerektirir.

| Parametre | Neden kritik |
|---|---|
| Toplam hasta sayısı | Hasta bazlı bölme kullanıldığı için etkin örneklem hasta sayısıdır, satır sayısı değil |
| Hasta çizelgesi uzunluğu (gün) | Kaşeksinin `t0` aralığını doğrudan belirler |
| Hasta başına döngü sayısı | Tedavi gecikmesi ve kaşeksi için geçmiş bağlamı sağlar |
| Hasta başına maksimum `t0` sayısı | Tek hastanın veri setini domine etmesini önler |
| Risk başına minimum pozitif olay sayısı (her bölmede) | Güven aralıklarının yorumlanabilir olması için |
| Kaşeksi için ayrı örneklem hesabı | Kaşeksi modülü, dar geçerli `t0` aralığı ve sansür olasılığı nedeniyle daha büyük bir kohort gerektirebilir. Nihai hasta ve olay sayısı, ikili ve zaman-aşımı hedefler için ayrı ayrı formal örneklem büyüklüğü hesabıyla belirlenecektir (bkz. aşağıdaki not) |
| Yaş bandı başına minimum örneklem ve olay sayısı | Alt grup raporlamasının eşiği |
| Kaşeksi için ayrı kohort gerekip gerekmediği | Zaman bütçesi aritmetiğinin sonucu |
| Green/Yellow/Orange/Red için üç kesim noktası | Klinik maliyet ve onkolog görüşüyle validation üzerinde dondurulması için |

### Örneklem büyüklüğü nasıl hesaplanacak

Bu sayılar deneyimsel kestirimle veya olay-başına-değişken türü kestirme kurallarla
belirlenmeyecektir. Riley ve arkadaşları bu tür kestirme kuralların (örneğin değişken
başına 10 olay) kullanılmaması gerektiğini açıkça belirtmektedir.

Her risk için minimum örneklem, hedef türüne uygun formal yöntemle hesaplanır ve hesabın
girdileri belgelenir:

| Girdi | Nereden gelir |
|---|---|
| Beklenen olay sıklığı | Risk spesifikasyonundaki literatür ve generator parametreleri |
| Aday parametre sayısı | Feature kayıt defterindeki risk maskesi |
| Beklenen model performansı | Pilot çalıştırma veya literatürden muhafazakâr kestirim |
| Hedef shrinkage faktörü | Aşırı uyumu sınırlamak için önceden belirlenir |
| Kabul edilebilir kalibrasyon sapması | Klinik maliyet değerlendirmesiyle |
| Sansürleme oranı | Yalnızca zaman-aşımı hedeflerinde; kaşeksi için kritik |

İkili hedefler ve zaman-aşımı hedefleri için hesap **ayrı ayrı** yapılır. Hesaplama
`pmsampsize` gibi bu yöntemi uygulayan bir araçla yürütülür ve girdileriyle birlikte
reprodüksiyon paketine konur.

Kaşeksi bu hesapta özel dikkat gerektirir: geçerli `t0` aralığı dar, sansürleme olasılığı
yüksek ve hedef zaman-aşımı tipindedir. Bunun daha büyük bir kohort gerektirmesi
beklenebilir, ancak **ne kadar büyük olacağı hesaplanmadan yazılmayacaktır.**

### Çıktı

- `journal/spec/00_common_temporal_protocol.md`

### Bitti kriteri

Belgeyi okuyan biri risk modüllerini görmeden hasta, zaman çizelgesi, veri kaynağı,
tahmin anı, bölme yöntemi ve eksik veri mekanizmasını yeniden kurabilmektedir.

## Eksen 3 Risk başına literatür ve klinik spesifikasyon

Kod yazılmadan önce her bağımsız risk için ayrı spesifikasyon hazırlanacaktır.

### Her risk dosyasının zorunlu içeriği

1. Klinik kavram ve çalışma kapsamı
2. Uygun hasta ve tedavi alt grubu
3. Hariç tutma ve not applicable koşulları
4. Tahmin edilen olayın açık tanımı
5. Gözlem penceresi, `t0` ve tahmin ufku
6. Risk faktörleri ve her faktörün literatür kaynağı
7. Kanıt gücü sınıfı
8. Faktörün beklenen yönü ve makul etkileşimleri
9. Veri kaynağı ve tahmin anındaki erişilebilirliği
10. ML feature, güvenlik kuralı veya açıklama değişkeni ayrımı
11. Gizli fizyolojik süreç varsayımları
12. Gözlem sıklığı, gürültü ve eksiklik modeli
13. Gelecek olay hedefi ve hedef türü; klinik olarak ordinal değilse dört seviyeli arayüz
    renginden ayrı sabit ufuklu ikili etiket veya zaman-aşımı tanımı
14. Rule baseline tanımı
15. Yanlış negatif ve yanlış pozitiflerin güvenlik etkisi
16. Kabul ve test kriterleri

### Paylaşılan sinyaller kuralı

İki risk aynı feature'ı kullanabilir. Ateş, ishal, ANC veya tedavi türü birden fazla
risk için gerçekten anlamlı olabilir. Yasak olanlar şunlardır:

- İki riskin hedef olayının aynı şekilde tanımlanması
- Bir risk etiketinin başka bir riskin etiketi kullanılarak üretilmesi
- Etiketin modelin gördüğü tek bir feature'ın doğrudan kopyası olması
- Paylaşılan feature'ın her riskteki rolünün kaynak ve mekanizma olmadan kullanılması

### Risk dosyaları

```text
journal/spec/
  00_common_temporal_protocol.md
  01_febrile_neutropenia.md
  02_infection.md
  03_dehydration.md
  04_renal_toxicity.md
  05_hepatic_toxicity.md
  06_immunotherapy_ae.md
  07_malnutrition.md
  08_treatment_delay.md
  09_cachexia.md
  10_overall_deterioration_fusion.md
  99_out_of_scope.md
```

### Bitti kriteri

Her klinik feature'ın kaynağı, birimi, zaman bilgisi, veri kaynağı, kullanım amacı ve
eksiklik davranışı tanımlıdır. Literatürde olup gerçek sistemde bulunmayan faktörler
gizlenmez; erişilemiyor olarak işaretlenir ve sınırlılık tablosuna eklenir.

## Eksen 4 Ortak generator çekirdeği

### Dosya yapısı

```text
journal/generator/core/
  config.py
  patient.py
  diagnoses.py
  treatments.py
  timeline.py
  historical_measurements.py
  laboratory.py
  observations.py
  missingness.py
  events.py
  validation.py
```

### Tasarım kuralları

- Çekirdek tek başına risk etiketi oluşturmaz.
- Her hasta kalıcı `patient_id` taşır.
- Laboratuvar ve günlük gözlemler tarihli olur.
- Aynı hastada birden fazla döngü ve `t0` bulunabilir.
- Yaş, tanı, tedavi, komorbidite ve laboratuvarlar bağımsız rastgele kolonlar değildir.
- Eksiklik yalnızca tamamen rastgele oluşturulmaz; hasta ve kötüleşme durumuyla ilişkili
  eksiklik senaryoları da bulunur.
- Bütün rastgelelik seed ve konfigürasyonla yeniden üretilebilir.

### Bitti kriteri

Risk modülü olmadan çalıştırıldığında geçerli hasta, tedavi, zaman, laboratuvar ve günlük
gözlem tabloları üretmektedir. Şema, birim ve tarih testlerinin tamamı geçmektedir.

## Eksen 5 Bağımsız risk modüllerini geliştirme

### Dosya yapısı

```text
journal/generator/risks/
  febrile_neutropenia.py
  infection.py
  dehydration.py
  renal_toxicity.py
  hepatic_toxicity.py
  immunotherapy_ae.py
  malnutrition.py
  treatment_delay.py
  cachexia.py
```

### Her modülün ortak arayüzü

| Fonksiyon | Görev |
|---|---|
| `eligibility(patient, treatment)` | Riskin bu hasta için uygulanabilirliğini belirler |
| `latent_process(patient, cycle, rng)` | Modelin görmediği gizli süreci üretir |
| `observe(latent, timeline, rng)` | Gürültülü ve eksik gözlemleri üretir |
| `events(latent, timeline, rng)` | Tarihli gelecek olaylarını üretir |
| `label(events, t0, horizon)` | Yalnızca `t0` sonrasındaki olaydan etiketi türetir |
| `validate(records)` | Risk özelindeki veri kalite kontrollerini çalıştırır |

### Değişmez kurallar

- Model yalnızca `t0` ve öncesindeki verileri görür.
- Hedef yalnızca `t0` sonrasındaki tanımlı olaydan türetilir.
- Mevcut olayı tanımlayan kesin kriterler tahmin feature'ı yerine güvenlik kuralı olarak
  değerlendirilir.
- Latent değişkenler model girdisine girmez.
- Risk modülü başka bir riskin etiketini kullanmaz.
- Generator B, Generator A'nın yalnızca farklı seed ile çalıştırılmış hali olmaz.
- Etiket hesabına modelin `t0` anında gördüğü hiçbir feature girmez (bkz. 2.6).

### Bitti kriteri

Modül, spesifikasyondaki olayları ve gözlemleri üretmekte; birim, zaman, nedensellik,
uygunluk ve sızıntı testlerini geçmektedir. Dağılımlar belgelenmiş varsayımlarla uyumludur.
Klinik gerçeklik iddiası onkolog değerlendirmesi tamamlanmadan yapılmaz.

## Eksen 6 Feature kayıt defteri ve risk maskeleri

### Her feature için tutulacak bilgiler

- Ad ve açıklama
- Birim
- Veri kaynağı
- Statik veya zamansal olma durumu
- Lookback penceresi
- Güncellenme sıklığı
- Literatür kaynağı
- Kanıt gücü
- Beklenen etki yönü
- Kullanılabildiği riskler
- Tahmin anında erişilebilirlik
- Eksiklik davranışı
- Readiness için zorunlu olup olmadığı
- Ölçümün kaynağı, doğrulama durumu ve güvenilirlik sınıfı
- Guardrail veya ML feature ayrımı
- Yaş ve tedavi uygulanabilirliği
- Redundancy ve shortcut riski

### Teknik kurallar

- Feature üretimi yalnızca ortak kayıt defterinden yapılır.
- Risk maskeleri kayıt defterinden otomatik türetilir.
- Statik baseline ve geçmiş öykü bilgileri risk penceresinden bağımsız kullanılabilir.
- Zamansal feature yalnızca risk spesifikasyonunda izin verilen geçmiş pencereyi kullanır.
- Hiçbir feature `t0` sonrasındaki kaydı kullanamaz.
- Eğitim, Colab, test ve servis aynı feature kodunu çağırır.
- Dataset ve AI servisi altında ikinci feature kodu kopyası bulunmaz.

### Çıktılar

- `journal/features/registry.yaml`
- `journal/features/build.py`
- `journal/features/masks.py`
- `journal/features/tests/`

### Bitti kriteri

Otomatik testler, her modelin yalnızca izinli ve tahmin anında erişilebilir feature'ları
kullandığını; eğitim ve servis feature vektörlerinin birebir aynı olduğunu kanıtlamaktadır.

## Eksen 7 Risk özelinde rule baseline

### Yapılacaklar

- Her risk için basit ve açıklanabilir kural baseline'ı yazmak.
- Gerçekleşmiş olay tespiti ile gelecekteki olay tahminini ayırmak.
- Acil güvenlik kurallarını ML skorundan bağımsız çalıştırmak.
- Rule only, ML only ve rule plus ML çıktı sözleşmesini tanımlamak.
- Füzyon yöntemi ve override davranışını eğitimden önce sabitlemek.
- Her risk için ML'in kural üzerinde sağlaması gereken çok boyutlu ek değer ölçütlerini
  deneyler çalıştırılmadan önce yazmak ve dondurmak (bkz. 2.7).

### Febril nötropeni örneği

- Mevcut ateş ve nötropeni kriteri bir olay tespiti veya güvenlik uyarısıdır.
- Modelin görevi bu gerçekleşmiş olayı yeniden öğrenmek değildir.
- ML modeli olay oluşmadan önce, tanımlanan gelecek ufkunda febril nötropeni riskini
  tahmin eder.
- Dashboard gerçekleşmiş güvenlik alarmı ile tahmini riski ayrı gösterir.

### Bitti kriteri

Her bağımsız risk için rule baseline aynı test örneklerinde çalışmakta ve ML ile doğrudan
karşılaştırılabilmektedir.

## Eksen 8 Dataset v3 üretimi ve doğrulama

### Generator A

- Eğitim ve validation verisini üretir.
- Hasta bazlı bölme uygulanır.
- Aynı hastanın bütün döngüleri tek bölmede kalır.
- Aynı hastadan alınan `t0` sayısı sınırlandırılır veya ağırlıklandırılır.

### Generator B

- Yalnızca final robustness ve dış dağılım testi için kullanılır.
- Ayrı konfigürasyon ve mümkün olduğunca ayrı implementasyon kullanır.
- Farklı dağılım, eksiklik, gürültü, etkileşim ve ölçüm mekanizmaları içerir.
- Generator A fonksiyonlarını doğrudan yeniden kullanarak aynı bağımlılıkları kopyalamaz.
- Aynı riskin tüm pozitif senaryosunu eğitimden kaldırmaz.
- Görülmemiş senaryo kombinasyonları ve hasta arketipleri üretir.

### Doğrulama testleri

- Şema, tür, birim ve aralık kontrolleri
- Tarih sırası ve `t0` sızıntı kontrolleri
- Yaş tanı tedavi uyumluluğu
- 18-90 yaş gruplarının dağılım ve olay yeterliliği
- Yaşla ilişkili değişkenlerin mantıksal ve klinik tutarlılığı
- Risk uygulanabilirlik kontrolleri
- Olay ve sınıf dağılımları
- Eğitimde fazla örneklenen gruplar için örnekleme ağırlıkları ile ham ve hedef dağılım
  karşılaştırması
- Eksik veri mekanizmaları
- Evde antropometrik ölçüm için gözlemci ve ölçüm hatası senaryoları
- Aykırı değer ve imkansız kombinasyonlar
- Feature korelasyonu ve redundancy
- Label ile feature arasındaki doğrudan kopyalar
- Counterfactual testler
- Metamorphic testler
- Dağılım ve eşik duyarlılığı

Sentetik olay sıklıkları gerçek klinik prevalans olarak sunulmayacaktır. Literatürde
güvenilir oran bulunuyorsa karşılaştırma ve duyarlılık aralığı olarak kullanılacak;
bulunmuyorsa generator varsayımı açıkça işaretlenecektir.

### Çıktılar

- `dataset-v3` ortak tabloları
- Dokuz risk görünümü ve overall fusion görünümü
- Dataset card
- Generator card
- Veri sözlüğü
- Otomatik doğrulama raporu

### Bitti kriteri

Kırmızı bayrak oluşturan zaman sızıntısı, imkansız kombinasyon, doğrudan etiket kopyası
ve açıklanamayan feature bulunmamaktadır. Kalan varsayımlar açıkça belgelenmiştir.

## Eksen 9 Colab ve model deneyleri

### Notebook yapısı

```text
journal/notebooks/
  00_current_system_audit.ipynb
  01_dataset_v3_validation.ipynb
  10_febrile_neutropenia.ipynb
  20_infection.ipynb
  30_dehydration.ipynb
  40_renal_toxicity.ipynb
  50_hepatic_toxicity.ipynb
  60_immunotherapy_ae.ipynb
  70_malnutrition.ipynb
  80_treatment_delay.ipynb
  90_cachexia.ipynb
  91_overall_deterioration_fusion.ipynb
  92_rule_ml_fusion.ipynb
  93_robustness_and_shift.ipynb
  99_reproduce_journal_results.ipynb
```

Notebooklar ana iş mantığını içermez. Generator, feature ve model kodu repodaki Python
paketinden çağrılır. Notebooklar analiz, tablo ve figür üretir.

### Deney protokolü

- Hasta bazlı train, validation ve dokunulmamış final test ayrımı
- Model ve hiperparametre seçiminin yalnızca validation üzerinde yapılması
- Final testin yalnızca dondurulmuş modelle bir kez kullanılması
- 10-30 seed veya hesaplama maliyetine göre önceden belirlenmiş tekrar sayısı
- Yüzde 95 güven aralıkları
- Prevalence veya majority baseline ile risk özelinde rule baseline
- Logistic Regression
- Yalnızca klinik hedef gerçekten sıralıysa Ordinal Logistic Regression
- Random Forest
- CatBoost veya LightGBM
- Explainable Boosting Machine
- XGBoost
- Olay zamanı ve sansürleme kullanılan risklerde cause-specific Cox, Fine-Gray veya
  önceden seçilmiş uygun survival modeli
- Rule only, ML only ve rule plus ML karşılaştırması

### Metrikler

- Bütün sınıflama hedeflerinde accuracy, balanced accuracy, macro F1, sınıf başına
  precision/recall/F1 ve tam confusion matrix
- İkili hedeflerde AUROC ve özellikle sınıf dengesizliğinde AUPRC
- Gerçekten ordinal klinik hedeflerde weighted Cohen kappa, ortalama mutlak sınıf hatası
  ve within-one-level accuracy
- Olasılıksal sınıflama çıktılarında Brier score, expected calibration error ve
  kalibrasyon eğrileri
- Zaman-aşımı analizlerinde C-index, zamana bağlı AUC, integrated Brier score ve
  kümülatif insidans kalibrasyonu
- Yüz izlem dönemi başına yanlış alarm
- Kritik olay kaçırma oranı
- Doktor incelemesine yönlendirilen örnek oranı

### Alt grup ve robustness analizleri

- Yaş grubu
- Tanı
- Tedavi
- ECOG
- Komorbidite yükü
- Eksik veri düzeyi
- Ölçüm gürültüsü
- Generator B
- Senaryo önceliği kayması
- Eşik perturbasyonu
- Görülmemiş hasta arketipleri ve kombinasyonları

### Bilimsel delta analizi

**Temel kural: v2.1 sayıları delta tablosuna girmez.**

Konferans sürümünün rakamları, Eksen 1'de belgelenen bozuk bir protokolden gelmektedir:
aynı değerlendirme bölmesi hem model ailesi seçimi hem final raporlama için kullanılmış,
etiketlerde gözlenebilir cue terimi bulunmakta, on model aynı feature şemasını
paylaşmaktadır. Bu rakamlar yeniden çerçevelenerek karşılaştırılabilir hale getirilemez;
kırık bir referansa karşı ölçülen bir delta, deltanın kendisini de kırık yapar.

Ayrıca hedef tipi değişmiştir: v2.1 hedefleri dört sınıflı ordinaldi, v3 hedefleri sabit
ufuklu ikili olay veya zaman-aşımı olayıdır ve renk seviyeleri artık eğitim etiketi
değildir. İki sürümün macro-F1 değerleri aynı niceliği ölçmemektedir.

**v2.1 sonuçları atılmaz.** Makalede `historical conference results` başlığı altında,
kendi protokolü ve kendi hedef tanımıyla ayrıca sunulur. Yalnızca v3 rakamlarıyla doğrudan
performans deltasına sokulmaz. v2.1 bu programda performans referansı değil, Eksen 1'de
belgelenen metodolojik sorunların kanıtıdır.

**Deltalar v3'ün kendi içinde ölçülür.** Aynı veri, aynı protokol, aynı seed'ler ve aynı
hedef tanımı kullanılarak **kontrollü tanısal ablation varyantları** üretilir ve doğru
varyantla karşılaştırılır:

| Delta | Tanısal ablation varyantı | Ölçülecek etki |
|---|---|---|
| Risk maskesi | Tüm feature'ların her riske açıldığı varyant | Performans farkı ve zamansal sızıntının büyüklüğü |
| Etiketin zamansal konumu | Etiketin `t0` sonrası olaydan değil, gözlem penceresi içinden üretildiği varyant | Döngüsellik kaldırıldığında ölçülen zorluk artışı |
| Gözlenebilir cue terimi | Etiket şiddetine gözlenebilir bir sütunun doğrudan katıldığı varyant | Cue teriminin tek başına şişirdiği performans miktarı |
| Kural yerine ML | Rule-only çıktı | ML'in kural üzerine ek katkısı |
| Generator A yerine B | Bağımsız test generator'ı | Dağılım dışı performans kaybı |
| Eksiklik ve gürültü | Artırılmış eksiklik ve ölçüm hatası | Dayanıklılık kaybı |
| Feature grupları | Veri kaynağı bazlı çıkarma | Her kaynağın marjinal katkısı |

Karşılaştırma iki farklı veri seti arasında değil, tek bir veri seti üzerinde tek bir
metodolojik kusurun açılıp kapatılmasıyla yapıldığı için karşılaştırılabilirlik sorunu
önemli ölçüde azaltılır. Tamamen ortadan kalktığı iddia edilmez: varyantlar yine
sentetiktir ve generator varsayımlarına bağlıdır.

**Tanısal ablation varyantlarının kullanım sınırı.** Bu varyantlar yalnızca veri sızıntısının
ve metodolojik kusurların etkisini göstermek için üretilir. Kesinlikle:

- model ailesi veya hiperparametre seçiminde kullanılmaz,
- nihai performans tablosunda birincil sonuç olarak yer almaz,
- AI servisine, doktor paneline veya onkolog değerlendirme paketine girmez,
- dondurulmuş servis modelinin eğitiminde kullanılmaz.

Varyantlar `journal/experiments/ablations/` altında ayrı konfigürasyon dosyalarıyla
tanımlanır, tanısal amaçlı olarak işaretlenir ve yeniden üretim komutunun parçasıdır.

### Bitti kriteri

Her ana sonuç farklı seed'lerde ve güven aralıklarıyla raporlanmıştır. Makaledeki tüm
tablo ve figürler tek bir yeniden üretim komutuyla oluşturulabilmektedir.

## Eksen 10 Model paketini ve AI servisini hazırlama

### Yapılacaklar

- Her risk için seçilen servis mekanizmasını dondurmak: ek değer ölçütlerini geçen ML
  modeli ve güvenlik kuralları veya yalnızca kural tabanlı araştırma prototipi çıktısı.
- Eşiği geçmeyen aday ML modellerini servis modelinden ayrı araştırma artefaktı olarak
  saklamak.
- Overall deterioration füzyonunu dondurmak.
- Feature registry ve risk maskelerini model paketiyle birlikte sürümlemek.
- Eğitim ve servis feature üretimini tek kaynakta birleştirmek.
- Yeni API sözleşmesini sürümlü endpoint altında eklemek.
- Eski model ve endpointi korumak.
- Readiness kurallarını risk spesifikasyonlarıyla eşleştirmek.
- Kural motorunu gerçek servis yoluna bağlamak.
- Rule alert ve ML prediction sonuçlarını ayrı döndürmek.
- `not_applicable`, `monitoring`, `rule_alert`, `predicted_risk` ve
  `data_quality_warning` durumlarını tanımlamak.
- Model sürümü, veri sürümü, `t0`, kullanılan pencere ve eksik alanları kaydetmek.
- Colab ile servis çıktılarının birebir eşitliğini test etmek.

### Bitti kriteri

Aynı giriş, araştırma paketinde ve AI servisinde tolerans sınırları içinde birebir aynı
feature vektörünü ve tahmin sonucunu üretmektedir. Eski sürüme geri dönülebilmektedir.

## Eksen 11 Backend mobil uygulama ve doktor paneli

### Backend

- Yeni AI sözleşmesi için sürümlü DTO'lar
- Hasta, tedavi, laboratuvar ve günlük kayıt tarih doğrulamaları
- Tahmin ve model sürümü denetim kayıtları
- Yetki ve rol kontrolleri
- Eski sözleşmenin geriye dönük uyumluluğu

### Mobil uygulama

- Yalnızca nihai feature listesinde gerekli olan yeni alanları eklemek
- Gelecek gün girişi ve aynı gün tekrar kaydı kontrolleri
- Eksik veri ve monitoring durumunun anlaşılır gösterimi
- Kaşeksi için hastadan yalnızca anlamlı sıklıkta kilo, iştah, besin alımı ve aktivite
  kaydı istemek; gereksiz günlük ölçüm yükü oluşturmamak
- Gereksiz hasta yükü oluşturan alanları kaldırmak

### Doktor paneli

- Rule alert ile ML prediction ayrımı
- Uygulanamaz risk ve monitoring durumu
- Veri kalitesi ve eksik alan görünümü
- Model score değerinin klinik olasılık gibi sunulmaması
- Tahmin zamanı, pencere, model sürümü ve açıklama görünümü
- Kaşeksi ekranında tarihli kilo seyri, bazal değere göre değişim, besin alımı ve fonksiyon
  bağlamını birlikte göstermek
- Kaşeksi için yeterli uzun dönem geçmişi yoksa zorunlu skor yerine monitoring durumunu
  ve tamamlanması gereken alanları göstermek
- Overall deterioration'ın birleşik çıktı olarak işaretlenmesi

### Sistem testleri

- API gecikmesi ve throughput
- Eşzamanlı kullanıcı yükü
- Model yükleme ve servis kesintisi
- Veritabanı ve AI servisi hata senaryoları
- Eksik ve bozuk veri davranışı
- Kimlik doğrulama ve yetkilendirme
- Audit log ve model sürümü
- Uçtan uca regresyon testleri

### Bitti kriteri

Yeni model paketiyle uçtan uca demo mevcut konferans sürümü kadar kararlı çalışmaktadır.
Eski ve yeni sürüm arasında kontrollü geçiş mümkündür.

## Eksen 12 Onkolog değerlendirmesine hazırlık

Bu eksen iki parçaya ayrılır.

### 12A Zorunlu değerlendirme paketi

- Her risk için körleştirilmiş sentetik vaka kitabı
- Normal, sınırda, kritik, eksik verili ve karşı-olgusal vakalar
- Farklı yaş gruplarını ve yaşla uyumlu tanı, tedavi, komorbidite örüntülerini kapsayan vakalar
- Kaşeksi için kısa dönem semptom değişikliği ile gerçek uzun dönem ilerlemeyi ayıran vakalar
- Risk tanımları ve feature kaynaklarının kısa klinik özeti
- Model, kural ve füzyon sonuçlarının ayrı gösterimi
- Klinik gerçekçilik formu
- Risk seviyesi ve aciliyet formu
- Açıklama kalitesi ve kullanılabilirlik formu
- Yanlış negatif ve yanlış pozitif değerlendirme alanı
- Sistem sürümü ve vaka üretim kaydı

### 12B Onkolog değerlendirme çalışması

- Çalışma başlamadan önce üniversitenin etik kurul veya kurumsal değerlendirme
  gerekliliği hakkında resmi görüş alınması
- Katılımcı bilgi ve onam sürecinin hazırlanması
- Mümkünse en az iki bağımsız onkolog
- Uzmanların birbirinin ve modelin yanıtlarını görmeden değerlendirme yapması
- Onkolog model, onkolog kural ve onkolog onkolog uyum analizi
- Weighted kappa ve güven aralıkları
- Anlaşmazlıkların nitel analizi
- Uzman geri bildirimi sonrasında kuralların kontrollü revizyonu

12B gerçekleştirilemiyorsa sistem klinik olarak doğrulanmış sayılmaz. Makale uygulamalı
yapay zeka ve yazılım mühendisliği çalışması olarak konumlandırılır; uzman değerlendirmesi
gelecek çalışma olarak açıkça belirtilir. 12A paketi yine tamamlanır.

### Bitti kriteri

Zorunlu vaka ve değerlendirme paketi eksiksizdir. Uzman çalışması yapılmışsa gerekli
kurumsal süreçler, yöntem ve sonuçlar izlenebilir biçimde raporlanmıştır.

## Eksen 13 Reprodüksiyon ve yayın paketi

### Yapılacaklar

- Dondurulmuş commit ve release
- Konferans sürümünden ayrı code ve dataset DOI
- Kod ve veri lisansları
- Ortam ve bağımlılık lock dosyaları
- Docker ve Compose yapılandırması
- Generator A ve B konfigürasyonları
- Kaydedilmiş hasta bazlı split indeksleri
- Bütün random seed değerleri
- Dataset, model ve sonuç checksum'ları
- Dataset card, generator card ve model cardlar
- Otomatik şema, maske, risk sayısı ve model yükleme testleri
- Temiz makinede tek komutla yeniden üretim
- Üretilen değerlerle makale tablolarının tolerans karşılaştırması

### Bitti kriteri

Temiz ortamda belgelenmiş tek akış; datasetleri, modelleri, metrikleri, tabloları ve
figürleri yeniden üretmektedir.

## Eksen 14 Dergi makalesi

### Makalenin kimliği

Makale klinik olarak doğrulanmış bir tahmin sistemi iddiasında bulunmayacaktır. Ana
konumlandırma; literatür temelli sentetik zamansal veri, risk özelinde modelleme,
dağılım dışı dayanıklılık, rule versus ML katkısı ve tam yığın karar destek mühendisliği
olacaktır.

### Yapılacaklar

- Kabul edilen konferans bildirisine açık atıf
- Giriş bölümünde journal extension paragrafı
- Conference to journal farklar tablosu
- Açık araştırma soruları
- Risk özelinde klinik ve zamansal problem tanımları
- `t0`, lookback, prediction horizon ve outcome şeması
- Dataset v3 ve iki generator tasarımı
- Rule only, ML only ve fusion sonuçları
- Delta, robustness ve alt grup analizleri
- Yaş gruplarına göre örnek sayısı, olay oranı, ayırım, kalibrasyon ve hata analizi
- Malnütrisyon ile kaşeksinin ayrı hedefler, zaman pencereleri ve klinik kullanım amaçları
  olduğunu gösteren yöntem ve sonuç bölümü
- Sistem performansı ve hata senaryoları
- Onkolog değerlendirmesi varsa yöntem ve sonuçları
- Savunmacı olmayan sınırlılık ve genellenebilirlik bölümü
- Risk özelinde yeterli, güncel ve doğrudan ilgili kaynakça
- Çizelge ve grafiklerin vektör çıktıları
- Ekran görüntülerinin okunabilir şekilde kırpılması
- Finansman, çıkar çatışması, yazar katkısı ve etik beyanları
- Veri, kod ve model erişilebilirlik beyanları
- Klinik kullanım uyarısı
- Sistem adı ve marka karışıklığı kararı
- Hedef dergiye özel biçimlendirme
- Editöre konferans genişletmesini açıklayan ön yazı

Kaynak sayısı yapay bir hedef olarak kullanılmayacaktır. Kapsamın gerektirmesi halinde
yaklaşık 45-60 nitelikli kaynak beklenebilir; önemli olan her klinik, veri ve yöntem
kararının doğru kaynakla desteklenmesidir.

### Bitti kriteri

Makaledeki bütün bulgular dondurulmuş reprodüksiyon paketinden gelmektedir. Konferans
çalışmasıyla farklar açıktır. Klinik iddialar kanıt düzeyini aşmamaktadır. Makale, ek
dosyalar ve ön yazı hedef derginin kontrol listesini karşılamaktadır.

## 5 Risk geliştirme sırası

Riskler akut, subakut ve uzun dönemli gruplar halinde geliştirilecektir.

### Akut riskler

1. Febrile neutropenia
2. Infection
3. Dehydration

### Subakut riskler

4. Renal toxicity
5. Hepatic toxicity
6. Immunotherapy related adverse event
7. Malnutrition
8. Treatment delay

### Uzun dönemli risk

9. Cachexia progression

### Birleşik çıktı

10. Overall clinical deterioration fusion

Febrile neutropenia ilk ve en ayrıntılı örnek olacaktır. Bu paket tamamlandığında diğer
risklerin kullanacağı şablon, test yapısı ve kalite kapıları sabitlenmiş olacaktır.

## 6 Risk başına çalışma döngüsü

Her bağımsız risk aşağıdaki sıradan geçer:

```text
Literatür matrisi
  -> klinik ve zamansal spesifikasyon
    -> feature kayıtları
      -> risk generator modülü
        -> rule baseline
          -> veri kalite testleri
            -> risk görünümü
              -> Colab model karşılaştırması
                -> ablation ve robustness
                  -> model kartı
                    -> kabul veya revizyon kararı
```

Bir risk kabul edilmeden sonraki riskin nihai implementasyonuna geçilmez. Ortak altyapı
çalışmaları paralel hazırlanabilir, ancak risk sonuçları ayrı kalite kapısından geçer.

## 7 Risk kabul kontrol listesi

Bir risk yalnızca aşağıdaki koşulların tamamını sağladığında tamamlanmış sayılır:

- Klinik hedef açık ve tek anlamlıdır.
- Uygun hasta ve tedavi grubu tanımlıdır.
- `t0`, gözlem penceresi ve tahmin ufku tanımlıdır.
- Her klinik feature'ın literatür kaynağı ve kanıt sınıfı vardır.
- Gerçek sistemde bulunmayan faktörler açıkça listelenmiştir.
- Model `t0` sonrasındaki hiçbir veriyi kullanmaz.
- Etiket tek bir gözlenebilir feature'ın kopyası değildir.
- Etiket başka bir risk etiketinden türetilmez.
- Güvenlik kuralı ve ML tahmini ayrıdır.
- Rule baseline sonucu raporlanmıştır.
- ML'in rule baseline üzerine ek katkısı ölçülmüştür.
- Feature maskesi eğitim ve serviste aynıdır.
- Hasta bazlı sızıntısız split kullanılmıştır.
- Çoklu seed sonuçları ve güven aralıkları vardır.
- Generator B ve dağılım kayması testleri tamamlanmıştır.
- Eksik veri ve ölçüm gürültüsü testleri tamamlanmıştır.
- Ablation ile gereksiz feature'lar incelenmiştir.
- Alt grup sonuçları raporlanmıştır.
- Model kartı ve sınırlılıklar hazırlanmıştır.
- Tek komutla yeniden üretim testi geçmektedir.

## 7B Overall deterioration füzyonu için kabul kriterleri

Bölüm 7'deki kontrol listesi dokuz bağımsız risk için yazılmıştır. Füzyon çıktısı ayrı bir
nesnedir ve kendi kabul kriterlerini gerektirir:

- Füzyon kuralı açık, yazılı ve deneylerden önce dondurulmuştur.
- Overall çıktının bir olasılık veya onuncu model değil, çok ufuklu bakım önceliği özeti
  olduğu kullanıcı arayüzünde açıkça belirtilmiştir.
- Yetmiş iki saatlik akut, 7 günlük subakut ve 30 günlük uzun dönem katmanları ayrı
  gösterilmiş; farklı ufuklar tek bir sahte olasılıkta birleştirilmemiştir.
- Hangi risk kombinasyonunun hangi inceleme önceliğini ürettiği tam olarak tanımlıdır.
- Aktif güvenlik uyarısının füzyon tarafından bastırılamadığı test edilmiştir.
- Bir veya daha fazla risk `not_applicable` olduğunda davranış tanımlıdır.
- Bir veya daha fazla risk `monitoring` durumundayken davranış tanımlıdır.
- Veri kalitesi düşükken füzyonun zorlanmadığı, `data_quality_warning` döndürdüğü test edilmiştir.
- Füzyon çıktısı hiçbir bağımsız riskin etiketini eğitim hedefi olarak kullanmaz.
- Mantıksal tutarlılık, monotonluk, açıklanabilirlik ve risk kartlarına izlenebilirlik
  otomatik testlerle doğrulanmıştır.
- Onkolog değerlendirmesinde okunabilirlik, eyleme geçirilebilirlik, yanlış güven hissi ve
  kafa karışıklığı ayrıca ölçülmüştür.
- Alternatif füzyon kuralları karşılaştırılmış ve seçim gerekçelendirilmiştir.
- İkincil keşifsel analiz yapılırsa 7 günlük plansız yatış/acil başvuru olayı dokuz etiketin
  mekanik birleşimi olarak üretilmemiş; risk süreçleri ve diğer hasta faktörleriyle klinik
  olarak makul nedensel ilişkileri belgelenmiştir.
- Bu 7 günlük olay karşılaştırmasında 72 saatlik ve 7 günlük bileşenler, basit `en yüksek
  risk` yaklaşımı ve güvenlik kuralları kullanılmış; 30 günlük kaşeksi sonucu aynı ufukta
  bir olasılık gibi değerlendirilmemiştir.
- Keşifsel sonuçlar sentetik iç doğrulama olarak adlandırılmış; klinik dış doğrulama
  iddiasında bulunulmamıştır.
- Overall özet onkolog değerlendirmesinde yarar göstermiyor veya kafa karıştırıyorsa
  sadeleştirilir ya da doktor panelinden çıkarılır.

## 8 Geri dönüş ve sürüm güvenliği

Programın her noktasında:

- Konferans sürümü etiketli ve değişmemiş halde kalır.
- Konferans dataset ve modelleri üzerine yazılmaz.
- Yeni çalışmalar `journal-v3` ve `journal/` altında yaşar.
- Eksen 10'a kadar mevcut AI servisi ve uygulama akışı değişmez.
- Eksen 10 ve 11'de yapılan entegrasyon sürümlü endpoint ve model manifesti kullanır.
- Yeni modeller yeterli bulunmazsa eski model paketi kullanılmaya devam eder.
- Yeni dal bırakıldığında mevcut uygulama etkilenmez.

Bu nedenle program geri dönüşlüdür. Büyük bilimsel değişiklikler veri ve AI katmanında
yapılırken çalışan ürün korunur.

## 8B Temel klinik kaynaklar

Bu bölüm programın dayandığı çekirdek kaynakları tutar. Risk başına tam kaynak listeleri
her riskin kendi `literature_matrix.csv` dosyasında bulunur; buradaki liste yalnızca
program düzeyindeki kararların dayanağıdır ve genişletilecektir.

**Kaşeksi tanımı ve evrelemesi**

Fearon K, Strasser F, Anker SD, ve ark. Definition and classification of cancer cachexia:
an international consensus. *Lancet Oncology*. 2011;12(5):489-495.
doi:10.1016/S1470-2045(10)70218-7 · PMID: 21296615

Kullanım: kaşeksi tanımı, tanı ölçütü (yüzde 5 üzeri kilo kaybı; BMI 20 altında veya
sarkopeni varlığında yüzde 2 üzeri kayıp), prekaşeksi-kaşeksi-refrakter evrelemesi.

**Kaşeksi ölçütlerinin antropometrik ve laboratuvar alternatifleri**

Huo Z, Chong F, Li N, ve ark. (INSCOC Group). Diagnostic Criteria for Cancer-Associated
Cachexia: Insights from a Multicentre Cohort Study. *Journal of Cachexia, Sarcopenia and
Muscle*. 2025;16(1):e13703. doi:10.1002/jcsm.13703 · PMID: 39949111

Kullanım: bu kohortta referans alınan ASMI bileşeni yerine MAMA, FFMI, baldır çevresi,
el kavrama gücü ve nötrofil-lenfosit oranı ile değiştirilmiş ölçütlerin karşılaştırılması. 5769 katılımcı,
5110 hastada nihai analiz. Altı değiştirilmiş ölçütün duyarlılığı yüzde 82.4 ile 90.7
arasında. Baldır çevresi ve NLR'nin aday gösterge olarak incelenmesinin dayanağı bu
çalışmadır; evde ölçüm geçerliliğini veya 30 günlük ilerleme tahminini kanıtlamaz.

**Malnütrisyon tanımı**

Cederholm T, Jensen GL, Correia MITD, ve ark. GLIM criteria for the diagnosis of
malnutrition: a consensus report from the global clinical nutrition community. *Journal of
Cachexia, Sarcopenia and Muscle*. 2019;10(1):207-217. doi:10.1002/jcsm.12383

Kullanım: malnütrisyonun fenotipik ve etiyolojik ölçütleri; etiyolojik ölçütlerin hastalık
yükü ve inflamasyonu da içermesi, dolayısıyla malnütrisyon ile kaşeksinin karşıt değil
örtüşen sendromlar olarak ele alınması.

**Yaşlı yetişkin onkoloji değerlendirmesi**

Dale W, Klepin HD, Williams GR, ve ark. Practical Assessment and Management of
Vulnerabilities in Older Patients Receiving Systemic Cancer Therapy: ASCO Guideline
Update. *Journal of Clinical Oncology*. 2023;41(26):4293-4312.
doi:10.1200/JCO.23.00933 · PMID: 37459573

Kullanım: 65 yaş ve üzerindeki hastalarda kronolojik yaşın yanında fiziksel ve bilişsel
işlev, komorbidite, çoklu ilaç kullanımı, beslenme ve sosyal destek gibi kırılganlık
alanlarının değerlendirilmesi. V3 bu alanların yalnızca erişilebilir ve kaynaklandırılmış
olanlarını generator varsayımı olarak temsil eder; geriatri değerlendirmesi yaptığını
iddia etmez.

**Genç yetişkin tanı ve rejim bağlamı**

- National Cancer Institute. [*Testicular Cancer Treatment (PDQ), Health Professional
  Version*](https://www.cancer.gov/types/testicular/hp/testicular-treatment-pdq). BEP ve
  ilgili tedavi bağlamı. Erişim: 13 Eylül 2026.
- National Cancer Institute. [*Hodgkin Lymphoma Treatment (PDQ), Health Professional
  Version*](https://www.cancer.gov/types/lymphoma/hp/adult-hodgkin-treatment-pdq). ABVD ve
  evreye göre alternatif tedavi bağlamları. Erişim: 13 Eylül 2026.
- National Cancer Institute. [*Osteosarcoma and UPS of Bone Treatment (PDQ), Health
  Professional Version*](https://www.cancer.gov/types/bone/hp/osteosarcoma-treatment-pdq).
  MAP tedavi bağlamı. Erişim: 13 Eylül 2026.
- National Cancer Institute. [*Ewing Sarcoma Treatment (PDQ), Health Professional
  Version*](https://www.cancer.gov/types/bone/hp/ewing-treatment-pdq). VDC/IE tedavi
  bağlamı. Erişim: 13 Eylül 2026.

Bu kaynaklar tanı-rejim uyumluluğunu destekler; yaşa koşullu görülme olasılıkları için
ayrıca epidemiyolojik kaynak gerekir.

**Kaynak doğrulama durumu.** Bu programdaki her bağlantı ve künye, makaleye girmeden önce
oturum açılmamış bir tarayıcıdan doğrulanacaktır. Mevcut durum:

| Kaynak | Durum |
|---|---|
| Fearon 2011 (doi, PMID) | Doğrulandı |
| Huo 2025 INSCOC (doi, PMID) | Doğrulandı |
| Cederholm 2019 GLIM (doi) | Doğrulandı |
| NCI PDQ testis | Doğrulandı |
| NCI PDQ osteosarkom | Doğrulandı |
| NCI PDQ Ewing | Doğrulandı |
| NCI PDQ Hodgkin lenfoma | Doğrulandı |
| Dale ve ark. 2023, ASCO geriatrik onkoloji | Doğrulandı |
| Riley ve ark. 2019 Part II (Stat Med) | Doğrulandı |
| Riley ve ark. 2020 (BMJ) | Doğrulandı |

Bu tablodaki tüm kaynaklar doğrulanmış durumdadır. Eksen 3'te eklenecek risk özelindeki
kaynaklar da aynı kurala tabidir: künye ve bağlantı doğrulanmadan `literature_matrix.csv`
içine yazılmaz.

**Pediatrik editöryel kurul uyarısı.** Osteosarkom ve Ewing sarkomu PDQ özetleri NCI'nin
**PDQ Pediatric Treatment Editorial Board**'u tarafından hazırlanmaktadır. Biz bu tanıları
18-30 yaş **yetişkin** bandında kullanıyoruz. Bu özetlere atıf yapmak yanlış değildir ancak
tek başına yeterli değildir; genç yetişkin onkolojisi için erişkin kaynaklı bir kılavuz veya
derleme de eklenmelidir. Aksi halde yetişkin rejim bağlamının pediatrik özetten türetildiği
eleştirisi haklı olur. Bu ek kaynak Eksen 3'te, tanı spesifikasyonları yazılırken
bulunacaktır.

**Örneklem büyüklüğü metodolojisi**

Riley RD, Snell KIE, Ensor J, ve ark. Minimum sample size for developing a multivariable
prediction model: Part II — binary and time-to-event outcomes. *Statistics in Medicine*.
2019;38(7):1276-1296. doi:10.1002/sim.7992

Riley RD, Ensor J, Snell KIE, ve ark. Calculating the sample size required for developing
a clinical prediction model. *BMJ*. 2020;368:m441. doi:10.1136/bmj.m441

Kullanım: ikili ve zaman-aşımı hedefler için minimum örneklem hesabı; shrinkage, kalibrasyon
hassasiyeti ve aday parametre sayısı temelli ölçütler. Değişken başına sabit olay sayısı gibi
kestirme kuralların kullanılmaması gerektiğinin dayanağı da bu kaynaklardır.

**Eklenecekler**

Aşağıdaki başlıklarda kaynaklar risk spesifikasyonları yazılırken eklenecek ve bu bölüme
taşınacaktır:

- Febril nötropeni risk sınıflaması ve G-CSF profilaksisi kılavuzları
- Ateşli nötropenide şiddet sınıflaması (MASCC, CISNE)
- Sisplatin ve diğer ajanların nefrotoksisite profilleri
- İlaca bağlı karaciğer hasarı değerlendirme ölçütleri
- İmmün ilişkili advers olay yönetim kılavuzları
- CTCAE sürümü ve derecelendirme tanımları
- Osteosarkom ve Ewing sarkomu için evreye ve tedavi bağlamına uygun rejimler
- Dehidratasyon ve hidrasyon değerlendirme ölçütleri
- Klinik tahmin modeli raporlama kılavuzları (TRIPOD+AI, DECIDE-AI)

**Kaynak kullanım kuralı:** Programda veya makalede bir klinik eşik, ölçüt ya da dağılım
varsayımı kullanılıyorsa kaynağı ve kanıt sınıfı `literature_matrix.csv` içinde kayıtlı
olmalıdır. Kaynağı olmayan varsayımlar "generator varsayımı" olarak işaretlenir ve
sınırlılık tablosuna girer. Kaynak sayısı hedefi yoktur; her kararın doğru kaynakla
desteklenmesi hedeftir.

## 9 İlk başlanacak işler

Uygulama sırası değiştirilmeyecektir:

1. Konferans sürümünü dondur ve bütün mevcut çıktıları doğrula.
2. Mevcut v2.1 rule baseline ve bilimsel denetimi tamamla.
3. `keep change remove` karar matrisini çıkar.
4. Ortak zamansal protokolü kilitle.
5. Febrile neutropenia literatür ve klinik spesifikasyon paketini hazırla.
6. Ortak generator çekirdeğini kur.
7. Febrile neutropenia modülünü, rule baseline'ını ve testlerini tamamla.
8. İlk risk için Colab deneylerini bitir ve şablonu doğrula.
9. Kalan riskleri belirlenen sırayla geliştir.
10. Multi risk füzyon, sistem entegrasyonu, onkolog paketi ve makaleyi tamamla.

İlk yeni dataset satırı, Eksen 1 denetimi ve Eksen 2 protokolü tamamlanmadan
üretilmeyecektir. Bu kural, yeni veri setini ikinci kez baştan yapmak zorunda kalmamızı
önler.
