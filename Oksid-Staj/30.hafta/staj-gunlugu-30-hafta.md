#  Staj Gelişim Günlüğü - 30. Hafta: Multiprocessing FPS Motoru, Self-Healing (Oto-İyileştirme) ve Deterministik Veri Aktarımı

Bu hafta, FPS tahminleme projesinin "Faz B" (Günlük Otonom Operasyon) hedeflerine ulaştım. Sistem mimarisini dış kaynaklardaki arayüz ve kimlik (ID) değişikliklerine karşı dayanıklı hale getiren "Kendi Kendini İyileştirme" algoritmaları geliştirdim. Ayrıca FPS hesaplama sürelerini dakikalara indiren Paralel İşlem (Multiprocessing) motorunu devreye aldım ve ML öncesi veri senkronizasyonunu hatasız hale getirdim.

---

##  Odak Alan 1: Multiprocessing (Paralel) FPS Hesaplama Motoru (Turbo Mod)

Sisteme "God of War Ragnarök" gibi yeni oyunlar ve yüzlerce yeni donanım eklendiğinde, on binlerce kombinasyonun FPS değerlerinin (FHD, QHD, UHD x 4 farklı preset) tek bir işlemcide hesaplanmasının büyük bir darboğaz yarattığını tespit ettim.

* **İşlemci Mimarisine Entegrasyon (os.cpu_count):** Hesaplama mantığını, sunucunun/bilgisayarın sahip olduğu fiziksel çekirdek sayısını algılayacak (`os.cpu_count`) şekilde yeniden tasarladım.
* **İş Yükü Bölüştürme (Chunking):** CPU listesini, tespit edilen çekirdek sayısına (Örn: 8) eşit parçalara (chunks) böldüm.
* **Paralel Yürütme (ProcessPoolExecutor):** Python'un `concurrent.futures` kütüphanesini kullanarak her bir çekirdeğe bir CPU chunk'ı atadım.
    * *Optimizasyon:* Her çekirdek kendi içinde donanımları kombinliyor, CatBoost FPS modelini çalıştırıyor ve hesaplanan matrisleri 50'lik yığınlar (Batch) halinde `bulk_upsert_data` ile veritabanına basıyor.
* **Sonuç:** Saatler sürebilecek binlerce kombinasyon hesaplaması, tüm çekirdeklerin %100 kullanılmasıyla (Turbo Mod) dakikalar seviyesine indirildi.

---

##  Odak Alan 2: Kendi Kendini İyileştiren Sistem (Self-Healing) ve ID Senkronizasyonu

Sisteme yeni bir oyun eklendiğinde, kaynak (scraping) sitesinin arka planda donanım ID'lerini değiştirdiği sinsi bir "Edge Case" tespit ettim. Bu durum eski URL'lerin ve eşleşmelerin patlamasına (404 Not Found) yol açıyordu.

* **Self-Healing Modülü (`b1_self_healing_sync`):** Dış sitenin API uç noktalarına asenkron istekler atarak güncel ID şemalarını çeken yeni bir modül yazdım.
* **Geriye Dönük Güncelleme:** Kaynak sitede ID değiştiğini anlayan sistem; veritabanımızdaki `hardware_site_mapping`, `site_dropdown_items` tablolarını ve bekleyen kazıma kuyruğundaki (`scraping_queue`) hedef URL'leri **otomatik olarak** yeni ID'lere göre güncelledi. Bu sayede insan müdahalesine gerek kalmadan geçmiş veriler kurtarıldı.
* **Sert Durdurma (Hard-Stop):** Kritik API istekleri başarısız olursa, hatalı verinin içeri akmasını önlemek için `sys.exit(1)` ile sistemin otonom olarak kendini durdurması sağlandı.

---

##  Odak Alan 3: Delta Kuyruk Yönetimi (Sıfır İsraf)

Kazıma kuyruğunun (Scraping Queue) şişmesini ve aynı donanımların defalarca kazınmasını önlemek için kuyruk mimarisi baştan yazıldı.

* **Dinamik Oyun Okuma:** Oyun listesi statik (hardcoded) olmaktan çıkarılıp dinamik olarak veritabanından alınır hale getirildi.
* **Sıfır İsraf (Zero-Waste) Mantığı (`b2_delta_queue_builder`):** Kuyruk oluşturucu; bekleyen ve hatalı işleri temizleyerek sadece **"hiç kazınmamış ve yeni olan"** kombinasyonları tespit eder. Veritabanının "UNIQUE" kısıtlama avantajı kullanılarak sadece bu aradaki fark (Delta) saniyeler içinde kuyruğa eklenir.

---

##  Odak Alan 4: Deterministik Veri Dağıtımı ve ML Hazırlığı

Toplanan ham verilerin Makine Öğrenmesi eğitiminden önce doğru formatta ve doğru tablolarda olduğundan emin olmak için yeni bir aktarım scripti (`b3_transfer_real_fps`) yazdım.

* **Görünümden Tabloya (View to Table):** Kazınmış gerçek FPS verileri, önceden hazırlanan karmaşık SQL görünümü (`ml_fps_dataset_expanded_v1`) üzerinden okunuyor.
* **RAM Dostu Aktarım:** Dev veri seti RAM'i şişirmemek adına 5000'lik yığınlar (Batch Size) halinde işleniyor.
* **Deterministik UPSERT:** Veriler PostgreSQL ve MySQL tablolarına eksiksiz (deterministik) şekilde aktarılıyor ve otonom pipeline'da bu adım ML eğitiminden hemen öncesine sabitlenerek kusursuz bir veri bütünlüğü sağlanıyor.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **High Performance Computing** | `ProcessPoolExecutor` ile donanım hesaplamalarını CPU çekirdeklerine dağıtarak **Multiprocessing (Paralel)** mimari kurma. |
| **Disaster Recovery / Edge Case** | Dış kaynak API'lerindeki şema değişikliklerini fark edip kendi URL'lerini ve DB tablolarını güncelleyen **Self-Healing** mekanizması. |
| **Data Engineering** | Yeni eklenecek kombinasyonları Unique Constraints üzerinden hesaplayan **Delta Queue (Sıfır İsraf)** sistemi. |
| **Data Pipeline Automation** | ML eğitimi öncesi veritabanları arası dev veri aktarımını RAM şişirmeden (5000 Batch) yapan **Deterministik UPSERT** scriptleri yazma. |

###  Gelecek Hafta Hedefleri

1.  **Otonom Pipeline Testi:** Yazılan Faz B (Self-Healing, Delta Queue, ML Transfer) modüllerinin gece çalışacak (Nightly Cronjob) uçtan uca testlerinin analiz edilmesi.
2.  **API ve UI Senkronizasyonu:** Yeni hesaplanan FPS matrislerinin ve eklenen yeni oyunların (God of War Ragnarök vb.) web API'lerine ve UI arayüzlerine yansıtılması.
3.  **Model Eğitimi:** Geriye dönük kurtarılan (Self-Healed) ve yeni eklenen verilerle CatBoost modelinin yeniden eğitilerek performans farkının ölçülmesi.