# Devlog

> En son yapılan iş en üstte. Format: `## YYYY-MM-DD HH:MM` + madde madde yapılanlar.

## 2026-11-24 05:30
- Hata bildirimleri artık kendiliğinden kaybolmuyor: sağ üstte ✕ ile kapanıyor, metin seçilip kopyalanabiliyor (uzun hatalar kaydırılabilir). Başarı bildirimleri eskisi gibi 3 sn sonra kayboluyor.

## 2026-11-24 05:15
- **Eski uygulamadan veri taşıma (antrenman-defteri → SaboFit):** Kullanıcının eski yedeği (`Desktop/yedek.txt`, surum 2) bir kerelik, repoya girmeyen bir script ile SaboFit yedek formatına çevrildi → `Desktop/sabofit-yedek.json`.
  - 7 hafta (5 Ekim – 22 Kasım), 32 gün, 422 set; programlar (gün başlığı, set×tekrar, AI egzersiz notları, kaslar Latince→Türkçe), gün notları → "Gün sonu notu", yemekler, kilo ölçümleri (2), 9 Kasım analizi (detaylı metin, "HAFTALIK NOT" maddeleri → öneriler, sonraki haftanın programı), 16 Kasım program notu → 9 Kasım analizine "Gelecek hafta için kararlar" olarak.
  - Dolu her set "yapıldı" sayıldı. Plank'te tekrar = saniye, tek taraflı hareketler egzersiz notunda belirtildi. Boy (173 cm) aktarılmadı → kullanıcı Ayarlar'a girecek.
  - Kullanıcının isteğiyle bu hafta (23 Kasım, geçen haftadan kopyalanmış) **atlandı**.
- `index.html` düzeltmeleri:
  - Yedek yükle: dosyada `settings` yoksa mevcut ayarlar korunuyor (önceden varsayılana dönüyordu). Bildirimde hafta sayısı gösteriliyor.
  - Süreç → "Haftalık hacim": devam eden hafta hariç, ilk 2 ve son 2 kayıtlı haftanın ortalaması kıyaslanıyor (yarım ilk hafta yüzünden +%989 görünüyordu → +%103, 12.6 → 25.5 ton/hafta).

