#  Staj Günlüğü - 3. Hafta: Operasyonel Hızlanma ve İleri Düzey RAG Mimarisine Giriş

Bu hafta, şirket sistemine adaptasyon sürecimi tamamlayarak, verilen görevlerdeki sorumluluğumun ve hızımın arttığını hissettim. Haftayı hem şirket içi operasyonları doğrudan iyileştiren otomasyonları tamamlayarak hem de gelecekteki LLM (Büyük Dil Modeli) uygulamalarımız için kritik bir altyapı olan **RAG (Retrieval-Augmented Generation)** sisteminin temellerini atarak geçirdim.

##  Odak Alan 1: Şirket İçi Operasyonel Otomasyonlarda Vites Yükseltme

Haftaya, çeşitli departmanların verimliliğini artırmayı hedefleyen dört farklı şirket içi otomasyon göreviyle başladım.

### 1. Tamamlanan ve Aktif Olan Otomasyonlar (3 Adet)

* **Amaç:** Farklı departmanların ihtiyaç duyduğu güncel verilerin anlık olarak sağlanması ve raporlanması.
* **Teknik Uygulama:** n8n platformundaki uyum sürecimin tamamlanmasıyla birlikte, artık platformun **tool'larını (düğümlerini)** daha rahat kullanarak iş akışlarını hızlıca kurguladım.
* **Akış Detayı:**
    1.  **Veri Çekme ve Güncelleme:** Veritabanından (MySQL vb.) belirlenen tabloların alınması ve güncel verilerin çekilmesi sağlandı.
    2.  **Format Dönüşümü:** Çekilen bu tabloların otomatik olarak **Excel dosyasına** dönüştürülmesi gerçekleştirildi.
    3.  **Dağıtım:** Oluşturulan bu raporların, n8n üzerinden ilgili kişilere **otomatik e-posta eki** olarak gönderilmesi aktif hale getirildi.
* **Durum:** Bu üç otomasyondan ikisi aktif olarak çalışmaya başladı, üçüncüsü ise test aşamasının tamamlanmasını beklemektedir.

### 2. Kritik Öğrenim: SOAP API Deneyimi ve Otomasyonun Sınırları

Haftanın en önemli teknik kazanımlarından biri, PTT-KEP sistemine entegrasyon projesi sırasında karşılaştığım yeni bir API mimarisiydi.

* **Hedef:** Şirkete gelen e-tebligatların otomatik olarak taranması ve ilgili departmanlara özel olarak mail ile bildirim gönderilmesi.
* **SOAP API ile Tanışma:** Dokümantasyonu incelerken, API servislerinin **REST API** yerine **SOAP (Simple Object Access Protocol)** mimarisiyle kurulduğunu gördüm. REST API'ye hâkim olmama rağmen, SOAP benim için ilk kez karşılaştığım bir durumdu.
* **Adaptasyon:** SOAP'un XML tabanlı yapısını, WSDL (Web Services Description Language) dosyalarını ve çalışma prensibini hızla kavradım. Bu süreç, yeni ve farklı teknik sistemlere adapte olma yeteneğimin ne kadar hızlı olduğunu gösteren değerli bir pratik oldu.
* **Karar Verme ve Sınır Çizme:** Ne var ki, bu API'lere giriş yapılırken **SMS veya e-imza gibi İki Faktörlü Doğrulama (2FA)** yöntemlerinin zorunlu olduğu tespit edildi. Otomasyonla 2FA süreçlerini aşmaya çalışmanın, harcanacak zaman ve efor açısından **değerli olmayacağına** karar verildi. Bu durum, teknik olarak mümkün olsa bile, kurumsal kaynakları doğru önceliklere yönlendirmenin ve bir otomasyon projesinin **fizibilite (yapılabilirlik) sınırlarını** net bir şekilde belirlemenin önemini pekiştirdi. Bu proje şimdilik askıya alındı, ancak her an devreye alınabilir bir plan olarak tutulmaktadır.

---

##  Odak Alan 2: RAG Sistemi Temelleri ve LLM Chatbot Veri Üretimi

Yöneticilerim tarafından bu hafta en önemli görülen görevlerden biri, şirket içi bilgi birikimini kullanacak bir **LLM Chatbot uygulaması için Soru-Cevap (Q&A) veri seti** üretmekti. Bu, ilk ciddi **RAG (Retrieval-Augmented Generation)** projem oldu.

### 1. LangChain ve Vektör Veritabanı Mimarisi

