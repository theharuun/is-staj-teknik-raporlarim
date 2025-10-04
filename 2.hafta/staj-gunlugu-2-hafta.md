#  Staj Günlüğü - 2. Hafta: Kurumsal Optimizasyon, Güvenlik Kısıtlamaları ve NLP Altyapısı

İkinci haftam, geçtiğimiz hafta temellerini attığım otomasyon yeteneklerini, hem şirketin iç işleyişine hem de stratejik rekabet analizine entegre etme üzerine kurulu yoğun bir tempo ile geçti. Bu süreçte, özellikle veri toplama ve Yapay Zeka modelleme altyapısının temelini attım.

---

##  Odak Alan 1: Kurumsal Süreç Otomasyonları

Haftanın başlangıcında, farklı departmanların kritik süreçlerini optimize etmeye odaklanmam istendi. Bu otomasyonlar, hem operasyonel verimliliği artırdı hem de çalışanların manuel iş yükünü azalttı.

### 1. E-Ticaret Ürün Durumu Takip Sistemi

* **Amaç:** E-ticaret ürünlerinin, çeşitli operasyonel veya lojistik sebeplerle **satışa kapatılması gereken** durumlar için otomatik bir izleme ve bilgilendirme sistemi kurmak.
* **İş Akışı:** Ürün durumundaki değişikliği algılayan bir tetikleyici (trigger) ile otomasyon başlatıldı.
* **Çözüm:** Değiştirilen (satışa kapatılan) ürünlerin güncel bilgilerini ve durumlarını ilgili **kişilere otomatik olarak e-posta yoluyla** ileten sistemler kuruldu. Bu sayede bilgi akışı hızlandırılmış ve hata payı en aza indirilmiştir.

---

### 2. Kritik Ar-Ge Duyuruları Takip ve Özetleme Sistemi

Bu, otomasyon, web scraping ve yapay zeka analizini birleştiren en kapsamlı otomasyondu.

* **Hedef:** Belirlenen kritik sanayi ve **Ar-Ge duyuru sayfasındaki** yeni duyuruları web scraping ile çekmek, içeriklerini analiz etmek ve ilgili kişilere özet halinde iletmek.

#### A. Veri Toplama ve Analiz: Metin Çözümleme ve OCR Macerası

Duyuru sayfasının içeriği başarıyla kazındı ve duyuru içerisinde PDF dosyası varsa, bu PDF'ler indirildi. Ancak **Yapay Zeka (AI) ile Özetleme** aşamasına geldiğimizde, kritik bir sorunla karşılaştım:

* **Problem Keşfi:** PDF içeriklerini analiz ettiğimde, beklediğimden çok daha az metin olduğunu veya hiç metin olmadığını keşfettim. Detaylı incelemeler sonucunda, PDF'lerin içindeki metinlerin **salt metin (text)** formatında değil, **resimler şeklinde** gömülerek PDF'e dönüştürüldüğünü, bu yüzden standart metin çıkarma yöntemleriyle algılanmadığını fark ettim.

