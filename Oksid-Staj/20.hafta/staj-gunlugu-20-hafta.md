#  Staj Gelişim Günlüğü - 20. Hafta: Anti-Bot Bypass, Kesintisiz Veri Hattı ve Katı (Strict) ML Veri Ayrımı

Bu hafta, 80.000 satırlık devasa FPS veri setini toplama operasyonunu başlattım. Üçüncü parti servislerin limitlerine takılınca proaktif bir yaklaşımla kendi Cloudflare bypass çözümümü geliştirdim. Ayrıca Makine Öğrenmesi (ML) modelinin gelecekteki (henüz piyasaya çıkmamış) donanımları da tahmin edebilmesi için veri seti ayrım (train/test split) mantığında en katı ve gerçekçi kuralları uyguladım.

---

##  Odak Alan 1: Dışa Bağımlılığın Kesilmesi ve DrissionPage Entegrasyonu

FPS tahminleme modelinin eğitimi için gereken 80.000 kombinasyonluk veri setinin çekilmesi sürecinde, altyapı maliyetleri ve limitasyonlarla karşılaştım ve strateji değiştirdim.

### 1. Scraper.do Limiti ve Cloudflare Engeli

* **Sorun:** Veri kazıma işlemi için kurguladığım üçüncü parti servis (Scraper.do), ücretsiz sürümünde yalnızca 1.000 isteğe (request) izin veriyordu. 80.000 kombinasyonluk hedef için bu servis yetersiz ve maliyetli hale geldi.
* **Çözüm (Anti-Bot Bypass):** Hedef sitenin Cloudflare korumasını üçüncü parti bir API olmadan, yerel kaynaklarla aşmak için araştırmalar yaptım. Bu süreçte modern ve güçlü bir web otomasyon kütüphanesi olan **DrissionPage**'i keşfettim ve kod tabanıma başarıyla entegre ettim. Bu sayede API bağımlılığını ortadan kaldırdım.

### 2. Paralel İşleme Yanılgısı ve Seri İşleme Kararı

* **Mimari Karar:** İlk etapta verileri daha hızlı çekmek için paralel (multithreading/multiprocessing) mimariyi denedim. Ancak lokal bilgisayarda aynı anda çok sayıda tarayıcı sürücüsünün (browser driver) açılması, sistem kaynaklarını tüketerek yönetilebilirliği ortadan kaldırdı.
* **Stabilite Odağı:** Hız yerine stabiliteyi (reliability) seçerek mimariyi seri (sequential) işleme formatına geri döndürdüm.
* **Güncel Durum:** Mevcut durumda kod, müdahale gerektirmeden sorunsuz çalışmaktadır. İlk 20.000 veri başarıyla çekilmiştir. Geri kalan 60.000 verinin çekilmesi için sistem otonom olarak hafta sonu boyunca çalışmaya bırakılmıştır.

---

##  Odak Alan 2: Geleceğe Uyumlu Regresyon Modeli ve Strict Split

TechPowerUp üzerinden çekilen global CPU ve GPU teknik özellikleri üzerinden eğitilecek modelin, ezber (memorization) yapmasını kesin olarak engellemek için veri hazırlık sürecini yeniden kurguladım.

* **Data Leakage (Veri Sızıntısı) Koruması:** Daha önceki haftalarda fark ettiğim veri sızıntısı problemini çözmek için en katı yöntem olan **Strict Isolation (Sıkı İzolasyon)** kuralını getirdim.
* **Yeni Split Mantığı:** Bir donanım (Örneğin 'X' model CPU veya 'Y' model GPU) **ya sadece eğitim (Train) setinde ya da sadece test (Test) setinde** yer alabilir. Herhangi bir donanım parçasının her iki sette de bulunması kesinlikle yasaklanmıştır.
* **Mühendislik Çıktısı:** Bu zorlayıcı kısıtlama (constraint), modelin donanımların isimlerini ezberlemesini imkansız kılar. Model mecbur kalarak donanımların *fiziksel özelliklerini* (çekirdek sayısı, frekans hızı, mimari yapısı vb.) öğrenmek zorunda kalacaktır. Böylece sistemimiz, piyasaya henüz çıkmamış yepyeni bir CPU veya GPU eklendiğinde bile sadece teknik verilerine bakarak doğru FPS tahminini yapabilen gerçek bir **Regresyon Modeli** olacaktır.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Anti-Bot Engineering** | Cloudflare korumalarını yerel ortamda aşmak için **DrissionPage** kütüphanesini kullanarak API bağımlılığını (ve maliyeti) sıfırlama. |
| **Resource Management** | Paralel işlemcili web scraping'in donanım limitlerini (OOM/Crash riskleri) analiz edip, veri kaybını önlemek için stabil **Serial Execution** mimarisine dönme kararı. |
| **Advanced MLOps** | Modelin genelleme (generalization) yeteneğini maksimize etmek için CPU ve GPU bazında **Strict Train/Test Split** kuralını uygulama. |
| **Otonom Sistem Yönetimi** | 80.000 satırlık ETL (Extract, Transform, Load) işini insan müdahalesiz hafta sonu çalışacak şekilde **batch job** olarak ayarlama. |

---

###  Gelecek Hafta Hedefleri (Kapanış Fazı)

Gelecek hafta, projenin ürünleşme (productization) ve belgelendirme süreçlerine ağırlık verilecektir:

1. **Model Eğitimi:** Hafta sonu tamamlanacak 80.000'lik dev veri seti ile Nihai Regresyon / Makine Öğrenmesi modelinin eğitilmesi ve doğruluk metriklerinin çıkarılması.
2. **Kullanıcı Arayüzü (UI) Tasarımı:** Eğitilen modelin kullanımını kolaylaştırmak ve son kullanıcıya sunmak üzere arayüz geliştirilmesi.
3. **Dokümantasyon:** Tüm geliştirme sürecinin, kurulan ETL mimarisinin ve ML kararlarının kurumsal raporlama standartlarına uygun şekilde belgelenmesi.