#  Staj Gelişim Günlüğü - 18. Hafta: Remote AI Eğitimi, Vektör Altyapı Migrasyonu ve Domain Odaklı Mimari

Bu hafta, staj sürecimin en teknik ve kriz yönetimi gerektiren haftalarından biriydi. FPS tahminleme modelini 7000 epoch boyunca uzak sunucularda eğiterek üretim ortamına hazır hale getirdim. Aynı zamanda, Google'ın embedding modelini yayından kaldırmasıyla oluşan global servis kesintisi riskini, lokal modellere geçiş yaparak (Infrastructure Migration) profesyonelce yönettim. Yazılım mimarisini ise "Driver & Domain" yapısına evirerek sürdürülebilirliği artırdım.

---

##  Odak Alan 1: İleri Seviye FPS Model Eğitimi ve Entegrasyon

FPS tahminleme modelinin eğitimi için yerel kaynakların yetersiz kalacağını öngörerek ve "Overfitting" (Aşırı Öğrenme) sorunlarını çözerek modeli mükemmelleştirdim.

### 1. Remote Training ve Data Leakage Önlemi

* **Remote GPU Eğitimi:** Modelin eğitim sürecini hızlandırmak için kendi bilgisayarım yerine, şirketin yüksek işlem gücüne sahip **Remote GPU sunucusuna** uzak bağlantı kurarak gerçekleştirdim. Bu altyapıda modeli tam **7000 Epoch** boyunca eğittim ve çok daha kararlı sonuçlar elde ettim.
* **Overfitting ve Feature Selection:** Önceki denemelerde modelin, "FPS Skor" ve "Genel Skor" gibi sonucu doğrudan ele veren parametreleri ezberlediğini (kopya çektiğini) fark ettim. Bu durum, veri biliminde **Data Leakage (Veri Sızıntısı)** olarak adlandırılan ve modelin gerçek dünyada başarısız olmasına yol açan bir durumdur. Bu parametreleri veri setinden çıkarıp modeli saf donanım özellikleriyle yeniden eğiterek gerçek bir öğrenme (generalization) sağladım.

### 2. Çıktı Matrisleri ve UI Entegrasyonu

Modelin çıktılarını kullanım alanına göre iki farklı matris yapısında sundum:

* **Web UI Matrisi (3x4):** Web sitesindeki detay sayfası için kapsamlı bir yapı kurdum. **2 farklı oyun, 52 CPU ve 102 GPU**'nun tüm kombinasyonlarını içeren bu yapıda, kullanıcıya 3x4'lük bir matris (farklı ayar ve çözünürlüklerde) sunarak "Bu sistemle kaç FPS alırım?" sorusuna detaylı yanıt verildi.
* **Chatbot Entegrasyonu (1x3):** Chatbot'un "Sistem Toplama" modülüne entegrasyon sağladım. Kullanıcı bir sistem topladığında, seçilen CPU ve GPU'ya göre chatbot anlık olarak **1080p, 2K ve 4K** (1x3 Matris) çözünürlüklerde beklenen FPS değerini üretip kullanıcıya sunabiliyor.

---

##  Odak Alan 2: Kritik Altyapı Krizi ve Vektör Migrasyonu

Haftanın en büyük teknik olayı, Chatbot'un ve RAG sisteminin bel kemiği olan **Google `models/text-embedding-004` embedding modelinin** Google tarafından **deprecated (yayından kaldırılmış)** edilmesiydi. Bu durum, vektör aramalarının çalışmaması riskini doğurdu.

* **Lokal Modele Geçiş (Migration):** Cloud bağımlılığını azaltmak ve sürdürülebilirliği sağlamak için **Lokal (On-Premise) `intfloat/multilingual-e5-base`** bir modele geçiş planladım.
* **Multilingual Entegrasyon:** Hugging Face üzerinden çok dilli (multilingual) desteği olan, Türkçe performansında başarılı bir model seçtim ve sisteme entegre ettim.
* **Re-Indexing ve Embedding:** Vektör veri tabanımızdaki (PostgreSQL/pgvector) tüm veriler, eski modelle vektörleştirildiği için geçersiz hale gelmişti. Tüm veri setini yeni lokal modele göre **sıfırdan embedding işlemine** tabi tuttum ve veritabanını yeniden indeksledim.
* **Prompt Uyumluluğu:** Chatbot'un kullanıcı girdilerini işleyen prompt mekanizmasını da bu yeni kütüphaneye uyumlu hale getirerek sistemi eskisinden daha hızlı ve güvenli bir şekilde ayağa kaldırdım.

