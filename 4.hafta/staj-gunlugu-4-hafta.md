
#  Staj Günlüğü - 4. Hafta: NLP Derinlikleri, Atıf Çözümleme ve LLM Benchmark

Dördüncü staj haftası, üç günlük katılımıma rağmen, teknik derinlik ve problem çözme becerisi açısından en verimli haftalarımdan biri oldu. Bu hafta, operasyonel otomasyonlara (Captcha çözümlü görev gibi) ara vererek, tüm odağımı LLM (Büyük Dil Modeli) uygulamamızın kalitesini artırmaya ve model değerlendirme (benchmarking) süreçlerini öğrenmeye ayırdım.

##  Odak Alan 1: RAG Sisteminde Atıf (Anaphora) Probleminin Çözümü

Geçen hafta kurduğumuz RAG (Retrieval-Augmented Generation) tabanlı soru-cevap üretim sisteminde, üretilen soruların kalitesini düşüren kritik bir dilbilimsel problem tespit edildi.

### 1. Problemin Tespiti: Anaphora (Atıf) Sorunu

* **Gözlem:** RAG sistemi tarafından üretilen sorularda, kaynak metinden çekilen bağlamı işaret eden "bu", "şu", "o" gibi **işaret zamirleri/sıfatları** (deictic expressions) sıkça kullanılıyordu. Bu durum, sorunun kendi başına anlamsız kalmasına veya bağlamı kaybetmesine neden oluyordu.
* **Kavramsallaştırma:** Yaptığım araştırmalar sonucunda, bu sorunun **NLP (Doğal Dil İşleme)** alanında **"Anaphora Resolution" (Atıf Çözümleme)** olarak adlandırıldığını öğrendim. Bu, bir kelimenin, kendisinden önce kullanılan başka bir kelimeye atıfta bulunduğu durumu ifade eder. İnsan zekası için doğal olan bu durum, bilgisayar modellerine net bir şekilde öğretilmesi zor bir konudur.

### 2. Hazır Çözüm Arayışı ve Türkçe Engeli

* **Kütüphane Keşfi:** Problemin çözümüne yönelik araştırmalarımda **`coreferee 1.4.1`** gibi Python kütüphanelerini buldum. Bu kütüphanenin, İngilizce, Almanca, Fransızca gibi dillerde atıf çözümlemede güçlü olduğunu tespit ettim.
* **Yerelleştirme Engeli:** Ne yazık ki, kütüphanenin dokümantasyonunu incelediğimde **Türkçe dil desteği** olmadığını gördüm. Bu durum, hazır çözümler yerine **Türkçe'ye özel, yaratıcı bir çözüm** geliştirmem gerektiğini gösterdi.

### 3. Agent Tabanlı Yaratıcı Çözüm Geliştirme

Türkçe atıf sorununu çözmek için, mevcut **Agent tabanlı soru-cevap üretim** düzenimizi optimize etmeye karar verdim:

* **Yeni Prompt Stratejisi:** Generator (Üretici) Agent'ın prompt'una yeni bir talimat ekledim.
* **Alias (Takma Ad) Üretimi:** Agent'tan, bir soru oluştururken referans olarak kullanacağı ana metin parçası için öncelikle bir **Title (Başlık)** oluşturmasını istedim.
* **Referans Değişimi:** En önemlisi, agent'ın bu Title'a referans eden **en az 3 tane Alias (Takma Ad)** oluşturmasını ve soru ile cevap içerisinde "bu/şu" gibi zamirler yerine **bu oluşturulan Alias'ları** kullanmasını sağladım.

Bu yaratıcı prompt müdahalesi sayesinde, **atıf problemini dilbilimsel bir kütüphane kullanmadan, LLM'in kendi üretkenlik gücünü kullanarak** büyük oranda çözmeyi başardım.

---

##  Odak Alan 2: vLLM Kütüphanesi ve LLM Benchmark Altyapısı

Bu haftanın bir diğer önemli kazanımı, yöneticimin yönlendirmesiyle **LLM'lerin performansını ve verimliliğini değerlendirme** süreçlerini öğrenmek oldu.

### 1. vLLM Kütüphanesi ile Tanışma

