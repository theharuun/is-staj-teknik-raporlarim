#  Staj Gelişim Günlüğü - 26. Hafta: Veri Zenginleştirme, Akıllı UPSERT Mimarisi ve Model Stratejisi

Bu hafta, mevcut otomasyonların erişim yetkilerini (DevOps/DBA süreçleri) düzenleyerek haftaya başladım. FPS tahminleme projesinde global model ile alan odaklı (domain-specific) modelin performans kıyaslamalarını yaparak stratejik bir yön haritası belirledim. En büyük operasyonum ise, Fiyat Benchmark projesine "Detay Sayfası Analizi" yeteneği kazandıracak 6 aşamalı gelişmiş bir veri zenginleştirme (Data Enrichment) boru hattı (pipeline) tasarlayıp ilk siteye entegre etmek oldu.

---

##  Odak Alan 1: Otomasyon Bakımı ve Veritabanı Yetki Yönetimi (Privilege Management)

Rutin sağlık kontrollerim sırasında, veritabanına bağlı kritik bir otomasyonun çalışmadığını tespit ettim.

* **Kök Neden Analizi (Root Cause):** Hata loglarını incelediğimde, sorunun kodsal bir hata değil, veritabanı seviyesinde bir **Yetki (Permission/Grant)** eksikliği olduğunu buldum. Otomasyonun tetiklemeye çalıştığı spesifik bir SQL fonksiyonu (Trigger/Function) için çalıştırma (EXECUTE) yetkisi eksikti.
* **Aksiyon:** Yöneticimle iletişime geçerek ilgili servis hesabı için yetki tanımlaması talep ettim. İzinlerin onaylanıp uygulanmasının ardından otomasyon yeniden sağlıklı ve kesintisiz çalışmaya başladı. Bu durum, veri güvenliği ve erişim yönetimi (IAM) süreçlerindeki tecrübemi pekiştirdi.

---

##  Odak Alan 2: FPS Modelinde "Generalization vs. Domain" Analizi

Genişletilmiş donanım havuzumuz üzerinden 320 farklı CPU/GPU ve 16 oyun kombinasyonunu içeren devasa bir veri seti oluşturuldu. 

* **Veri Ön İşleme (Preprocessing):** Bu geniş veri seti üzerinde global bir öğrenme modeli çıkartmak için özellik mühendisliği (Feature Engineering) ve veri temizleme adımlarına yoğunlaştım.
* **Stratejik Karar:** Yaptığım testler sonucunda, tüm dünyadaki donanımları genellemeye çalışan bu büyük modelin, şu an için şirketimizin kendi stoklarına özelleştirilmiş olan **Domain-Learning (Alan Odaklı)** modelin başarısına henüz ulaşamadığını gözlemledim. Domain modelimiz kısıtlı ama net veriyle çok daha isabetli sonuçlar veriyor.
* **Gelecek Vizyonu:** Global modelin genelleme yeteneğini artırmak için mevcut 320k'lık verinin de ötesine geçecek, çok daha masif bir veri çekme operasyonu ileriye dönük Ar-Ge planlarımıza dahil edildi. Şimdilik "Domain Model" ile production (canlı) ortamında ilerlemeye devam edilecek.

---

##  Odak Alan 3: Fiyat Benchmark Projesi - Gelişmiş Veri Zenginleştirme Mimarisi

İş birimlerinden gelen "Sadece fiyat/stok değil, ürünün karakteristik özelliklerini (barkod, üretici kodu vb.) de toplayalım" talebi üzerine, veri çekme mimarimizi (ETL) yormayacak ve SOLID prensiplerine sadık kalacak **6 Aşamalı Akıllı Entegrasyon** planını tasarladım ve ilk hedef sitede canlıya aldım.

### Planlanan ve Uygulanan Mimari Adımlar:

1.  **Şema Genişletmesi (Schema Evolution):** Log ve Ana ürün tablolarına, yeni çekilecek karakteristik alanlar (üretici kodu, işlemci serisi vb.) için yeni sütunlar eklendi.
2.  **In-Memory Caching (Hafıza Hazırlığı):** Sistem bir markayı taramaya başlamadan önce, veritabanına tek bir `SELECT` atarak mevcut bilinen ürünlerin URL/ID'lerini RAM üzerinde bir **Python Set (Küme)** objesine (O(1) arama karmaşıklığı için) alıyor. 
3.  **Dinamik Hedefleme (Deferred Scraping):** Liste sayfaları taranırken, okunan her ürün ID'si bu "Bilinenler Kümesi" ile kıyaslanıyor. Eğer ürün yeniyse, detay URL'si geçici bir listeye (`urls_to_visit`) ekleniyor.
4.  **Oturum Kapanmadan Detay Ziyareti:** Sayfalama bittikten sonra ama oturum (Session) kapatılmadan hemen önce, bot sadece bu `urls_to_visit` listesindeki *yeni* ürünlerin detay sayfalarına girip karakteristik bilgileri çekiyor ve RAM'deki JSON objesine enjekte ediyor. Bu sayede her seferinde tüm ürünlerin detayına girilmesi (gereksiz trafik ve ban riski) engelleniyor.
5.  **NDJSON İhracı:** Hem detayları çekilmiş yeni ürünler hem de sadece fiyatı güncellenen eski ürünler (detay alanları `null` olarak) standart NDJSON formatında diske yazılıyor.
6.  **Akıllı UPSERT (COALESCE Mimarisi):** Veritabanına aktarım (Ingestion) sırasındaki en kritik mühendislik dokunuşu burada yapıldı. SQL tarafındaki `ON DUPLICATE KEY UPDATE` sorgusuna **`COALESCE`** fonksiyonunu entegre ettim. Böylece; eski bir ürünün sadece fiyatı güncellenmek üzere geldiğinde, beraberinde gelen `null` karakteristik alanlar veritabanındaki mevcut dolu verileri **ezmiyor**. (Örn: `uretici_kodu = COALESCE(VALUES(uretici_kodu), uretici_kodu)`).

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Data Engineering (ETL)** | Gereksiz ağ trafiğini önleyen **In-Memory Caching** (Python Set) tabanlı akıllı URL hedefleme mantığı kurgulama. |
| **Advanced SQL** | `UPSERT` işlemlerinde veri kaybını (null-overwrite) önlemek için **`COALESCE`** fonksiyonunu mimariye entegre etme. |
| **Model Evaluation** | Makine öğrenmesinde **Generalization (Genelleme)** ve **Domain-Specific (Alan Odaklı)** modellerin performans ve iş/maliyet kıyaslamasını yapma. |
| **Sistem Yönetimi** | Veritabanı fonksiyonlarında **EXECUTE yetkileri** ve rol yönetimi (Role-based access) konularında pratik kriz yönetimi. |

###  Gelecek Hafta Hedefleri

1.  **Veri Zenginleştirme Yaygınlaştırması:** İlk sitede başarıyla test edilen "Detay Sayfası Scrape ve COALESCE" mimarisinin, hedeflenen diğer e-ticaret sitelerinin Adapter sınıflarına (plugin mantığıyla) entegre edilmesi.
2.  **Model İyileştirmeleri:** Canlıda kullanılacak olan Domain-Specific FPS modelinin güncel verilerle ince ayarlarının (Fine-tuning) yapılması.
3.  **Otomasyon Monitörizasyonu:** Streamlit üzerinden kurulan hata takip ekranının (Dashboard) yeni eklenen detay kazıma loglarını da kapsayacak şekilde güncellenmesi.