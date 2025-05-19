#  Materi Basis Data: Trigger vs Procedure & Transaksi SQL

##  NOmor 5. Trigger vs Procedure dalam Otomatisasi Diskon dan Promosi

###  Tujuan Pembelajaran
- Memahami konsep dasar **Trigger** dan **Stored Procedure** dalam basis data.
- Menjelaskan perbedaan mendasar antara trigger dan procedure.
- Menentukan skenario yang tepat untuk penggunaan masing-masing.
- Mengimplementasikan contoh trigger dan procedure untuk promosi dan diskon.

---

### Apa Itu Trigger?

Trigger adalah objek database yang secara otomatis dieksekusi (atau "dipicu") ketika peristiwa tertentu terjadi pada tabel, seperti `INSERT`, `UPDATE`, atau `DELETE`.

> Trigger cocok untuk automasi **langsung** yang bergantung pada aktivitas tabel.

#### Ciri-ciri:
- Dijalankan **otomatis** saat terjadi event.
- Digunakan untuk menjaga integritas data atau menambahkan logika otomatis.
- Tidak dapat dipanggil secara manual.

---

###  Apa Itu Stored Procedure?

Stored Procedure adalah kumpulan pernyataan SQL yang disimpan di dalam database dan dapat dipanggil secara eksplisit oleh pengguna atau aplikasi.

> Procedure cocok untuk proses **terstruktur** yang kompleks dan terkontrol.

#### Ciri-ciri:
- Harus **dipanggil secara eksplisit**.
- Dapat menerima parameter (input/output).
- Cocok untuk logika bisnis dan batch processing.

---

###  Perbandingan Singkat

| Aspek                       | Trigger                         | Procedure                         |
|----------------------------|----------------------------------|-----------------------------------|
| Cara Eksekusi              | Otomatis oleh DBMS               | Manual, dipanggil oleh user/aplikasi |
| Waktu Eksekusi             | Saat DML terjadi (`INSERT`, dll) | Kapan saja sesuai kebutuhan       |
| Cocok Untuk                | Automasi kecil, validasi         | Proses logika kompleks            |
| Kelebihan                  | Tanpa campur tangan user         | Fleksibel dan dapat dikontrol     |
| Kekurangan                 | Sulit dilacak/dibaca             | Harus dipanggil secara manual     |

---

###  Contoh Implementasi

#### Trigger: Otomatis Diskon 10% jika Quantity > 100

```sql
CREATE TRIGGER apply_discount
BEFORE INSERT ON OrderDetails
FOR EACH ROW
BEGIN
  IF NEW.Quantity > 100 THEN
    SET NEW.UnitPrice = NEW.UnitPrice * 0.90;
  END IF;
END;
```
#### Procedure: Terapkan Diskon Berdasarkan Tipe Promosi
```sql
CREATE PROCEDURE apply_promotion(IN order_id INT)
BEGIN
  DECLARE promo_type VARCHAR(50);
  
  SELECT PromotionType INTO promo_type
  FROM Promotions WHERE OrderID = order_id;
  
  IF promo_type = 'Buy1Get1' THEN
    -- logika untuk Buy 1 Get 1
  ELSEIF promo_type = '20Percent' THEN
    -- logika potongan 20%
  END IF;
END;
```


##  Nomor 6 – Latihan Transaksi SQL (SAVEPOINT, ROLLBACK, COMMIT)

###  Soal
Diberikan sebuah tabel `R` sederhana sebagai ilustrasi untuk memahami bagaimana mekanisme transaksi bekerja dalam SQL.

```sql
CREATE TABLE R (a INT, b INT);

INSERT INTO R VALUES (5, 6);
SAVEPOINT a;
INSERT INTO R VALUES (7, 8);
SAVEPOINT b;
INSERT INTO R VALUES (9, 10);
ROLLBACK TO b;
INSERT INTO R VALUES (11, 12);
ROLLBACK TO a;
INSERT INTO R VALUES (13, 14);
COMMIT;
```
## Penjelasan Langkah demi Langkah

1. Menambahkan baris (5, 6) ke tabel.
     
```sql
INSERT INTO R VALUES (5, 6);
```
  Tabel:

  (5, 6)

2. Menyimpan titik checkpoint dengan nama 1.

```sql
SAVEPOINT my_savepoint_1;

```
3. Menambahkan baris (7, 8).
   
```sql
INSERT INTO R VALUES (7, 8);
```
  Tabel:
  
  (5, 6)
  (7, 8)

4. Menyimpan checkpoint 2 setelah baris (7, 8) ditambahkan.

```sql
SAVEPOINT my_savepoint_2;
```

5. Menambahkan baris (9, 10).

```sql
INSERT INTO R VALUES (9, 10);
```

  Tabel:
  
  (5, 6)
  (7, 8)
  (9, 10)

6. Membatalkan perubahan setelah SAVEPOINT 1.

```sql
ROLLBACK TO my_savepoint_1;
````

7. Menambahkan baris (11, 12).

```sql
INSERT INTO R VALUES (11, 12);
```

  Tabel:
  
  (5, 6)
  (11, 12)
  ROLLBACK TO a;

8. Menyimpan perubahan secara permanen ke database.

```sql
COMMIT;
```

## Hasil Akhir Tabel R
```
(5, 6)
(11, 12)
```
## Kesimpulan

- SAVEPOINT membuat titik pemulihan dalam transaksi.

- ROLLBACK TO akan membatalkan semua perubahan setelah titik tertentu, bukan semuanya.

- COMMIT akan menyimpan perubahan yang telah dilakukan hingga titik itu secara permanen ke database.




