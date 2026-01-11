# Deney Sonu Teslimatı

Sistem Programlama ve Veri Yapıları bakış açısıyla veri tabanlarındaki performansı öne çıkaran hususlar nelerdir?

Aşağıda kutucuk (checkbox) ile gösterilen maddelerden en az birini seçtiğiniz açık kaynak kodlu bir VT kaynak kodları üzerinde göstererek açıklayınız. Açıklama bölümüne kısaca metninizi yazıp, kod üzerinde gösterim videonuzun linkini en altta belirtilen kutucuğa yerleştiriniz.

- [X]  Seçtiğiniz konu/konuları bu şekilde işaretleyiniz. **!**
    
---

# 1. Sistem Perspektifi (Operating System, Disk, Input/Output)

### Disk Erişimi

- [X]  **Blok bazlı disk erişimi** → block_id + offset
- [ ]  Rastgele erişim

### VT için Page (Sayfa) Anlamı

- [X]  VT hangisini kullanır? **Satır/ Sayfa** okuması

---

### Buffer Pool

- [X]  Veritabanları, Sık kullanılan sayfaları bellekte (RAM) kopyalar mı (caching) ?

- [X]  LRU / CLOCK gibi algoritmaları
- [X]  Diske yapılan I/O nasıl minimize ederler?

# 2. Veri Yapıları Perspektifi

- [X]  B+ Tree Veri Yapıları VT' lerde nasıl kullanılır?
- [ ]  VT' lerde hangi veri yapıları hangi amaçlarla kullanılır?
- [X]  Clustered vs Non-Clustered Index Kavramı
- [ ]  InnoDB satırı diskte nasıl durur?
- [ ]  LSM-tree (LevelDB, RocksDB) farkı
- [ ]  PostgreSQL heap + index ayrımı

DB diske yazarken:

- [X]  WAL (Write Ahead Log) İlkesi
- [ ]  Log disk (fsync vs write) sistem çağrıları farkı

---

# Özet Tablo

| Kavram      | Bellek             | Disk / DB         |
| ----------- | ---------------    | ----------------- |
| Veri birimi | Değişken / Nesne   | Page              |
| Adresleme   | Pointer            | Page + Offset     |
| Hız         | O(1)               | Page IO           |
| PK          | Yok                | Index anahtarı    |
| Yazma yöntem| Doğrudan güncelleme| Wal(Sıralı yazma) |
| Veri yapısı | Array / Pointer    | B+Tree            |
| Cache       | CPU cache          | Buffer Pool       |

---

# Video [Linki](https://www.youtube.com/watch?v=Nw1OvCtKPII&t=2635s) 

---

# Açıklama (Ort. 600 kelime)

Bir veritabanının performansını belirleyen en temel kısıt diskin yavaşlığıdır. İşlemci (CPU) ve bellek (RAM) çok hızlıyken, diskten veri okumak kaplumbağa hızı gibidir. Veritabanları bu yavaşlığı aşmak için "akıllıca" bazı yöntemler kullanır.

1. Sayfa (Page) ve Blok Mantığı Veritabanı, diskten veri okurken "bana sadece 5. satırı getir" demez. Bunun yerine, o satırı da içine alan büyük bir kutuyu, yani bir Sayfayı (Page) komple okur. Bu sayfalar genelde 8KB veya 16KB boyutundadır. Neden? Çünkü diske bir kere gitmişken çevredeki verileri de alıp getirmek, ileride lazım olabilecek veriler için tekrar diske gitme zahmetinden bizi kurtarır.

2. Buffer Pool (Bellek Yönetimi) Disk çok yavaş olduğu için veritabanı, diskten okuduğu sayfaları RAM üzerinde Buffer Pool denilen bir alanda saklar. Eğer aynı veri tekrar lazım olursa, diske hiç gitmeden doğrudan RAM'den verir. Ancak RAM sınırlıdır. RAM dolduğunda, "en az kullanılan" sayfaları (LRU - Least Recently Used) dışarı atar ve yerine yenilerini koyar. Bu sayede disk trafiği (I/O) minimize edilmiş olur.

3. B+ Tree ve İndeksleme Milyonlarca satır arasından bir veriyi bulmak için tüm tabloyu baştan sona taramak (Full Table Scan) felakettir. Veritabanları, verileri B+ Tree adı verilen bir ağaç yapısında tutar. Bu yapı sayesinde, aradığımız veriye sadece birkaç adımda ulaşabiliriz. Clustered Index kullandığımızda, verinin kendisi bu ağacın yapraklarında alfabetik/sayısal sırada dizili durur. Bu da aralık sorgularını (örneğin: ID'si 10 ile 50 arasındakiler) inanılmaz hızlandırır.

4. WAL (Write Ahead Log) ve Yazma Hızı Veritabanına bir şey kaydettiğinizde, veritabanı bunu hemen ana veri dosyasına gidip yazmaz. Çünkü ana dosyada yer bulup yazmak "rastgele erişim" gerektirir ve yavaştır. Bunun yerine, yapılan işlemi hemen bir Günlük (WAL) dosyasına alt alta sırayla yazar. Sıralı yazmak (Append-only) çok daha hızlıdır. Eğer sistem çökerse, bu günlüğe bakarak veriyi kurtarabilir.

Özetle; veritabanı performansı, veriyi diskte sayfa bazlı gruplamak, indeks ile hızlı bulmak ve mümkün olduğunca her şeyi RAM'de (Buffer Pool) halletmek üzerine kuruludur.

## VT Üzerinde Gösterilen Kaynak Kodları

Buffer Pool & LRU/CLOCK Algoritması [Linki](https://github.com/chen3593/PostgreSQL/blob/master/freelist.c) \
Blok Mantığı ve Sayfa Okuma (I/O Minimizasyonu) [Linki](https://github.com/postgres/postgres/blob/master/src/backend/storage/buffer/bufmgr.c) \
WAL (Write Ahead Log) İlkesi  [Linki](https://github.com/postgres/postgres/blob/master/src/backend/access/transam/xlog.c)
B+ Tree ve Index Yapısı [Linki](https://github.com/postgres/postgres/blob/master/src/backend/access/nbtree/nbtsearch.c)
Clustered vs Non-Clustered (Heap) Ayrımı [Linki](https://github.com/postgres/postgres/blob/master/src/backend/access/heap/heapam.c)
... \
...
