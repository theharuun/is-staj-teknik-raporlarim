#  Staj Gelişim Günlüğü - 11. Hafta: Hibrit RAG'de Dinamik SQL Entegrasyonu ve Kurumsal Raporlama Testleri

Bu hafta, stajın önceki dönemlerinde kurduğumuz iki ana projeye odaklandım: **Kişiselleştirilmiş Kurumsal Raporlama Otomasyonu** ve **Akıllı Hibrit RAG Chatbot Sistemi**. Hibrit RAG sisteminde, en kritik mimari adım olan **SQL ve Vektör Araması** entegrasyonunu kod düzeyinde olgunlaştırdım.

---

##  Odak Alan 1: Kişiselleştirilmiş Kurumsal Raporlama Otomasyonu

İç işleyişte önemli bir yer tutan, iki farklı şirketin raporlama ihtiyaçlarını birleştiren karmaşık bir otomasyon üzerinde çalıştım.

* **Amaç:** İki farklı şirketin iç raporlarını alarak, **gruplama, tablo formatında sunma** ve ilgili yönlendirme **linklerini** içeren, **kişi bazlı** farklı formatlarda e-posta raporları oluşturmak.
* **Kişiselleştirme:** Otomasyon, alıcı kişilerin rolüne göre rapor içeriğinin ve formatının değiştiği (Örn: Kimine sadece özet tablo, kimine detaylı tablo + linkler) karmaşık bir mantıkla ayarlandı.
* **Test ve Canlıya Alma Planı:** Bu kişiselleştirilmiş ve kritik otomasyon, bir aylık (30 günlük) **detaylı test sürecine** sokuldu. Testler başarıyla tamamlandıktan sonra, otomasyonun **doğrudan e-posta yoluyla** (manuel kontrolsüz) aktif hale getirilmesi planlandı. Bu, sistemin yüksek güvenilirliğe ulaştığını gösteren önemli bir adımdır.

##  Odak Alan 2: Akıllı Hibrit RAG Sisteminin Kodsal Olgunlaşması

Önceki haftalarda mimarisini tasarladığımız **Hibrit RAG Sistemi**, bu hafta **SQL Filtreleme** katmanının dinamik hale getirilmesiyle somutlaştı.

### 1. Dinamik SQL Filtreleme Geliştirilmesi (Hard Filter)

* **Amaç:** Sistemin, kullanıcının serbest metin sorgusundan (**Prompt**) birincil filtreleri (Fiyat, marka, stok, bütçe) dinamik olarak çıkarmasını ve bu filtreleri SQL sorgusuna çevirmesini sağlamak.
* **Uygulama:** LLM (Large Language Model) gücü kullanılarak:
    1.  Kullanıcının sorgusu alınır.
    2.  LLM'den, belirlenen kategori ve kategori şemasına (veritabanı alanları) uygun **SQL filtrelerini** çıkaracak bir **Prompt Mühendisliği** kurgusu yapıldı.
    3.  Çıkarılan bu SQL filtreleri, elimizdeki **tüm ürün havuzunu daraltmak** için kullanılır (Tier 1 & 2 Filtreleme). Bu, **Sert Filtreleme (Hard Filter)** aşamasını temsil eder.

### 2. Hibrit Arama ve Öneri Sistemi

* **Vektör Araması:** SQL filtresinden **kalan ürün havuzu** içinden, kullanıcının **anlamsal niyetini** (Örn: "akıcı oyun") karşılayan ürünleri bulmak için **Vektör Araması (Soft Search)** yapılır.
* **Kullanıcı Odaklı Çıktı:** Sonuçlar, sadece metinsel cevap olarak değil, aynı zamanda ürünlerin **linkleri ve görselleriyle** birlikte sunulacak şekilde bir **öneri sistemi demo adımlarına** ilerletildi.

### 3. Veri Zenginleştirme Yönü (Tier 5 - Kullanıcı Yorumları)

* Sistemin cevap kalitesini ve güvenilirliğini artırmak için **Tier 5** katmanına odaklanma kararı alındı.
* **Karar:** Projeye, **son kullanıcı yorumları ve değerlendirmeleri** üzerine veri kazıma (scraping) süreçlerinin eklenmesine karar verildi. Bu, sistemin sadece teknik özelliklere değil, aynı zamanda **kullanım deneyimine dayalı** sorulara da cevap vermesini sağlayacaktır (Örn: "Bu üründe ghosting var mı?").

### 4. Projenin Horizontal Genişlemesi (Kategori Çeşitliliği)

* **Stratejik Yönlendirme:** Proje, tek bir kategoride (Vertical) belirli bir olgunluk seviyesine ulaştığı için, sistemin genelliğini ve değerini göstermek amacıyla **diğer kategorilere (Horizontal)** odaklanılması talimatı alındı.
* **Yeni Kategoriler:** **CPU (İşlemciler)** ve **GPU (Ekran Kartları)** gibi yüksek performans ve teknik detay gerektiren yeni kategoriler belirlendi.
* **Planlama:** Gelecek hafta için bu kategorilere yönelik **teknik ve performans verilerinin kazınması** için uygun kaynak (scraping siteleri) tespiti yapıldı ve detaylı **planlamalar** tamamlandı.

---

##  Haftalık Kazanımlar, Vizyon ve İleriye Dönük Planlar

Bu hafta, temel mimarinin hayata geçirilmesi ve projenin kapsamının stratejik olarak genişletilmesi açısından kritikti.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Dinamik SQL Entegrasyonu** | Kullanıcının serbest metin **Prompt'undan** yola çıkarak, **kategori şemasına uygun** ve kurallara dayalı **SQL filtreleri** (Marka, Fiyat vb.) üreten LLM tabanlı bir mekanizma geliştirdim. |
| **Hibrit Arama Somutlaştırması** | Sert Filtreleme (SQL) ve Yumuşak Arama (Vektör) aşamalarının kodda entegrasyonunu sağlayarak, tasarlanan **Hibrit RAG** mimarisini çalışan demo adımlarına taşıdım. |
| **Kişiselleştirilmiş Raporlama** | İki farklı şirketin raporlarını birleştiren, **alıcıya göre formatı değişen** ve tablo/link içeren karmaşık bir otomasyonu test aşamasına taşıyarak **kurumsal raporlama otomasyonu** yeteneğimi pekiştirdim. |
| **Stratejik Kapsam Genişletme** | Tek bir kategorideki dikey (vertical) gelişimin ardından, sistemin genel geçerliliğini ispatlamak için **CPU ve GPU** gibi yeni, teknik olarak zorlu kategorilere **yatay (horizontal)** genişleme planlamasını yaptım. |
| **Veri Kalitesi Planı** | RAG sisteminin güvenilirliğini artırmak amacıyla, **son kullanıcı yorumları** gibi dış kaynaklardan veri kazıma ihtiyacını belirleyip, bunun için yol haritası çıkardım. |