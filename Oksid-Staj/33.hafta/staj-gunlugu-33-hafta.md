#  Staj Gelişim Günlüğü - 33. Hafta: Sunucu Ortamı Optimizasyonları, API Güvenliği ve Otonom Veri Takibi

Bu hafta, sunucu ortamında (Linux) çalışan web otomasyonlarının kararlılığını artıracak DOM (Document Object Model) okuma güncellemeleri yaptım. FastAPI tabanlı mikroservislerimizin dışa açıklığını kontrol altına alarak güvenlik katmanlarını güçlendirdim. Fiyat Benchmark projesinin veri şemasını "Kategori" bazlı genişletirken, şirket içi süreçleri destekleyecek yepyeni bir n8n tabanlı kamu duyuru takip otomasyonunu canlıya aldım.

---

##  Odak Alan 1: FastAPI Production Güvenliği ve Uç Nokta (Endpoint) Yönetimi

Geliştirdiğimiz "RAU Analiz API" servisinin canlı sunucuda (production) çalışmaya başlamasıyla birlikte, güvenlik ve yetkisiz erişim kontrollerini sıkılaştırdım.

* **API Şema Gizliliği:** FastAPI varsayılan olarak `/docs`, `/redoc` ve `/openapi.json` gibi etkileşimli dokümantasyon sayfalarını açık tutar. Geliştirme ortamında faydalı olan bu durum, canlı sunucuda kötü niyetli kişilere sistemin tüm haritasını verebilir.
* **Müdahale:** FastAPI uygulamasının (app) başlatılma konfigürasyonuna müdahale ederek `docs_url=None`, `redoc_url=None` ve `openapi_url=None` parametrelerini atadım. Bu sayede API'nin Swagger ve ReDoc arayüzlerini dış erişime tamamen kapatarak ciddi bir güvenlik (Obfuscation/Gizleme) katmanı sağladım.

---

##  Odak Alan 2: Linux Headless Ortamında DOM Render ve Veri Çekme Optimizasyonu

Fiyat Benchmark botlarımız Linux sunucusunda Headless (başsız) olarak çalışırken, fiyat verilerinin eksik veya boş geldiği bir anomali tespit ettim.

* **Kök Neden Analizi (Root Cause):** Selenium'da sıkça kullanılan `.text` özelliği, yalnızca tarayıcı ekranında "görsel olarak render edilmiş" (visible) metinleri döndürür. Linux sunuculardaki Headless Chrome ortamında bazı CSS yapıları farklı yorumlandığı veya tam render edilmediği için `.text` komutu başarısız oluyordu.
* **Mühendislik Çözümü:** Fiyat veya metin çekme fonksiyonlarımı revize ederek, görsel render'a bağımlı olan `.text` yerine, doğrudan HTML DOM yapısına inip elementin içeriğini okuyan `.get_attribute("textContent")` metoduna geçiş yaptım.
* **Sonuç:** Bu düşük seviyeli (low-level) DOM müdahalesi sayesinde işletim sistemi veya render farklılıklarından kaynaklanan veri kayıpları sıfıra indirildi.

---

##  Odak Alan 3: Veritabanı Şema Evrimi (Schema Evolution) ve Entegrasyon

İş birimlerinin veri analitiği yeteneklerini artırmak adına, tüm Fiyat Benchmark sistemine "Kategori" boyutunu ekledim.

* **Şema Güncellemesi:** SQLAlchemy modellerine ve veritabanı tablolarına `kategori` (`String(1024)`) sütununu ekledim.
* **ETL Revizyonu:** Önceden kurduğum modüler SOLID yapının avantajını kullanarak, hedeflenen tüm sitelerin detay ve liste sayfası parser'larına (ayrıştırıcılarına) kategori verisini yakalayacak mantığı ekledim.
* **Kusursuz Entegrasyon:** Çekilen kategori verileri, sözlük (dict) objelerine (`r.get("kategori")`) dinamik olarak entegre edildi ve veri zenginleştirme (Data Enrichment) süreci tüm siteler için başarıyla tamamlandı.

---

##  Odak Alan 4: Kamu Verisi Takip Otomasyonu (TÜBİTAK) ve N8N

Şirket süreçlerini yakından ilgilendiren TÜBİTAK resmi duyurularının manuel olarak takip edilmesinin yarattığı zaman kaybını önlemek için tam otonom bir sistem kurguladım.

* **HTTP Request Mimarisi:** Ağır çalışan Selenium tarayıcıları yerine, n8n üzerinden doğrudan API mantığıyla çalışan hafif ve hızlı `HTTP GET` istekleri (request) kurguladım.
* **Delta Kontrolü ve Detay Kazıma:** Sistem her gün duyuru listesi sayfasını tarıyor, mevcut veritabanı ile karşılaştırarak "sadece yeni gelen" duyuruları tespit ediyor. Yeni bir duyuru varsa, detay sayfasına ikinci bir istek atıp içeriği kazıyor.
* **Uyarı Sistemi (Alerting):** Kazılan ve veritabanına kaydedilen bu yeni duyurular, HTML formatında derlenerek ilgili departman yöneticilerine otomatik e-posta (Email Alert) olarak gönderiliyor.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **API Security** | FastAPI'nin otomatik oluşturulan Swagger/ReDoc dokümantasyonlarını devre dışı bırakarak **Production Güvenliği** sağlama. |
| **Advanced Web Scraping** | Linux/Headless render sorunlarını aşmak için `.text` yerine **`.get_attribute("textContent")`** kullanarak ham DOM'dan veri çıkarma. |
| **Data Enrichment** | Tüm benchmark sitelerinin ETL mimarisine yeni bir boyut (**Kategori** alanı) ekleyerek veritabanı şemasını genişletme. |
| **Event-Driven Automation** | N8N ve HTTP GET node'ları ile durum (State) tutan, sadece yeni içeriklerde tetiklenen bir **Duyuru Takip ve E-posta** sistemi kurma. |