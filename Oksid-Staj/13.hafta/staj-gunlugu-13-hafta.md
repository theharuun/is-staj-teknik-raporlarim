#  Staj Gelişim Günlüğü - 13. Hafta: Tersine Mühendislik, API Keşfi ve Sürdürülebilir Otomasyon Arayışları

Bu hafta, hem kurumsal denetim süreçlerini destekleyen kritik bir otomasyonu hayata geçirdim hem de AI Chatbot projemizin veri derinliğini artırmak için yüksek korumalı platformlardan veri çekme (scraping) süreçlerinde ileri seviye teknikler uyguladım. Ayrıca, otomasyon altyapımızı daha üst seviyeye taşıyacak yeni araçların (Apache Airflow gibi) fizibilite çalışmalarına başladım.

---

##  Odak Alan 1: Denetim Departmanı İçin Kritik Raporlama Otomasyonu

Denetim departmanının operasyonel verimliliğini artırmak amacıyla yeni bir otomasyonu canlıya aldım.

* **İş Akışı:** Son 30 gün içerisinde iki kez geri gönderilen (iade/değişim) ürünlerin listesi sistemden otomatik olarak çekilmektedir.
* **Çıktı:** Bu veriler, analiz edilebilir bir Excel formatına dönüştürülerek ilgili departman yetkililerine e-posta yoluyla iletilmektedir. Bu sayede hatalı ürün veya süreçlerin tespiti hızlandırılmıştır.
* **Sürdürülebilirlik:** n8n dışındaki daha kurumsal ve karmaşık iş akışlarını yönetmek adına **Apache Airflow** gibi araçlar için eğitim ve araştırma süreçlerini başlattım.

---

##  Odak Alan 2: FPS Veri Seti İçin Tersine Mühendislik ve API Keşfi

AI Chatbot'un "oyun performansı" sorularına cevap verebilmesi için gerekli olan FPS verilerini toplama aşamasında, yüksek korumalı bir hedef sitedeki engelleri aşmak için stratejik bir yol izledim.

### 1. Networking ve API Yakalama Süreci

Hedef sitedeki Oyun/GPU/CPU/Çözünürlük kombinasyonlarını basit yöntemlerle çekmenin imkansız olduğunu (IP ban riski ve eksik veri) fark ettikten sonra networking bilgimi kullandım:

* **API Keşfi:** Tarayıcının ağ (Network) trafiğini inceleyerek, dropdown listelerini besleyen gizli API uçlarını tespit ettim.
* **Veri Eşleme:** Bu API'den gelen verilerle sitedeki oyunların ve donanımların benzersiz ID'lerini topladım. Bu ID'ler ve isimler aracılığıyla, her kombinasyonun detay sayfasına giden URL yapısını deşifre ettim.

### 2. Job Queue ve Veri Bütünlüğü

* **Eşleşme Algoritması:** Şirketimizdeki CPU ve GPU listelerini, hedef sitedeki isim/ID listesiyle büyük oranda eşledim.
* **Job Queue:** En popüler 30 oyun ve standart çözünürlükler için oluşacak binlerce URL kombinasyonunu bir "Job Queue" (İş Kuyruğu) mantığıyla veritabanına kaydettim.

### 3. Profesyonel Scraping ve Parser Mimarisi

* **Hizmet Entegrasyonu:** Yüksek korumalı detay sayfalarından HTML verisini güvenli bir şekilde alabilmek için **Scraper.do** gibi profesyonel proxy/scraping servislerini sürece dahil ettim.
* **Parser ve Runner:** Gelen HTML içeriğini anlamlı verilere dönüştürecek özel bir **Parser** kodu yazdım. Bu yapı, daha önce kurduğum "Worker" ve "Runner" mimarisiyle entegre edilerek `fps_result` tablosunu besleyecek şekilde hazırlandı.

---

##  Odak Alan 3: Hukuki Gelişmeler ve Raf Projelerinin Dönüşü

Haftanın en önemli idari gelişmesi, daha önce yasal/prosedürel sebeplerle rafa kaldırdığımız **CAPTCHA aşma** projesiyle ilgili oldu.

* **Yasal Onay:** Şirket içindeki yasal engellerin çözüldüğü bilgisini aldık. Bu durum, önümüzdeki haftalarda bu kritik otomasyonun kaldığı yerden devam edeceği ve sisteme entegre edileceği anlamına geliyor.

---

##  Haftalık Kazanımlar ve Teknik Çıkarımlar

Bu hafta, karmaşık teknik engelleri profesyonel araçlar ve networking bilgisiyle aşarak projenin ölçeğini büyüttüm.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Tersine Mühendislik** | Web sayfalarının Network trafiğini analiz ederek, dinamik içerikleri besleyen **gizli API uçlarını keşfetme** ve veri toplama süreçlerini bu kanala taşıma. |
| **Proxy Servis Entegrasyonu** | Anti-bot korumalarını aşmak için **Scraper.do** gibi profesyonel araçları mevcut ETL mimarisine entegre etme becerisi. |
| **Job Queue Yönetimi** | Binlerce URL kombinasyonunu veritabanında **iş emri (job)** olarak saklayıp, veri çekme sürecini yönetilebilir ve takip edilebilir bir yapıya oturtma. |
| **Kurumsal Tool Arayışı** | Daha güvenilir ve ölçeklenebilir iş akışları için **Apache Airflow** gibi sektör standardı araçları öğrenmeye başlama. |

###  Gelecek Hafta Hedefleri

* **FPS Veri Çekiminin Tamamlanması:** Scraper.do ve parser ikilisiyle `fps_result` tablosunun doldurulması.
* **ML Kaynak Hazırlığı:** Toplanan FPS verilerinin, ilerideki performans tahminleme modeline uygun hale getirilmesi.
* **CAPTCHA Projesine Dönüş:** Yasal onay sonrası projenin mevcut durumunun analiz edilmesi ve yeni planlamanın yapılması.
