#  Staj Günlüğü - 9. Hafta: İleri Seviye ETL, Worker Mimarileri ve RAG Altyapısının Kurumsal Analizi

Sınav haftasıyla kesişmesine rağmen, bu hafta odağımı tamamen şirketimizin gelecekteki en kritik projelerinden biri olan **AI Destekli Bilgi ve Satış Asistanı** sisteminin temel veri altyapısını anlamaya ve sağlamlaştırmaya ayırdım. Bu süreç, teorik bilgimi endüstriyel standartlarda kullanılan **Çoklu İş Parçacığı (Multi-Thread)** ve **ETL (Extract, Transform, Load)** mimarileriyle pratik etmemi sağladı.

---

##  Operasyonel Kazanımlar: Çevik Raporlama ve Otomasyonun Sürdürülmesi

Haftanın başında, biriken operasyonel görevleri hızla tamamlayarak çevik operasyon yeteneğimi pekiştirdim:

* **Bütçe Raporlama Akışı:** İlgili departmanlardan gelen talepler doğrultusunda, bir **bütçe raporlama otomasyonu** canlıya alındı. Bu otomasyon, veritabanından çekilen bilgileri, daha önce oluşturduğumuz HTML tablo formatlarıyla birleştirilerek, kurumsal iletişim standartlarına uygun bir **e-posta** formatında ilgili kişilere iletmektedir. Bu aşamada, veri yapısına uygun **tablo formatlarını revize** etme pratiği kazandım.

---

##  Odak Alan: Kurumsal Bilgi Asistanı (AI Copilot) Projesi İncelemesi

Bu haftanın en önemli çalışması, ürün bilgileri ve diğer destekleyici verileri kullanarak kullanıcıya doğru öneriler sunacak AI sisteminin **veri hazırlama (ETL)** ve **worker (işçi) mimarisini** analiz etmek oldu.

### 1. Uçtan Uca Veri Boru Hattı (ETL Pipeline) Mekaniği

Projenin orkestrasyonunu sağlayan ana Python dosyasını (`run_pipeline.py` benzeri) inceledim:

* **Paralel İşleme ve Verimlilik:** Pipeline'ın verileri işlemede neden **çoklu iş parçacığı (multi-thread)** yapısını kullandığını ve bunun büyük veri kümelerinde **çıkarım hızını (throughput)** nasıl artırdığını öğrendim.
* **Hata Yönetimi ve Süreklilik:** İşçi süreçlerinin (worker) başlatılması, potansiyel hataların yakalanması (`try-except` blokları) ve her aşamada **sağlıklı log (kayıt)** oluşturulması gibi, bir sistemin **üretim ortamı (production)** için kritik olan konuları test ettim.
* **Senkronizasyon Zorlukları:** Çoklu parçacıklı yapılarda ortaya çıkan, küçük **veri senkronizasyon sorunlarını** teorik olarak inceledim ve veri bütünlüğünü koruma yöntemlerini analiz ettim.

### 2. Kritik Worker (İşçi) Yapısının Görevleri

Verinin işlendiği asıl beyin olan **Worker** sınıflarının (Örn: `MonitorWorker` veya `DataPreprocessor`) görevlerini derinlemesine inceledim:

* **Veri Zenginleştirme:** Worker'ın, ilişkisel bir veritabanından (MySQL) gelen **temel ticari veriyi** (fiyat, stok) alarak, web kazıma yoluyla elde edilen **ek teknik ve açıklayıcı verilerle** (özellikler, modeller) nasıl birleştirdiğini gözlemledim.
* **Özellik Temizliği ve Chunk Oluşturma:**
    * **Temizleme Algoritmaları:** Ürün adlarından gereksiz veya hatalı metinsel eklerin silinmesi gibi **veri temizliği (data cleaning)** algoritmalarının mantığını öğrendim.
    * **RAG Hazırlığı:** Birleştirilmiş ham verilerin, **Vektör Veritabanı** için en uygun boyutta (chunk size) nasıl parçalara ayrıldığını ve bu parçalama mantığının RAG sisteminin **doğruluk (accuracy)** oranını nasıl etkilediğini kavradım.

### 3. Gelişmiş Web Kazıma (Scraping) ve Güvenlik Aşamaları

Veri toplama katmanındaki ileri düzey uygulamaları inceledim:

