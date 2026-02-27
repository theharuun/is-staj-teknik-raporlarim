#  Staj Gelişim Günlüğü - 22. Hafta: Kurumsal Deployment, Hata İzolasyonu ve CatBoost Model Optimizasyonu

Bu hafta, staj sürecinin en kritik aşamalarından biri olan "Canlıya Alma (Deployment)" ve "Sistem Gözlemlenebilirliği (Observability)" süreçlerine odaklandım. Fiyat Benchmark sistemini Docker ve Systemd ile şirket sunucularına taşıdım. Beklenmedik bir web arayüzü (UI) değişim krizini mimari tasarımım sayesinde hasarsız atlattım. Eş zamanlı olarak FPS tahminleme modelinin mimarisini optimize ederek projeyi nihai sunum aşamasına getirdim.

---

##  Odak Alan 1: Fiyat Benchmark Sisteminin Canlıya Alınması ve Observability

Geliştirilen fiyat analiz altyapısını, yerel ortamdan kurumsal sunucu ortamına taşıyarak tam zamanlı ve güvenli bir servise dönüştürdüm.

### 1. Merkezi Loglama Mimarisi (Centralized Logging)

* **Sorun:** Veritabanına yazma (DB Write) ve veri kazıma (Scrape) gibi farklı iş mantıkları (Business Logic) kendi içlerinde ayrı log dosyaları üretiyordu. Bu durum, olası hataların takibini zorlaştırıyordu.
* **Çözüm:** Tüm modüllerin loglarını tek bir merkezde toplayan, günlük olarak otomatik dönen (Daily Rolling Logs) merkezi bir loglama mimarisi kurguladım. Bu sayede sistemin sağlığı tek bir dosyadan anlık olarak izlenebilir hale geldi.

### 2. Docker, Systemd ve API Güvenliği

* **Konteynerizasyon ve Süreç Yönetimi:** Hazırladığım FastAPI uygulamalarını **Docker** konteynerleri içine alarak Linux sunucusunda ayağa kaldırdım. Uygulamanın sunucu kapanıp açıldığında bile otomatik başlaması ve izole çalışması için **systemd servisi** olarak yapılandırdım.
* **Kurumsal Ağ Entegrasyonu:** Ayağa kalkan API uç noktaları, şirket intranet ağına dahil edildi. Güvenlik için `Bearer Token` kimlik doğrulama mekanizması aktif edilerek sadece yetkili iç servislerin bu API'lere erişebilmesi sağlandı.

### 3. Hata İzolasyonu ve Plugin/Adapter Mimarisinin Zaferi

* **Kriz Anı:** Merkezi loglama sistemini incelerken, hedef sitelerden birine erişilemediğini ve veri çekilemediğini tespit ettim.
* **Kök Neden Analizi (Root Cause):** Manuel kontroller sonucunda, ilgili sitenin arayüzünde (UI) köklü bir değişime gidildiği ve veri kazıma kodlarımızdaki CSS seçicilerinin (selectors) patladığı anlaşıldı.
* **Mimari Başarı:** Sistemi en başından beri **Plugin/Adapter Mimarisi** ile tasarlamış olmamın meyvesini burada topladım. Bir sitenin çökmesi, tüm pipeline'ı durdurmadı; diğer sitelerin veri çekme işlemleri *kusursuz bir şekilde çalışmaya devam etti.* (Fault Tolerance). Sadece hata veren adapter/plugin modülüne odaklanarak kodları hızla güncelledim ve sorunu dakikalar içinde çözdüm.

---

##  Odak Alan 2: FPS Model Mimarisi Revizyonu ve CatBoost

Modelin performansını ve tahmin kararlılığını artırmak için makine öğrenmesi mimarisinde stratejik bir değişikliğe gittim.

* **Multi-Target'tan Bağımsız Modellere Geçiş:** İlk etapta 1080p, 2K ve 4K FPS değerlerini aynı anda tahmin eden "Multi-RMSE Multi-Target" model mimarisini kullanıyordum. Ancak her çözünürlüğün donanım darboğazı (CPU vs GPU bottleneck) farklı olduğu için, bu yapıyı bozarak **her bir hedef (çözünürlük/ayar) için bağımsız birer regresyon modeli (Single-Target)** oluşturma kararı aldım.
* **CatBoost Hiperparametre Optimizasyonu:** Kurulan yeni bağımsız modelleri, kategorik verilerde (işlemci ailesi, ekran kartı mimarisi vb.) son derece başarılı olan **CatBoost** algoritması ile eğittim. Farklı hiperparametre kombinasyonları (Learning rate, depth, l2_leaf_reg vb.) defalarca test edilerek en düşük hata payına (MAE/RMSE) ulaşıldı.
* **Sunum Hazırlığı:** Eğitilen nihai modeller Web UI arayüzüne entegre edildi. Arayüzdeki son kullanıcı deneyimi (UX) iyileştirmeleri tamamlanarak proje, paydaşlara ve üst yönetime yapılacak **final sunumu için tam hazır hale getirildi.**

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Sistem Gözlemlenebilirliği** | Modüler süreçleri birleştirerek günlük dönen (rolling) **merkezi bir loglama altyapısı** kurma. |
| **DevOps & Deployment** | Linux sunucu ortamında **Docker ve systemd** kullanarak FastAPI tabanlı mikroservisleri canlıya alma. |
| **Fault Tolerance (Hata Toleransı)** | **Plugin/Adapter** tasarım deseni sayesinde, bir bileşendeki (UI değişikliği) çökmenin tüm sistemi etkilemesini engelleme. |
| **ML Architecture** | Çoklu hedef (Multi-target) karmaşasını çözerek, **CatBoost** tabanlı bağımsız regresyon modelleri kurgulama ve hiperparametre optimizasyonu yapma. |

###  Gelecek Hafta Hedefleri

* **Proje Sunumu:** FPS Tahminleme Modelinin ve yenilenen UI arayüzünün şirket yönetimine sunumunun gerçekleştirilmesi.
* **Dokümantasyon:** Fiyat Benchmark API'lerinin (Swagger/OpenAPI) ve Adapter mimarisinin geliştirici dokümantasyonunun tamamlanması.
* **Performans İzleme:** Canlıya alınan sunucudaki Docker konteynerlerinin CPU/RAM tüketimlerinin bir hafta boyunca izlenmesi ve gerekirse kaynak optimizasyonu yapılması.
