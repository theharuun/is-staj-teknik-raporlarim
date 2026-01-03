#  Staj Gelişim Günlüğü - 14. Hafta: Veri Senkronizasyonu, Hata Toleranslı ETL ve Fiyat Benchmark Sistemleri

Bu hafta, FPS tahminleme projesinin veri altyapısını optimize etmeye, durdurulan kritik otomasyon projesini (CAPTCHA) modernize ederek yeniden başlatmaya ve şirket için stratejik öneme sahip yeni bir "Fiyat Karşılaştırma" (Benchmark) sistemi kurmaya odaklandım.

---

##  Odak Alan 1: FPS Tahminleme Sistemi ve Gelişmiş Veri Mimarisi

FPS veri setinin toplanması sürecinde, verinin sadece çekilmesi değil, en verimli ve hatasız şekilde depolanması için mimari iyileştirmeler yaptım.

### 1. One-to-Many Mapping ve Veri Çoğaltma Stratejisi

* **Mimaride İyileştirme:** Yerel ürün ID'lerimizi hedef site ID'leri ile eşleştirirken **"One-to-Many" (Bire-Çok)** ilişkisine sahip bir mapping tablosu kurguladım.
* **Verim Artışı:** Bu yapı sayesinde, dış kaynaktan çekilen tek bir FPS kombinasyon sonucu, bizim veritabanımızdaki birden fazla (muadil/benzer) CPU veya GPU ile aynı anda ilişkilendirilebilecek. Bu hamle, planladığımız 70 bin kombinasyonun efektif değerini ve kapsamını çok daha yukarıya çekmiş oldu.

### 2. Hata Toleranslı Batch İşleme ve Bağlantı Yönetimi

Sistemin yüksek yük altında veritabanı bağlantı (Connection) hataları almaması için **Batch Processing** odaklı yeni bir iş akışı kurguladım:

* **İş Akışı Döngüsü:** 1. **DB Read:** `queue_job` tablosundan 30'lu setler (batch) halinde okuma yapılır.
2. **Connection Break:** Veritabanı bağlantısı serbest bırakılır (pooling yükünü azaltmak için).
3. **Processing:** `Scraper.do` üzerinden veri çekilir ve özel hazırladığım parser ile işlenerek geçici bir **NDJSON** dosyasına yazılır.
4. **DB Update:** Veritabanı tekrar açılarak işlenen 30'luk setin durumu güncellenir.
* **Sonuç:** Bu yöntemle, uzun süren scraping işlemleri sırasında veritabanı bağlantısını açık tutmayarak "connection timeout" veya "pool exhaustion" gibi riskleri tamamen ekarte ettim.

### 3. ML İçin Hazır Veri Görünümü (View)

* **Veri Birleştirme:** `fps_result`, `queue_job` ve `mapping` tablolarını `JOIN` operasyonlarıyla bir araya getiren kapsamlı bir **Database View** oluşturdum. Bu View, ileride geliştirilecek olan ML modeli için doğrudan "eğitim verisi" (training data) sağlayacak bir kaynak haline geldi.

---

##  Odak Alan 2: CAPTCHA Otomasyonu ve Geriye Dönük Veri Kazanımı

Yasal onay sonrası geri dönülen otomasyon projesinde, sistemin modernizasyonunu ve geçmiş verilerin taranmasını sağladım.

* **Standardizasyon:** Veritabanı tablolarını isim standartlarına ve modern şemalara göre revize ettim.
* **Gelişmiş Paging ve Dinleme:** Sayfadaki **paging (sayfalama)** işlemlerini dinleyen bir mekanizma kurdum. Bu sayede, sistemin 10'ar adetlik setler halinde 4.000'in üzerindeki tüm geçmiş mailleri de geriye dönük olarak çekmesini ve işlemesini sağlayacak formatı kurguladım ve bu formatı uygulanması kaldı.

---

##  Odak Alan 3: Yeni Proje - Pazar Fiyat Benchmark Sistemi

Şirketin rekabet gücünü artırmak amacıyla, piyasadaki güncel fiyatları takip eden ve karşılaştıran bir sistemin temellerini attım.

* **Hedef:** Belirlenen 3 büyük e-ticaret sitesinden ürün fiyatlarını anlık olarak çekip, kendi ürünlerimizle karşılaştırabileceğimiz bir **Benchmark** oluşturmak.
* **Durum:** Sitelerden bir tanesinin koruma mekanizmaları ve veri yapısı tamamen çözüldü. Gelecek hafta, diğer iki site için de scraping ve parser mimarileri kurulacak.
* **Yatay Genişleme:** Bu sistem, sadece bir marka veya kategori ile sınırlı kalmayıp, tüm ürün portföyüne yayılacak şekilde yatayda genişleyebilen modüler bir yapıda tasarlanmıştır.

---

##  Haftalık Kazanımlar ve Teknik Çıkarımlar

Bu hafta, veritabanı yönetimi ve büyük veri işleme süreçlerinde kurumsal çözüm yeteneklerimi sergiledim.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Bağlantı Yönetimi** | DB pooling ve connection sorunlarını önlemek için **Batch Processing & NDJSON** tabanlı veri yazma stratejisi geliştirme. |
| **İlişkisel Veri Mimarisi** | **One-to-Many mapping** tabloları ve karmaşık **SQL View** yapıları ile ML modellerine girdi sağlayacak veriyi optimize etme. |
| **Geriye Dönük Veri İşleme** | Paging mekanizmalarını kullanarak binlerce kayıtlık geçmiş veriyi (4k+ mail) sisteme dahil edebilecek bir akış kurgulama. |
| **Pazar Analizi Otomasyonu** | Çoklu site desteğine sahip, modüler bir fiyat takip ve karşılaştırma (Benchmark) sistemi mimarisi başlatma. |

###  Gelecek Hafta Hedefleri

* **Benchmark Genişletme:** Diğer 2 hedef site için scraping ve parser süreçlerinin tamamlanması.
* **FPS Veri Seti Dolumu:** Kurgulanan batch sistemi ile 70k+ verinin çekilmeye başlanması.
* **CAPTCHA Aktif Test:** Revize edilen mail çekme ve CAPTCHA aşma sisteminin canlı testlerine geçilmesi.
