#  Staj Günlüğü - 6. Hafta: Tam Otonom Kritik Doküman Yönetim Sistemi Kurulumu

Bu hafta, önceki haftalarda temelleri atılan ve birçok teknik zorluk içeren kritik bir projenin (Hassas Kurumsal Doküman Bildirim Sistemi) **CAPTCHA çözümlü otomasyonunu** başarıyla tamamlayarak sistemi baştan sona çalışır hale getirdim. Bu süreç, farklı teknolojileri tek bir amaca hizmet edecek şekilde entegre etme ve veri bütünlüğünü sağlama konusunda üst düzey pratik kazandırdı.

##  Odak Alan 1: Kritik Kurumsal Doküman Bildirim Sistemi (Uçtan Uca Otonom Akış)

Sistem, harici bir **Resmi Kurumun Doküman Portalından** gelen hassas bildirimleri otomatik olarak indiren, sınıflandıran, ekleriyle birleştiren ve ilgili departmanlara e-posta yoluyla ileten tam otonom bir akış üzerine kuruldu.

### 1. Sistem Mimarisi ve Entegrasyon Zinciri

Bu otomasyon, dört ana bileşenin kusursuz entegrasyonuyla hayat buldu:

1.  **Worker Bot:** Selenium tabanlı Python botu.
2.  **API Geçidi:** FastAPI sunucusu (Botu tetikler ve dosya indirme/işleme servisi sunar).
3.  **Orkestratör:** n8n (Ana iş akışını, akışı ve departman yönlendirmelerini yönetir).
4.  **Zeka Katmanı:** Gemini AI (Görsel ve Doküman Analizi).

### 2. Kritik Aşama: CAPTCHA Engeli ve Çözümü

* **Çözüm:** Haftanın ilk kazanımı, portalın **CAPTCHA mekanizmasını** aşmak oldu. Bu, **Gemini'nin görsel analiz modelini** kullanan Python tabanlı bir fonksiyon yazılarak gerçekleştirildi. Bu hamle, otomasyonun önündeki en büyük login engelini kaldırdı.

### 3. Otonom İş Akışının Adım Adım İnşaası

Sistemin tüm akışı, veri bütünlüğünü koruyacak ve mantıksal kararlar alacak şekilde tasarlandı:

* **Tetiklenme:** n8n, bir webhook ile **FastAPI** sunucusunu tetikler.
* **İndirme:** **Selenium botu** çalışır, Gemini'den CAPTCHA cevabını alarak portala giriş yapar ve tüm okunmamış bildirimleri (ana dokümanlar ve ekleri) yerel bir **`Islenmedi`** klasörüne indirir.
* **n8n'e Bildirim:** İndirme tamamlandıktan sonra, Python kodu klasörü tarar, dosyaları **Bildirim ID'sine** göre gruplar ve **n8n'e** "Yeni işlenecek bildirimler bunlar" diyerek akışı başlatır.
* **Doküman Analizi ve Sınıflandırma (AI Zekası):**
    1.  n8n akışı, bildirimin sadece **'ana dokümanı'**nı ayırır.
    2.  Bu ana doküman, bir HTTP isteği ile **Gemini'ye (Analyze Document)** yollanır ve **"Bu doküman hangi departmana ait? (Hukuk, Finans, İK vb.)"** sorusu sorulur.
    3.  AI'den gelen cevaba göre bir **Switch düğümü** akışı, ilgili departmanın (Örn: 'Finans' kolu) mail gönderme yoluna yönlendirir.
* **Ekleri Geri Bağlama (Veri Bütünlüğü):** Bu, akışın en karmaşık kısmıydı. Sınıflandırılan ana dokümana, aynı Bildirim ID'sine sahip olan **tüm ek dosyaları** geri bağlamak gerekiyordu. Bu, bir **Split**, **HTTP Request** ve **Code bloğu** kullanılarak, tüm PDF'leri Bildirim ID'sine göre tekrar gruplayan özel bir mantıkla çözüldü.
* **Nihai Dağıtım:** **Send Email düğümü**, sınıflandırma sonucu belirlenen departmanın mail adresine, doğru konu başlığı ve o bildirime ait **tüm ek dosyalarla (ana + ekler)** birlikte e-postayı atar.

### 4. Kritik Öğrenim: Race Condition (Yarış Durumu) Yönetimi

Sistemin test edilmesi sırasında, üretim ortamında ortaya çıkabilecek kritik bir hata tespit ettim:

* **Problem:** n8n maili gönderirken, Python'un dosyaları taşıması gereken durumlar çakışıyordu. n8n dosya indirmeye çalışırken Python'un dosyayı çoktan taşıması veya silmesi gibi bir **'yarış durumu' (Race Condition)** hatası oluşuyordu.
* **Çözüm:** Bu hatayı yönetmek için, Python koduna **`/mark-as-processed`** adında yeni bir **API Endpoint'i** ekledim. n8n akışı, maili **başarıyla gönderdikten sonra** bu API'yi tetikliyor ve dosyaların güvenli bir şekilde **`Islendi`** klasörüne taşınmasını sağlıyor. Bu yöntem, dosya bütünlüğünü ve güvenilirliğini garanti altına aldı.

##  Odak Alan 2: Diğer Otomasyonlar ve İlerideki Vizyon

Bu büyük projenin yanı sıra, şirket içi bir otomasyon daha canlıya alındı.

* **Veri Gruplama Otomasyonu:** Veritabanından çekilen bir tablonun, **Code Node** kullanılarak belirli kriterlere göre gruplandırılması ve farklı 10 grubun, belirlenen 4 mail adresine özel olarak e-posta ile gönderilmesi otomasyonu tamamlandı.

##  Haftalık Kazanımlar, Vizyon ve İleriye Dönük Planlar

Bu hafta, teorik bilgimi gerçek bir "production" seviyesinde, karmaşık, çok katmanlı bir sistem kurma ve yönetme becerisine dönüştürdüm.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Tam Otonom Sistem Kurulumu** | Selenium, FastAPI, n8n ve Gemini AI'ı entegre ederek, zorlu bir CAPTCHA engelini aşan ve dokümanları sınıflandıran **uçtan uca otonom bir iş akışı** başarıyla kuruldu. |
| **Kritik Hata Yönetimi** | **"Race Condition" (Yarış Durumu)** hatasını tespit etme ve bunu **`/mark-as-processed`** gibi özel bir API Endpoint'i ve akış mantığı ile güvenli bir şekilde çözerek **veri bütünlüğünü** sağlama becerisi. |
| **Karmaşık Veri Bütünlüğü** | 'Ana' dokümanları sınıflandırdıktan sonra, ilgili bildirime ait **tüm 'ek' dokümanları** Bildirim ID'si bazında tekrar gruplayıp tek bir mailde birleştirme gibi zorlu bir **veri manipülasyonu** sürecini yönettim. |
| **Çoklu Teknoloji Entegrasyonu** | Python'un görevli bir "worker" ve n8n'in bir "orkestratör" olarak görev yaptığı **Mikroservis Mimarisi** içinde haberleşmeyi (Webhook/API) kusursuz hale getirdim. |

###  Gelecek Hafta Hedefleri

* **Şirket İçi Uygulama Geliştirme:** Takım arkadaşlarımla birlikte, şirket içi bir sayfaya enjekte edilerek bilgileri kazıdığım sistemin (bir önceki haftanın Captcha çözümü) verilerini, PHP ile yazılacak bir arayüz üzerinden veritabanına aktarmaya başlayacağım. Benim rolüm bu altyapının Yapay Zeka tarafını (veri toplama/sınıflandırma) kurgulamak olacaktır.