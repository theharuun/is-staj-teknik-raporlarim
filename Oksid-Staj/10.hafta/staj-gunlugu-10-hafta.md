#  Staj Gelişim Günlüğü - 10. Hafta: Hibrit RAG Mimarisi Tasarımı ve Stratejik Veri Pivotları

Bu hafta, şirketin yeni nesil AI asistanı projesi için **temel mimariyi (Sistem Beynini)** baştan sona tasarlama, detaylı bir teknik doküman hazırlama ve canlı test senaryolarını kurgulama sorumluluğunu üstlendim. Bu çalışma, projeyi sadece bir fikirden çıkarıp, net hedefleri ve aşamaları olan kurumsal bir ürüne dönüştürmüştür.

---

##  Odak Alan 1: Niyet Odaklı Hibrit RAG Mimarisi Tasarımı

Haftanın en büyük başarısı, geleneksel arama motorlarının yetersiz kaldığı **"Aptal Tezgahtar Sendromu"nu** aşan, **Niyet Odaklı Hibrit Arama (Intent-Aware Hybrid RAG)** mimarisinin tasarlanması ve detaylı teknik dokümanının hazırlanması oldu.

### 1. Felsefe ve Çift Aşamalı Beyin Yaklaşımı

* **Sorun Tanımı:** Geleneksel aramaların sadece kelime eşleşmesine dayanarak kullanıcının **soyut niyetini** (örn: "Canlı renkler", "akıcı oyun deneyimi") anlayamama sorununa odaklandım.
* **Çözüm Mimarisi:** Sistemin, basit bir sorgu motoru yerine, iki zekâyı birleştiren **"Çift Aşamalı Beyin (Dual-Stage Brain)"** yaklaşımıyla çalışması tasarlandı:
    * **SQL Kesinliği:** Fiyat aralığı, marka veya stok durumu gibi **kesin, kural tabanlı** verilerin anlık ve hatasız filtrelenmesi.
    * **Vektör Zekâsı (RAG):** Kullanıcının soyut tariflerini **matematiksel benzerlik** (kosinüs benzerliği) üzerinden değerlendirerek **semantik (anlamsal)** arama yapılması.

### 2. RAG Beyninin 4 Ana Bileşeni

Sistemin modüler monolit yapısı, dört ana bileşenin kusursuz işbirliği üzerine kuruldu:

