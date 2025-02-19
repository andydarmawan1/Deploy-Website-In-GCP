# 🚀 Deploy Website di Google Cloud Platform (GCP)

Dokumentasi ini menjelaskan langkah-langkah untuk melakukan deploy website di Google Cloud Platform (GCP) menggunakan **Compute Engine (Virtual Machine), Nginx, dan Cloud DNS**.

## 📌 Prerequisites
Sebelum memulai, pastikan kamu sudah memiliki:
- **Akun Google Cloud** (https://cloud.google.com/)
- **Google Cloud SDK** terinstall di lokal (opsional)
- **Billing aktif** di Google Cloud
- **Domain** (jika ingin menggunakan custom domain)
  ![Uploading Screenshot 2025-02-11 at 15.09.27.png…]()
  
![alt text](https://github.com/andydarmawan1/Deploy-Website-In-GCP/blob/main/1717663591205.jpeg?raw=true)

## 📌 Langkah 1: Buat Virtual Machine (VM) di Compute Engine
1. **Buka Google Cloud Console**
2. **Navigasi ke Compute Engine** → VM Instances
3. Klik **Create Instance**
4. Isi detail:
   - Name: `my-web-server`
   - Region: Pilih lokasi yang dekat dengan user target
   - Machine type: `e2-micro` (gratis dalam Free Tier)
   - Boot disk: Pilih **Ubuntu 22.04 LTS**
   - Firewall: Centang **Allow HTTP & HTTPS traffic**
5. Klik **Create** dan tunggu hingga VM aktif

## 📌 Langkah 2: SSH ke Virtual Machine
1. Buka terminal dan jalankan perintah berikut:
   ```bash
   gcloud compute ssh my-web-server --zone=<your-zone>
   ```
   Gantilah `<your-zone>` dengan zona dari VM yang kamu buat.

2. Setelah masuk ke VM, update paket sistem:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

## 📌 Langkah 3: Install dan Konfigurasi Nginx
1. **Install Nginx**
   ```bash
   sudo apt install nginx -y
   ```
2. **Start dan enable service**
   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```
3. **Cek status Nginx**
   ```bash
   systemctl status nginx
   ```
4. **Tes akses website** dengan membuka **http://<external-ip>**
   - Dapatkan IP publik VM dengan perintah:
     ```bash
     gcloud compute instances list
     ```

## 📌 Langkah 4: Deploy Website
1. **Masuk ke direktori root Nginx**
   ```bash
   cd /var/www/html
   ```
2. **Hapus file default**
   ```bash
   sudo rm index.nginx-debian.html
   ```
3. **Upload file website**
   - Gunakan `scp` untuk mengunggah file dari lokal ke server:
     ```bash
     scp -r ./website-folder/* user@<external-ip>:/var/www/html/
     ```
   - Jika menggunakan Git:
     ```bash
     sudo apt install git -y
     git clone https://github.com/user/repo.git /var/www/html
     ```
4. **Pastikan permission benar**
   ```bash
   sudo chown -R www-data:www-data /var/www/html
   sudo chmod -R 755 /var/www/html
   ```
5. **Restart Nginx**
   ```bash
   sudo systemctl restart nginx
   ```
6. **Cek akses website** dengan membuka **http://<external-ip>**

## 📌 Langkah 5: Konfigurasi Domain (Opsional)
1. **Beli atau gunakan domain yang dimiliki**
2. **Gunakan Google Cloud DNS untuk pointing domain**
   - Buat **DNS Zone** di Cloud DNS
   - Tambahkan **A record** yang mengarah ke IP VM
3. **Tambahkan konfigurasi di Nginx**
   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```
   Ubah `server_name` menjadi domain yang digunakan:
   ```nginx
   server {
       listen 80;
       server_name example.com;
       root /var/www/html;
       index index.html;
   }
   ```
   Simpan dan restart Nginx:
   ```bash
   sudo systemctl restart nginx
   ```
4. **Akses website dengan domain baru!** 🎉

## 📌 Langkah 6: Setup HTTPS dengan Let's Encrypt (Opsional)
1. **Install Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   ```
2. **Dapatkan SSL Certificate**
   ```bash
   sudo certbot --nginx -d example.com -d www.example.com
   ```
3. **Otomatis perpanjang sertifikat**
   ```bash
   sudo certbot renew --dry-run
   ```
4. **Cek HTTPS di browser** dengan membuka **https://example.com**

## 🎯 Selesai! Website berhasil di-deploy di GCP 🚀
