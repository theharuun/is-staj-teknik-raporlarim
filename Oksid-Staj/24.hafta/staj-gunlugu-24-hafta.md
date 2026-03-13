#  Staj Gelişim Günlüğü - 24. Hafta: Domain Odaklı ML Pivotu, Gözlemlenebilirlik (UI) ve Regex Otomasyonları

Bu hafta, yöneticimin stratejik geri bildirimleri doğrultusunda FPS modelimizde "Domain Odaklı" (Domain-Specific) bir yaklaşıma geçiş yaptım. Ayrıca finansal veri eşleştirme süreçlerini Regex ile kodlayarak statik bir otomasyon kurdum ve veri kazıma operasyonlarımızın sağlığını izlemek için Streamlit tabanlı bir gösterge paneli (Dashboard) geliştirdim.

---

##  Odak Alan 1: Yönetim Yönlendirmesi ve Domain Odaklı FPS Modeli

Projenin en kritik dönüşümlerinden biri bu hafta yaşandı. FPS hesaplama modelimizin genelleme yeteneğini artırmak için geniş çaplı veri toplarken, müdürümün yönlendirmesiyle felsefi bir değişikliğe gittim.

* **Stratejik Pivot (Domain-Specific Model):** Dünyadaki tüm donanımları kapsayacak devasa bir model eğitmek yerine, şirketimizin (İncehesap) güncel ve kısıtlı işlemci/ekran kartı (CPU/GPU) stoğuna odaklanan **"Domain (Alan) Odaklı"** bir modelin ilk etapta çok daha verimli olacağına karar verildi. Bu yaklaşım, az veriyle bile "Overfitting" (aşırı öğrenme) riskini minimize ederek stoktaki kombinasyonları kusursuz öğrenmeyi hedefliyor.
* **Epey.com Entegrasyonu ve Veri Boru Hattı:** Arka planda devasa veri çekme işlemleri devam ederken, önceliği bu spesifik domaine verdim. İlgili donanımların teknik özelliklerini `epey.com` üzerinden çekecek modülleri geliştirdim.
* **Versiyon Kontrolü (Git) Çıktıları:** Bu süreçteki yoğun geliştirmelerimi commit geçmişime de yansıttım:
* `feat: Dışa aktarma, özellik çıkarma, düzleştirme, tahmin ve eğitim işlevlerine sahip Epey ML modülü eklendi` komutu ile temel eğitim boru hattını (pipeline) kurdum.
* `refactor : Model eğitim ve ön işleme komut dosyaları yeniden düzenlendi` adımıyla kod mimarisini optimize ettim.
* `feat: Epey FPS hesaplayıcı modülü eklendi; model yükleme, tahmin ve veri ön işleme işlevleri tanımlandı` komutu ile modeli çıkarım (inference) yapmaya hazır hale getirdim ve detaylı bir rapor sundum.



---

##  Odak Alan 2: Sistem Gözlemlenebilirliği ve Streamlit Dashboard

Arka planda çalışan otomasyonların (Rau Analiz vb.) durumunu izlemek için sadece veritabanı loglarına bağlı kalmak yerine, kullanıcı dostu bir arayüz geliştirdim.

* **Log Takip Arayüzü (Streamlit):** Python'un veri uygulamaları kütüphanesi olan Streamlit'i kullanarak interaktif bir UI geliştirdim. (Commit: `feat: Proje yapısı oluşturuldu; temel dosyalar eklendi ve veri yükleme, filtreleme, görselleştirme işlevleri geliştirildi.`)
* **İzlenen Metrikler:** Bu panel üzerinden günlük görevlerin başarı oranları, hatalı işlemler ve botların veri kazıma (scrape) süreçlerinde karşılaştığı hata logları görselleştirilerek anlık takip edilebilir hale getirildi.
* **Gelecek Adım (Deployment):** Önümüzdeki hafta bu arayüzü, şirketin "Rocky" Linux sunucusunda host ederek (deploy) tüm ekibin erişimine açmayı planlıyorum.

---

##  Odak Alan 3: Kural Tabanlı Finansal Otomasyon ve Anti-Bot Sistemleri

* **IBAN Eşleştirme (Regex Engine):** Geçen hafta planlanan Excel tabanlı IBAN ve kişi/şirket eşleştirme otomasyonu için Python tabanlı statik bir sistem kurdum. Açıklama metinlerindeki varyasyonları yakalamak için farklı **Regex (Düzenli İfadeler)** kütüphaneleri ve karmaşık kurallar uyguladım. (Commit: `feat: .gitignore ve match.ipynb dosyaları eklendi; IBAN eşleştirme ve veri işleme için ilk kurulum gerçekleştirildi.`) Şu an kod üzerinden manuel tetiklenen (statik) bu yapıyı, haftaya n8n ile entegre ederek dinamik ve otonom bir sürece dönüştürmeyi hedefliyorum.
* **Cloudflare Bypass (Matematiksel Çözüm):** Hedef sitelerdeki güvenlik önlemlerini aşmak için veri kazıma (scraping) yeteneklerimi derinleştirdim. Cloudflare'in anti-bot mekanizmalarını atlatmak adına bot hareketlerini daha organik hale getiren matematiksel hesaplamalar ve çözümler (browser fingerprinting, timing analysis vb.) üzerine düzenlemeler getirdim.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Agile & Strategic Pivot** | Yöneticinin yönlendirmesiyle global bir modelden, şirket stoğuna uygun **Domain-Specific (Alan Odaklı)** ML modeline hızlı geçiş yapabilme yeteneği. |
| **Data Observability** | Otomasyon loglarını ve hata metriklerini **Streamlit** kütüphanesi ile interaktif bir web paneline (UI) taşıma. |
| **NLP & Rule-Based Logic** | Finansal veri eşleştirme süreçlerini karmaşık **Regex** kuralları ile otomatikleştirerek operasyonel süreyi kısaltma. |
| **Advanced Web Scraping** | Cloudflare korumalarını aşmak için sezgisel ve **matematiksel bypass** yöntemleri geliştirme. |

###  Gelecek Hafta Hedefleri

1. **Rocky Sunucu Deployment:** Geliştirilen Streamlit tabanlı log takip arayüzünün şirket içi Rocky Linux sunucusunda ayağa kaldırılması ve test edilmesi.
2. **n8n ile Dinamik Otomasyon:** Jupyter/Python ortamında (`match.ipynb`) statik olarak çalışan IBAN eşleştirme kodlarının n8n iş akışlarına (Workflow) dahil edilerek tam otonom hale getirilmesi.
3. **Domain Model Optimizasyonu:** Epey.com'dan çekilen teknik verilerle kurulan yeni FPS hesaplayıcı modülünün hiperparametre ince ayarlarının (Tuning) yapılması.