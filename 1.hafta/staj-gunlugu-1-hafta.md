# Staj Günlüğü - 1. Hafta: Otomasyon Mimarisine Giriş ve Yapay Zeka Çözümleri

## Giriş ve Kurulum: Yeni Dünyanın "Workflow" Dili

Stajımın ilk haftası, şirketimizin temel hedeflerini ve piyasada üstlendiği rolü anlamaya adadığım bir başlangıçla geçti. Hızlı adaptasyon sürecinin ardından, bana tahsis edilen şirket bilgisayarı üzerinde, günümüzün en kritik konularından biri olan **Yapay Zeka Destekli Otomasyon Çözümleri** geliştirmek üzere kolları sıvadım.

Bu noktada, otomasyon iş akışları (workflow) oluşturmak için seçilen ana platform olan **n8n** ile tanıştım. Daha önce bu kavramlara aşina olsam da, artık bu "yeni dünyanın" fiili bir parçası olmak, benim için heyecan verici bir dönüm noktası oldu.

İlk aşamada, n8n platformunu başarıyla kurdum. Planım, önce platformun temelini oluşturan **Node'ları (Düğümleri)** ve iş akışlarını kavrayabilmek adına basit projelerle (YouTube gibi kaynaklardan) ilerlemekti. Bu kavrayışı edindikten sonra, teorik bilgiyi pratiğe dökerek, çalıştığım e-ticaret şirketine özel, değer yaratan ilk iş akışımı hayata geçirmeye başladım.

---

## Proje 1: AI Agent Destekli Etkileşimli E-Ticaret Bilgi Botu

**Amaç:** Belirli bir veri setinden (veritabanı veya Google E-Tablolar gibi) bilgi çekerek, kullanıcının Telegram üzerinden girdiği doğal dil girdisine karşılık, anlamlı ve alakalı bir çıktı üreten bir yapay zeka destekli sohbet botu (chatbot) oluşturmak.

### 1. Güvenli Ağ ve Telegram Entegrasyonu: Tünelleme Sanatı

Bu projenin ilk ve en kritik öğrenme noktası, **Telegram Bot Node'unun** bağlantı sorunuydu. Telegram, güvenlik politikaları gereği, `http` gibi yerel (local) veya güvenliği şüpheli ağlardan gelen bağlantılara izin vermiyordu.

**Çözüm: Ngrok Kullanımı**

Araştırmalarım sonucunda, yerel sunucumda çalıştırdığım API'yi dış dünyaya güvenli bir şekilde açmak için bir **Tünelleme Aracı** olan **ngrok**'u keşfettim.

* **Mimari Kavrayışı:** Ngrok, yerelde açmaya çalıştığım API'yi, dışarıdan erişilebilir, güvenli (https destekli) bir tünel içerisine alarak sarmalıyor. Bu sayede, Telegram'ın güvenlik gereksinimlerini karşılayan bir ağ üzerinden kimlik ve bağlantı işlemlerini sorunsuz bir şekilde gerçekleştirdim.
* **Aşama:** **BotFather** aracıyla özel bir Telegram sohbet botu oluşturdum. Ardından, botun *Access Token*'ını kullanarak n8n'deki **Telegram Bot Node'unu** yapılandırdım. Bu Node, kullanıcıdan mesaj geldiğinde tetiklenecek şekilde ayarlandı (Trigger Node).

### 2. Veri Kaynağı Bağlantısı: Google Servisleri ve OAuth2

Sıradaki adım, botun arama yapacağı veri kaynağı olarak belirlediğim **Google E-Tablolar (Google Sheets)** servisini bağlamaktı. Bu süreç, harici servislerle güvenli kimlik doğrulama yapısını (Authentication) derinlemesine öğrenmemi sağladı.

* **OAuth2 Süreci:** Google Console (Cloud) üzerinde yeni bir proje oluşturuldu. Bu proje içerisinde, **Google Sheets API** ve gerekli diğer API'ler etkinleştirildi.
* **Güvenli Konfigürasyon:** Bağlantı, modern kimlik doğrulama standardı olan **OAuth2** prensibiyle sağlandı. Gerekli konfigürasyonları yapmak için n8n'in kendi dokümantasyonunu ve özellikle şu faydalı videoyu referans aldım: **[Google OAuth Authentication in n8n - From Setup to Connection](http://www.youtube.com/watch?v=FBGtpWMTppw)**.
* **Veri Seti:** Bağlantı tamamlandıktan sonra, bir e-ticaret ürünü veri seti içeren bir test E-Tablosu oluşturdum ve bu verileri **Google Sheets Node'u** ile iş akışına çektim.

### 3. Çekirdek Mantık: Arama Algoritması ve Kod Node'u

Veriler ve kullanıcı girdisi (prompt) artık akıştaydı. Odaklandığım alan, bu iki veri kümesini anlamlı bir şekilde eşleştirmekti.

