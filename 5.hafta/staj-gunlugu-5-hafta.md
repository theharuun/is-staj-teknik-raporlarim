#  Staj Günlüğü - 5. Hafta: CRNN Mimarisi, Captcha Çözümü ve Çevik Otomasyon

Bu hafta, stajın beşinci haftası olarak, hem operasyonel otomasyon süreçlerinde çevikliğimi gösterdim hem de haftalardır bekleyen teknik bir meydan okuma olan **Captcha çözümlü otomasyon** üzerinde kritik ilerlemeler kaydettim. Bu süreçte, Makine Öğrenimi (ML) model kaydı ve Görüntü İşleme alanında önemli zorluklarla karşılaştım ve bu zorlukları başarıyla aştım.

##  Odak Alan 1: Şirket İçi Operasyonlarda Çeviklik ve Planlama

Hafta boyunca, iş geliştirme ve finans departmanlarının anlık raporlama ihtiyaçlarına yönelik hızlı ve etkili çözümler sunuldu.

### 1. Haftalık Raporlama Otomasyonlarının Hızla Kurulması

* **İş Geliştirme Departmanı:** Pazartesi günü ilk iş olarak, iş geliştirme departmanının **PowerBI** üzerinden takip ettiği verilerin haftalık olarak çekilmesi, **Excel** formatına dönüştürülmesi ve ilgili yetkililere **e-posta** ile iletilmesi otomasyonu, geçmiş tecrübelerimle hızlıca kurgulandı ve tamamlandı.
* **Finans Departmanı Planlaması:** Finans departmanının üzerindeki manuel yükü azaltmak amacıyla toplantılar yapıldı. Bu toplantılarda, belirli finansal oranlara göre listelerin otomatik oluşturulması ve yetkililere mail atılması gerektiği belirlendi ve **üç otomasyon fikri** ortaya çıktı. Bu fikirlerden biri, haftanın son günü **aktif hale getirilerek** hemen kullanıma sunuldu.

Bu hızlı başarılar, n8n platformuna tamamen adapte olduğumu ve şirket içi ihtiyaçları proaktif bir şekilde çözebildiğimi hissettiriyor bana.

##  Odak Alan 2: Captcha Çözümlü Otomasyon ve Mimari Karar Değişikliği

Haftalardır planlanan ancak karmaşıklığı nedeniyle bekleyen **Captcha çözümlü login otomasyonu** projesine bu hafta yoğun mesai harcandı.

### 1. CRNN Mimarisi ve Model Kayıt Engeli

* **Hedef:** Belirli bir harici web sayfasına otomatik giriş yapmak için **CRNN (Convolutional Recurrent Neural Network)** mimarisine dayalı yerel bir model kullanmak.
* **Problem Tespiti:** İnternet üzerindeki hazır CRNN model kodlarını incelerken, modelde kullanılan **`Lambda` katmanının** güncel Makine Öğrenimi kütüphanelerinde artık **`deprecated` (kullanım dışı)** olduğunu fark ettim. Bu durum, modelin sağlıklı bir şekilde `.keras` veya benzer bir uzantıyla kaydedilmesini ve otomasyon içinde kullanılmasını engelliyordu.
* **Çözümleme:** Güncel literatür taraması ve araştırmalar sonucunda, `Lambda` katmanının işlevini yerine getirmesi için yerine **`Transpose` katmanının** kullanılması gerektiğini öğrendim.
* **Uygulama:** Modeli bu katman değişikliği ile güncelledikten sonra, CRNN modelini sorunsuz bir şekilde **`.keras` uzantılı** olarak kaydedebildim ve kullanıma hazır hale getirdim.

### 2. Maliyet/Verimlilik Odaklı Mimari Değişikliği (AI Agent Entegrasyonu)

Model kaydı başarılsa da, bir sonraki aşamada farklı bir pratik engelle karşılaştım:

