# Test Scenarios & Test Cases ~ ( Test Senaryosu & Test Durumu )

## 1. Giriş

**Bugünkü Konular:**
- **Test Senaryosu (Test Scenario)**
- **Test Case (Test Durumu)**
- **Proje içinde Test Yürütme (Test Execution)** — STLC’nin (Software Testing Life Cycle) son aşamasından hemen önce gelen ve yazılım test sürecinin en kritik adımlarından biridir.

**Neden Önemli:**
- Doğru senaryo ve test case yazımı, test sürecinin **verimliliğini ve doğruluğunu** doğrudan etkiler.  
- Bu çalışmalar hem test ekibine hem de geliştiricilere **zamandan ve maliyetten tasarruf** sağlar.  
- Yayına çıkmadan önce olası hataların yakalanmasına yardımcı olur.  
- Gerçek kullanıcı deneyimini simüle ederek ürünün **son kullanıcı beklentilerini** karşıladığından emin olmamızı sağlar.

---

## 2. Test Scenario (Test Senaryosu)

**Tanım:**  
Test senaryosu, yazılımda **neyin test edileceğini** üst düzeyde tanımlar. Detaylara girmeden, ilgili fonksiyonun ya da sürecin genel kapsamını belirtir. Çoğunlukla **gereksinim dokümanları** veya kullanıcı hikâyelerinden (user stories) üretilir.

**Özellikler:**
- Kapsayıcıdır, **genel fonksiyonları** hedefler.
- Kullanıcı bakış açısına odaklanır.
- Fonksiyonelitenin sonucunu hedefler.
- **Üst düzey** bir bakış sağlar.
- Yazımı hızlıdır, bakımı kolaydır.

**Avantajlar:**
- Hızlı yazılır ve uygulanır.
- Geniş kapsam sağlar; tüm ana fonksiyonları kapsar.
- Son kullanıcı bakış açısıyla süreci simüle eder.
- Test ekipleri için kontrol listesi gibi çalışır.
- Agile (Çevik) yöntemlerde hız kazandırır.

**Dezavantajlar:**
- Detay eksikliği olabilir.
- Bazı kenar durumlar (edge cases) gözden kaçabilir.
- Deneyimli test mühendisleri gerektirir.
- Yanlış yazılırsa test kapsamı yetersiz olabilir.

**Örnek Başlıklar:**
- “Kullanıcı sisteme giriş yapabilmelidir.”
- “Sepete ürün eklenebilmelidir.”
- “Kredi kartı ile ödeme yapılabilmelidir.”

**Örnek Amaç & Ön Koşul:**
- **Amaç:** Kasada sorunsuz bir ödeme süreci sağlamak.
- **Ön Koşullar:**
  - Kullanıcı hesabına giriş yapılmış olmalı.
  - Sepette en az bir ürün bulunmalı.

**Adımlar:**
1. Sepet sayfasına git.
2. Sepet içeriğini doğrula (miktar, fiyat, varyant).
3. “Ödeme İşlemine Devam Et” butonuna tıkla.
4. Kargo bilgilerini gir veya onayla.
5. Ödeme yöntemini seç ve bilgileri gir.
6. “Satın Alma İşlemini Tamamla” butonuna tıkla.

---

## 3. Test Case (Test Durumu)

**Tanım:**  
Biyolojideki hücre gibi, yazılım testinin en küçük **temel taşıdır**. Açık ve anlaşılır olmalıdır; her test mühendisi baktığında kolayca anlamalıdır.  
Tek bir kontrol adımı üzerine yazılır. Gruplandırarak yazmak, modülerlik ve hız kazandırır. Kesin ifadeler kullanılmalıdır.

**Bileşenler (Türkçeleştirilmiş):**
- **Modül Adı (Module Name):** Test edilen fonksiyon.
- **Test Case ID:** Her test case için benzersiz kimlik.
- **Test Eden Kişi (Tester Name):** Testi gerçekleştiren kişi.
- **Test Senaryosu:** Testin kapsayacağı kısa açıklama.
- **Test Case Açıklaması:** Test edilen koşul veya özellik.
- **Test Adımları (Test Steps):** Testi çalıştırmak için yapılacak işlemler.
- **Ön Koşullar (Prerequisite):** Test öncesinde sağlanması gereken durumlar.
- **Test Önceliği (Test Priority):** Testin önem sırası.
- **Test Verisi (Test Data):** Gerekli giriş verileri.
- **Beklenen Sonuç (Test Expected Result):** Testten beklenen çıktı.
- **Gerçek Sonuç (Actual Result):** Test sonrası elde edilen çıktı.
- **Ortam Bilgileri (Environment Information):** İşletim sistemi, yazılım versiyonu vb.
- **Durum (Status):** Passed / Failed / N/A.
- **Yorumlar (Comments):** Ek notlar.

