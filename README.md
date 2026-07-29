# Enterprise Sales & Performance Analytics (Qlik Sense)

Bu proje; kurumsal satış, maliyet, müşteri ve coğrafi veri setlerinin ilişkisel veri ambarı prensiplerine göre modellenmesi ve Qlik Sense üzerinde interaktif dashboard'lara dönüştürülmesi amacıyla geliştirilmiştir.

---

## 📊 Veri Modelleme ve Mimarisi

Projede, analitik sorgu performansını maksimize etmek ve bellek (RAM) tüketimini optimize etmek amacıyla **Yıldız Mimarisi (Star Schema)** kurgulanmıştır:

- **Merkezi Fact (sales) Tablosu:** Ciro, maliyet ve miktar gibi hesaplanabilir metrikleri barındırır.
- **Dimension Tabloları:** `customer`, `Itemmaster`, `SalesRep` ve `MasterCalender` tabloları merkezdeki `sales` tablosuna 1-to-Many ilişkilerle bağlanmıştır. `customer` tablosu ise lokasyon analitiği için `cities` tablosuna bağlanarak hibrit bir **Snowflake Schema** esintisi sunar.
- **Synthetic Key Optimizasyonu:** Tabloların içeri aktarılması sırasında oluşan veri şişmesini ve döngüsel ilişkileri (Circular Loops) engellemek adına, tekrarlı veri sütunları mimariden stratejik olarak silinmiş; tablolar arası bağlar tamamen tekil benzersiz anahtarlara (Unique Keys) indirgenmiştir.

---

## 🛠️ Teknik Kod Açıklamaları (ETL Processes)

Veri yükleme ve temizleme aşamasında (`qlik_load_script.txt` içinde) kullanılan temel algoritmalar:

### 1. Bölge Standardizasyonu ve Veri Temizliği
- Cities tablosunda `If(Match(...))` fonksiyonu ile `Germany`, `Spain` ve `UK` gibi farklı ülkeler tek bir `Europe` bölgesi altında normalize edilmiştir.
- Customers tablosundaki eksik/tanımsız `Region Code` verileri `If-IsNull` ile kontrol edilerek `Europe` bölgesine atanmış; 1-5 arası kodlar ise `Pick(Match(...))` yapısıyla dinamik olarak `Nordic`, `Europe`, `USA`, `Asia`, `Japan` string değerlerine dönüştürülmüştür.

### 2. Dinamik Zaman Boyutu Oluşturma (Master Calendar)
- Satış tablosundaki en küçük ve en büyük tarihler `MinDate`/`MaxDate` olarak `Peek()` fonksiyonu ile hafızaya alınmıştır.
- `AutoGenerate` ve `While` döngüsü kullanılarak bu iki tarih arasındaki tüm günler `IterNo()` sayacı ile satır satır sıfırdan üretilmiştir.
- Ham tarihler üzerinden; `Year`, `Quarter` (`Ceil(Month/3)` formülüyle), `Month`, `Week` ve `YearMonth` boyutları türetilerek zamansal bütünlük sağlanmıştır.

---

## 📂 Proje Çıktıları (Artifacts)
* 💻 ETL Kaynak Kodları: [qlik_load_script.txt](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/qlik_load_script.txt)
* 📦 Qlik Source App: [sales_performance_analysis.qvf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/sales_performance_analysis.qvf)
* 📊 Yönetici Özeti Raporu: [Dashboard.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Dashboard.pdf)
* 🛍️ Ürün Analitiği Raporu: [Product Details.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Product_Details.pdf)
* 👥 Müşteri Analitiği Raporu: [Customer Details.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Customer_Details.pdf)
* 🗺️ Coğrafi Konum Raporu: [Customer Location.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/Customer_Location.pdf)
* 📈 Veri Hikayeleştirme Raporu: [story telling.pdf](https://github.com/melikekara3/Enterprise-Sales-Performance-Analytics/blob/main/story_telling.pdf)

---

## 📦 .qvf Dosyasını Açma

GitHub, `.qvf` dosyalarını tarayıcı üzerinden önizlemeyi desteklemez (dosya boyutu ve 
format kısıtlaması nedeniyle). Uygulamayı görüntülemek için:

1. **Qlik Sense Desktop'ı indirin** (ücretsiz): 
   [https://www.qlik.com/us/trial/qlik-sense-desktop](https://www.qlik.com/us/trial/qlik-sense-desktop)
2. Yukarıdaki `sales_performance_analysis.qvf` linkine tıklayın, sağ üstteki 
   **"Download raw file"** butonuyla dosyayı bilgisayarınıza indirin.
3. Qlik Sense Desktop'ı açın → **"Open App"** → indirdiğiniz `.qvf` dosyasını seçin.
4. Uygulama açıldığında sol menüden **App Overview**, **Dashboard**, **Story** 
   sekmelerine göz atarak veri modelini ve interaktif görselleri inceleyebilirsiniz.

> 💡 Qlik Sense Desktop yalnızca Windows'ta çalışır. Mac/Linux kullanıcıları için 
> alternatif olarak yukarıdaki PDF raporları (Dashboard, Customer Details, Product 
> Details, Customer Location, Story Telling) statik önizleme sağlar.
