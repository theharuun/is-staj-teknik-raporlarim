#  Staj Gelişim Günlüğü - 21. Hafta: Performans Optimizasyonu, ML Veri Stratejileri ve API Tabanlı Orkestrasyon

Bu hafta, büyük veri çekme operasyonlarında karşılaşılan bellek/performans darboğazlarını çözerek sistem hızını maksimize ettim. FPS tahminleme modelinde veri sızıntısını (Data Leakage) önlemek için en ideal "Split" stratejisini belirleyip eğitime başladım. Ayrıca fiyat analiz projesini API mimarisine taşıyarak n8n üzerinden yönetilebilir, zaman serisi (Time-Series) odaklı kurumsal bir yapıya dönüştürdüm.

---

##  Odak Alan 1: Web Scraping Performans Darboğazı ve Profil Yönetimi

Hafta sonu otonom çalışması için bırakılan 80.000 satırlık veri çekme işleminin beklentinin çok altında (72 saatte 17.000 veri) kaldığını tespit ettim ve sisteme köklü bir refactor işlemi uyguladım.

* **Sorun Tespiti (Memory Leak & Lazy Loading):** Otomasyonun aynı Chrome profili üzerinden binlerce URL'yi ziyaret etmesi, tarayıcı önbelleğinin (cache/profile bloat) şişmesine ve sistemin yavaşlamasına neden oldu. Bu yavaşlama, hedef sitelerdeki "Lazy Loading" mekanizmalarının tetiklenmesini bozdu ve normalde 3-4 saniye süren tek bir işlem 1-2 dakikalara kadar çıktı.
* **Mühendislik Çözümü (Profile Refreshing):** Kodu, belirli bir iterasyon sayısına (scrape adedine) ulaştıktan sonra mevcut Chrome profil dosyasını (User Data) tamamen silip, sıfırdan yeni ve temiz bir profil yaratarak kaldığı yerden devam edecek şekilde revize ettim.
* **Operasyonel Başarı:** Bu "Çöp Toplama / Profil Yenileme" stratejisi sayesinde sistem inanılmaz bir hız kazandı. Kalan devasa veri setinin tamamını (yaklaşık 40.000 kayıt) sadece **38 saat** gibi rekor bir sürede çekerek ETL sürecini başarıyla tamamladım.

---

##  Odak Alan 2: FPS Regresyon Modeli ve "Combination Split" Stratejisi

Toplanan temiz veri seti üzerinden FPS Regresyon modelini çıkartmak için çeşitli eğitim/test (Train/Test) ayırma stratejilerini denedim.

* **Strict Split Denemeleri:** İlk etapta en katı kural olan "Strict Split" (CPU ve GPU'nun test setinde tamamen benzersiz olması) denendi. Ancak model, daha önce donanım özelliklerini hiç görmediği için belirli bir seviyeden sonra öğrenmeyi durdurdu (Underfitting).
* **Combination Split Kararı:** En ideal ve gerçek dünya senaryosuna (Production Case) en uygun yöntemin **Combination Split** olduğuna karar verdim.
* *Mekanizma:* Model, eğitim veri setinde bir CPU'yu veya GPU'yu tekil olarak tanıyor; ancak test setinde karşılaştığı **"CPU + GPU Kombinasyonunu"** daha önce hiç görmemiş oluyor.
* *Sonuç:* Bu yöntem, veri sızıntısını (Data Leakage) engellerken modelin donanım bileşenleri arasındaki "sistem uyumunu" tahmin etme (sıfırdan sistem toplama simülasyonu) yeteneğini en üst düzeye çıkardı. Şu an hiperparametre optimizasyonları ve eğitim süreçleri devam etmektedir.



---

##  Odak Alan 3: Fiyat Benchmark Projesinde Zaman Serisi ve API Orkestrasyonu

Fiyat analiz sistemini, iş birimlerinin talebi doğrultusunda "30 günlük fiyat değişim trendini" gösterecek şekilde güncelledim ve sistemin çalışma mimarisini modernize ettim.

* **Zaman Serisi (Time-Series) Veritabanı Tasarımı:** Tabloya "Tarih İndeksi" ve "Fiyat İndeksi" adında iki yeni sütun ekledim. Bu sütunlar, 30 elemanlı birer dizi (Array/List) gibi çalışarak, ilgili tarihin indeksindeki güncel fiyatı eşleştiriyor. Böylece veri tabanında gereksiz satır şişmesinin önüne geçilerek kompakt bir geçmiş (history) takibi sağlandı.
* **Microservice ve API Mimarisi:** Python tabanlı veri çekme (scraping) betiklerini izole birer uç noktaya (Endpoint) dönüştürerek bir API servisi haline getirdim.
* **Güvenlik ve n8n Orkestrasyonu:** Bu API'leri `HTTPBearer` token doğrulama mekanizmasıyla güvence altına aldım. İş akışlarını yönetmek için **n8n** üzerinde `HTTP Request` modülleri kurguladım.
* **Lokal Test ve Deployment Hazırlığı:** Servisi kendi bilgisayarımda `Uvicorn` sunucusu ile ayağa kaldırıp, `ipconfig` üzerinden yerel IP adresimi tespit ederek 8501 portundan n8n üzerinden başarılı test istekleri (request) attım.

---

##  Haftalık Kazanımlar Tablosu

| Kazanım Alanı | Açıklama ve Teknik Detay |
| --- | --- |
| **Performance Engineering** | Tarayıcı profillerinin şişmesini (Profile Bloat) engelleyen **dinamik profil yenileme** algoritmasıyla scraping hızını %300+ artırma. |
| **Advanced Data Splitting** | Gerçek dünya "Sistem Toplama" senaryolarını simüle etmek için ML modelinde **Combination Split** stratejisini uygulayarak veri sızıntısını engelleme. |
| **Database Design** | Geçmiş 30 günlük fiyat verilerini tutmak için SQL tablolarında senkronize çalışan **Array/Index bazlı zaman serisi** kurgusu. |
| **API & Orkestrasyon** | Monolitik betikleri **FastAPI/Uvicorn** tabanlı güvenli (Bearer Token) uç noktalara dönüştürüp **n8n** ile otomatize etme. |

###  Gelecek Hafta Hedefleri

* **Sunucu Dağıtımı (Deployment):** Sistem yöneticileriyle (SysAdmin) koordineli çalışarak lokalde test edilen API ve n8n mimarisini şirket sunucularına taşımak ve IP konfigürasyonlarını tamamlamak.
* **Model Optimizasyonu:** FPS tahminleme modelinin hiperparametre ayarlarını (Hyperparameter Tuning) tamamlayarak doğruluk metriklerini (MAE/RMSE) son haline getirmek.
* **UI/Raporlama Geçişi:** Eğitilen modelin sonuçlarını sunacak arayüz veya raporlama altyapısının inşasına başlamak.