* **Pratik Engel:** Eğittiğim modelin veri seti, hedef sayfadaki **Captcha font stili ve gürültü yapısıyla** tam olarak eşleşmiyordu. Modelin sıfırdan, yüksek kaliteli veriyle eğitilmesi uzun zaman ve kaynak gerektirecekti.
* **Yönetici Vizyonu ve Çözüm Odaklılık:** Yöneticimiz, otomasyonun **günde sadece bir kez** çalışacağı bilgisini vererek, bu veri bulma ve eğitme zorluğunun üstesinden gelmek için **hızlı, ucuz ve güvenilir** bir alternatif önerdi: **AI Chatbot Entegrasyonu.**
* **Yeni Mimari:** Model tabanlı çözümü geçici olarak kenara bırakarak, **image tabanlı bir Gemini prompt'u** (Görsel İşleyen LLM) entegre ettim. Bu AI Agent, Captcha görselini doğrudan analiz edip metni dönecektir.
* **Gelecek Kaynağı Oluşturma:** Bu yeni mimariyi kurarken, aynı zamanda **orijinal sayfadaki Captcha görsellerini yerel bir alanda depolayacak** bir kod dizilimi ekledim. Bu, gelecekte, daha sağlam bir veri setine sahip olduğumuzda, yerel CRNN modelini yeniden eğitmek için bir **veri kaynağı (dataset)** oluşturma amacını taşıyor.
* **Durum:** Otomasyonun en zorlu giriş ve doğrulama kısmı tamamlanmıştır.

### 3. Otomasyonun Gelecek Aşamaları

Önümüzdeki haftanın planları netleşti:

1.  Giriş yapıldıktan sonra **okunmamış mesajlar listesine** ulaşmak.
2.  Mesajların içeriğini okumak ve detaylarındaki eklentilerdeki **PDF'leri analiz etmek** (OCR veya metin çıkarma).
3.  Verileri sınıflandırmak ve ilgili departmanlara **her gün tetiklenecek** şekilde mail içeriği ve ekleriyle birlikte otomasyonu tamamlamak.

---

##  Haftalık Kazanımlar, Vizyon ve İleriye Dönük Planlar

Bu hafta, hem operasyonel otomasyonlardaki çevikliğimi hem de derin teknik konularda çözüm odaklı düşünme yeteneğimi pekiştirdim.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Model Mimarisi Adaptasyonu** | Makine Öğrenimi alanında, **CRNN mimarisindeki `Lambda` katmanının** `Transpose` katmanıyla değiştirilmesi gerektiğini öğrenerek, **`deprecated`** ML model kaydı engellerini aşma yeteneği kazandım. |
| **Çözüm Odaklılık ve Fikir Üretimi** | Captcha modelini eğitmeye çalışmanın zorluğu karşısında, **hızlı, ucuz ve güvenilir** bir alternatif olarak **image tabanlı AI Chatbot** entegrasyonuna geçiş yaptım. |
| **Gelecek İçin Veri Stratejisi** | Geçici AI çözümünü kullanırken bile, orijinal Captcha görsellerini depolayarak gelecekteki **yerel model eğitimi için ham veri seti oluşturma** vizyonunu hayata geçirdim. |
| **Operasyonel Çeviklik** | Finans ve İş Geliştirme departmanlarının anlık raporlama ihtiyaçlarına yönelik **hızlı ve çoklu otomasyon planlaması** ve uygulaması (3 otomasyon fikrinden biri aktif). |
| **Derinleşme Alanı** | CRNN mimarisini daha derinlemesine tanıma ve model kaydı, sürümü gibi ML operasyonel konularında pratik deneyim kazanma. |

###  Gelecek Hafta Hedefleri

* **Captcha Otomasyonunun Finali:** Login sonrası okunmamış mesajları çekme, eklentideki PDF'leri okuma, analiz etme ve ilgili departmanlara sınıflandırılmış mail olarak gönderme otomasyonunu tamamlamak.
* **LLM Kalite İyileştirmesi:** Eldeki Soru-Cevap oluşturma Agent'larına odaklanarak **soru cevap kalitesini artırmak** ve veri setini **Base Model üzerine Finetuning** etmeye hazır hale getirmek.
* **Benchmark Raporu:** Elde edilen LLM (Finetuned) ve VLM (Vision Language Model) modellerinin **vLLM kütüphanesi** ile detaylı, geniş bir test seti üzerinde **Benchmark analizlerini** yapıp, sonuçları raporlaştırarak sunmak.