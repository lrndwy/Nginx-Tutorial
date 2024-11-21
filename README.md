# Nginx Symlink Configuration Guide

Tutorial ini memberikan panduan lengkap untuk mengkonfigurasi symlink di Nginx, termasuk membuat symlink untuk subdomain dan pengaturan lainnya.


## 🔗 Apa Itu Symlink di Nginx?
Symlink (symbolic link) adalah "shortcut" yang menunjuk ke file atau direktori lain. Dalam Nginx:
- File konfigurasi disimpan di `/etc/nginx/sites-available`.
- Symlink dibuat di `/etc/nginx/sites-enabled` untuk mengaktifkan konfigurasi.

---

## 📂 Struktur Direktori Nginx
- **`/etc/nginx/sites-available`**  
  Direktori tempat file konfigurasi disimpan.
- **`/etc/nginx/sites-enabled`**  
  Direktori tempat symlink dibuat untuk mengaktifkan konfigurasi di Nginx.

---

## 🛠️ Cara Membuat Konfigurasi Subdomain dengan Symlink

### 1. **Buat File Konfigurasi Subdomain**
- Navigasi ke direktori `sites-available`:
```bash
  cd /etc/nginx/sites-available
```
 
- Buat file konfigurasi untuk subdomain (contoh: `app.example.com`):

```bash
sudo nano app.example.com
```
 
- Isi file dengan konfigurasi berikut:


```nginx
server {
    listen 80;
    server_name app.example.com;

    root /var/www/app;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
 
- Simpan dan keluar (tekan `Ctrl + O`, lalu `Ctrl + X`).


---

### 2. **Buat Symlink di `sites-enabled`**  
- Buat symlink dari file di `sites-available` ke `sites-enabled`:

```bash
sudo ln -s /etc/nginx/sites-available/app.example.com /etc/nginx/sites-enabled/
```
 
- Verifikasi symlink berhasil dibuat:


```bash
ls -l /etc/nginx/sites-enabled
```

Output akan menampilkan:


```javascript
lrwxrwxrwx 1 root root 45 Nov 21 14:00 app.example.com -> /etc/nginx/sites-available/app.example.com
```


---

### 3. **Uji Konfigurasi Nginx**  
- Periksa apakah konfigurasi Nginx valid:


```bash
sudo nginx -t
```
 
- Jika valid, reload Nginx untuk menerapkan konfigurasi:


```bash
sudo systemctl reload nginx
```


---

### 4. **Akses Subdomain**  
- Pastikan direktori root (`/var/www/app`) berisi file `index.html`:

```bash
echo "<h1>Hello from app.example.com</h1>" | sudo tee /var/www/app/index.html
```
 
- Akses subdomain di browser:
`http://app.example.com`


---


## 🗑️ Cara Memutuskan Symlink 

Jika ingin menonaktifkan subdomain:
 
1. Hapus symlink di `sites-enabled`:

```bash
sudo rm /etc/nginx/sites-enabled/app.example.com
```
 
2. Reload Nginx:


```bash
sudo systemctl reload nginx
```
File konfigurasi asli di `sites-available` tidak akan terhapus.

---


## 📚 Tips Tambahan 

### Membuat SSL/TLS (HTTPS) untuk Subdomain 
Gunakan **Certbot**  untuk mendapatkan sertifikat SSL gratis:

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d app.example.com
```

### Menghapus File Konfigurasi 
Jika ingin benar-benar menghapus subdomain, hapus file konfigurasi di `sites-available`:

```bash
sudo rm /etc/nginx/sites-available/app.example.com
```


---


## ❓ FAQ 
Q: Apa bedanya file di `sites-available` dan `sites-enabled`? 
- **`sites-available`** : Tempat penyimpanan semua file konfigurasi Nginx.
 
- **`sites-enabled`** : Berisi symlink ke file konfigurasi yang aktif.

### Q: Apakah symlink memengaruhi file asli? 

Tidak, menghapus symlink tidak akan memengaruhi file asli.


---


Selamat mencoba! 🚀 Kalau ada pertanyaan, jangan ragu untuk bertanya. 😊