## 2026-11-24 04:30
- **Web uygulaması (PWA), v0.3:**
  - `manifest.webmanifest` (standalone, portre, koyu tema, Türkçe), `icons/` (SVG + 192/512/maskable PNG, apple-touch-icon, favicon; headless Chrome ile SVG'den üretildi).
  - `sw.js` service worker: sayfa için önce ağ (güncellemeler hemen gelir) sonra önbellek; simge/manifest için önbellek; başka sitelere (yapay zekâ API'leri, esm.sh) dokunmuyor → uygulama internetsiz de açılıyor (yapay zekâ özellikleri hariç).
  - `index.html`: manifest/ikon/iOS meta etiketleri, durum çubuğu için güvenli alan boşluğu, çekip yenileme (overscroll) kapalı.
  - Ayarlar → Genel: "Uygulama olarak yükle" satırı. Android/Chrome'da "Yükle" butonu (`beforeinstallprompt`), iOS'ta Safari "Ana Ekrana Ekle" talimatı, yüklüyse "✓ Uygulama olarak çalışıyor".
- **Hata düzeltmesi:** Hiç kaydı olmayan ilk açılışta `settings.prompts` tanımsız olduğu için Ayarlar sayfası çöküyordu (prompt düzenleyici eklendiğinden beri). `load()` artık varsayılanları da `normalizeData`'dan geçiriyor.
- Test: yerel sunucuda temiz profille konsol hatası yok; service worker kuruldu ve tüm uygulama dosyalarını önbelleğe aldı.

## 2026-11-24 04:00
- Git reposu oluşturuldu (`main`), `.gitignore` eklendi. Yayın öncesi dosyalarda API anahtarı taraması yapıldı (temiz).
- GitHub'a **public** repo olarak gönderildi: https://github.com/sabahattincicek/sabofit
- GitHub Pages açıldı (kaynak: `main` / kök): https://sabahattincicek.github.io/sabofit/
- Not: Veriler (kayıtlar, API anahtarları) sitede değil, her cihazın kendi tarayıcısında tutuluyor. Repo public olduğu için `devlog.md`, `roadmap.md`, `CLAUDE.md` de herkese açık.

## 2026-11-24 03:40
- **Düzenlenebilir prompt şablonları:** Ayarlar'a "Yapay zekâ talimatları (prompt)" bölümü eklendi. 3 şablon: Sistem talimatı, Program oluşturma, Haftalık analiz. `{değişken}` yer tutucuları (`{profil}`, `{gecmis}`, `{onceki_analizler}`, `{son_hafta_kayitlari}`, `{bu_hafta}`, `{ek_not}`, `{hafta}`, `{hafta_tarih}`, `{sonraki_hafta_tarih}`, `{birim}`, `{koc_talimatlari}`); değişkene dokununca imlece ekleniyor. "Varsayılana dön" ve "👁 Önizle" (seçili hafta için giden tam metin + kopyala). Varsayılanla aynı/boş şablon `settings.prompts[k] = ''` olarak saklanır → varsayılan güncellenirse otomatik gelir. JSON şeması şablona dahil değil, otomatik ekleniyor.
- **Geçmiş bağlamı genişletildi:** Daha önce program oluştururken sadece profil + önceki 4 haftanın özeti, analizde ise bunlara ek olarak haftanın kayıtları gidiyordu; önceki analizler hiç gitmiyordu. Artık:
  - Program: profil + **son 2 analiz** (özet, öne çıkanlar, "Gelecek hafta için kararlar" bölümü, o haftaki ek not) + önceki 4 hafta özeti + **kaydı olan son haftanın tüm detaylı kayıtları** + ek not.
  - Analiz: profil + son 2 önceki analiz + önceki 4 hafta özeti + bu haftanın tüm kayıtları + ek not; ayrıca "önceki kararlar uygulandı mı" değerlendirmesi isteniyor.
- Sistem talimatına "düzgün ve yazım hatası olmayan Türkçe" kuralı eklendi.

## 2026-11-24 03:00
- **Haftaya özel ek not:** "Yapay zekâ ile program oluştur" ve "Haftayı analiz et / Analizi yeniden oluştur" (ana sayfa + Süreç hafta detayı) artık önce bir not penceresi açıyor (`askNote`). Yazılan metin standart prompt'a "SPORCUNUN BU HAFTAYA ÖZEL EK İSTEKLERİ" başlığıyla ekleniyor; analizde sonraki haftanın programı için de geçerli. Boş bırakılabilir, "Vazgeç" isteği iptal eder.
- Not `program.extraNote` / `analysis.extraNote` olarak saklanıyor; analiz ekranında "Senin notun" kutusunda görünüyor, yeniden analizde pencereye önceki not dolu geliyor. Otomatik analizde pencere açılmıyor (not yok).
- Test (headless, yapay zekâ çağrısı taklit edilerek): not prompt'a giriyor, kaydediliyor, vazgeç istek göndermiyor.

## 2026-11-24 02:30
- Kullanıcı Gemini'de ~2 saat boyunca sürekli "model çok yoğun" (5xx) hatası aldı.
- Gemini / OpenAI / uyumlu servis çağrılarına otomatik tekrar deneme eklendi: 429 ve 5xx hatalarında 5 sn, 15 sn, 30 sn bekleyip 3 kez daha deniyor (günlük kota hatasında denemiyor). Anthropic SDK zaten kendi tekrar denemesini yapıyor.
- 5xx hata mesajı Türkçeleştirildi: "Servis şu an çok yoğun… daha hafif bir model seç".
- Tarayıcıdan erişim (CORS) kontrolü: OpenRouter, Groq, Mistral izin veriyor (hata yanıtlarında da) → "OpenAI uyumlu" seçeneğiyle kullanılabilir. Cerebras hata yanıtında CORS başlığı döndürmüyor.

## 2026-11-24 00:20
- Ayarlar → Model: `<datalist>` önerisi kullanışsızdı (kutu doluyken liste açılmıyordu). Yerine "Test et" sonrası görünen normal bir **seçim kutusu** (`#s-model-sel`) eklendi; altında elle yazma kutusu duruyor. Çekilen liste sağlayıcı başına `settings.modelLists` içinde saklanıyor, sayfa yenilense de kalıyor.

## 2026-11-24 00:10
- **Çoklu yapay zekâ sağlayıcısı:** Ayarlar'da sağlayıcı seçimi geri geldi: Anthropic (Claude), OpenAI, Google Gemini, OpenAI uyumlu (özel API adresi; DeepSeek, OpenRouter, Groq vb.).
  - Anahtar ve model her sağlayıcı için ayrı saklanıyor (`settings.keys`, `settings.models`, `settings.baseUrl`). Eski `apiKey`/`model` alanları otomatik taşınıyor.
  - Model artık serbest metin + öneri listesi; "Test et" sağlayıcının model listesini çekip listeyi dolduruyor ve anahtarı doğruluyor.
  - Çağrılar: Anthropic → SDK (değişmedi); OpenAI → `chat/completions` + `json_schema` strict; Gemini → `generateContent` + `responseMimeType: application/json`; uyumlu servis → `json_object` (desteklemezse onsuz tekrar). OpenAI dışında şema prompt'a da ekleniyor, yanıt metninden JSON ayıklanıyor.
  - Not: OpenAI hatalı anahtarla POST'ta CORS başlığı döndürmüyor (tarayıcı "bağlantı hatası" görüyor) → kullanıcı "Test et"e yönlendiriliyor.
- Test (sahte anahtarlarla, headless Chrome): 4 sağlayıcıya da istek gidiyor ve hatalar doğru gösteriliyor. Gerçek anahtarla başarılı yanıt henüz denenmedi.

## 2026-11-23 23:40
- Ayarlar: "Sağlayıcı" alanı soluk/kilitli bir kutu yerine sabit bilgi satırı yapıldı ("Anthropic (Claude)" etiketi). Kullanıcı kilitli kutuyu hata sandı.

## 2026-11-23 23:30
- **Uygulama çalışır hale getirildi (v0.2).** `index.html` örnek verilerden arındırıldı, tüm içerik JS ile veriden üretiliyor. CSS aynı kaldı.
- Veri: `localStorage` anahtarı `sabofit:v1` → `{ settings, weeks: {"YYYY-MM-DD"(pazartesi): {program, analysis}}, logs: {"YYYY-MM-DD": {ex: {exId: {name, sets:[{kg,reps,done}], note}}, weight, food, endNote}} }`. İlerleme takibi egzersiz **adına** göre.
- Ana sayfa: hafta ileri/geri, gün seçimi, set girişi (✓ boş alanları önceki/hedef değerle doldurur), + / − set, egzersiz notu, kilo (son ölçüme göre fark), yediklerim, gün sonu notu — yazarken otomatik kaydediliyor. Program yoksa "Yapay zekâ ile program oluştur" / "Geçen haftanın önerdiği programı uygula".
- Hafta analizi: istatistikler ve gelişmeler (önceki → bu hafta) yerelden hesaplanıyor; özet, detaylı analiz, öneriler ve gelecek hafta programı yapay zekâdan. "Programı uygula" sonraki haftaya yazıyor.
- Yapay zekâ: Anthropic TS SDK (`esm.sh/@anthropic-ai/sdk@1.7.0`, tarayıcıdan `dangerouslyAllowBrowser`), varsayılan `claude-opus-5-5`, effort `high`, streaming + `finalMessage()`, JSON şemalı yapılandırılmış çıktı (`output_config.format`), Opus/Sonnet için `fallbacks: "default"` (beta `server-side-fallback-2026-07-01`). Otomatik analiz: ayarlanan gün/saatten sonra uygulama ilk açıldığında çalışır; "onayımla uygula" kapalıysa program sonraki haftaya otomatik yazılır.
- Süreç: tüm grafikler, özet kutuları, ana hareketler, kas dağılımı, devamlılık ve geçmiş haftalar gerçek veriden; hafta detayında gün gün setler (rekorlar yeşil) ve o haftanın analizi.
- Ayarlar: tüm alanlar kaydediliyor; "Test et" `models.retrieve` ile anahtarı doğruluyor. Yedek indir/yükle (API anahtarı yedeğe girmez), tüm veriyi metin olarak kopyala, tüm kayıtları sil (ayarlar korunur), 7 günlük yedek hatırlatma bandı.
- Kaldırılanlar: başka yapay zekâ sağlayıcıları (sadece Anthropic), "hafta başlangıcı" ve "dinlenme sayacı" ayarları.
- Test: headless Chrome'da örnek veriyle tüm sayfalar ve etkileşimler hatasız; sahte anahtarla API'ye istek gidip "API anahtarı geçersiz" hatası doğru gösteriliyor. **Gerçek anahtarla program/analiz üretimi henüz denenmedi.**

## 2026-11-23 22:30
- **Ayarlar sayfası** arayüz taslağı eklendi; alt menüdeki 3 sayfa arası geçiş çalışıyor.
  - Yapay zekâ: sağlayıcı, model, API anahtarı (göster/gizle), bağlantı testi; otomatik haftalık analiz (gün + saat), programı onayla uygula, koç talimatları.
  - Profil ve hedefler: yaş, boy, ana hedef, hedef kilo, deneyim, antrenman günleri, seans süresi, ekipman, sakatlıklar/kısıtlar (yapay zekâya bağlam olarak gidecek).
  - Genel: kg/lb, hafta başlangıcı, dinlenme sayacı.
  - Yedekleme ve veri: son yedek bilgisi, yedek hatırlatması, JSON yedek indir/yükle, tüm veriyi metin olarak kopyala, tüm verileri sil.

## 2026-11-23 22:15
- Süreç → Geçmiş haftalar: bir haftaya tıklayınca **hafta detayı** açılıyor (geri butonu ile listeye dönülüyor).
  - "Antrenmanlar" sekmesi: hafta özet sayıları + gün gün açılır kartlar (egzersizler, setler kg×tekrar, rekor setler yeşil, egzersiz notları, kilo, yediklerim, gün sonu notu). Dinlenme günleri ayrı satır.
  - "Analiz" sekmesi: o haftanın yapay zekâ özeti, detaylı analiz (açılır), notlar/öneriler.
  - Şimdilik tüm haftalar aynı örnek veriyi gösteriyor (sadece başlık/tarih değişiyor).

## 2026-11-23 22:00
- **Süreç sayfası** arayüz taslağı eklendi (örnek veri). Alt menüden Ana Sayfa ↔ Süreç geçişi çalışıyor.
  - Zaman aralığı seçici (4 Hafta / 12 Hafta / Tümü — henüz işlevsiz).
  - Özet kutuları: antrenman günü, kilo değişimi, hacim artışı, eksiksiz hafta serisi.
  - Vücut ağırlığı çizgi grafiği ve haftalık hacim çubuk grafiği (SVG, dokununca hafta değeri gösteren ipucu).
  - Ana hareketler: her hareket için başlangıç → şimdi, % artış ve mini grafik.
  - Kas grubu dağılımı (haftalık set sayısı), devamlılık ızgarası (son 8 hafta), geçmiş haftalar listesi.

## 2026-11-23 21:45
- Hafta Analizi: Özet kartının altına açılır/kapanır **Detaylı analiz** alanı eklendi (`<details>`). İçinde yapay zekânın tam metin çıktısı (örnek): genel değerlendirme, egzersiz bazında analiz, toparlanma, beslenme, vücut ağırlığı, gelecek hafta kararları + "Metni kopyala" butonu (henüz işlevsiz).

## 2026-11-23 21:35
- `index.html`: "+ Egzersiz ekle" butonu kaldırıldı (egzersizleri yapay zekâ belirleyecek, elle ekleme yok).
- Gün şeridinin sonuna "Analiz ✦" kutusu eklendi; tıklanınca gün görünümü yerine **Hafta Analizi** görünümü açılıyor.
- Hafta Analizi taslağı (örnek veri): özet istatistikler (antrenman günü, toplam set, hacim, kilo), yapay zekâ özeti, egzersiz gelişmeleri (önceki → bu hafta), notlar/öneriler, gelecek hafta programı önizlemesi, "Programı uygula" ve "Analizi yeniden oluştur" butonları.

## 2026-11-23 21:25
- `index.html`: Egzersiz başlıkları tıklanabilir yapıldı → Google Görseller'de "<egzersiz> exercise proper form" araması yeni sekmede açılıyor.
- Başlığın altına çalıştırılan kaslar etiket olarak eklendi (Türkçe isim); tıklanınca "<anatomik isim> muscle anatomy" görsel araması açılıyor.

## 2026-11-23 21:15
- Proje tanımı: kişisel antrenman takip sitesi (tek kullanıcı, mobil görünüm). Kayıtlar yapay zekâya analiz ettirilip haftalık program üretilecek.
- Sayfalar: Ana Sayfa, Süreç, Ayarlar.
- `index.html` oluşturuldu: Ana sayfanın **sadece arayüz taslağı** (örnek verilerle, kayıt yok).
  - Hafta başlığı + 7 günlük gün seçici, günün programı (ör. Push Günü), ilerleme çubuğu.
  - Egzersiz kartları: set / önceki / kg / tekrar / tamamlandı, "+ Set ekle", "Not ekle".
  - Günlük kayıt: kilo ölçümü, bugün ne yedim, gün sonu notu.
  - Alt navigasyon: Ana Sayfa / Süreç / Ayarlar.

## 2026-11-23 21:08
- `roadmap.md` ve `devlog.md` dosyaları oluşturuldu.
- `CLAUDE.md` eklendi: oturum başında sadece `devlog.md` ilk 150 satırı okunacak; devlog/roadmap kuralları tanımlandı.