1.  **Bağlam Yönlendiricisi (Sticky Router):** Sohbetin başlangıcında kullanıcının odaklandığı ürün kategorisini (Örn: "Monitör") anlar ve konuşma boyunca bu bağlamı korur.
2.  **Muhakeme Motoru (Reasoning Engine):** Kullanıcının soyut isteklerini (**"Daha canlı renk istiyorum"**) alarak bunları **yorumlama ve teknik gereksinimlere** (**"IPS panel gerekir"**) çevirme görevini üstlenir.
3.  **Hibrit Getirici (Hybrid Retriever):** RAG çekirdeğini oluşturur ve aramayı iki kritik aşamada yapar:
    * **Adım A – Sert Filtreleme (Hard Filter – SQL):** Bütçe ve stok gibi kesin kurallarla potansiyel ürün havuzunu hızla daraltır (Örn: 1000 üründen 50'ye).
    * **Adım B – Yumuşak Arama (Soft Search – Vector):** Kalan daraltılmış havuz içinde, **kosinüs benzerliği** kullanarak kullanıcının niyetine **en uygun** ürünleri sıralar.
4.  **Cevaplayıcı (Generator):** Teknik verileri alarak bunları doğal, gerekçelendirilmiş ve bir satış danışmanı üslubuyla kullanıcıya sunar.

### 3. Stratejik Veri Katmanları ve Kritik Pivot Kararları

Tasarım, altı katmanlı (Tier System) bir veri modelini öngördü ve bu süreçte önemli bir **stratejik karar (Pivot)** alındı:

* **Tier 1 & 2 (Temel ve Teknik Veri):** İlişkisel veritabanımızdan alınan fiyat/stok bilgisi ve scraping ile zenginleştirilen teknik özellikler (Panel tipi, sRGB).
* **Tier 3 (Rehberler):** Eğitici içerikler (Örn: "VA vs IPS Karşılaştırması") RAG bağlamını zenginleştirir.
* **Tier 4 (Performans Verisi) – Stratejik Pivot:** İlk plan, global performans sitelerinden veri çekmekti, ancak yerel ürünlerin eşleşme oranının düşük olması (%5) nedeniyle **stratejik bir pivot** yapıldı.
    * **Yeni Çözüm:** Tier 2 verisi (teknik özellikler), yerel puanlama ve fiyat bilgisi kullanılarak **özel bir algoritma** geliştirildi. Bu algoritma, otomatik olarak **"Fiyat/Performans Kralı"** veya **"Premium Segment"** gibi sınıflandırmalar üretir. Bu sayede, **sıfır maliyetle %100 kapsama** sağlandı.
* **Tier 5 & 6 (Gelecek Vizyonu):** Kullanıcı yorumları (Geliştirme aşamasında) ve satış sonrası destek için kullanılacak kullanım kılavuzları (Post-Sales Brain) planlandı.

##  Odak Alan 2: Çift Beyin (Dual Brain) Gelecek Vizyonu

Sistem, gelecekteki görevleri karşılamak üzere iki ayrı işlevsel beyne bölündü:

* **Sol Beyin (Satış Modu):** **Tier 1–5** verilerini kullanarak ürün önerme, açıklama ve karşılaştırma yapar. (Şu anki odak.)
* **Sağ Beyin (Destek Modu):** **Tier 6** verisini (kullanım kılavuzları) kullanarak **satış sonrası destek** ve sorun çözme (Örn: "Ürünün menüsü açılmıyor" sorusuna kılavuzdan çözüm sunma) görevlerini üstlenir.

### 4. Canlı Test Senaryolarının Kurgulanması

Tasarımın doğruluğunu kanıtlamak için, sistemin **Niyet Analizi, Hafıza, Filtreleme ve Yorumlama** yeteneklerini ölçen senaryolar (Kanıtlar) kurgulandı:

* **Senaryo 1 (RAG):** Soyut istekleri (Oyun performansı) Tier 4 performans verisiyle gerekçelendirme.
* **Senaryo 2 (Hibrit):** Önceki bağlamı (Oyun monitörü) unutmadan üzerine **SQL fiyat filtresi** (5000-8000 TL arası) uygulayabilme.
* **Senaryo 3 (Muhakeme):** Çapraz niyetleri ("Oyun ve kod yazmak") harmanlayarak teknik verilerle (IPS panel, çözünürlük) akıllı kıyaslama yapabilme.

---

##  Operasyonel Kazanımlar: Otomasyonların Canlıya Alınması

Bu yoğun tasarım sürecinin yanı sıra, iki adet şirket içi operasyonel otomasyonu tamamlayarak canlıya aldım:

* **İş Geliştirme Raporlama Otomasyonu:** Günde bir kez çalışacak şekilde tasarlandı. İlgili ürün ve performans verilerini işleyerek **Excel formatında** hazırlanan raporu, belirlenen yetkililere otomatik olarak gönderen bir sistem kuruldu.
* **Mevcut Otomasyon Testi:** Daha önce geliştirilen bir otomasyonun saha testlerini başarıyla tamamladım ve sonuçları ilgili birimlere ileterek sistemin stabil çalıştığını onayladım.

##  Haftalık Kazanımlar ve Çözüm Mimarlığı

Bu hafta, stajyerlik sürecinde edindiğim tüm teknik bilgileri, **kurumsal seviyede bir çözüm mimarisi** tasarlamak için kullanarak önemli bir sıçrama yaptım.

| Kazanım Alanı | Açıklama ve Teknik Detay |
| :--- | :--- |
| **Hibrit RAG Mimarisi** | SQL'in kesinliği ile Vektör aramanın zekâsını birleştiren **Çift Aşamalı Beyin** (Muhakeme Motoru, Hibrit Getirici) mimarisini uçtan uca tasarlama ve dokümante etme. |
| **Stratejik Veri Pivotu** | Düşük eşleşme oranları nedeniyle global veri kaynakları yerine, **yerel veriyi (Tier 2) kullanarak özel bir algoritma** ile **Fiyat/Performans sınıflandırması** üretme gibi kritik, maliyetsiz bir stratejik karar alma yeteneği. |
| **Çift Beyin Vizyonu** | Uygulamayı, **Satış Öncesi (Pre-Sales)** ve **Satış Sonrası (Post-Sales)** olmak üzere iki ayrı işlevsel beyne bölerek projenin gelecekteki kapsamını netleştirme. |
| **Senaryo Tabanlı Kanıt** | Geliştirilen mimarinin başarısını kanıtlamak için **niyet analizi, hafıza ve hibrit arama** gibi kritik yetenekleri ölçen detaylı test senaryoları kurgulama pratiği. |
| **Operasyonel Başarı** | **İş Geliştirme** departmanı için günde bir kez çalışan, **Excel tabanlı raporlama otomasyonunu** hızlıca canlıya alma. |