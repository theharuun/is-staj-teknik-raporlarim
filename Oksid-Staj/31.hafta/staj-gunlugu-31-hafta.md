#  Staj Gelişim Günlüğü - 31. Hafta: 13 Adımlı Otonom Orkestrasyon, SoC Prensipleri ve Çipset Odaklı ML Genellemesi

Bu hafta, parçalı halde çalışan tüm otomasyon ve modelleme süreçlerini tek bir çatı altında toplayarak 13 adımlık tam otonom bir "Master Pipeline" (Ana Boru Hattı) kurguladım. Kod tabanını tekrarlardan arındırarak SOLID prensiplerine uygun hale getirdim. Makine Öğrenmesi (ML) modelimize donanım çipsetlerini tanıma yeteneği ekleyerek tahminleme kapasitesini artırdım ve iş birimlerinin talepleri doğrultusunda SQL mantıklarımızı (Business Logic) güncelledim.

---

##  Odak Alan 1: ML Modelinde "Çipset" Tabanlı Genelleme ve Veri Kurtarma

FPS modelinin daha önce hiç görmediği donanım kombinasyonlarındaki başarısını (Generalization) artırmak için veri ön işleme (preprocessing) ve eğitim stratejilerinde köklü değişiklikler yaptım.

* **Referans Profilleri (Reference Profiles):** Model, daha önce hiç karşılaşmadığı yeni bir "Marka + Model" ekran kartı (GPU) veya işlemci (CPU) gördüğünde artık tıkanmıyor. Eğer o donanımın temel **Çipsetini (Chipset)** geçmiş verilerinden tanıyorsa, kendi genel hafızasından bir "Referans Profili" çağırarak son derece yüksek doğrulukta tahmin yapabiliyor. Bu sayede modelin adaptasyon yeteneği maksimize edildi.
* **Eksik Veri (Null) Toleransı:** Dış kaynaklardan çekilen verilerde "Minimum FPS" ve "Maksimum FPS" değerlerinin boş (null) gelmesi ihtimaline karşı modele defansif bir yetenek (Feature Generation) eklendi. Veri eksik olsa bile model dinamik olarak bu eksikliği tolere edecek özellikleri kendi içinde üreterek çalışmaya devam ediyor.

---

##  Odak Alan 2: 13 Adımlı Pipeline ve Separation of Concerns (SoC)

Sistemin kalbi olan otonom orkestratörü, 13 farklı iş adımını (Job) sırayla ve güvenle yürütecek şekilde modernize ettim.

* **Separation of Concerns (Kavramların Ayrılığı):** Daha önce aynı kod bloğu içerisinde hem kaynak veritabanına (Örn: PostgreSQL) hem de hedef veritabanına (Örn: MySQL) aynı anda bağlanarak veri aktaran "Spagetti" yapıları tamamen ayrıştırdım. 
    * *Yeni Mimari:* Pipeline'ın 13. adımı olarak sadece "Canlı Ortam Senkronizasyonu" modülünü kurguladım. Sistem önce tüm işini kendi içinde bitiriyor, ardından izole edilmiş tek bir hamle ile hedef veritabanını güncelliyor. Bu, bağlantı (connection) havuzu sorunlarını tamamen çözdü.
* **Durum Yönetimi (State Management):** Deterministik FPS verilerinin veritabanına basıldığı kritik adımı her koşulda çalışacak şekilde (bypass koşulunu inaktif ederek) güncelledim. Böylece model eğitimi atlansa bile veri bütünlüğünün her tetiklenmede korunması garanti altına alındı.
* **Kod Temizliği:** Tüm adımlar temizlendikten sonra, tekrara düşen (WET - Write Everything Twice) fonksiyonlar DRY (Don't Repeat Yourself) prensibiyle merkezi modüllere taşındı ve daha okunabilir, sürdürülebilir bir yapı elde edildi.

---

##  Odak Alan 3: İş Mantığı (Business Logic) ve Toplu Veri İşleme (Bulk Upsert)

Sistemin sadece teknik olarak değil, şirketin ticari hedefleriyle de tam uyumlu çalışması için SQL katmanında optimizasyonlar yaptım.

* **Özelleştirilebilir Hazır Sistemler Entegrasyonu:** Daha önceki yapıda sadece "Stokta Olan" ürünler hesaplanıyordu. Bu mantığı güncelleyerek, ürün tekil stokta olmasa bile eğer bir **"Hazır Sistem"** içerisinde seçilebilir (konfigüre edilebilir) durumdaysa, o CPU/GPU'nun da FPS hesaplamalarına dahil edilmesini sağladım. Bu, doğrudan satış ekranlarına değer katan bir hamle oldu.
* **Bulk Upsert Optimizasyonu:** Matris hesaplamaları sonucu ortaya çıkan devasa veri setlerinin veritabanına yazılması işlemi (Toplu Upsert) optimize edildi ve oyun kimlikleri (Game IDs) daha hızlı işlenecek şekilde güncellendi.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Yazılım Mimarisi (SOLID & SoC)** | Çift veritabanı bağlantılarını izole ederek veri aktarımını tek bir adımda toplayan **Separation of Concerns** prensibini uygulama. |
| **Gelişmiş Makine Öğrenmesi** | Modeli, marka isminden bağımsız olarak donanımın **Çipset (Chipset)** mimarisini tanıyıp referans üretecek şekilde geliştirme. |
| **Pipeline Orchestration** | Kazıma, Kendi Kendini İyileştirme (Self-Healing), ML Eğitimi ve DB Senkronizasyonunu kapsayan **13 adımlık otonom boru hattı** kurma. |
| **Business-Tech Alignment** | SQL sorgularını, stokta olmayan ancak **"Hazır Sistem" konfigürasyonlarında satılan** parçaları da kapsayacak şekilde revize etme. |

###  Gelecek Hafta Hedefleri

1. **Performans Yük Testleri:** 13 adımlık yeni pipeline'ın tam yük altında (bütün oyunlar ve donanımlar aktifken) uçtan uca süre (execution time) ve RAM kullanım analizlerinin yapılması.
2. **Kapsam Genişletmesi:** ML modelinin tahmin yeteneğini artırmak için donanım mimarisi dışında RAM hızı (MHz) veya anakart veri yolu (PCIe versiyonu) gibi dar boğaz (Bottleneck) yaratabilecek yeni özelliklerin (Features) araştırılması.