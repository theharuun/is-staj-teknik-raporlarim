#  Staj Gelişim Günlüğü - 17. Hafta: Dinamik ETL Mimarisi, Veri Normalizasyonu ve ML Entegrasyon Stratejisi

Bu hafta, veri toplama süreçlerimizi kurumsal seviyede yönetilebilir kılacak yeni bir veritabanı mimarisi inşa ettim ve AI Chatbot projemizi, şirketin kısa vadeli hedefleri doğrultusunda "Akıllı Performans Gösterge Paneli" yapısına evriltecek stratejik adımları attım.

---

##  Odak Alan 1: Dinamik ve Yönetilebilir ETL Pipeline Mimarisi

Yeni eklenen siteler ve markalarla birlikte, arama parametrelerinin her sitede farklılık göstermesi (Case-sensitivity, özel karakterler vb.) sorununu kökten çözecek bir **Mapping Katmanı** kurguladım.

* **Üçlü Tablo Yapısı:** Veri yönetimini koddan veritabanına taşımak amacıyla `Markalar`, `Siteler` ve bu ikisini birbirine bağlayan `Markalar_Siteler` tablolarını oluşturdum.
* **Arama Pattern Yönetimi:** Her sitenin markaları arama biçimi (Örn: "xxx" vs "XXX") farklı olduğu için, bu varyasyonları `Markalar_Siteler` tablosunda tanımlayarak sistemi tamamen dinamik hale getirdim. Artık yeni bir marka veya site eklendiğinde kod değişikliği gerekmeden sadece DB üzerinden yönetilebilecek bir altyapı mevcut.
* **Pipeline Durumu ve Genişleme:** Mevcut 3 site için pipeline kurulumu tamamlandı. Yeni eklenen 4 site için de aynı modüler yapı kullanılarak ETL süreçleri inşa ediliyor. Hedefim, bu veri mühendisliği görevini tamamladıktan sonra, **n8n** gibi bir orkestratör ile sistemin günlük olarak otomatik güncellenmesini sağlayacak otomasyonu kurmaktır.

---

##  Odak Alan 2: Chatbot'tan "Akıllı Performans Aracı"na Stratejik Pivot

Şirket Genel Müdürü ile gerçekleştirdiğimiz sunum sonrasında, projenin ilk aşamasında chatbot yerine doğrudan kullanıcıya dokunan somut bir araç geliştirilmesine karar verildi.

* **Pazar Odaklı Geliştirme:** İlk etapta tam kapsamlı bir chatbot yerine; web sitemizdeki **stokta bulunan** CPU/GPU'lar ve en popüler 8-10 oyun üzerinden kombinasyonlar üreten bir sistem üzerine yoğunlaştık.
* **FPS Model Entegrasyonu:** Eğittiğim **Inference (Çıkarım) Modelini**, sistem toplama havuzu ve ürün detay sayfalarıyla entegre ettim.
* **Yeni Kullanıcı Deneyimi:** Bu yapı sayesinde kullanıcılar, ürün detayında veya sistem toplama ekranında seçtikleri CPU, GPU ve çözünürlüğe göre anlık FPS tahminlerini görebilecekler. Bu sistem, başlangıçta kural bazlı bir gösterim sunsa da arka planda ML modelimizden beslenen, ileride ise chatbot tool'larına kolayca evrilebilecek bir modül olarak tasarlandı.

---

##  Haftalık Kazanımlar ve Teknik Çıkarımlar

Bu hafta, teknik borçları azaltan ve projenin değerini artıran kritik kararlar aldım.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **İlişkisel Veri Yönetimi** | Marka ve site bazlı arama patternlerini DB düzeyinde normalize ederek **hardcoded bağımlılıkları minimize etme**. |
| **Ölçeklenebilir ETL** | Yeni kaynakların (4 yeni site) mevcut pipeline yapısına hızla entegre edilebileceği modüler bir veri çekme sistemi kurma. |
| **ML-to-Product Entegrasyonu** | Eğitilen ML modelini teorik bir demo olmaktan çıkarıp, **stok verisi ve popüler oyunlar** ile eşleştirerek doğrudan kullanıcıya sunulacak bir özelliğe dönüştürme. |
| **Orkestrasyon Planlaması** | Sürekli güncel veri akışı için n8n tabanlı bir **Scheduled Job** (Zamanlanmış Görev) mimarisi kurgulama. |

###  Gelecek Hafta Hedefleri

* **Yeni Sitelerin Entegrasyonu:** Hedeflenen 4 yeni sitenin ETL süreçlerinin tamamlanması ve ana pipeline'a dahil edilmesi.
* **FPS Dashboard Gelişimi:** Web sitesindeki "Sistem Toplama" ekranına entegre edilecek FPS hesaplama mantığının test edilmesi.
* **Otomasyon Kurulumu:** Veri çekme süreçlerini günlük periyotlara bağlayacak n8n iş akışlarının yayına alınması.