* **Kod Node'u:** Bu işleme mantığını oturtmak için bir **Kod Node'u (Code Node)** ekledim.
* **Levenshtein Benzerlik Oranı:** Bu kod bloğu içerisinde, kullanıcının girdiği kelime (örneğin bir ürün adı veya kategorisi) ile veri tabanındaki (E-Tablolardaki) ürünler arasında benzerliği hesaplayan bir algoritma kullandım. Özellikle **Levenshtein Benzerlik Oranı Hesaplama Fonksiyonu** sayesinde, birebir eşleşme olmasa bile, yazım hatalarına veya varyasyonlara sahip anahtar kelimeleri başarıyla yakalayabildim.
* **Çıktı Hazırlama:** Yakalanan ilgili ürün bilgisi, yapay zeka ajanı için girdi olarak hazırlandı.

### 4. Yapay Zeka Ajansı (AI Agent) Entegrasyonu

Son aşamada, kullanıcının girdisi (`prompt`) ve arama sonucu bulunan ürün bilgisi, bir yapay zeka modeline (Google Gemini modeli) iletildi.

* **API Anahtarı:** Bu entegrasyon için **AI Studio** üzerinden bir API anahtarı (`Key`) oluşturdum ve bunu ilgili Node'a girdim.
* **AI Agent Kurulumu:** Basit bir **Hafıza Node'u** ile donatılmış (geliştirilmeye açık), **Sistem Mesajı (System Message)** ve **Kullanıcı Mesajı (User Message)** olarak yapılandırdığım AI Agent'ı kurdum.
    * **Sistem Mesajı (Persona):** Botun bir e-ticaret yardım botu rolünde davranması sağlandı.
    * **Kullanıcı Mesajı:** Kullanıcının girdisi + eşleşen ürün bilgisi bu kısımda modele aktarıldı.
* **Nihai Çıktı:** Agent'ın ürettiği cevap, son **Telegram Send Message Node'u** ile kullanıcıya doğal bir sohbet formatında iletildi.

**Kazanımlar ve Önemli Öğrenimler:**

* **Güvenlik Protokolleri:** Telegram gibi harici servislerin yerel ağ bağlantısı kısıtlamalarını ve **ngrok** gibi tünelleme araçlarının bu kısıtlamaları güvenli bir şekilde aşma yöntemlerini öğrendim.
* **Hizmet Bağlantıları:** Google servisleri gibi büyük platformların **OAuth2** tabanlı güvenli bağlantı (Authentication) süreçlerini ve Google Console (Cloud) üzerinden API yönetimi prensiplerini kavradım.
* **Fuzzy Matching (Bulanık Eşleştirme):** Kod Node'u içerisinde, metin işleme ve arama algoritmalarından **Levenshtein benzerlik oranını** kullanarak kullanıcı deneyimini zenginleştiren arama mantıkları oluşturmayı pratik ettim.

---

## Proje 2: Rekabet Analizi ve Haftanın Ürünleri Workflow'u

İlk projenin ardından, yöneticimin talebi üzerine, şirketimizin rekabet gücünü artırmayı hedefleyen daha kapsamlı bir iş akışına başladım.

**Amaç:** Birden fazla büyük e-ticaret platformundan güncel ürün verilerini (popülerlik, yorum sayısı, puan gibi metrikler) kazımak (web scraping) ve elde edilen verileri bir LLM/AI Agent kullanarak "Gözden Kaçırılan Haftanın Ürünleri" gibi bağlamsal önerilere dönüştürmek.

### 1. Dinamik Veri Kaynaklarını Keşfetme ve API Çözümlemesi

Bu projenin en teknik kısmı, geleneksel web kazıma yöntemleri yerine, hedef sitelerin dinamik veri yükleme mekaniğini anlamak oldu.

* **Geleneksel Olmayan API Erişimi:** Hedef alınan büyük e-ticaret platformlarının (Örn: *Büyük E-ticaret Platformu A*, *Platform B*) API'leri üzerinden veri çekmeye odaklandım. Bu sitelerin API sistemlerinin düşündüğüm kadar korumalı olmadığını fark ettim.
* **Dinamik Sayfalama (Infinite Scroll):** Sayfanın "sürüklendikçe yüklenmesi" (infinite scroll/lazy loading) prensibine göre çalıştığını ve her kaydırma hareketinin arka planda yeni bir API isteğini tetiklediğini gözlemledim.
* **Network Tabı İncelemesi:** Tarayıcımın Geliştirici Araçları (Developer Tools) üzerinden **Network Tab'ı** detaylıca inceleyerek, bu dinamik yüklemeyi tetikleyen asıl API uç noktalarını (endpoints) yakaladım. API isteklerinin yapısını ve parametrelerini çözümleyerek, bu API'lere doğrudan programatik istekler atma yöntemini geliştirdim.

### 2. Veri Kayıt ve İşleme Aşaması

Çözümlediğim API'lere düzenli istekler atarak, şirketimizin sattığı kategorilerle eşleşen güncel ürün bilgilerini çekmeye başladım.

