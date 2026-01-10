#  Staj Gelişim Günlüğü - 16. Hafta: ML Model İnference, API Güvenliği ve Endüstriyel Veri Boru Hatları

Bu hafta, staj sürecimin en yoğun teknik çıktılarından bazılarını ürettim. FPS tahminleme modelinin ilk canlı demosunu oluşturmaktan, kurumsal bir API'nin güvenlik altyapısını kurgulamaya ve uçtan uca bir fiyat analiz pipeline'ı inşa etmeye kadar geniş bir yelpazede çalıştım.

---

##  Odak Alan 1: FPS Tahminleme ML Modeli ve RAG Entegrasyonu

Haftanın ilk yarısında, elimdeki kısıtlı veri setiyle FPS tahminleme mimarisini tamamlayarak bir **Inference (Çıkarım)** modeli haline getirdim.

* **Model Performansı ve Overfitting Yönetimi:** Eğitim sürecinde modelin veriyi ezberlemesi (overfitting) riskine karşı stratejik önlemler aldım. Modelin görmediği (train ve test setinde bulunmayan) CPU, GPU teknik özellikleri ve oyun etiketleri ile yaptığım denemelerde, **MAE (Mean Absolute Error)** değerini **13** seviyesinde tutmayı başardım. Bu, uç değerlerde ufak sapmalar olsa da genel tahminlerin kaynak site verilerine oldukça yakın olduğunu kanıtladı.
* **Agentic RAG ve Tool Yeteneği:** Modeli chatbot sistemine entegre ederken, LLM'in bu modeli bir **"Tool (Araç)"** olarak kullanması gerektiğini saptadım. Kullanıcı niyetinde (intent detection) FPS ihtiyacı sezildiğinde, sistemin bu hesaplamayı tetikleyeceği bir akış kurguladım.
* **UI/UX Çözümü:** Kullanıcının serbest metin (prompt) üzerinden ürünleri veritabanı ile eşleştirirken yaşadığı belirsizlikleri (naming mismatches) gidermek adına mimariyi evrilttim: Önerilen ürünler üzerinden kullanıcıya sunulan **dinamik dropdown** seçenekleri ile kesin kombinasyonlar oluşturulmasını ve bir buton tetiklemesiyle ML modelinden anlık sonuç alınmasını sağlayan kullanıcı dostu bir formata geçiş yaptım.

---

##  Odak Alan 2: API Güvenliği, DevOps ve SOLID Yazılım Standartları

Canlıya alınacak olan CAPTCHA çözümlü bot sisteminin kurumsal ağ içerisinde güvenli ve stabil bir şekilde çalışması için sistem mühendisliği ekibiyle koordineli bir çalışma yürüttüm.

* **Bearer Token Güvenliği:** API güvenliğini sağlamak amacıyla sistem bağımlılıklarına (dependencies) **Bearer Token** kontrolü ekledim. Bu sayede, yetkisiz kullanıcıların sistemi tetiklemesi engellenerek kurumsal bir güvenlik katmanı oluşturuldu.
* **Yazılım Kalitesi (Refactoring):** Kod tabanındaki tekrarlı fonksiyonları temizleyerek **OOP (Nesne Yönelimli Programlama)** ve **SOLID** prensiplerine uygun, daha modüler ve genişletilebilir bir yapıya geçiş yaptım.
* **Konfigürasyon ve Deployment:** Sistemin ayağa kaldırılması için gerekli olan sunucu konfigürasyonlarını planladım; önümüzdeki hafta sistem mühendisi ile nihai yayına alım sürecini tamamlayacağız.

---

##  Odak Alan 3: Uçtan Uca Ürün Benchmark Pipeline Kurulumu

Fiyat ve stok takibi için hedeflenen 3 büyük e-ticaret sitesinden veri toplayan sistemi, tamamen dinamik ve veritabanı kontrollü bir hale getirdim.

* **Pipeline Mimarisi:** Botun markaları sırasıyla veritabanından alıp, siteleri sırayla dolaşarak verileri topladığı ve ardından bir sonraki siteye geçtiği tam otomatik bir akış kurdum.
* **Geniş Şemalı (Wide-Column) Veri Modeli:** Farklı sitelerden gelen farklı veri alanlarını (fields) karşılayabilmek adına, tüm olasılıkları kapsayan en geniş sütun sayısına sahip (wide-column) merkezi bir DB tablosu tasarladım.
* **NDJSON'dan DB'ye Aktarım:** Toplanan NDJSON dosyalarını sırasıyla okuyup bu merkezi tabloya aktaran kodu yazarak süreci uçtan uca tamamladım.
* **Durum Notu:** Üst yönetim tarafından izlenecek farklı stratejik yöntemler nedeniyle bu pipeline projesi şu an için askıya alınmıştır. Gelecekte ihtiyaç duyulması halinde tekrar aktif edilebilecek hazır bir altyapı olarak kütüphanemizde yerini almıştır.

---

##  Haftalık Kazanımlar ve Teknik Çıkarımlar

Bu hafta, sadece kod yazmakla kalmayıp sistem tasarımı ve güvenlik gibi kritik disiplinlerde de gelişim gösterdim.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **ML Inference ve Doğrulama** | Kısıtlı veriyle **MAE 13** başarısına ulaşan, overfitting kontrolü yapılmış ve "görülmemiş veri" üzerinde test edilmiş bir tahminleme modeli üretme. |
| **Kurumsal API Güvenliği** | Bağımlılık yönetimi (Dependency Injection) üzerinden **Bearer Token** tabanlı kimlik doğrulama mekanizması kurma. |
| **Mimari Standartlar** | Yazılımın ömrünü uzatan ve bakımını kolaylaştıran **SOLID** prensiplerini ve modüler yapıyı gerçek projeye uygulama. |
| **Etkileşimli AI Tasarımı** | LLM niyet analizi ile kullanıcı seçimlerini harmanlayan (Dropdown + Button Trigger) hibrit bir etkileşim modeli kurgulama. |
| **Veri Mühendisliği** | Farklı veri yapılarına sahip kaynaklardan gelen verileri normalize ederek **merkezi bir tabloda** toplama (ETL Pipeline). |

###  Gelecek Hafta Hedefleri

* **Model İyileştirme:** FPS tahminleme kısmındaki dropdown ve ürün eşleşme mantığının kullanıcı deneyimi açısından kusursuzlaştırılması.
* **Final Deployment:** Sistem mühendisi ile CAPTCHA botunun konfigürasyonlarının tamamlanıp canlı ağa alınması.
* **Yeni Görev Adaptasyonu:** Üst yönetimden gelecek yeni direktifler doğrultusunda diğer projelerde veya yeni araştırmalarda aktif rol alma.