**Avantajlar:**
- Net adımlar sunar.
- Tekrarlanabilir test süreci sağlar.
- “Neyi” ve “nasıl” test ettiğimizi netleştirir.
- Ayrıntılı dokümantasyon oluşturur.
- Yazılımın doğru çalışıp çalışmadığını anlamada yardımcı olur.

**Dezavantajlar:**
- Yazması zaman alır.
- Bakım gerektirir.
- Test senaryolarına bağlıdır (senaryolar hazır olmadan oluşturulamaz).

---
---

### Örnek Ayrılmış Detaylı Test Case'ler

| **Bileşen**                   | **Değer** |
|--------------------------------|-----------|
| **Modül Adı (Module Name)**    | Kullanıcı Giriş Modülü |
| **Test Case ID**               | TC_LOGIN_01 |
| **Test Eden Kişi (Tester Name)**| Harun Korkmaz |
| **Test Senaryosu (Test Scenario)** | Kullanıcı giriş sayfasına ulaşabilmelidir. |
| **Test Case Açıklaması (Description)** | Giriş sayfasının doğru şekilde yüklenmesini doğrulama. |
| **Test Adımları (Test Steps)** | 1. Tarayıcıyı aç. <br> 2. Giriş sayfası URL’sini gir. |
| **Ön Koşullar (Prerequisite)** | Sistem çalışır durumda olmalı, internet bağlantısı olmalı. |
| **Test Önceliği (Priority)**   | Yüksek |
| **Test Verisi (Test Data)**    | URL: `https://ornekproje.com/login` |
| **Beklenen Sonuç (Expected Result)** | Giriş sayfası başarılı şekilde yüklenir ve form alanları görünür. |
| **Gerçek Sonuç (Actual Result)** | Sayfa başarıyla yüklendi. |
| **Ortam Bilgileri (Environment Info)** | Windows 11, Chrome 135, Backend API v2.3 |
| **Durum (Status)**             | Passed |
| **Yorumlar (Comments)**        | Sorun yok. |

---
---

| **Bileşen**                   | **Değer** |
|--------------------------------|-----------|
| **Modül Adı (Module Name)**    | Kullanıcı Giriş Modülü |
| **Test Case ID**               | TC_LOGIN_02 |
| **Test Eden Kişi (Tester Name)**| Harun Korkmaz |
| **Test Senaryosu (Test Scenario)** | Kullanıcı geçerli kullanıcı adını girebilmelidir. |
| **Test Case Açıklaması (Description)** | Kullanıcı adı alanına geçerli e-posta adresi girilmesini doğrulama. |
| **Test Adımları (Test Steps)** | 1. Kullanıcı adı alanına `testuser@example.com` gir. |
| **Ön Koşullar (Prerequisite)** | Giriş sayfası açık olmalı. |
| **Test Önceliği (Priority)**   | Yüksek |
| **Test Verisi (Test Data)**    | Kullanıcı Adı: `testuser@example.com` |
| **Beklenen Sonuç (Expected Result)** | Kullanıcı adı alanı, girilen değeri doğru şekilde kabul eder. |
| **Gerçek Sonuç (Actual Result)** | Alan, girilen değeri doğru şekilde kabul etti. |
| **Ortam Bilgileri (Environment Info)** | Windows 11, Chrome 135 |
| **Durum (Status)**             | Passed |
| **Yorumlar (Comments)**        | Sorun yok. |

---
---

