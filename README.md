# Turkiye'nin Saglik Yuku ve Bilimsel Arastirma Oncelikleri: Veriye Dayali Bir Uyum Analizi (2020-2025)

Bu proje, Turkiye'nin en buyuk 10 hastalik yuku (DALY) ile PubMed yayinlari arasindaki oncelik farkini inceler. 250.000'den fazla makaleyi siniflandirmak icin bir NLP modeli kullanarak, bilimsel ilginin toplumsal ihtiyacla ne kadar ortustugunu analiz eder ve veriye dayali politika onerileri sunar.

---

## 1. Problem Tanimi

Bilimsel Ar-Ge kaynaklari (fonlar, akademik tesvikler) ile toplumun en acil saglik ihtiyaclari (hastalik yuku) arasinda bir uyumsuzluk olma riski bulunmaktadir. Bu calisma, bu olasi uyumsuzlugu, 2020-2025 yillari arasindaki bilimsel yayinlari analiz ederek veriye dayali olarak olcmeyi hedefler.

## 2. Metodoloji ve Proje Akisi

Proje 5 ana adimda tamamlanmistir:

### Adim 1: Hedef Veri Tespiti (Hastalik Yuku)
* **Kaynak:** IHME (Global Burden of Disease - GBD) 2019 verisi.
* **Gorev:** Turkiye icin en yuksek 10 DALY (Sakatliga Ayarlanmis Yasam Yillari) kategorisi belirlendi. Bu 10 kategori, projenin "hedef tahtasi" olarak kullanildi.

### Adim 2: Buyuk Veri Toplama (Yayinlar)
* **Kaynak:** PubMed E-utilities API.
* **Gorev:** API limitlerini (10k sorgu siniri) asmak icin "Bol ve Fethet" yontemi kullanilarak, 2020-2025 (12 Kasim) arasi "Turkiye/Turkey" adresli **~252.000** makalenin meta verisi (Ozet, MeSH) cekildi ve bir CSV dosyasina kaydedildi.

### Adim 3: NLP Model Gelistirme ve Siniflandirma
* **Metodoloji (Pivot):** Yapilan fizibilite testi, makalelerin buyuk bir bolumunde (%60+) MeSH etiketinin **eksik** oldugunu gosterdi. Bu nedenle, kural tabanli bir eslestirme yerine bir NLP Metin Siniflandirma modeli gelistirilmesine karar verildi.
* **Egitim Seti Olusturma:**
    1.  MeSH etiketi *olan* (~184k) makaleler, 10 hedef kategori veya "Diger" olarak etiketlendi.
    2.  Modele "alakasiz" makaleleri tanimayi ogretmek icin, 10 hedef kategoriden (~11.7k) ve "Diger" kategorisinden alinan rastgele bir orneklemden (~20k) olusan **11 kategorili** bir "Altin Standart" egitim seti (`df_train_11cat`) olusturuldu.
* **Model Egitimi:** Bu 31.7k'lik set kullanilarak bir TF-IDF Vectorizer ve `class_weight='balanced'` parametreli bir Logistic Regression modeli egitildi. Model, test setinde **%89 dogruluk (accuracy)** elde etti.
* **Tahmin:** Egitilen bu 11 kategorili model, etiketi olmayan ("MeSH_Yok") ~55.000 makaleyi siniflandirmak icin kullanildi.

### Adim 4: Karsilastirmali Analiz
* **Metodoloji (Pivot):** GBD verisinin karmasikligi (YLL ve YLD bilesenleri) nedeniyle yuzdesel (%) karsilastirma yerine, **"Siralama Korelasyonu" (Rank Correlation)** analizi tercih edildi.
* **Yontem:** **Liste A (DALY Siralamasi)** ile **Liste B (Yayin Sayisi Siralamasi)** yan yana getirilerek "Uyumsuzluk Skoru" (Sira Farki) hesaplandi.

### Adim 5: Gorsellestirme ve Sonuc
* Bulgular, `matplotlib` ve `seaborn` kullanilarak dagilim (scatter) ve cubuk (bar) grafikleri ile gorsellestirildi.

---

## 3. Bulgular: Uyumsuzluk Haritasi

Analiz, bilimsel arastirma odagi ile toplumsal hastalik yuku arasinda belirgin uyumsuzluklar oldugunu gostermistir:

| Kategori | DALY Siralamasi (Yuk) | Yayin Siralamasi (Odak) | Uyumsuzluk (Yuk - Odak) | Sonuc |
| :--- | :---: | :---: | :---: | :--- |
| **Iskemik Kalp Hastaligi** | **1** | 4 | **-3** | **Ihmal Sinyali** |
| **Inme (Felc)** | **2** | 5 | **-3** | **Ihmal Sinyali** |
| **Bel ve Boyun Agrilari** | **3** | 7 | **-4** | **Ihmal Sinyali** |
| **Neonatal Bozukluklar** | **4** | 8 | **-4** | **Ihmal Sinyali** |
| **KOAH** | **7** | 9 | **-2** | **Ihmal Sinyali** |
| **Bas Agrisi Bozukluklari** | **10** | 10 | **0** | Mukemmel Uyum |
| **Akciger Kanseri** | **6** | 3 | **+3** | Yuksek Odak |
| **Kronik Bobrek Hastaligi** | **9** | 6 | **+3** | Yuksek Odak |
| **Diyabet** | **5** | 1 | **+4** | Yuksek Odak |
| **Depresif Bozukluklar** | **8** | 2 | **+6** | **En Yuksek Odak** |


## 4. Politika Onerisi

Analiz, bilimsel odagin DALY yuku en yuksek alanlar (orn: Kalp Hastaliklari, Inme) yerine, DALY yuku de yuksek olan ancak daha populer (orn: Diyabet, Depresyon) alanlara kaydigini gostermektedir.

**Oneri:** TUBITAK ve Saglik Bakanligi, DALY yuku en tepede olan **Iskemik Kalp Hastaligi, Inme (Felc), Bel ve Boyun Agrilari** ve **Neonatal Bozukluklar** gibi ihmal edilmis alanlara yonelik hedefli fonlama cagrilari (targeted funding calls) acarak bu "uyumsuzluk" farkini kapatmalidir.

## 5. Kullanilan Teknolojiler

* Python
* Pandas & Numpy
* Scikit-learn (TfidfVectorizer, LogisticRegression, classification_report)
* Matplotlib & Seaborn
* PubMed E-utilities API
* Google Colab
