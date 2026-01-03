#  Staj Gelişim Günlüğü - 15. Hafta: Dinamik Adaptasyon, Veritabanı Odaklı Botlar ve ML Model Entegrasyonu

Bu hafta, canlıya çıkış öncesi son revizyonları tamamlamaya, veri toplama süreçlerini dinamikleştirmeye ve AI Chatbot projemizin en vizyoner ayağı olan **FPS Tahminleme Modelini** hayata geçirmeye odaklandım.

---

##  Odak Alan 1: Canlı Öncesi Adaptasyon ve CAPTCHA Bot Revizesi

Canlıya alınması planlanan CAPTCHA çözümlü bot sistemimizin, hedef platformdaki arayüz (UI) değişikliklerine uyum sağlaması için kapsamlı bir güncelleme süreci yürüttüm.

* **UI Adaptasyonu:** Hedef sayfanın arayüzünde yapılan güncellemeler sonucunda, **ETL Pipeline** üzerindeki seçici (selector) ve veri yakalama kodlarını revize ettim.
* **Sonuç:** Sistem, platformun yeni görsel yapısına tam uyumlu hale getirildi. Son test aşamasına geçilen bot, çok yakında tam zamanlı operasyon için aktif hale getirilecektir.

---

##  Odak Alan 2: Dinamik Fiyat Benchmark Sistemi Geliştirmesi

Pazar analizi ve fiyat rekabetçiliği için geliştirilen benchmark sistemini, statik yapıdan çıkarıp tamamen dinamik ve veritabanı kontrollü bir yapıya dönüştürdüm.

* **Dinamik Marka Yönetimi:** Daha önce fonksiyonlar içinde sabit (hardcoded) olarak tutulan marka arama parametrelerini, **veritabanından dinamik olarak okunacak** şekilde güncelledim.
* **Otomatize Tarama:** Sistem artık veritabanında tanımlanan markaları sırasıyla seçip, belirlenen 3 büyük e-ticaret sitesinde ürün taraması yapmaktadır.
* **Veri Yapılandırması:** Botlar tarafından çekilen fiyat, stok ve ürün bilgileri, performans ve veri güvenliği için **NDJSON** formatında depolanmaktadır.
* **Gelecek Adım:** Gelecek hafta, bu NDJSON verilerinin kalıcı veritabanı tablolarına basılması (bulk insert) ve nihai benchmark tablolarının oluşturulması sağlanacaktır.

---

##  Odak Alan 3: FPS Tahminleme İçin Machine Learning (ML) Mimarisi

AI Chatbot projesinin en karmaşık katmanı olan donanım performans tahminleme kısmında, statik veri çekiminden **tahminleme modeline** geçiş yaptım.

* **Model Eğitimi İçin Örneklem:** Çekilen binlerce kombinasyon arasından seçilen 1000 adetlik stratejik veri seti, yerel CPU/GPU eşleştirmeleriyle genişletilerek **2.000 adetlik benzersiz bir eğitim seti** haline getirildi.
* **ML Model Gelişimi:** Tüm kombinasyonların çekilmesini beklemeden, sistem mimarisini oturtmak adına bu veri setiyle ML modelini kurmaya başladım. Modelin tamamlanmasına sadece bir günlük çalışma kalmış durumdadır.
* **Reasoning (Muhakeme) Entegrasyonu:** Bu model, Chatbot'un **Reasoning Engine** katmanına entegre edilecektir.
* **İş Akışı:** Kullanıcı "Bu oyun bu sistemde kaç FPS verir?" gibi bir niyet (intent) sergilediğinde, sistem bu niyeti anlayacak ve ML modeline parametreleri göndererek anlık bir tahmin üretecektir.
* **Sonuç:** Üretilen FPS tahmini, **Retrieval** katmanına ek bir veri olarak sunulacak ve **Generator** kısmında kullanıcıya çok daha kapsamlı, teknik dayanağı olan cevaplar verilecektir.



---

##  Haftalık Kazanımlar ve Teknik Çıkarımlar

Bu hafta, yazılım geliştirme yaşam döngüsünün (SDLC) tüm aşamalarında (bakım, geliştirme, inovasyon) aktif rol aldım.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Sistem Bakımı ve Adaptasyon** | Canlı sistemlerdeki UI değişikliklerini hızla analiz edip **ETL süreçlerini** yeni yapıya göre stabilize etme yeteneği. |
| **Veritabanı Odaklı Mimari** | Bot yönetimini kod seviyesinden veritabanı seviyesine taşıyarak (Dynamic Scraping), sistemi daha yönetilebilir ve ölçeklenebilir hale getirme. |
| **İleri Analitik ve ML** | RAG sistemini sadece bir veri arama motoru olmaktan çıkarıp, **Makine Öğrenmesi** modelleriyle desteklenen bir **"Tahminleme ve Karar Destek"** mekanizmasına dönüştürme. |

###  Gelecek Hafta Hedefleri

* **ML Model Deployment:** FPS tahmin modelinin tamamlanması ve Chatbot'un akışına (Pipeline) dahil edilmesi.
* **Zenginleştirilmiş RAG (Advanced RAG)** Model çıktılarını (FPS tahmini) Retrieval katmanına bir bağlam (context) olarak ekleyerek, LLM'in daha tutarlı teknik cevaplar üretmesini sağlama. 
* **Benchmark Veri Basımı:** NDJSON formatındaki pazar verilerinin veritabanına aktarılması ve ilk benchmark raporlarının alınması.
* **Bot Canlı Testleri:** UI revizesi yapılan CAPTCHA sisteminin canlı ortamda stabilite testlerinin tamamlanması.