| **Bileşen**                   | **Değer** |
|--------------------------------|-----------|
| **Modül Adı (Module Name)**    | Kullanıcı Giriş Modülü |
| **Test Case ID**               | TC_LOGIN_03 |
| **Test Eden Kişi (Tester Name)**| Harun Korkmaz |
| **Test Senaryosu (Test Scenario)** | Kullanıcı geçerli şifreyi girebilmelidir. |
| **Test Case Açıklaması (Description)** | Şifre alanına geçerli şifre girilmesini doğrulama. |
| **Test Adımları (Test Steps)** | 1. Şifre alanına `Password123!` gir. |
| **Ön Koşullar (Prerequisite)** | Giriş sayfası açık olmalı. |
| **Test Önceliği (Priority)**   | Yüksek |
| **Test Verisi (Test Data)**    | Şifre: `Password123!` |
| **Beklenen Sonuç (Expected Result)** | Şifre alanı, girilen değeri doğru şekilde kabul eder (maskelenmiş görünür). |
| **Gerçek Sonuç (Actual Result)** | Alan, girilen değeri kabul etti ve maskelendi. |
| **Ortam Bilgileri (Environment Info)** | Windows 11, Chrome 135 |
| **Durum (Status)**             | Passed |
| **Yorumlar (Comments)**        | Sorun yok. |

---
---

| **Bileşen**                   | **Değer** |
|--------------------------------|-----------|
| **Modül Adı (Module Name)**    | Kullanıcı Giriş Modülü |
| **Test Case ID**               | TC_LOGIN_04 |
| **Test Eden Kişi (Tester Name)**| Harun Korkmaz |
| **Test Senaryosu (Test Scenario)** | Kullanıcı “Giriş” butonuna tıkladığında başarılı şekilde ana sayfaya yönlendirilmelidir. |
| **Test Case Açıklaması (Description)** | Giriş butonuna tıklama sonrası doğru yönlendirmeyi doğrulama. |
| **Test Adımları (Test Steps)** | 1. “Giriş” butonuna tıkla. |
| **Ön Koşullar (Prerequisite)** | Kullanıcı adı ve şifre alanları geçerli bilgilerle doldurulmuş olmalı. |
| **Test Önceliği (Priority)**   | Yüksek |
| **Test Verisi (Test Data)**    | Kullanıcı Adı: `testuser@example.com` <br> Şifre: `Password123!` |
| **Beklenen Sonuç (Expected Result)** | Kullanıcı ana sayfaya yönlendirilir ve kullanıcı adı sağ üst köşede görünür. |
| **Gerçek Sonuç (Actual Result)** | Ana sayfaya yönlendirildi, kullanıcı adı görüntülendi. |
| **Ortam Bilgileri (Environment Info)** | Windows 11, Chrome 135, Backend API v2.3 |
| **Durum (Status)**             | Passed |
| **Yorumlar (Comments)**        | Sorun yok. |

---
---

## 4. Test Execution (Testin Yürütülmesi)

**Tanım:**  
Hazırlanan test senaryoları ve test caselerinin, **test ortamında** uygulanmasıdır. Bu aşamada ortaya çıkan **başarılı veya başarısız sonuçlar**, proje durumunu net biçimde gösterir.

**Aşamalar:**
1. **Test Ortamı Hazırlığı:** Donanım, yazılım, veritabanı, ağ ve test verileri hazırlanır.
2. **Testlerin Uygulanması:** Planlanan sırayla testler yürütülür.
3. **Sonuçların Raporlanması:** Beklenen ve gerçek sonuçlar karşılaştırılır.
4. **Hata Yönetimi:** Hatalar bildirilir, düzeltilir, yeniden test edilir (**Re-Testing**).  
   Geniş çaplı kontroller için **Regression Testing** uygulanabilir.

**Önemi:**
- Gereksinimlerle uyumu doğrular.
- Yayına hazır olup olmadığına karar vermeyi sağlar.
- Proje kalite standartlarını güvence altına alır.

---

## 5. Kapanış

**Bugünkü Öğrendiklerim:**
- Test senaryosu ile test case’in birbirini tamamlayan yapılar olduğunu gördüm.
- İyi hazırlanmış test dokümanlarının zaman, maliyet ve kalite açısından projeye değer kattığını öğrendim.
- Test yürütmenin yalnızca teknik değil, aynı zamanda **stratejik** bir süreç olduğunu fark ettim.

**İleride Sağlayacağı Katkılar:**
- Daha planlı ve organize test süreçleri.
- Gerektiğinde net, tekrar edilebilir test adımları.
- Takım içi iletişimde bilgi akışını güçlendiren, **projeye hayat veren** dokümantasyon.
