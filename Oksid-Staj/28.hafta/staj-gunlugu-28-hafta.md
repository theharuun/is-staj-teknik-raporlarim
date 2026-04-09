#  Staj Gelişim Günlüğü - 28. Hafta: Tarayıcı Optimizasyonları, Katmanlı Arama Algoritmaları ve ML Mimarisi

Bu hafta, Fiyat Benchmark projemizdeki detay sayfası kazıma (scraping) yeteneklerini yatayda genişleterek 5. sitenin entegrasyonunu tamamladım. Epey.com üzerinden beslenen FPS tahminleme projesi için hem veri çekme boru hattını (ETL) hem de Makine Öğrenmesi (ML) model mimarisini baştan aşağı revize ettim. Ayrıca Chrome sürücülerinin performansını işletim sistemi düzeyinde optimize ettim.

---

##  Odak Alan 1: Tarayıcı Optimizasyonu ve Benchmark Genişlemesi

Web scraping işlemlerinin kalbinde yer alan tarayıcı otomasyonunu (Undetected ChromeDriver) daha hızlı ve stabil hale getirmek için düşük seviyeli (low-level) konfigürasyonlar uyguladım.

* **Chrome Performans ve Ölçeklendirme (DPI) İyileştirmeleri:** * `--disable-features=CalculateNativeWinOcclusion` argümanı ile Chrome'un arka plandaki pencereleri dondurma (occlusion) özelliğini devre dışı bıraktım. Bu sayede arka planda çalışan process'lerin performans kayıplarını engelledim.
    * `--force-device-scale-factor=1` ve `--high-dpi-support=1` argümanlarıyla çözünürlük ölçeklendirme sorunlarını çözerek, farklı işletim sistemlerinde UI elementlerinin (butonlar, menüler) yerlerinin kaymasını ve tıklama hatalarını (Click Intercepted) ortadan kaldırdım.
* **X ve Y Siteleri Adapter Entegrasyonları:**
    * Fiyat benchmark projesi kapsamında iki yeni sitenin (X ve Y siteleri) detay sayfası kazıma işlemleri için Adapter, Normalizer, Parser ve Selector yapılarını yazdım.
    * Oturum kontrolleri (Session Management), marka bulunamadığı durumlara karşı defansif hata yönetimi ve pop-up temizleme algoritmaları eklendi.
* **Mevcut Durum:** Hedeflenen 6 benchmark sitesinden **5 tanesi eksiksiz olarak (detay sayfaları dahil) tamamlandı.** API'ler lokal bilgisayarımda başarıyla çalışıyor. Gelecek hafta 6. sitenin bitimiyle birlikte Sistem Yöneticisi (SysAdmin) ile Linux sunucu deployment süreçleri başlayacak.

---

##  Odak Alan 2: Katmanlı Arama Algoritması (Fallback Mechanism) ve Veri Temizliği

FPS hesaplama modelinin ana beslenme kaynağı olan `BaseEpeyScraper` modülünü, aranan ürünü her koşulda bulabilmesi için "Zeki Arama" mantığıyla yeniden yazdım.

* **Veri Kirliliğini Önleme (Domain Isolation):** Oyuncu ve son kullanıcı sistemleri için FPS tahminlediğimizden, modelin kafasını karıştıracak olan **Sunucu (Server) İşlemcilerini (Xeon, EPYC)** SQL tarafında filtreleyerek dışarıda bıraktım.
* **4 Katmanlı Zeki Arama (Super Fallback) Algoritması:** Botun bir ürünü bulamaması durumunda pes etmesini engelleyen 4 aşamalı bir arama akışı kurguladım:
    1.  **Standart Arama:** Temizlenmiş ürün adıyla arama (`clean_query`).
    2.  **Kısaltılmış Arama:** İlk deneme başarısız olursa, ürün adının sadece ilk 3 kelimesiyle arama (`short_query`).
    3.  **Raw Fallback:** Ürün adının son 10 kelimesini (genellikle ayırt edici model numaraları) içeren ham arama.
    4.  **Super Fallback (Doğrudan Navigasyon):** Tüm aramalar başarısız olursa veya site içi arama motoru çökmüşse, önceden bilinen URL üzerinden doğrudan sayfaya gitme (`target_url`).
* **Anti-Bot:** Bu akış içerisine Cloudflare ("just a moment") atlatma ve DOM tabanlı pop-up temizleme fonksiyonları entegre edildi.

---

##  Odak Alan 3: Makine Öğrenmesi (ML) Mimari Refactoring ve Epey Modülü

TPU (TechPowerUp) için kurguladığım veri çekme ve modelleme mantığının bir benzerini, Epey.com verileriyle çalışacak şekilde sıfırdan inşa ettim.

* **Job Queue (İş Kuyruğu) Sistemi:** Yeni bir donanım geldiğinde bunu algılayan, kombinasyonlar üreten ve bunları FPS çekimi için sıraya sokan (pending -> completed) bir kuyruk mimarisi oluşturuldu.
* **Model ve Klasör Mimarisi Revizyonu:** Proje dosyalarını profesyonel bir ML projesinde olması gerektiği gibi `config`, `export`, `featurize`, `split` ve `train` modüllerine böldüm.
* **MultiRMSE ve Multi-Target Geçişi:** `ModelConfig` sınıfını, birden fazla hedef sütunu (Örn: 1080p, 1440p, 4K FPS değerleri) aynı anda tahmin edebilecek (Multi-Target Regression) şekilde güncelledim ve kayıp fonksiyonunu (Loss Function) **MultiRMSE** olarak belirledim.
* **Özellik Çıkarımı (Feature Engineering):** Float, integer ve boolean veri tiplerinin daha iyi işlenmesi için parser fonksiyonları geliştirildi. Performansı etkileyen yeni türetilmiş özellikler (derived features) ve oyun meta verileri sisteme eklendi.
* **Arayüz (UI) Güncellemesi:** Eğitilen bu yeni model, donanım kombinasyonu arama işlevselliğinin eklendiği güncel **Streamlit UI** paneline başarıyla entegre edildi.

---

##  Odak Alan 4: n8n Otomasyonları ve Revizyonlar

Rutin otomasyon kontrolleri sırasında iş birimlerinden gelen raporlama taleplerini karşıladım. Mevcut n8n iş akışlarından birinde, sistemden çekilen verinin daha spesifik olması için arka plandaki SQL sorguları analiz edilerek ilgili filtre revizyonları gerçekleştirildi ve otomasyon kesintisiz çalışmaya devam etti.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Tarayıcı Performansı (Browser Automation)** | `--disable-features=CalculateNativeWinOcclusion` ve DPI ayarları ile headless browserların donma ve UI kayma sorunlarını çözme. |
| **Gelişmiş Arama (Fallback Mechanism)** | Bir ürün bulunamadığında sırasıyla Kısa, Ham ve URL tabanlı **4 katmanlı arama algoritması** çalıştırarak başarı oranını maksimize etme. |
| **ML Mimari Tasarımı** | **Multi-Target Regression** desteği, **MultiRMSE** kayıp fonksiyonu ve ayrıştırılmış modüler proje yapısı (`featurize`, `split`, `train`) kurgulama. |
| **Özellik Mühendisliği (Feature Engineering)** | Sunucu işlemcilerini veri setinden dışlama ve yeni oyun meta verilerini özellik olarak (feature extraction) modele dahil etme. |
| **Uçtan Uca Teslimat (End-to-End)** | Benchmark projesinde 5 sitenin detay sayfası kazıma işlemlerini tamamlama ve Epey FPS UI'ını canlı sunuma hazır hale getirme. |