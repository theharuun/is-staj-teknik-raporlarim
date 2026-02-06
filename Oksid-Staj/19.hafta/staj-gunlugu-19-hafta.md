#  Staj Gelişim Günlüğü - 19. Hafta: Veri Bütünlüğü, Kriptografik Uyumluluk ve ML Veri Stratejisi

Bu hafta, mevcut projelerin derinliklerine inerek yapısal sorunları giderdim. Fiyat benchmark sistemindeki veri tekilliği sorununu mimari bir değişiklikle çözdüm, şifreleme altyapısını PHP uyumluluğu için modernize ettim ve FPS modelindeki "Data Leakage" sorununu tespit ederek veri toplama stratejimi kökten değiştirdim.

---

##  Odak Alan 1: Fiyat Benchmark Projesi ve "Compact Table" Mimarisi

Benchmark projesinin son ayağı olan üçüncü sitenin entegrasyonu sırasında, veri bütünlüğünü tehdit eden kritik bir yapısal sorun tespit ettim ve çözdüm.

### 1. Unique ID Sorunu ve DOM Parsing Revizesi

* **Sorun Tespiti:** Hedef sitenin listeleme sayfalarında (Listing Page) "Ürün ID" sandığımız verinin aslında bir HTML etiketi olduğu ve benzersiz olmadığı anlaşıldı. Aynı ID'nin farklı ürünlerde tekrar etmesi, veri kirliliğine yol açıyordu.
* **Çözüm:** Scraper ve Parser algoritmalarını revize ettim. Listeleme sayfası yerine, ürün detay sayfalarındaki (Product Detail Page) farklı bir CSS selektörüne odaklanarak, her ürünün **gerçekten benzersiz (unique)** sistem ID'sini yakalayan yeni bir mantık kurdum.

### 2. Log ve Compact Tablo Mimarisi

Veritabanı yapısını, tarihçeyi korumak ve anlık durumu hızlı sorgulamak için iki katmanlı hale getirdim:

* **Log Tablosu (Append-Only):** Her taramada elde edilen verilerin ham haliyle basıldığı, tarihçenin tutulduğu tablo.
* **Compact Tablo (Current State):** Aynı üründen sadece bir tane olmasını garanti eden (Unique Constraint) özet tablo.
* **UPSERT Mekanizması:** Yazılım mimarisini **"Insert or Update" (UPSERT)** mantığına göre kurguladım. Yeni bir veri geldiğinde Log tablosuna eklenirken, Compact tabloda ilgili ürün varsa güncelleniyor (Update), yoksa yeni oluşturuluyor (Insert). Bu sayede Compact tablo her zaman en güncel "Snapshot" verisini sunuyor.

---

##  Odak Alan 2: Çapraz Platform Şifreleme (Python <-> PHP)

Captcha çözümlü bot projesinde kullanılan şifreleme kütüphanesinin, arka uç (Backend) sistemleriyle uyumsuzluğu giderildi.

* **Uyumluluk Sorunu:** Python tarafında kullanılan `Fernet` kütüphanesinin, PHP tabanlı sistemlerde karşılığının bulunmaması veya entegrasyonunun zor olması nedeniyle iletişim sorunu yaşandı.
* **PyNaCl Migrasyonu:** Güvenlik protokolünü değiştirerek, endüstri standardı olan ve hem Python hem PHP tarafında güçlü desteği bulunan **NaCl (Networking and Cryptography Library)** tabanlı `PyNaCl` kütüphanesine geçiş yaptım.
* **Sonuç:** Şifreleme ve şifre çözme (Encryption/Decryption) kodları güncellendi. Artık iki sistem arasındaki veri akışı, standartlara uygun ve güvenli bir şekilde sağlanıyor.

---

##  Odak Alan 3: FPS Modelinde Data Leakage ve Büyük Veri Operasyonu

FPS tahminleme modelinin eğitim sürecinde, modelin başarısını yapay olarak yüksek gösteren bir hata fark ettim ve veri stratejimi değiştirdim.

### 1. Data Leakage (Veri Sızıntısı) ve Overfitting

* **Sorun:** Train/Test ayrımını sadece kombinasyon bazlı yaptığım için, aynı GPU'nun farklı CPU'larla hem eğitim (Train) hem de test setinde yer aldığını fark ettim. Bu durum, modelin donanım özelliklerini öğrenmek yerine **GPU'nun kimliğini ezberlemesine** (Memorization) yol açıyordu.
* **Müdahale:** Ayrımı (Split) daha katı kurallara bağladım; bir GPU eğitim setindeyse, test setinde asla yer almamasını sağladım.
* **Sonuç:** Bu düzeltme sonrası modelin başarı skoru düştü. Bu beklenen bir durumdu ve sorunun kaynağının **veri azlığı ve çeşitlilik eksikliği** olduğunu doğruladı.

### 2. TechPowerUp Entegrasyonu ve 80K Kombinasyon

Modeli güçlendirmek için daha fazla ve çeşitli veriye ihtiyaç duyuldu:

* **Teknik Veri Kazıma:** Dünya genelinde kabul görmüş donanım veritabanı **TechPowerUp** üzerinden, en popüler 100 CPU ve 100 GPU'nun tüm teknik özelliklerini çeken bir "Scraper Modülü" geliştirdim.
* **Stratejik Split:** Veri sızıntısını önlemek için CPU ve GPU'ları daha veri toplama aşamasında **%80 Eğitim / %20 Test** havuzlarına ayırdım.
* **Job Queue:** Bu havuzlardan **80.000 adetlik** devasa bir kombinasyon listesi ürettim. Şu an scraping kuyruğu (queue) oluşturuldu ve FPS değerlerinin çekilmesi için sistem hazır hale getirildi.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Data Integrity** | Benchmark projesinde detay sayfasından **Unique ID** yakalayarak veri tekilleştirme ve **UPSERT** mimarisi kurma. |
| **Interoperability** | Python ve PHP arasındaki şifreleme uyumsuzluğunu **PyNaCl** kütüphanesine geçerek çözme. |
| **MLOps & Debugging** | Model eğitiminde **Data Leakage** durumunu tespit etme ve Train/Test ayrımını donanım bazlı izole etme. |
| **Big Data Collection** | TechPowerUp entegrasyonu ile **80.000 kombinasyonluk** veri seti oluşturma ve job queue hazırlığı. |

###  Gelecek Hafta Hedefleri

* **Big Data Scraping:** Hazırlanan 80.000'lik kombinasyon kuyruğunun çalıştırılarak FPS değerlerinin veritabanına işlenmesi.
* **Model Retraining:** Yeni ve temiz (leakage-free) veri seti ile modelin yeniden eğitilmesi ve performansın ölçülmesi.
* **Offer Price Entegrasyonu:** Benchmark projesindeki Compact Tablo'ya, "Ürün Liste Fiyatı" (Offer Price) sütununun eklenmesi ve veri akışının sağlanması.
