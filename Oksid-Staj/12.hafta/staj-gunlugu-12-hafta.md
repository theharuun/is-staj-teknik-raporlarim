#  Staj Gelişim Günlüğü - 12. Hafta: Mimari Ölçekleme, Karmaşık Veri Eşleme ve Performans Tahminleme

Bu hafta, daha önce kurduğum hibrit RAG sisteminin teknik altyapısını **yatayda genişleterek** (horizontal scaling), e-ticaretin en teknik ve rekabetçi iki kategorisi olan **CPU (İşlemci)** ve **GPU (Ekran Kartı)** segmentlerini sisteme dahil etmeye odaklandım. Bu süreç, kurduğum mimarinin esnekliğini test etme ve karmaşık veri eşleme sorunlarını çözme fırsatı sundu.

---

##  Odak Alan 1: ETL Pipeline'ın Yatayda Genişletilmesi ve Mimari Başarı

Önceki haftalarda monitör kategorisi için kurduğum altyapıyı, artık sistemin omurgasını oluşturan donanım bileşenlerine uyarladım.

* **Template-Factory Mimarisinin Gücü:** Daha önce geliştirdiğim **Template-Factory** (Şablon Fabrikası) mimarisi sayesinde, yeni kategoriler için sıfırdan kod yazmak yerine mevcut yapıyı genişlettim. Bu sayede farklı donanım bileşenlerini (CPU/GPU) aynı standartlarda ve hızda çekebilen bir **"Veri Kazıma ve İşleme Fabrikası"** kurmuş oldum.
* **Stratejik Önceliklendirme:** Teknik raporlamalar sonucunda, kullanıcı deneyimi için kritik olan CPU ve GPU kategorilerine öncelik verildi. Bu nedenle monitör kategorisi için planlanan **Tier 5 (Kullanıcı Yorumları)** katmanı, kaynakların daha verimli kullanımı adına ileri bir tarihe ertelendi.
* **Gelecek Veri Katmanları:** Şu an teknik özelliklere odaklanılsa da, ilerleyen aşamalarda sistemi besleyecek olan **Buying Guide** (Satın Alma Rehberleri), forumlar ve makaleler için veri kaynağı araştırmaları tamamlandı.

---

##  Odak Alan 2: Veri Eşleme (Entity Matching) Zorlukları ve Çözümü

Farklı kaynaklardan gelen verileri, şirketimizin mevcut ürün portföyü ile eşleştirme sürecinde ciddi teknik zorluklarla karşılaştım.

* **Standart Farklılıkları:** Ürünlerin isimlerinin dünya standartları, Türkiye distribütör formatları ve hatta rakip sitelerin kendi yazım formatları arasında ciddi farklılıklar olduğunu gözlemledim (Örn: Bir yerde "RTX 4060 Ti" olarak geçen ürünün diğer tarafta "4060 Ti 8GB OC" gibi karmaşık bir yapıda olması).
* **Başarılı Entegrasyon:** Bu isimlendirme ve format karmaşasını, geliştirdiğim **normalizasyon algoritmaları** ve eşleme mantığı ile başarıyla aştım. Şirket veritabanındaki CPU/GPU stoklarını, dış kaynaklardan gelen teknik verilerle %100 uyumlu hale getirdim.

---

##  Odak Alan 3: Gelecek Vizyonu - Donanım Odaklı ML ve FPS Tahminleme

Haftanın son aşamasında, sistemin sadece teknik bir sözlük değil, gerçek bir **"Performans Danışmanı"** olması için yeni bir ihtiyaç keşfettik.

* **Kullanıcı Niyeti (User Intent):** Kullanıcıların sadece "Hangi ekran kartı?" diye değil, "Bu sistemle X oyununda kaç FPS alırım?" gibi performansa dayalı sorular soracağı öngörüldü.
* **Oyun Performans Verileri:** Bu ihtiyacı karşılamak için, yüzlerce kombinasyonun (CPU + GPU ikilileri) oyun bazlı FPS değerlerini gösteren veri kaynakları için arayış başlattım.
* **ML Modeli Planlaması:** Geleceğe yönelik olarak, eldeki teknik veriler ve FPS benchmark'ları üzerinden tahminleme yapabilecek bir **Machine Learning (ML) modeli** geliştirme üzerine stratejik toplantılar planladım. Bu, sistemin RAG yapısını "Tahminleme Zekası" ile birleştirecek bir adım olacaktır.

---

##  Haftalık Kazanımlar ve Teknik Çıkarımlar

Bu hafta, bir yazılım mimarı gibi düşünerek sistemin sürdürülebilirliğini ve veri bütünlüğünü sağladım.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Mimari Esneklik (Scalability)** | Kurduğum **ETL Pipeline** ve **Template-Factory** yapısının, farklı veri tiplerine (Monitörden CPU/GPU'ya) kolayca adapte olabildiğini kanıtladım. |
| **Gelişmiş Veri Eşleme** | Farklı isimlendirme standartlarına sahip verileri merkezi bir veritabanında **normalize ederek eşleme** (Entity Matching) konusundaki teknik yetkinliğimi geliştirdim. |
| **Yatay Genişleme Stratejisi** | Veri toplama süreçlerini dikey (tek kategori) yerine yatayda (çoklu kategori) genişleterek sistemin **ürün havuzu kapsamını** ciddi oranda artırdım. |
| **ML ve Tahminleme Vizyonu** | Statik veriden dinamik performansa geçiş için **FPS bazlı veri toplama** ve bu verileri işleyecek bir **ML modeli** kurgulama aşamasına geçiş yaptım. |

###  Gelecek Hafta Hedefleri

* **FPS Veri Seti Oluşturma:** Belirlenen kaynaklardan oyun performans verilerinin çekilmesi ve mevcut donanım verileriyle ilişkilendirilmesi.
* **ML Model Mimari Tasarımı:** Donanım özelliklerine göre performans tahmini yapacak modelin girdi/çıktı parametrelerinin belirlenmesi.
* **Dikey Derinleşme:** CPU ve GPU kategorileri için de rehber ve forum içeriklerinin (Tier 3) sisteme dahil edilmesi.

