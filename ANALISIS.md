# Analisis Perbaikan Sistem

- **Nama:** Pandu Adi Utama
- **NIM:** H1H024032

## Permasalahan 1

### Gejala

Docker Compose gagal dijalankan dan menampilkan pesan:

```bash
yaml: line 5, column 8: mapping values are not allowed in this context
```

### Penyebab

Pada file `docker-compose.yml`, deklarasi service tidak menggunakan sintaks YAML yang benar.

```yaml
services
```

YAML mengharuskan setiap key diakhiri dengan tanda titik dua (`:`).

### Solusi

Mengubah konfigurasi menjadi:

```yaml
services:
```

---

## Permasalahan 2

### Gejala

Docker Compose gagal melakukan validasi volume database.

### Penyebab

Service database menggunakan volume:

```yaml
- db-data:/var/lib/mysql
```

Namun volume yang dideklarasikan adalah:

```yaml
volumes:
  database-data:
```

Terjadi ketidaksesuaian nama volume.

### Solusi

Mengubah deklarasi volume menjadi:

```yaml
volumes:
  db-data:
```

---

## Permasalahan 3

### Gejala

Container web3 gagal dibangun.

### Penyebab

Build context mengarah ke folder yang tidak tersedia.

```yaml
context: ./web33
```

Sedangkan direktori yang tersedia adalah:

```text
web3
```

### Solusi

Mengubah konfigurasi menjadi:

```yaml
context: ./web3
```

---

## Permasalahan 4

### Gejala

Build image web1 gagal dan menampilkan pesan:

```bash
php:8.2-apach: not found
```

### Penyebab

Terdapat kesalahan penulisan nama image pada Dockerfile web1.

```dockerfile
FROM php:8.2-apach
```

### Solusi

Memperbaiki konfigurasi menjadi:

```dockerfile
FROM php:8.2-apache
```

---

## Permasalahan 5

### Gejala

Build image web3 gagal.

### Penyebab

Dockerfile web3 menggunakan image yang salah.

```dockerfile
FROM php:8.2-apche
```

### Solusi

Mengubah konfigurasi menjadi:

```dockerfile
FROM php:8.2-apache
```

---

## Permasalahan 6

### Gejala

Container MySQL gagal melakukan inisialisasi database.

### Penyebab

File `init.sql` masih mengandung format Markdown.

```sql
CREATE TABLE students ...
````

Sintaks tersebut bukan bagian dari SQL sehingga tidak dapat diproses oleh MySQL.

### Solusi

Menghapus seluruh code block Markdown dan menyisakan perintah SQL saja.

```sql
CREATE TABLE students (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nim VARCHAR(20),
    nama VARCHAR(100)
);
````

---

## Permasalahan 7

### Gejala

Container Nginx gagal dijalankan.

### Penyebab

File `nginx.conf` masih mengandung syntax Markdown.

```nginx
events {}
````

Nginx membaca baris tersebut sebagai directive yang tidak valid.

### Solusi

Menghapus seluruh tanda Markdown sehingga hanya tersisa konfigurasi Nginx yang valid.

---

## Permasalahan 8

### Gejala

Nginx gagal melakukan startup dan menampilkan error:

```bash
host not found in upstream "web11"
````

### Penyebab

Nama host upstream tidak sesuai dengan nama service yang tersedia.

```nginx
server web11:80;
```

### Solusi

Mengubah konfigurasi menjadi:

```nginx
server web1:80;
```

---

## Permasalahan 9

### Gejala

Request menuju web3 selalu gagal.

### Penyebab

Port upstream tidak sesuai dengan port Apache pada container web3.

```nginx
server web3:8080;
```

Sedangkan Apache berjalan pada port 80.

### Solusi

Mengubah konfigurasi menjadi:

```nginx
server web3:80;
```

---

## Permasalahan 10

### Gejala

Web3 tidak dapat diakses melalui load balancer.

### Penyebab

Service web3 hanya terhubung ke network backend.

```yaml
networks:
  - backend
```