* **Çözüm: Optik Karakter Tanıma (OCR) Entegrasyonu:** Bu engeli aşmak için, görüntü tabanlı metinleri algılayan ve metne dönüştüren **OCR (Optical Character Recognition)** teknolojisine yöneldim.
    1.  **PDF Dönüşümü:** Öncelikle n8n iş akışı içinde PDF dosyasını, HTTP isteği ile OCR servisine gönderilebilecek formata, yani **PDF-to-Base64** formatına dönüştürdüm.
    2.  **OCR API Kullanımı:** İnternet üzerinde, ücretsiz kullanım limiti yüksek olan **[ocr.space/OCRAPI](https://ocr.space/OCRAPI)** gibi harici bir OCR servisini HTTP isteği ile entegre ettim.
    3.  **Metin Çıkarma:** Base64 formatındaki PDF'i bu API'ye gönderdim ve API'den gelen **çıkarılmış metin (extracted text)** ile süreci tamamladım.
    4.  **Nihai Analiz:** Elde edilen metin, AI modeline (LLM) yollandı ve özetleme/analiz süreci başarıyla tamamlanarak otomasyon akışına entegre edildi.

Bu süreç, **bir otomasyon projesinin sadece akışı kurmaktan ibaret olmadığını, aynı zamanda beklenmedik veri formatı engellerini aşmak için yaratıcı ve çok katmanlı çözümler gerektirdiğini** gösteren önemli bir vizyon kazandırdı.

#### B. Kritik Öğrenim: Hafıza Yönetimi (Durum Koruma)

* **Problem:** Aynı duyuruların defalarca mail olarak gönderilmesi, bilgi kirliliğine neden olacaktı.
* **Çözüm: MySQL Entegrasyonu:** Bu otomasyonun bir **hafızaya** sahip olması gerekiyordu. Bu amaçla bir **MySQL** veritabanı kuruldu.
* **Teknik Uygulama:** Duyuru sayfasının **detay linki** (URL) kullanılarak tabloda **benzersiz bir anahtar (Unique Key)** oluşturuldu. Otomasyon, yeni bir duyuru geldiğinde önce bu URL'yi veritabanında arıyor; eğer kayıt mevcut değilse (yeni duyuruysa), mail atma ve veritabanına kaydetme işlemini gerçekleştiriyor.

---

##  Odak Alan 2: Yerel NLP Modeli Altyapısı ve İleri Web Kazıma

Bu haftanın en stratejik ilerlemesi, e-ticaret rekabet analizi projesini bir adım öteye taşıyarak **Yerel NLP Modelimizi** kurmak için gerekli altyapıyı hazırlamak oldu.

### 1. Veri İhtiyacı ve Etiketleme Stratejisi

* **Hedef:** Harici bir Yapay Zeka modeline (Gemini vb.) olan bağımlılığı azaltmak ve kendi iş ihtiyaçlarımıza özel bir **Yerel NLP Modeli** (Örn: NER, Duygu Analizi) eğitmek.
* **İlk Adım:** Model eğitimi için etiketlenmiş, yüksek kaliteli **yorum veri setine** ihtiyaç duyuldu.
* **Etiketleme:** Güncel en çok satan ürünlerin son 50 yorumu çekildi ve **Gemini** gibi mevcut güçlü AI chatbotları kullanılarak bu yorumlar etiketlendi (etiketli veri üretimi).

### 2. Güvenlik Kısıtlamalarını Aşma ve Yeni Mimariye Geçiş

Veri toplama sürecinde **n8n'in yerleşik Web Scraping nodelarının** yetersiz kaldığı kritik bir noktayla karşılaştım.

* **Problem:** E-ticaret platformlarının **Cloudflare** benzeri gelişmiş güvenlik korumaları, `BeautifulSoup` gibi geleneksel kütüphaneleri kullanan n8n nodelarını yakalıyor ve veri çekmeyi engelliyordu.
* **İlk Çözüm Denemesi (Harici Servisler):** Araştırmalar sonucunda **ScrapingBee** gibi harici bir web kazıma hizmetini (3. parti) test ettim. Bu servis, tarayıcı açılışı taklit ederek güvenlik politikalarını aşmada başarılı oldu. Ancak, ücretsiz limitlerin hızla tükenmesi, **sürekli harici bağımlılığın getireceği maliyet ve sorunları** görmemi sağladı.

### 3. Yerel Mikroservis Geliştirme (Python + FastAPI)

Harici bağımlılıktan kurtulmak için en sağlam yönteme geçiş yapıldı: **Kendi Kazıma API'mizi geliştirmek.**

* **Mimari:** Yerel bilgisayarımda bir **Python scripti** yazdım ve bunu **FastAPI** kullanarak bir **Yerel Endpoint** (Örn: `http://localhost:8080/yorumlar`) olarak ayağa kaldırdım.
* **Teknik Çözüm:** Bu Python scripti içinde, güncel web sayfalarının karmaşık güvenlik mekanizmalarını ve JavaScript yüklemelerini yönetebilen **Selenium** ve **WebDriver** gibi kütüphaneleri kullanarak sayfalara ulaştım ve yorumları başarıyla çektim.

### 4. Bağlantı Sorunu ve Ngrok ile Çözümün Tekrarı

Yerel API'yi kurduktan sonra, n8n iş akışına bağlarken bir erişim sorunu ortaya çıktı.

* **Problem:** **Sunucu bağlantılı kurulan n8n platformu**, benim **yerel (local) bilgisayarımdaki** (Örn: `http://localhost:8080`) API'ye doğrudan erişemiyordu.
* **Çözüm:** Geçen hafta Telegram bağlantısı için kullandığım **Ngrok tünelleme aracını** tekrar kullandım. Ngrok sayesinde, yereldeki FastAPI endpoint'ini dış dünyaya güvenli bir tünel üzerinden açtım.
* **Nihai İş Akışı:** N8n'deki **HTTP Request Node'u**, artık yerel Gemin-based agent'lar yerine, Ngrok tarafından sağlanan bu tünel aracılığıyla FastAPI endpoint'ine istek atarak tüm veri çekme işlemini gerçekleştirdi. Bu, **iş akışını sadeleştirdi** ve **harici AI/Kazıma Node'larına olan bağımlılığı tamamen kaldırdı.**

---

##  Gelecek Vizyonu ve Stratejik Hedefler

Bu hafta atılan altyapı adımları, projenin gelecekteki ölçeklenmesi ve bağımsızlığı için kritik önem taşımaktadır.

* **NLP Modeli Geliştirme:** Etiketli veri seti yeterli büyüklüğe ulaştığında, yöneticimin yönlendirmesiyle bu veriyi kullanarak **kendi yerel NLP modelimizi** (Örn: NER, Duygu Analizi) eğitmeye başlayacağız. Bu, harici 3. parti AI servislerine olan bağımlılığı ortadan kaldıracaktır.
* **Yatayda Genişleme:** Mevcut çalışmalarımı tek bir e-ticaret sitesi üzerinden, yerel ve yabancı **diğer ünlü e-ticaret sitelerine yayarak** kapsamlı bir rekabet analizi veri havuzu oluşturmak.
* **Güvenilir Tünelleme Araştırması:** Bir Sistem Yöneticisi (System Admin) büyüğümün önerisiyle, Ngrok yerine kendi domainimizle tünelleme yapabilen ve daha güvenilir olabilecek **Cloudflare Tunnel** gibi çözümleri araştıracağım. Bu, gelecekteki otomasyonlarımız için daha güvenli ve profesyonel bir altyapı sağlayacaktır.

---

##  Haftalık Kazanımlar ve Anahtar Öğrenimler

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Durum Koruma (State Management)** | Otomasyonlarda **tekrar eden işlemleri engellemek** için **MySQL** üzerinde, duyuru linki gibi benzersiz alanları kullanarak **"Hafıza" (Memory) mantığı** geliştirdim. |
| **AI ile Metin Analizi** | Duyuru PDF'lerinin içeriğini yapay zeka ile çekip özetleme ve bu çıktıyı mail otomasyonuna entegre etme becerisi. |
| **Güvenlik Kısıtlamalarını Aşma** | Geleneksel kütüphanelerin Cloudflare gibi korumaları aşamadığı durumlarda, **Selenium ve WebDriver** kullanarak tarayıcı tabanlı, insana benzer web kazıma (scraping) yöntemlerine geçiş yaptım. |
| **Mikroservis Mimarisi** | Veri kazıma işlemini n8n'den ayırarak **Python ve FastAPI** ile kendi **yerel API Endpoint'imi** kurdum. Bu, iş akışı mantığını sadeleştirdi ve kod esnekliğini artırdı. |
| **Ağ Tünelleme Stratejisi** | Farklı sunucularda (n8n sunucusu ve local host) çalışan iki servisin iletişimini sağlamak için **Ngrok** tünellemesini yeniden uygulayarak, yerel API'ye güvenli erişim sağladım. |
| **NLP Bağımsızlık Yolu** | Kendi **etiketli yorum veri setimizi** oluşturarak, şirketin gelecekte harici AI servislerine bağımlı olmadan kendi özelleştirilmiş NLP modellerini eğitmesinin altyapısını kurdum. |

---

Gelecek haftalar, hem otomasyon kapsamını genişletmek hem de **RAG, GraphDB ve GraphRAG** gibi popüler ve ileri düzey mimarilerde kendimi geliştirmeye odaklanarak geçecek. Yeni süreçler ve öğreneceklerim için heyecanlıyım.