* **Amaç:** Şirketimizde ürettiğimiz veya halihazırda var olan (Base Model) LLM'lerin ve VLM'lerin performansını karşılaştırmak ve değerlendirmek için bir **Benchmark (Kıyaslama)** sistemi kurmak.
* **Teknoloji:** Bu süreç için yüksek performanslı LLM çıkarımı (inference) için tasarlanmış olan **vLLM** kütüphanesini araştırmaya başladım.
* **Öğrenme Süreci:** Orijinal dokümantasyonlar ve YouTube kaynaklarını kullanarak vLLM'in temel metrikleri (hız, verimlilik, gecikme süresi) nasıl ölçtüğünü ve model karşılaştırmalarının nasıl yapıldığını detaylıca öğrendim.

### 2. Benchmark Altyapısı Zorlukları (GPU Kısıtlaması)

* **Donanım İhtiyacı:** vLLM gibi yüksek performanslı çıkarım kütüphanelerinin verimli çalışması için **GPU (Grafik İşlemci Ünitesi) donanımına** ihtiyaç duyulmaktadır.
* **Pratik Engel:** Bu ihtiyacı aşmak için **Google Colab'ın T4 GPU'sunu** kullanmaya çalıştım. Ancak, piyasadaki birçok güncel Base Model'in (genellikle 7B, 13B ve üzeri parametreli) mevcut kısıtlı GPU hafızasına sığmadığını deneyimledim.
* **Sonuç İletimi:** Bu kısıtlamayı üstlerime ilettim ve elimizdeki mevcut donanım/kaynaklarla yalnızca **2-3 milyar parametreli** daha küçük modellerin test edilebileceğini, daha büyük modeller için daha güçlü donanım gerekeceğini raporladım. Bu durum, teorik bilgiyi pratik kaynak kısıtlamalarıyla birleştirerek fizibilite değerlendirmesi yapma becerimi geliştirdi.

---

##  Haftalık Kazanımlar, Vizyon ve İleriye Dönük Planlar

Bu hafta, stajın ilk haftalarındaki hızın üzerine çıkarak, nicel otomasyonlardan ziyade **nitelikli ve mimari açıdan kritik** problemlere odaklanarak önemli adımlar attım.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **NLP Atıf Çözümleme** | Türkçe diline özel **Anaphora (Atıf)** problemini tespit ettim ve **Agent Prompt Engineering** kullanarak **Alias (Takma Ad) üretimi** ile bu sorunu yaratıcı bir şekilde çözdüm. |
| **LLM Benchmark Kavrayışı** | **vLLM** kütüphanesinin temel çalışma prensiplerini, LLM'lerin performans metriklerini ve karşılaştırma yöntemlerini öğrendim. |
| **Kaynak Optimizasyonu** | Colab T4 gibi kısıtlı GPU ortamlarında büyük dil modellerini çalıştırmanın zorluklarını deneyimledim ve **teknik kısıtlamalar doğrultusunda fizibilite raporu** hazırlama becerisi kazandım. |
| **Problem Tespiti** | Gelişmiş bir RAG sisteminde dahi ortaya çıkabilen ince dilbilimsel hataları (zamir/sıfat kullanımı) **gözlemleme ve kök neden analizi yapma** yeteneği. |
| **Çevik Odak Değişikliği** | Planlanan otomasyon görevlerinden (Captcha çözümü gibi) kritik altyapı projelerine (Soru Kalitesi/Benchmark) **yöneticinin yönlendirmesiyle hızla odak kaydırma** becerisi. |

###  Gelecek Hafta Hedefleri

* **Otomasyon Odaklanması:** Bekleyen **Captcha çözümlü login otomasyonu** görevine odaklanarak, yeni bir güvenlik/sınıflandırma engelini aşmayı hedeflemek.
* **LLM Geliştirme:** LLM tarafındaki mevcut **Soru-Cevap** veri setini olgunlaştırmak ve bu veriyi kullanarak test/eğitim süreçlerine hazırlanmak.
* **İleri Teknoloji:** Gelecek haftalarda **GraphDB** ve **GraphRAG** mimarileri üzerinde çalışmalara başlamak.