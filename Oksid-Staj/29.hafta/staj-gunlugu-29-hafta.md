#  Staj Gelişim Günlüğü - 29. Hafta: Otonom ML Orkestrasyonu, Event-Driven API'ler ve Monitör (UI) Geliştirme

Bu hafta, ayrı ayrı geliştirdiğim ETL, API ve Makine Öğrenmesi (ML) modüllerini tek bir otonom boru hattında (Autonomous Pipeline) birleştirdim. Fiyat Benchmark projemiz için Event-Driven (olay güdümlü) API'ler yazarak n8n entegrasyonunu tamamladım. Ayrıca sistem gözlemlenebilirliği (Observability) için hem Streamlit hem de PHP/Docker tabanlı iki farklı yönetim paneli (Monitor UI) geliştirdim.

---

##  Odak Alan 1: Uçtan Uca Otonom FPS ML Orkestratörü (Pipeline)

Bugüne kadar parçalı olarak (manuel veya yarı otomatik) çalışan ETL, Veri Temizleme, Model Eğitimi ve Çıkarım (Inference) aşamalarını `main_runner.py` altında birleştirerek **Tam Otonom** bir sisteme dönüştürdüm.

* **10 Aşamalı Sıralı İşlem ve Bypass Mantığı:** Sistem, veri toplamadan model eğitimine kadar 10 adımı sırayla çalıştırıyor. Sisteme **"Bypass Sinyali"** özelliği ekledim: Eğer veri kazıma adımında veritabanına *yeni bir donanım kombinasyonu* eklenmediyse, sistem gereksiz yere model eğitimine geçmiyor ve diğer adımları atlayarak sunucu kaynaklarını koruyor.
* **API Geçişi (Scraper.do) ve Yedek Mantık:** Hedef kaynak sitesi kazıma işlemi, yerel Selenium'dan **Scrape.do asenkron API'sine** geçirildi. Hız ve bot bypass güvenilirliği artırıldı. Ayrıca GPU eşleştirme sürecine "URL Slug -> Başlık Benzerliği" olmak üzere 2 aşamalı bir "Fallback" (Yedek) sistemi eklendi.
* **Sıfır Kesinti (Zero Downtime) Model Aktarımı:** Orkestratör, yeni eğitilen CatBoost modelini otomatik olarak izole edip FPS API'sinin `assets/latest` dizinine kopyalıyor. Bu sayede model güncellenirken API kesintiye uğramıyor.
* **Matris Ön Hesaplama:** Sık sorulan kombinasyonların anında cevaplanması için ML tahminleri önceden matris olarak hesaplanıp `Fps_predictions` tablosuna yazıldı.
* **Gelişmiş Loglama:** Gece çalışan cron işlerini (Cronjobs) takip etmek için terminal ve dosya tabanlı "Çift Akışlı" (Dual-Stream) loglama sistemi kuruldu.

---

##  Odak Alan 2: Event-Driven API'ler ve N8N Entegrasyonu (Fiyat Benchmark)

Fiyat Benchmark projesinin N8N (Orkestratör) ve kontrol paneli tarafından yönetilebilmesi için API katmanını "Olay Güdümlü" (Event-Driven) hale getirdim.

* **Targeted Scraping (Hedefli Tarama):** Tüm veritabanını taramak yerine, dışarıdan gelen bir tetikleme ile sadece istenen site veya markayı tarayabilen `/run/custom` endpoint'i (uç noktası) geliştirildi. CLI (Komut Satırı) desteği için `--target-site` ve `--target-brand` argümanları eklendi.
* **Dinamik Marka Yönetimi:** Markaların aktif/pasif (Toggle) durumlarını değiştiren ve neden pasife alındığını (Notlar) veritabanına kaydeden `/config/toggle-brand` API'si yazıldı.
* **SOLID ve Pydantic Refactoring:** API kodları temizlendi; Pydantic veri modelleri `routes.py` dosyasından ayrılarak `schemas.py` altına taşındı. Bu durum dokümantasyona (`rau_docs.md`) da yansıtıldı.

---

##  Odak Alan 3: Sistem Monitörü (Streamlit & PHP MVC) Geliştirmesi

Hem FPS hem de Benchmark API'lerinin iş birimleri tarafından kontrol edilebilmesi için Faz 1 ve Faz 2 olarak iki farklı kullanıcı arayüzü (UI) geliştirdim.

* **Faz 1 - Streamlit UI:** FPS API'si ve Marka/Özel Tarama API'leri ile doğrudan konuşan Python tabanlı bir Streamlit paneli yapıldı.
* **Faz 2 - PHP/Apache Docker Monitörü (RAU Analiz):** Kurumsal BT standartlarına daha uygun olması için, sistemi baştan aşağı PHP 8.2 ve Apache üzerinde çalışacak şekilde konteynerize ettim (Dockerfile).
    * **Mimari:** Güvenlik standartları gereği MVC benzeri bir yapıya geçildi. Ana giriş dosyası (`index.php`) ve public asetler `public/` klasörüne taşındı. Arka plan iş mantığı `ajax_handler` ve `ApiController` sınıflarıyla ayrıştırıldı.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Pipeline Orchestration** | ETL ve ML süreçlerini birleştiren, **Bypass** yetenekli ve **Zero Downtime** model kopyalama mimarisine sahip Otonom Runner kurgulama. |
| **Backend API (FastAPI)** | N8N entegrasyonu için CLI destekli, Pydantic şemalarıyla korunan **Event-Driven (Hedefli Tarama)** API uç noktaları yazma. |
| **DevOps & Containerization** | PHP 8.2 ve Apache sunucusu için gerekli kütüphaneleri (extensions) içeren **Dockerfile** yazma ve güvenli dizin mimarisi (`public/`) kurma. |
| **Database Performance** | PostgreSQL `INNER JOIN` ve Unique Constraint ihlallerini veri temizliği ile çözme; FPS tahminlerini pre-calculate ederek DB'ye basma. |

###  Gelecek Hafta Hedefleri

1. **PHP Monitörünün Canlıya Alınması:** Geliştirilen PHP/Docker tabanlı RAU Analiz Monitörünün (UI) testlerinin tamamlanıp sunucuda aktif edilmesi.
2. **N8N İş Akışları:** Yazılan Event-Driven API'lerin N8N üzerindeki webhook ve tetikleyici (Trigger) nodelarına entegre edilerek iş birimlerinin kullanımına sunulması.
3. **ML Monitoring:** Otonom çalışan FPS modelinin zaman içindeki performans sapmalarını (Data Drift / Model Decay) izleyecek metriklerin loglara eklenmesi.