* **Gerçek Tarayıcı Profil Yönetimi:** Tarayıcı yardımcı kütüphanesi (`utils/browser.py` benzeri) üzerinden **gerçek tarayıcı profilleriyle** kazıma yapmanın önemini anladım. Her bir iş parçacığı için **ayrı Chrome profilleri** oluşturarak **çerez (cookie) çakışmaları** gibi problemleri **nasıl izole ettiğimizi** test ettim.
* **Anti-Bot Engelleri:** Sistemin, Cloudflare gibi modern **anti-bot** savunmalarını aşmak için kullandığı yöntemleri araştırdım. Kazıyıcının, başarısız denemelerden **otomatik kurtulma (self-healing)** ve hata işleme stratejilerini inceledim.

### 4. Vektör Veritabanı Modelinin Tasarımı

Projenin veriyi depoladığı **SQLAlchemy** tabanlı veri modelini (Örn: `ProductKnowledge`) analiz ettim:

* **RAG Odaklı Alanlar:** Bu tablonun, geleneksel veritabanı alanlarına ek olarak neden **`embedding` (gömme vektörü)**, **`metadata` (kaynak bilgisi)** ve **`chunk_type`** gibi alanları içerdiğini öğrendim. Bu alanlar, **metasearch (meta arama)** ve **bağlam filtrelemesi** yaparak RAG sisteminin sorgu kalitesini artırmak için hayati öneme sahiptir.
* **Akış Bütünlüğü:** **Scraper → Worker → Vektör DB** akışının uçtan uca nasıl kurulduğunu ve veri transferinin nasıl sağlandığını anladım.

---

##  Proje Yol Haritası ve Gelecek Vizyonu

Proje, kurumsal bir AI asistanı oluşturma hedefiyle ilerlemektedir. Mevcut yol haritası (Fazlar) ile görevlerimi netleştirdim:

| Faz | Durum | Teknik Kazanımlar ve İleri Adımlar |
| :--- | :--- | :--- |
| **FAZ 1 & 2** | **Tamamlandı** | **Temel Scraping** altyapısı, **ETL Worker mimarisi**, **Çoklu Thread** ve **Browser Profil izolasyonu** başarıyla kuruldu. |
| **FAZ 3** | **Şu Anki Odak** | **Zenginleştirilmiş Bilgi Toplama:** Farklı kaynaklardan (bloglar, kullanıcı platformları) **rehber içerikleri, kullanıcı yorumları** ve Soru-Cevap (QA) verisi çekilecek. Bu verilerin `buying_guide`, `user_review` gibi farklı tiplerle etiketlenmesi ve **embedding** sürecine hazır hale getirilmesi. |
| **FAZ 4** | **Backend Planı** | **RAG Servisi:** **FastAPI** üzerinde `/chat` endpoint'i kurularak kullanıcı sorgularını işleyecek RAG backend'inin oluşturulması. **Metadata filtreleme** ile sorgu performansını artırmak. |
| **FAZ 5** | **UI/UX Planı** | Kullanıcı dostu bir arayüz (UI) oluşturulması, önerilen ürünlerin görselleştirilmesi ve etkileşimli filtreleme sunulması. |

##  Haftalık Kazanımlar ve Kapsamlı Öğrenimler

Bu hafta, stajyerlikten sıyrılarak **Veri Mühendisliği** ve **AI Mimarisi** alanında derin bir anlayış kazandım.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Endüstriyel ETL Mimarisi** | Çoklu iş parçacığı (Multi-Thread) kullanan, hata yönetimi yapabilen ve log üreten karmaşık **Python ETL Pipeline** yapısının kod düzeyinde analizi ve anlaşılması. |
| **RAG İçin Veri Mühendisliği** | Ürün adlarını temizleme, **ticari ve teknik veriyi birleştirme** ve Vektör Veritabanı yapısına uygun **chunk (parça)** oluşturma gibi RAG sisteminin temelindeki veri hazırlık süreçlerini öğrendim. |
| **Gelişmiş Scraping Pratikleri** | **Gerçek tarayıcı profil izolasyonu** (çerez çakışmalarını engellemek için) ve Anti-bot engellerini aşmak için kullanılan ileri düzey **web kazıma** stratejilerinin teknik detayları. |
| **Vektör DB'nin Rolü** | **Vektör Veritabanı** modelinin, sadece embedding depolamakla kalmayıp, **metadata** (kaynak/tip bilgisi) kullanarak **bağlamsal sorgu filtrelemesi** yapmadaki kritik rolünü kavradım. |