---

##  Odak Alan 3: Mimari Dönüşüm (Factory -> Driver & Domain)

Scraper ve bot sayısının artmasıyla birlikte, mevcut "Worker/Factory" mimarisinin yönetimi (maintenance) zorlaşmaya başlamıştı.

* **Driver & Domain Mimarisi:** Projeyi daha modüler hale getirmek için **Driver ve Domain** tabanlı bir yapıya geçiş yaptım.
* **İzolasyon:** Artık her bir scraper, her bir kategori ve site kendi "Domain"i altında izole edildi. Bu sayede Chatbot servisleri, FPS botları ve Ürün Analiz botları birbirini etkilemeden, daha okunabilir ve yönetilebilir bir kod tabanına kavuştu.

---

##  Odak Alan 4: Otomasyon Genişlemesi ve Güvenlik

* **Ürün Benchmark (Zorlu Site Çözümü):** Hedeflenen 4 sitenin entegrasyonu tamamlandı. Buna ek olarak, HTML yapısı ve fonksiyonları oldukça dinamik olan ve bot koruması yüksek 5. bir sitenin de dinamikleri çözüldü ve pipeline'a dahil edildi.
* **GİB Otomasyonu (Multi-Company):** Dijital Vergi Dairesi botu, tek şirket yapısından çıkarılarak **iki farklı şirketi** aynı anda yönetebilecek şekilde revize edildi.
* **Fernet Şifreleme:** Veritabanında saklanan hassas kullanıcı bilgileri (şifreler), **Fernet** kütüphanesi ile simetrik olarak şifrelendi. Bot, çalışma anında ortak bir anahtar (key) kullanarak bu şifreleri çözüp sisteme giriş yapıyor. Bu sayede veritabanı güvenliği kurumsal standartlara taşındı.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **MLOps (Remote Training)** | 7000 Epoch'luk eğitimi **Remote GPU** sunucusunda tamamlama ve **Data Leakage**'ı önleyerek modelin başarısını artırma. |
| **Disaster Recovery** | Deprecated olan Google embedding modelinden, **Lokal Multilingual** modele başarılı bir veri tabanı ve kod migrasyonu gerçekleştirme. |
| **Architectural Refactoring** | Yönetilebilirliği artırmak için Factory pattern'den **Driver-Domain** pattern'e geçiş. |
| **Cyber Security** | Botların kullandığı hassas verileri **Fernet** şifreleme algoritması ile güvence altına alma. |
| **Ürün Geliştirme** | ML model çıktısını Web ve Chatbot arayüzlerine (1x3 ve 3x4 matrisler) entegre etme. |

---

###  Gelecek Hafta Hedefleri ve Detaylı Planlama

Gelecek hafta, veri kalitesi, takip edilebilirlik ve modelin iyileştirilmesi üzerine odaklanacağım:

1. **Günlük Durum Takip (Daily Status Log) Tablosu:**
* Ürün veri çekimi sırasında olası hataları ve süreci takip etmek için özel bir log tablosu oluşturulacak.
* **Mekanizma:** Bu tablo, anlık değil, **günlük bazda** tutulacak bir "Check-List" mantığında çalışacak.
* **İçerik:** *"X Markası, Y Sitesinde, Bugün (Tarih) başarıyla tarandı mı?"* sorusunun cevabını tutacak. Eğer o gün o marka için veri çekildiyse `Status: True`, çekilmediyse veya hata aldıysa `Status: False` olarak loglanacak. Böylece geriye dönük veri eksikliği analizi yapılabilecek.


2. **FPS Model Tuning (Feature Engineering):**
* Modelin hata payını düşürmek için farklı sütun adları ve yeni veri tipleri (statunlar) toplanarak model yeniden eğitilecek.
* Web sitesindeki FPS sayfasının UI kısmında kullanıcı deneyimini artıracak geliştirmeler yapılacak.


3. **Veri Eşleme (Matching) Desteği:**
* Hedef sitelerdeki ürünlerin çekilmesi tamamlandıktan sonra, bu ürünlerin bizim lokal veritabanımızdaki ürünlerle eşleştirilmesi sürecinde ekip arkadaşlarına teknik destek sağlanacak.