* **Veri Kaydı:** Elde ettiğim detaylı ürün verilerini (isim, fiyat, popülerlik, yorum sayısı, puan) güncel verilerin tutulacağı bir **E-Tablo Formuna (Sheets Formu)** kaydettim.
* **İş Akışı Döngüsü:** Tasarladığım bu akış, her gün bu API'lere tekrar istek atacak:
    * Yeni ürünler varsa bunları sisteme dahil edecek.
    * Yeni ürün yoksa veya popülerlik değiştiyse, var olan ürünler arasından henüz önerilmemiş, yüksek puan ve yoruma sahip ürünleri seçecek.

### 3. İleriye Yönelik Vizyon: Bağlamsal Öneriler (RAG/GraphRAG)

Workflow'un mevcut aşaması basit filtrelemeyle ürün önermeyi sağlasa da, yöneticimle birlikte geleceğe yönelik önemli bir vizyon belirledik:

* **Mevcut Filtreleme (Basit):** Popülerlik, puan ve yorum gibi basit sayısal filtrelemelerle öneri yapılması.
* **Geliştirme Hedefi (Bağlamsal):** İleride **RAG (Retrieval-Augmented Generation)** ve **GraphRAG** mimarilerinin kurulmasıyla, önerilerin sadece yüksek puana dayanması yerine, **yorumların bağlamına göre (Contextual Selection)** seçilmesi hedeflenmektedir. Bu, toplanan yorumlar üzerinde eğitilen bir LLM ile, ürünün niş kitleye hitap edip etmediği gibi derinlemesine analizler yapılabilmesini sağlayacaktır.

### 4. Çıktı ve Dağıtım

* **Mesaj Hazırlığı:** Seçilen ürün bilgisi, bir LLM modeli tarafından profesyonel bir öneri mesajına dönüştürüldü.
* **Dağıtım Kanalları:** Hazırlanan bu mesaj, ilk olarak **E-posta Sistemine** ve sonrasında **Telegram Botu** aracılığıyla ilgili ekiplere veya kullanıcılara otomatik olarak iletildi.

**Kazanımlar ve Önemli Öğrenimler:**

* **Web API Analizi:** Geleneksel web scraping yerine, web sitelerinin arka planda kullandığı dinamik API'leri (özellikle `paging` yerine `scroll` ile tetiklenen) tespit etme ve çözümleme yeteneği kazandım.
* **Veri Stratejisi:** Rekabet analizi için hangi metriklerin (yorum, puan, popülerlik) kritik olduğunu belirleme ve bu verileri düzenli akışa sokma stratejisi geliştirdim.
* **Mimari Vizyonu:** İleri seviye yapay zeka entegrasyonları (RAG/GraphRAG) ve bunların iş akışına nasıl dahil edilebileceğine dair teorik ve pratik bir vizyon oluşturdum.

---

## Sonuç ve Gelecek Hafta Hedefleri

Bu hafta, ilk olarak bireysel bir AI Agent botu oluşturarak otomasyon platformuna ısındım, ardından ise yöneticimin yönlendirmesiyle şirketin ana iş kollarına doğrudan etki edecek bir **Rekabet Analizi** iş akışının ilk fazını tamamladım. Şu an için sadece bir büyük e-ticaret platformunun dalı tamamlanmıştır ve diğer platformların API'lerini sisteme entegre etme işi devam etmektedir.

Boş vakitlerimde ise, şirketin iç operasyonlarını hızlandıracak ve verimliliğini artıracak başka bir **Şirket İçi Otomasyon** iş akışına odaklanmam istendi. Bu da gelecekteki haftaların ana gündem maddesi olacaktır.

| Konu/Kazanım | Detaylı Açıklama | Proje |
| :--- | :--- | :--- |
| **Güvenli Ağ Tünellemesi** | Telegram'ın kısıtlamalarını aşmak için **ngrok** aracılığıyla yerel API'yi güvenli ve harici erişime açma yöntemini öğrendim. | E-Ticaret Bilgi Botu |
| **Google Servis Entegrasyonu** | Google Sheets API'sine **OAuth2** prensibiyle güvenli bağlantı kurma ve Google Console proje yönetimi pratiği. | E-Ticaret Bilgi Botu |
| **Metin İşleme Algoritmaları** | Kullanıcı girdisi ile veri setini eşleştirmek için **Levenshtein Benzerlik Oranı** gibi Bulanık Eşleştirme (Fuzzy Matching) tekniklerini kullandım. | E-Ticaret Bilgi Botu |
| **Dinamik API Çözümleme** | E-ticaret sitelerinin tarayıcı Network Tab'ını kullanarak dinamik yükleme mekanizmalarını (Infinite Scroll) ve gizli API uç noktalarını keşfettim. | Rekabet Analizi |
| **LLM & RAG Vizyonu** | Toplanan verileri işlemek için basit bir **Gemini** modeli entegrasyonu yaptım ve gelecekteki **RAG/GraphRAG** mimarileri ile bağlamsal (contextual) öneri sistemleri geliştirme vizyonu edindim. | Her İki Proje |
http://googleusercontent.com/youtube_content/0