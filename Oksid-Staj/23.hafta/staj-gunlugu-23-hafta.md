#  Staj Gelişim Günlüğü - 23. Hafta: Edge Case Yönetimi, ML Genelleme Stratejileri ve Finansal Otomasyonlar

Bu hafta; canlıya alınan API'lerin hata ayıklama (debugging) süreçlerini tamamladım, FPS tahminleme modelimizin verimliliğini artırmak için "Genelleme (Generalization)" odaklı veri seti stratejimizi güncelledim ve finans/denetim birimleri için n8n üzerinden çalışan yeni kurumsal otomasyonlar tasarladım.

---

##  Odak Alan 1: Sunucu Hata Ayıklama (Debugging) ve n8n Orkestrasyonu

Geçtiğimiz hafta sunucuda systemd servisi olarak ayağa kaldırdığım API uç noktalarının (endpoints) canlı ortamdaki davranışlarını ve loglarını inceleyerek güvenirliğini (reliability) artırdım.

* **Log İncelemesi ve Kök Neden Analizi:** Pipeline kurgusunda, arayüzü (UI) değişen hedef siteden veri çekilemediğini loglar üzerinden tespit ettim. İncelemelerim sonucunda sorunun bir CSS seçici hatası değil, bir **"Session Management (Oturum Yönetimi) Edge Case'i"** olduğunu buldum. Sistem, zaten login (giriş) yapılmış bir oturumda "login adımını atla" mantığına sahip olmadığı için hata veriyordu.
* **Müdahale ve Deployment:** Kod tabanında bu "already_logged_in" (zaten giriş yapıldı) senaryosunu (case) kurgulayarak pipeline'ı güncelledim. Güncel kodları sunucuya çekip API'yi systemd üzerinden yeniden başlattım.
* **Orkestrasyon:** API'lerin stabil çalıştığı doğrulandıktan sonra, bu uç noktaları belirli periyotlarla ve kurallarla tetikleyecek yapıyı **n8n (Orchestrator)** üzerinde başarıyla kurguladım.

---

##  Odak Alan 2: Tersine Mühendislik ve ML Genelleme (Generalization) Stratejisi

FPS hesaplama motorumuz, tersine mühendislik algoritmalarımız sayesinde kaynak sitelerdeki değerlere çok yaklaştı. Ancak modelin **Genelleme (Generalization)** yeteneğini (hiç görmediği verilerdeki başarısını) artırmak için veri mimarimizde stratejik bir değişikliğe gittim.

* **Strateji Değişikliği (Donanım vs. Oyun):** İlk planım, donanım havuzuna 100'er CPU ve GPU daha ekleyerek 80.000 yeni kombinasyon (8 oyun üzerinden) üretmekti. Ancak stajyer ekibimizle yaptığımız beyin fırtınaları sonucunda, modelin farklı yük tiplerini öğrenebilmesi için "Oyun Çeşitliliğinin", "Donanım Çeşitliliğinden" daha kritik olduğuna karar verdik.
* **Yeni Veri Seti Mimarisi:** Mevcut donanım havuzumuzu (200'er CPU/GPU) sabit tutarak, test edilen oyun sayısını **8'den 16'ya** çıkardım.
* **Operasyon:** Scraping kodlarımı bu yeni oyun parametrelerine göre revize ettim ve veri çekme hattını (pipeline) yeniden başlattım. Tüm yeni kombinasyon verilerinin gelecek hafta tamamlanmasını bekliyorum.

---

##  Odak Alan 3: Veritabanı ve Finansal Süreç Otomasyonları (n8n & Python)

Şirketin iş birimlerinden gelen talepler doğrultusunda, n8n üzerinden manuel süreçleri ortadan kaldıran 3 farklı otomasyon geliştirdim.

### 1. SQL Tabanlı Raporlama Otomasyonları

* **Filtre Revizyonu:** Daha önce canlıya alınan bir veritabanı raporlama otomasyonunda, iş biriminin talebi üzerine SQL sorgularına yeni `WHERE` ve `JOIN` filtreleri ekleyerek raporun çok daha spesifik (granüler) hale gelmesini sağladım.
* **Virman Kontrol Otomasyonu:** "Virman (Hesaplar arası transfer) Kontrolleri" için istenen yeni raporu, mevcut n8n şablonumu (template) kullanarak ve sadece arka plandaki SQL sorgusunu değiştirerek çok hızlı bir şekilde sisteme dahil ettim.

### 2. LLM/Regex Destekli IBAN Eşleştirme Otomasyonu (Ar-Ge Aşaması)

* **Manuel Sürecin Tespiti:** Finans/Denetim ekibinin son 15 günlük iki ayrı Excel dosyasını (Giden IBAN'lar ve Açıklamalar) manuel olarak inceleyip şirket/şahıs eşleştirmesi yaptığını fark ettim.
* **Data Science & NLP Çözümü:** Açıklama (Description) metinleri içerisinden şirket veya şahıs isimlerini saptamak için **Regex (Düzenli İfadeler)** ve kurallı eşleştirme mantıklarını kullanan bir Python betiği (script) yazdım. Sistem şu an manuel çalıştırıldığında dahi saatler sürecek işi saniyelere indiriyor.
* **Gelecek Hafta Mimari Planı:** Bu Python kodunu tam otonom hale getirmek için bulut entegrasyonu planladım.
* **Planlanan Akış:** Bulut klasörüne (Google Drive vb.) yeni Excel yüklendiğinde `Trigger Node` tetiklenecek -> Dosya alınacak -> Python script (veya LLM API) ile veriler eşleştirilip temizlenecek -> Çıktı yeni bir Excel'e dönüştürülüp ilgili kişilere otomatik e-posta olarak atılacak.



---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Edge Case Handling** | Oturum açılmış (Logged-in) tarayıcı senaryosunu kurgulayarak scraping botlarının UI değişimlerinde çökmesini engelleme. |
| **Data Strategy & ML** | Modelin ezberlemesini önlemek ve genelleme yeteneğini artırmak için donanım artışı yerine **hedef değişken (oyun) çeşitliliğini** %100 oranında artırma kararı. |
| **Rapid Prototyping** | Mevcut n8n şablonlarını kullanarak yeni SQL tabanlı virman raporlama otomasyonunu rekor sürede canlıya alma. |
| **Text Parsing & Regex** | Finansal metin yığınlarından (Açıklama/IBAN) anlamlı veriler (Entity Extraction) çıkarmak için Regex/Python tabanlı bir ayrıştırıcı (parser) geliştirme. |

---

###  Gelecek Hafta Hedefleri

1. **FPS Veri Setinin Tamamlanması:** 16 oyun üzerinden çekilen genişletilmiş veri setinin tamamlanması ve modelin bu yeni verilerle yeniden eğitilmesi (Retraining).
2. **IBAN Otomasyonunun Canlıya Alınması:** Python ile yazılan IBAN/Şirket eşleştirme mantığının n8n üzerinde "Cloud Trigger -> Process -> Email" akışıyla (Workflow) tam otonom hale getirilmesi.
3. **Model Sonuç Analizi:** Yenilenen FPS modelinin sonuçlarının (RMSE/MAE) analiz edilip hedeflenen genelleme seviyesine ulaşılıp ulaşılmadığının test edilmesi.