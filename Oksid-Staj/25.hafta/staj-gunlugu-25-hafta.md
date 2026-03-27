#  Staj Gelişim Günlüğü - 25. Hafta: Modüler Yazılım Mimarisi, Streamlit Arayüzü ve Sistem Optimizasyonu

Bu hafta, mevcut sistemlerin sürdürülebilirliğini artırmak adına kapsamlı refactoring çalışmaları yürüttüm. Operasyonel otomasyonlarımızı güncelleyip optimize ederken, finans/denetim birimleri için geliştirdiğim IBAN eşleştirme algoritmasını bir Ar-Ge betiğinden (script) çıkarıp, kendi arayüzü olan modüler ve bağımsız bir web uygulamasına dönüştürdüm. 

---

##  Odak Alan 1: n8n Otomasyon Bakımı ve Teknik Borç (Technical Debt) Yönetimi

Haftaya, arka planda sürekli çalışan rutin otomasyonlarımızın sağlık kontrolleri ve kod iyileştirmeleri (refactoring) ile başladım.

* **Sürüm Güncellemeleri:** n8n iş akışlarımızda (workflows) zamanla "Deprecated" (kullanımdan kaldırılmış veya eski sürümde kalmış) statüsüne düşen nodeları tespit ettim.
* **Modernizasyon:** Bu eski nodeları, sistemin güvenliğini ve performansını artırmak amacıyla güncel versiyonlarıyla değiştirdim. Yeni nodeların gerektirdiği parametre ve veri yapısı değişikliklerini entegre ederek otomasyonların çok daha stabil çalışmasını sağladım.

---

##  Odak Alan 2: IBAN Eşleştirme Sisteminde SOLID ve Modüler Mimari

Geçtiğimiz haftalarda veri bilimi pratikleriyle (Jupyter Notebook / `.ipynb` formatında) geliştirdiğim Regex tabanlı IBAN eşleştirme kodunu, gerçek bir yazılım ürününe dönüştürmek için mimari bir revizyona gittim.

* **Separation of Concerns (Kavramların Ayrılığı):** Kod bloğunu baştan aşağı yeniden tasarlayarak her bir işlevi (veri okuma, Regex ile filtreleme, eşleştirme, dışa aktarma) kendi sınırları içine aldım.
* **SOLID Prensipleri ve Sınıf (Class) Yapısı:** Karmaşık ve iç içe geçmiş fonksiyonları, Nesne Yönelimli Programlama (OOP) standartlarına uygun olarak sınıflara böldüm. Bu sayede kod tabanı çok daha modüler, okunabilir, test edilebilir ve gelecekteki değişikliklere (yeni banka formatları vb.) kolayca adapte edilebilir hale geldi.

---

##  Odak Alan 3: Streamlit ile Web Arayüzü (UI) Tasarımı ve Test

Modüler hale getirdiğim arka uç (Backend) kodunu, iş birimlerinin (finans/denetim) benden bağımsız olarak kullanabilmesi için interaktif bir web arayüzüne (Frontend) entegre ettim.

* **Banka Hareketleri Eşleştirme Aracı:** Python'un veri uygulamaları çerçevesi olan **Streamlit** kütüphanesini kullanarak kullanıcı dostu bir arayüz geliştirdim.
* **İşlevsellik:** Hazırlanan arayüzde, kullanıcıların banka hareketlerini içeren Excel dosyalarını sisteme yükleyebilecekleri (Drag and drop) bir alan kurguladım. Sistem, yüklenen dosyadaki IBAN'ları okuyarak arka planda Oksid ve İncehesap referans kayıtlarıyla karşılaştırıyor ve eksik firmaları tespit ediyor.
* **Lokal Testler:** Uygulamayı öncelikle lokal (yerel) ortamımda başarıyla ayağa kaldırıp testlerini tamamladım.

---

##  Odak Alan 4: Sürekli Gelişim (Continuous Learning) ve Tasarım Desenleri

Kod yazmadığım boşluk zamanlarını, profesyonel gelişimim ve şirket projelerindeki kod kalitemi artırmak için teorik altyapımı güçlendirmeye ayırdım.
* **Refactoring.guru Çalışmaları:** Yazılım mühendisliği standartlarını belirleyen `refactoring.guru` platformu üzerinden **Clean Code (Temiz Kod)** ve **Design Patterns (Tasarım Desenleri)** içeriklerini detaylıca çalıştım.
* **İçselleştirme:** Öğrendiğim teorik bilgileri, hem şirket içinde kullandığımız mimarilerle hem de kendi projelerimde kurguladığım "Adapter/Plugin", "Factory" gibi desenlerle eşleştirerek bu kavramları tamamen içselleştirdim.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Maintenance & Refactoring** | n8n üzerindeki **deprecated** nodeları güncelleyerek sistemin teknik borcunu azaltma ve stabiliteyi artırma. |
| **Yazılım Mimarisi (OOP)** | Spagetti kodu veya statik notebook dosyalarını **SOLID** ve **Separation of Concerns** prensipleriyle modüler sınıflara (Classes) ayırma. |
| **UI/UX Geliştirme** | Son kullanıcının manuel müdahalesiz işlem yapabilmesi için **Streamlit** tabanlı "Sürükle-Bırak" (Drag & Drop) özellikli web paneli tasarlama. |
| **Teorik Derinlik** | **Clean Code** standartları ve yapısal/yaratımsal **Design Patterns** (Tasarım Desenleri) konularında derinleşme. |

###  Gelecek Hafta Hedefleri

1. **Sunucu Dağıtımı (Deployment):** Lokal ortamda başarıyla çalışan Streamlit IBAN Eşleştirme uygulamasının şirket sunucusunda (Rocky Linux vb.) ayağa kaldırılması ve yayınlanması.
2. **Otomasyonun Devri:** Sunucuya alınan uygulama sayesinde, IBAN eşleştirme sürecinin benim manuel tetiklememden tamamen kurtarılıp, ilgili departmanın kendi kendine (Self-service) kullanabileceği bir yapıya kavuşturulması.
3. **Yeni Sistem İhtiyaçları:** Canlıya alınan uygulamanın kullanıcı geri bildirimlerine göre olası hata yönetimi (Error Handling) güncellemelerinin yapılması.