### Solusi

Menambahkan network frontend.

```yaml
networks:
  - frontend
  - backend
```

---

## Permasalahan 11

### Gejala

Web1 gagal melakukan koneksi ke database.

### Penyebab

Hostname database tidak sesuai dengan nama service Docker.

```yaml
DB_HOST: mysql
```

### Solusi

Mengubah konfigurasi menjadi:

```yaml
DB_HOST: db
```

---

## Permasalahan 12

### Gejala

Web2 gagal melakukan autentikasi ke database.

### Penyebab

Password database yang digunakan salah.

```yaml
DB_PASS: wrongpassword
```

### Solusi

Mengubah password menjadi:

```yaml
DB_PASS: student123
```

---

## Permasalahan 13

### Gejala

Data identitas praktikan tidak sesuai.

### Penyebab

File `init.sql` masih menggunakan placeholder.

```sql
'REPLACE_NIM',
'REPLACE_NAME'
```

### Solusi

Mengganti placeholder dengan identitas praktikan.

```sql
'H1H024032',
'Pandu Adi Utama'
```

---

## Permasalahan 14

### Gejala

Informasi nama container yang ditampilkan pada web2 dan web3 tidak sesuai.

### Penyebab

Terdapat kesalahan penulisan label container.

```html
WEB-WEB
```

dan

```html
WEB-WOB
```

### Solusi

Mengubah menjadi:

```html
WEB-2
```

dan

```html
WEB-3
```

---

## Permasalahan 15

### Gejala

Identitas praktikan ditampilkan secara statis pada setiap web server.

### Penyebab

Aplikasi menggunakan variabel lokal.

```php
$nama = "ganti ke namamu";
$nim  = "ganti ke nimmu";
```

Data tidak diambil dari database.

### Solusi

Mengimplementasikan koneksi database menggunakan mysqli.

```php
$conn = new mysqli("db", "student", "student123", "responsi");

$result = $conn->query("SELECT * FROM students LIMIT 1");
$data = $result->fetch_assoc();
```

Kemudian menampilkan data hasil query.

---

## Permasalahan 16

### Gejala

Muncul error:

```bash
Fatal error: Class "mysqli" not found
```

### Penyebab

Image PHP belum memiliki ekstensi mysqli.

### Solusi

Menambahkan konfigurasi berikut pada Dockerfile web server:

```dockerfile
RUN docker-php-ext-install mysqli
```

Kemudian melakukan rebuild image.

```bash
docker compose build --no-cache
docker compose up -d
```

---

## Permasalahan 17

### Gejala

Aplikasi menampilkan error:

```bash
Table 'responsi.students' doesn't exist
```

### Penyebab

Database menggunakan volume lama sehingga proses inisialisasi tidak dijalankan kembali.

### Solusi

Menghapus volume lama dan melakukan deployment ulang.

```bash
docker compose down -v
docker compose up --build -d
```

Setelah volume dihapus, file `init.sql` kembali dijalankan dan tabel `students` berhasil dibuat.

---

# Hasil Akhir

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/1002ed30-2524-4af8-9169-2a68148bdb89" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/f5a92457-d13a-44d0-b7a2-e43bc75e4cf7" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/bbc3eb87-813c-45a3-9cc2-389dc6af8448" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/78eb0a23-f525-49e9-b0d2-0af6244da770" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/6ee70aac-f9f5-4184-baa6-d1ab8c176e60" />

Setelah seluruh perbaikan dilakukan:

* Docker Compose berhasil dijalankan tanpa error.
* Container nginx, web1, web2, web3, dan mysql-db berjalan dengan normal.
* Nginx berhasil berfungsi sebagai load balancer.
* Request didistribusikan ke WEB-1, WEB-2, dan WEB-3 menggunakan metode Round Robin.
* Seluruh web server berhasil terhubung ke database MySQL.
* Data identitas praktikan diambil langsung dari tabel `students`.
* Sistem dapat diakses melalui `http://localhost:8080`.
