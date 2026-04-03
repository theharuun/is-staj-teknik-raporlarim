#  Staj Gelişim Günlüğü - 27. Hafta: Cross-Platform Otomasyon Mimarisi, Process Yönetimi ve ETL Ölçeklendirmesi

Bu hafta, fiyat benchmark sistemimizi yeni hedeflere doğru genişletirken, arka planda çalışan web scraping altyapısının çekirdeğini (Core Infrastructure) baştan aşağı optimize ettim. Çoklu işlem (multi-process) mimarilerinde ortaya çıkan tarayıcı başlatma ve iletişim kopukluğu sorunlarını işletim sistemi (OS) seviyesinde çözerek, projenin hem lokalde hem de sunucuda sorunsuz çalışmasını garanti altına aldım.

---

##  Odak Alan 1: Tarayıcı Otomasyonu (Browser Automation) Darboğazı ve İşletim Sistemi Entegrasyonu

Arka planda birbirleriyle konuşan birden fazla sürecin (process) aynı anda Chrome sürücüleri (driver) başlatması, sistemin ölçeklenmesinde (scaling) ciddi iletişim kopukluklarına ve kilitlenmelere neden oluyordu. Bu sorunu çözmek için `driver_factory.py` modülünü tamamen yeniden yazdım.

**Karşılaşılan Sorunlar ve Çözüm Yaklaşımlarım:**

1.  **Dinamik Sürüm Yönetimi (Version Matching):** Yeni Chrome güncellemelerinin `undetected_chromedriver`'ı bozmasını engellemek için, işletim sistemini (Windows/Linux) dinamik olarak algılayan ve sistemdeki Chrome ana sürümünü (Örn: 132) Regex ile tespit eden platform bağımsız bir yapı kurdum.
2.  **SingletonLock Temizliği:** Chrome'un çökmesi durumunda profil klasöründe kalan ve yeni pencerelerin açılmasını engelleyen "SingletonLock" dosyasını tespit edip silen bir "Çöp Toplama" (Garbage Collection/Cleanup) mekanizması ekledim.
3.  **Subprocess Focus Sorunu (Windows API):** Pipeline zincirinde Chrome'un minimize (arka planda) başlayıp kilitlenmesi sorununu çözmek için `ctypes` kütüphanesini kullanarak doğrudan **Windows OS API**'lerine bağlandım. Açılan pencereyi zorla (force) ön plana getiren (`SetForegroundWindow` ve `ShowWindow`) donanımsal bir çözüm geliştirdim.
4.  **Defansif Başlatma (Retry Mechanism):** Sürücünün başlatılamaması ihtimaline karşı, bekleme süresini katlayarak artıran (Exponential Backoff benzeri) defansif bir "Retry" (Yeniden Deneme) algoritması kurguladım.

**Sonuç:** Bu köklü refactor işlemi sayesinde, yazdığım otomasyon kodları artık Windows veya Linux sunucu fark etmeksizin, işletim sistemine tam uyumlu, çökme toleransı yüksek (fault-tolerant) bir yapıya kavuştu.

---

##  Odak Alan 2: Fiyat Benchmark Projesi - Yeni Site Entegrasyonları

Geçen hafta ilk site için kurduğum detay sayfası entegrasyonunun (Akıllı UPSERT ve COALESCE) manuel testlerini ve kalite kontrollerini başarıyla tamamladım.

* **Adapter ve Parser Geliştirmeleri:** Bu başarılı testin ardından, hedefteki 2 yeni e-ticaret sitesi için kolları sıvadım. Her sitenin kendine has DOM yapısına uygun özel "Parser" ve "Adapter" sınıflarını yazarak 3 günlük yoğun bir çalışmayla sistem entegrasyonlarını sağladım.
* **Mevcut Durum:** An itibariyle sistem, 3 farklı e-ticaret platformundan sorunsuz şekilde fiyat, stok ve karakteristik ürün detayı toplayabilmektedir.

---

##  Odak Alan 3: İş Süreçleri ve Veri Tekilleştirme (Deduplication)

Rutin otomasyon kontrolleri sırasında, bir iş birimi tarafından mevcut bir raporlama otomasyonunda revizyon talep edildi.

* **İş Mantığı Revizyonu (Business Logic Update):** Sadece SQL sorgusundan dönen verilerin yeterli olmadığı, dönen veri kümesi üzerinde ek filtrelemeler yapılması gerektiği belirlendi.
* **Algoritmik Filtreleme:** SQL sorgusunun çıktısını kod tarafında alarak; `if` ve `while` döngüleri ile karmaşık iş kurallarını uyguladım. Verileri tekilleştirerek (deduplication) çok daha rafine ve hedefe yönelik bir tablo oluşturdum ve otomatize edilmiş mail akışını güncelledim.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **System Programming** | `ctypes` ve OS API'leri kullanarak çoklu süreçlerdeki (multiprocessing) pencere/odaklanma (focus) sorunlarını çözme. |
| **Fault Tolerance** | `SingletonLock` temizliği ve dinamik sürüm eşleştirme ile otonom çalışan sistemlerin çökme/kilitlenme risklerini minimize etme. |
| **Data Extraction (ETL)** | 2 yeni hedef site için özel DOM Parser ve Adapter modülleri yazarak benchmark sistemini yatayda genişletme. |
| **Data Processing** | SQL çıktılarını uygulama katmanında algoritmik filtrelerden geçirerek veri tekilleştirme (Data Deduplication) yapma. |

---

###  Gelecek Hafta Hedefleri

1.  **Tam Entegrasyon:** Fiyat benchmark projesi için hedefte kalan son 3 sitenin de adapter/parser sınıflarının yazılarak sisteme dahil edilmesi.
2.  **Sunucu Dağıtımı (Deployment):** İşletim sistemi bağımsız hale getirilen `driver_factory` modülünün gücüyle, projenin Linux sunucu ortamındaki çoklu process testlerinin tamamlanması.
3.  **Performans İzleme:** Eklenen yeni sitelerle birlikte artan veri yükünün, sunucu RAM ve CPU kaynakları üzerindeki etkisinin analiz edilmesi.