* **Teknoloji Kullanımı:** Öğrenim hayatımda basit çaplı kullandığım **LangChain** ve **RAG** sistemlerini bu projede aktif olarak kullanarak Python bilgimin üzerine Yapay Zeka katmanını inşa ettim.
* **RAG Akışı:**
    1.  **Ham Veri İşleme:** Şirketin elindeki ham PDF dokümanları (resmi ve kaliteli içerikler) sisteme yüklendi ve belirli bir kurguyla organize edildi.
    2.  **Bölümleme (Chunking):** PDF'ler, LLM'in bağlamı doğru yakalaması (Context) amacıyla belirlenen `chunk size` (parça büyüklüğü) ile küçük parçalara bölündü.
    3.  **Vektörleme ve Depolama:** Bölünen bu veri parçaları, **Similarity Search (Benzerlik Araması)** için optimize edilmiş **FAISS Vektör Veritabanı** içerisinde tutuldu.

### 2. Çift Agent Kurulumu: Soru Üretimi ve Değerlendirme

Üretilen Q&A veri setinin kalitesini sağlamak için **İki Yapay Zeka Agent'ı** içeren bir kurgu oluşturdum:

* **Üretici Agent:** RAG sistemiyle desteklenen bu agent, ham verilerin bağlamını kullanarak vektör veritabanından çektiği bilgilerle **Soru ve Cevapları (Q&A)** üretti.
* **Değerlendirici Agent:** Üretilen bu Q&A setini kontrol eden, kalitesini ve tutarlılığını değerlendiren ikinci bir agent kurguladım.

### 3. Maaliyet ve Verimlilik Hesaplaması

Projenin sadece teknik değil, aynı zamanda finansal ve operasyonel yönünü de ele aldım.

* **Amaç:** Üretilecek Q&A sayısını ve AI Agent'ların tüketeceği API anahtarlarının (Key) **maaliyetini** önceden hesaplamak.
* **Uygulama:** **`maaliyet_hesaplama.py`** adında bir fonksiyon yazdım. Bu fonksiyon, eldeki 63-64 adet PDF dosyasının parçalara (chunk) bölündüğünü varsayarak, her bir chunk başından **ortalama 7 soru** üretileceği hesabıyla, hem toplam Q&A sayısını hem de tahmini API maaliyetini hesaplayacak şekilde tasarlandı.

---

##  Haftalık Kazanımlar, Vizyon ve İleriye Dönük Planlar

Bu hafta okulun başlaması nedeniyle işe ayırdığım gün sayısının azalmasına rağmen, teknik derinlik açısından başlangıç hızımdan daha yukarı çıktığımı hissediyorum.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Operasyonel Çeviklik** | N8n platformunda 3 adet şirket içi otomasyonu hızlıca tamamlayıp aktif hale getirerek, iş akışı kurgulama ve dağıtım pratiğinde ustalaştım. |
| **Farklı API Mimarilerine Adaptasyon** | İlk kez karşılaştığım **SOAP API** dokümantasyonunu hızla inceleyip sistemi kavradım. Bu, adaptasyon becerimin önemli bir göstergesidir. |
| **RAG Mimarisi Kurulumu** | **LangChain** kütüphanesi ve **FAISS Vektör Veritabanı** kullanarak, şirket içi verilerden (PDF) bağlamsal bilgi çeken (Similarity Search) bir RAG sisteminin altyapısını kurdum. |
| **Veri Kalitesi ve Maaliyet Odaklılık** | Soru-cevap kalitesini artırmak için **Çift Agent** yapısı kurguladım ve geliştirdiğim Python fonksiyonu ile AI hizmetlerinin **maaliyetini ve veri üretim verimliliğini** önceden hesapladım. |
| **Fizibilite ve Zaman Yönetimi** | 2FA gibi engellerle karşılaşılan projelerde **otomasyon sınırlarını** net bir şekilde belirleyerek, değerli zamanı öncelikli ve yapılabilir görevlere yönlendirme kararını aldım. |

###  Gelecek Hafta Hedefleri

* **RAG Projesi Devamı:** Ham verilerden üretilen soru-cevap setini daha da geliştirmek ve nihai LLM Chatbot uygulamasına entegrasyonu hazırlamak.
* **Yeni Otomasyonlar:**
    * Bir giriş sayfasındaki **Captcha şifrelemesi** bulunan bir sayfaya giriş yaparak içerideki bilgileri sınıflandırma ve mail otomasyonuna bağlama üzerine çalışmak. Bu, yeni bir güvenlik bariyerini aşma challenge'ı olacaktır.
    * Anlık olarak şirket içinden gelebilecek yeni otomasyon taleplerine odaklanmak.
* **İleri Teknoloji Hedefi:** Kendimi **GraphDB** ve **GraphRAG** sistemlerinde geliştirerek günümüzün popüler ve bağlamsal zenginlik sağlayan konularında geri kalmamayı hedefliyorum.