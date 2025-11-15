#  Staj Günlüğü - 8. Hafta: Akıllı Zamanlama, Prosedür Kararları ve RAG Prototipleme

Sekizinci staj haftası, **otomasyon zamanlaması** konusuna yepyeni bir bakış açısı getirdiğim ve **zorlu teknik engeller karşısında stratejik kararlar** alarak kaynak yönetiminde olgunluk gösterdiğim bir dönem oldu. LLM projemizin ilk demosu için RAG altyapısını hızla prototiplemek, haftanın bir diğer kritik başarısıydı.

##  Odak Alan 1: Akıllı ve Dinamik Otomasyon Zamanlama Sistemi

Finans ve diğer departmanlardan gelen otomasyon istekleri, artık statik (belirli bir gün) yerine **aylık, üç aylık veya yıllık gibi belirli periyotlara** göre çalışması gereken raporlama sistemleri talep ediyordu. Bu, standart bir zamanlayıcı kullanmanın yetersiz kalacağı anlamına geliyordu.

### 1. Statik Yerine Dinamik Başlatma Algoritması

* **Problem:** Sadece periyodu belirlemek yetmezdi; otomasyonların, resmi tatil günlerine denk gelerek ertelenmesi veya yanlış çalışması engellenmeliydi.
* **Çözüm: Google Calendar API Entegrasyonu:** Algoritmik bir çözüm düşüncesiyle, otomasyonun başlatılacağı günü dinamik olarak hesaplamak için **Google Calendar API** gücünü kullanmaya karar verdim.
* **Teknik Uygulama:**
    1.  API üzerinden Türkiye'deki **resmi tatil günlerini** çektim.
    2.  Otomasyonun, belirlenen periyodun (ay başı vb.) tatil günlerine **denk gelmeyen ilk iş gününde** bir kez tetiklenecek şekilde başlangıç mekanizmalarını (Trigger) kurguladım.

Bu mimari, otomasyonun sadece **periyodik değil, aynı zamanda bağlamsal (çalışma gününe göre)** çalışmasını sağlayarak, sistemin güvenilirliğini ve profesyonelliğini üst seviyeye taşıdı. Bu otomasyon şu anda test aşamasına hazırdır ve önümüzdeki hafta onay alması beklenmektedir.

##  Odak Alan 2: Stratejik Karar: CAPTCHA Otomasyonunun Durdurulması

* **Gelişme:** Önceki haftalarda CAPTCHA çözümü için büyük çaba harcadığım ve teknik engelleri (CRNN, Gemini entegrasyonu) aştığım otomasyon projesi, **durdurulma kararı** aldı.
* **Gerekçe:** Şirket prosedürleri ve gizlilik gereklilikleri nedeniyle, harici bir sisteme robotik süreçlerle giriş yapmaya çalışmanın **makul olmadığına** ve engelleri aşmak için daha fazla zaman kaybetmeye değmeyeceğine karar verildi.
* **Vizyon ve Kazanım:** Bu karar, teknik olarak bir çözümü başarıyla üretmiş olmama rağmen, bir mühendis olarak **kaynak yönetimi, risk analizi ve şirket prosedürlerine uyumun** her zaman teknik başarıdan önce geldiğini gösteren değerli bir ders oldu. Proje teknik olarak tamamlanmış sayıldı ve kaynaklar diğer önceliklere yönlendirildi.

##  Odak Alan 3: RAG Chatbot Demostrasyonu Prototipi

Gelecekteki büyük LLM projesinin ilk adımı olarak, yöneticime sunulmak üzere ürünlerimiz üzerine kurulu basit bir RAG Chatbot demosu geliştirdim.

* **Amaç:** RAG sisteminin mantığını ve potansiyelini görsel olarak sergilemek, daha kapsamlı sistemin ön çalışmasını yapmak.
* **Teknik Uygulama (Prototipleme):**
    1.  n8n platformu kullanılarak, sadece 10 üründen oluşan **benchmark bilgileri** ile kısıtlı bir RAG sistemi altyapısı kuruldu.
    2.  Bir demo **chat arayüzü** oluşturuldu.
    3.  **Webhook** mekanizması kullanılarak:
        * Kullanıcının chat arayüzünden girdiği **Prompt**, n8n'e iletildi.
        * n8n, RAG altyapısını kullanarak ilgili cevabı üretti.
        * Cevap, webhook üzerinden chat arayüzüne geri iletildi.
* **Durum ve İlerisi:** Bu demo, RAG sisteminin potansiyelini göstermekle birlikte, gelecekte tüm ürünlerimizi içerecek ve daha sağlam bir mimariye sahip olacak şekilde **kapsamlı bir şekilde geliştirilmeye** devam edilecektir.

---

##  Haftalık Kazanımlar, Vizyon ve İleriye Dönük Planlar

Bu hafta, teknik uzmanlığımı, kurumsal karar alma ve planlama süreçleriyle birleştirerek önemli kazanımlar elde ettim.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Dinamik Zamanlama Mimarisi** | **Google Calendar API** kullanarak **resmi tatil günlerini hariç tutan** ve periyoda göre ilk iş gününü hesaplayan, statik olmayan akıllı otomasyon başlatma sistemi kurgulama yeteneği. |
| **Stratejik Kaynak Yönetimi** | Teknik olarak çözülse bile, şirket prosedürleri ve risk analizi gereği **otomasyonu durdurma** kararı alarak, zaman ve kaynakların daha verimli projelere yönlendirilmesi pratiği. |
| **Hızlı RAG Prototipleme** | Gelecekteki büyük LLM projesi için, **n8n ve Webhook** entegrasyonu ile kısıtlı bir veri seti üzerinde çalışan, işlevsel bir **RAG Chatbot demosu**nu kısa sürede hayata geçirme becerisi. |
| **API ve Workflow Entegrasyonu** | LLM projelerinde dahi, **Webhook** yapısını kullanarak harici bir arayüz ile ana iş akışı (n8n) arasındaki iletişimi hızlıca kurma pratiği. |

###  Gelecek Hafta Hedefleri

* **LLM Başvurusu Takibi:** LLM tarafındaki gelecek projelerimizle ilgili beklenen başvuru sonuçlarının takibi.
* **Otomasyon ve Planlama:** Finans ve diğer departmanların ilettiği yeni otomasyon istekleri ve planlamaları üzerine yoğunlaşmak.
* **Akıllı Otomasyon Testi:** Geliştirilen **dinamik zamanlama otomasyonunun** saha testlerini yaparak sistemin canlıya alınması sürecini yönetmek.