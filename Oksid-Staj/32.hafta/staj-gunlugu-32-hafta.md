#  Staj Gelişim Günlüğü - 32. Hafta: UI Revizyonu Kriz Yönetimi, Headless Optimizasyonları ve Sunucu Deployment

Bu hafta; rutin otomasyon kontrollerim sırasında tespit ettiğim köklü bir arayüz (UI) değişiminin yarattığı "Sanal Kaydırma" (Virtual Scrolling) krizini, işletim sistemi düzeyindeki tetikleyicilerle çözdüm. Sistem mimarisini dış API tabanlı bir Cloud Scraper ile güçlendirdim. Veritabanları arası (PostgreSQL -> MySQL) senkronizasyonu daha güvenli hale getirmek için offline `.sql` dump üreten bir sistem kurguladım ve haftayı Sistem Yöneticisi (SysAdmin) ile projelerin sunucuya dağıtım (Deployment) süreçlerini yürüterek tamamladım.

---

##  Odak Alan 1: Rutin Bakım, UI Değişimi ve "Headless" Sanal Kaydırma Krizi

Haftaya, Fiyat Benchmark otomasyonlarının günlük sağlık kontrollerini (Rutin Bakım) yaparak başladım ve hedef sitelerden birinde veri akışının kesildiğini fark ettim.

* **Sorun Tespiti (UI Overhaul):** Yaptığım incelemeler sonucunda, ilgili e-ticaret platformunun arayüzünde köklü bir değişime gittiğini ve modern frameworklerin kullandığı "Sanal Kaydırma" (Virtualized List / Lazy Loading) yapısına geçtiğini tespit ettim.
* **Headless (Başsız) Tarayıcı Çıkmazı:** Yeni tasarıma göre kodlarımı (seçiciler ve giriş süreçleri) güncelledim. Ancak çok spesifik bir sorunla karşılaştım: Tarayıcı normal (görsel) modda açıldığında sayfa sorunsuz aşağı kayarken, sunucuda kullanılan Headless (Görsel arayüzü olmayan) Chrome modunda sayfa yüksekliği render edilmediği için API istekleri tetiklenmiyor ve bot belirli bir ürün sayısında kilitleniyordu.
* **Mühendislik Çözümü (CSS Anchor Hack & Native Events):** 2 günlük yoğun bir Ar-Ge süreci yürüterek bu modern front-end bariyerini iki aşamalı bir "Hacker" yaklaşımıyla aştım:
    1. **CSS Enjeksiyonu:** Tarayıcının DOM yapısına JavaScript ile müdahale ederek `document.body` ve `documentElement` yüksekliklerini zorla `500000px` seviyesine esnettim. Bu hile, Headless Chrome'un "sayfa bitti" yanılgısına düşmesini engelledi.
    2. **İnsansı Jiggle (Sarsıntı) ve Native Eventler:** Sayfayı kaydırmak için JavaScript tabanlı komutlar kullanmak yerine, işletim sisteminin doğal klavye eventlerini (`Keys.PAGE_DOWN`, `Keys.END`, `Keys.PAGE_UP`) Selenium üzerinden gönderdim. "Aşağı in, bekle, yukarı çık" (Jiggle) şeklinde tamamen insansı bir algoritma kurgulayarak Sanal DOM'un verileri yüklemesini zorladım.
* **Sonuç:** Yeni UI yapısına tam uyum sağlandı ve Headless moddaki kilitlenme sorunu %100 aşıldı.

---

##  Odak Alan 2: Bulut Tabanlı Veri Kazıma (Cloud Scraper) Entegrasyonu

Boru hattımızın (Pipeline) dışa bağımlılıklarını yönetmek ve IP/Bot engellemelerini aşmak için yerel kodlarımızı "Cloud Scraper" altyapısına entegre ettim.

* **API Entegrasyonu:** Hedef URL'leri encode ederek şirket içi/harici Cloud Scraper endpointlerine istek atan, dönen JSON yanıtlarını güvenli bir şekilde ayrıştıran ve hataları (Timeout, HTML dönmesi vb.) yöneten yeni bir modül yazdım.
* **Pipeline Çevikliği:** Ana orkestratör kodundaki çevresel değişken (Environment Variables) atamalarını dinamikleştirerek kod bağımlılıklarını azalttım. Ayrıca, veri bulunmadığı durumlarda gereksiz ML adımlarının atlanması için `skip_if_no_data=True` mantığını operasyonel adımlara başarıyla entegre ettim.

---

##  Odak Alan 3: Güvenli Veritabanı Göçü (Offline SQL Dump Mimarisi)

FPS projesinde PostgreSQL'de işlenen ve matris haline getirilen verilerin, canlı hedef olan MySQL veritabanına aktarımında mimari bir iyileştirmeye gittim.

* **Direct-Connection (Doğrudan Bağlantı) Yerine Offline Dump:** İki farklı veritabanına aynı anda bağlanmak (Connection Timeout, DB Lock riskleri) yerine, PostgreSQL'den veriyi alıp MySQL formatında bir **`.sql` dosyası (Dump)** üreten offline bir modül yazdım.
* **Veri Formatlama ve Sanitizasyon:** Python datetime objelerini MySQL'in anlayacağı string formatına çeviren, saat dilimi (Timezone) uyumsuzluklarını gideren ve SQL Injection veya Syntax hatalarına karşı metin içindeki tek tırnakları (`'`) kaçış karakterleriyle (escape) temizleyen güvenli bir veri işleme fonksiyonu geliştirdim.

---

##  Odak Alan 4: Sistem Yöneticisi ile Sunucu Dağıtımı (Deployment)

Haftanın kapanışını, geliştirdiğim tüm bu yeni mimarileri yerel (Local) bilgisayarımdan kurumsal sunucu (Production) ortamına taşıyarak yaptım.

* **Koordinasyon:** FPS Pipeline'ı ve Fiyat Benchmark API kodlarını sunucuya taşıma aşamasında Sistem Yöneticisi (SysAdmin) ile doğrudan çalıştım. 
* **Bilgi Aktarımı:** SysAdmin operasyonları bizzat yürütürken; ben geliştirici şapkasıyla projenin mimarisini, Docker gereksinimlerini, loglama süreçlerinin nasıl işlediğini ve veritabanı akışlarını detaylıca aktararak deployment sürecinin sorunsuz tamamlanmasına teorik ve mimari destek sağladım.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Kriz Yönetimi ve UI/UX** | Rutin kontrolde fark edilen **UI değişiminin** getirdiği React Sanal DOM bariyerlerini, CSS manipülasyonu ve Native Keyboard eventleriyle aşma. |
| **System Architecture** | Yerel kazıma yöntemlerini bırakıp, API tabanlı **Cloud Scraper** mikroservisine entegrasyon sağlama ve pipeline dinamiklerini güçlendirme. |
| **Data Engineering** | DB'ler arası doğrudan bağlantı risklerini elimine eden, **Data Sanitization** (SQL Injection koruması vb.) içeren offline `.sql` dump üreteci yazma. |
| **DevOps & Production** | Geliştirilen mimarinin, SysAdmin koordinasyonu ve bilgi aktarımıyla canlı **Sunucu (Deployment)** ortamına aktarılma sürecini deneyimleme. |