# PANDUAN BELAJAR UTS - APP DEVELOPMENT

> Sumber utama: PPT Sesi 01–07 di folder `UTS_SEM5`, dengan referensi buku *Cloud Engineering for Beginners* (Adaora Nwodo, 2021) dan *PHP & MySQL: Server-side Web Development* (Jon Duckett, Wiley 2022).
> Bagian yang ditandai **[Tambahan]** tidak tertulis di PPT. Isinya materi standar untuk melengkapi jawaban kisi-kisi.

---

## 1. Learning Outcomes (LO)

- **LO1:** Explain the fundamental concepts of cloud computing, including service models and deployment types (Foundations of Cloud and Web Development).
  - Dibahas di **Sesi 01** (Introduction to Cloud Computing), **Sesi 02** (Cloud Architecture & Shared Hosting), dan **Sesi 03** (Web Hosting & cPanel Overview).
- **LO2:** Apply MySQL queries to create, read, update, and delete records in a relational database (Web Application Development with PHP & MySQL).
  - Dibahas di **Sesi 04** (PHP & Syntax Basics), **Sesi 05** (Forms & User Input), **Sesi 06** (Files & Sessions), dan **Sesi 07** (MySQL & Database Design).

### Peta Kisi-Kisi ke Materi PPT

| Soal | Topik | Sesi PPT |
|------|-------|----------|
| 1 | Model layanan cloud (SaaS, PaaS, IaaS) dan tipe deployment | Sesi 01 (Types of Cloud Computing), Sesi 02 (Cloud Services Model) |
| 2 | DevOps Lifecycle dan CI/CD | Sesi 01 (Continuous Delivery), Sesi 03 (DevOps Lifecycle) |
| 3 | Situs Static vs Dynamic | Sesi 04 (Static vs Dynamic), Sesi 07 (MySQL) |
| 4 | Penulisan file di PHP | Sesi 06 (File Handling Basics, Writing to a File) |

---

## 2. Pembahasan Rinci Kisi-Kisi UTS

---

### Soal 1: Model Layanan Cloud (SaaS, PaaS, IaaS)

#### 1.1 Konsep Dasar Cloud dan Cloud Computing

Menurut Sesi 01:

- **Cloud** adalah sekumpulan server yang dapat diakses melalui Internet. Server-server ini berada di data center di seluruh dunia dan berfungsi untuk menyimpan dan mengelola data, menjalankan aplikasi, atau melakukan komputasi berat.
- **Cloud computing** adalah ketersediaan layanan komputasi di cloud secara *on-demand* (sesuai permintaan). Bisnis tidak perlu lagi mengurus logistik server fisik dan bisa fokus pada operasi inti.
- Cloud bekerja dengan model **"pay as you go"**: bayar sesuai yang dipakai, tanpa komitmen seumur hidup. Analogi dari PPT: menyewa apartemen, bukan membeli rumah.

Tiga komponen pendukung cloud:

| Komponen | Fungsi |
|----------|--------|
| **Portals** | Portal manajemen agar administrator mudah mengelola resource aplikasi di cloud. |
| **Virtualization** | Lapisan abstraksi di atas hardware. CPU, memori, dan storage dipartisi menjadi banyak *virtual machine* (VM), sehingga satu hardware dipakai banyak pengguna. |
| **Automation** | Mengurangi atau menghilangkan intervensi manual, misalnya untuk provisioning server dan integrasi. |

**Manfaat cloud computing:** Reliability (data di-backup dan bisa dipulihkan), Scaling (mudah *scale up/out*), Continuous Delivery (pipeline deployment otomatis), Remote friendly (cukup koneksi internet stabil).

**Ancaman cloud computing:** Security threats (keamanan adalah tanggung jawab dua pihak), biaya besar untuk infrastruktur besar, dan ketergantungan pada service provider.

**Availability metric ("nines"):**

| Level | Uptime | Perkiraan downtime per tahun **[Tambahan]** |
|-------|--------|--------------------------------------------|
| Two nines | **99%** | ±3,65 hari |
| Four nines | **99,99%** | ±52,6 menit |
| Five nines (target banyak provider) | **99,999%** | ±5,26 menit |

#### 1.2 Penjelasan Mendalam Tiap Model Layanan

PPT Sesi 02 menyebut setiap model memberi **tingkat manajemen, fleksibilitas, dan kontrol** yang berbeda kepada pelanggan. Memilih model yang tepat adalah keputusan penting dalam strategi cloud bisnis.

##### a. Infrastructure as a Service (IaaS)

**Konsep:** Pengguna **menyewa infrastruktur IT** dari cloud provider dan **membayar sesuai pemakaian**. Contoh infrastruktur: storage, virtual machine, dan sistem operasi. Tim tidak perlu membeli dan merawat hardware fisik, sehingga biaya dan risiko logistik berkurang. IaaS memberi tim fleksibilitas untuk **mengelola seluruh infrastruktur cloud mereka sendiri**.

**Arsitektur (lapisan yang dikelola):**

```
+----------------------------+
| Applications               |  <- dikelola PENGGUNA
| Data                       |  <- dikelola PENGGUNA
| Runtime / Middleware       |  <- dikelola PENGGUNA
| Operating System           |  <- dikelola PENGGUNA
+----------------------------+
| Virtualization             |  <- dikelola PROVIDER
| Servers / Storage          |  <- dikelola PROVIDER
| Networking / Data Center   |  <- dikelola PROVIDER
+----------------------------+
```

**Kelebihan (PPT):**
- **Dynamic scaling:** layanan *on-demand self service*; tim bisa menambah atau mengurangi infrastruktur kapan saja dan hanya berinvestasi pada yang dibutuhkan.
- **Cost:** tidak perlu membeli kapasitas melebihi kebutuhan. Perusahaan kecil tidak harus membeli kapasitas sebesar perusahaan besar.
- **Reliability:** trafik dibagi ke beberapa server. Jika satu server mati, trafik dialihkan ke server lain.

**Kekurangan (PPT):**
- **Security breach:** provider mengamankan infrastrukturnya, tetapi tim tetap bertanggung jawab mengamankan aplikasinya. Kebocoran di sisi infrastruktur juga bisa berdampak pada data pelanggan.
- **Dependency on the service provider:** resource komputasi dan data center milik provider, sehingga timbul ketergantungan.

##### b. Platform as a Service (PaaS)

**Konsep:** PaaS adalah **perluasan dari IaaS**. Provider juga menyediakan **semua tools untuk mendukung seluruh siklus hidup aplikasi cloud**. Tim tidak perlu mengurus infrastruktur dan bisa **fokus men-deploy dan mengelola aplikasi**. Efisiensi developer meningkat karena fokus pada aplikasi, bukan pengadaan dan perawatan resource.

**Arsitektur:**

```
+----------------------------+
| Applications               |  <- dikelola PENGGUNA
| Data                       |  <- dikelola PENGGUNA
+----------------------------+
| Runtime / Middleware       |  <- dikelola PROVIDER
| Operating System           |  <- dikelola PROVIDER
| Virtualization             |  <- dikelola PROVIDER
| Servers / Storage          |  <- dikelola PROVIDER
| Networking / Data Center   |  <- dikelola PROVIDER
+----------------------------+
```

**Kelebihan (PPT):**
- **Completeness:** platform lengkap untuk build, test, dan deploy dalam satu lingkungan.
- **Collaboration:** kolaborasi lebih mudah, termasuk untuk tim yang tersebar di banyak lokasi.
- **Productivity:** developer fokus pada membangun, menguji, dan men-deploy aplikasi.

**Kekurangan (PPT):**
- **Dependency on the service provider:** sama seperti IaaS.
- **Security breach:** data pelanggan berada di cloud dan bisa bersifat rahasia. Tim wajib menerapkan langkah keamanan data dan memilih provider yang menjamin keamanan.

##### c. Software as a Service (SaaS)

**Konsep:** Provider **menjalankan dan mengelola seluruh stack**. SaaS dapat berjalan dalam **arsitektur multi-tenant** (satu instance aplikasi melayani banyak pelanggan). Pengguna tidak perlu memikirkan cara kerja internal layanan atau infrastrukturnya; yang penting **cara memakai layanannya**. Contoh dari PPT: layanan manajemen media berbasis cloud, tempat menyimpan media aplikasi web tanpa memikirkan infrastruktur penyimpanannya.

**Arsitektur:**

```
+----------------------------+
| (Konfigurasi akun & data   |  <- PENGGUNA hanya memakai
|  milik pengguna)           |
+----------------------------+
| Applications               |  <- dikelola PROVIDER
| Runtime / Middleware       |  <- dikelola PROVIDER
| Operating System           |  <- dikelola PROVIDER
| Virtualization             |  <- dikelola PROVIDER
| Servers / Storage          |  <- dikelola PROVIDER
| Networking / Data Center   |  <- dikelola PROVIDER
+----------------------------+
```

**Kelebihan (PPT):**
- **Cost:** model langganan yang bisa dibatalkan kapan saja.
- **Freedom:** pelanggan tidak perlu mengelola software; tanggung jawab sepenuhnya di provider.
- **Access:** cukup koneksi internet; semua pengguna mendapat pengalaman yang sama dan bisa berbagi data di berbagai perangkat.

**Kekurangan (PPT):**
- **Confidentiality:** menyimpan data di SaaS menimbulkan kekhawatiran keamanan. Developer harus mencegah penyerang atau tenant lain melihat data, dan risiko kebocoran tetap ada.
- **Internet connectivity:** butuh internet stabil. Tanpa internet, layanan tidak bisa dipakai dan produktivitas turun.

#### 1.3 Tabel Perbandingan Komprehensif

| Aspek | **IaaS** | **PaaS** | **SaaS** |
|-------|----------|----------|----------|
| **Definisi** | Sewa infrastruktur IT (storage, VM, OS), bayar sesuai pemakaian. | Perluasan IaaS; provider menyediakan tools untuk seluruh siklus hidup aplikasi. | Provider menjalankan dan mengelola seluruh stack; pengguna tinggal memakai software. |
| **Komponen yang disediakan** | Server, storage, jaringan, virtualisasi, VM. | Semua komponen IaaS + OS, runtime, middleware, tools build/test/deploy, database terkelola. | Semua komponen PaaS + aplikasi siap pakai. |
| **Target pengguna** | System administrator, DevOps/cloud engineer. | Software developer. | End user / bisnis. |
| **Tingkat kontrol pengguna** | **Paling tinggi.** Mengatur OS, runtime, middleware, aplikasi, data. | **Sedang.** Mengatur kode aplikasi dan data. | **Paling rendah.** Hanya mengatur pemakaian, konfigurasi akun, dan data sendiri. |
| **Fleksibilitas** | Tinggi | Sedang | Rendah |
| **Beban pengelolaan** | Tinggi | Sedang | Rendah |
| **Analogi [Tambahan]** | Menyewa tanah dan bangunan kosong, lalu mengisi sendiri. | Menyewa dapur lengkap, tinggal memasak. | Memesan makanan jadi di restoran. |
| **Kelebihan (PPT)** | Dynamic scaling, cost, reliability. | Completeness, collaboration, productivity. | Cost (langganan), freedom, access. |
| **Kekurangan (PPT)** | Security breach, dependency on provider. | Dependency on provider, security breach. | Confidentiality, internet connectivity. |
| **Contoh platform populer** | **AWS EC2**, **Microsoft Azure Virtual Machines**, Google Compute Engine. | **Heroku**, **Firebase (Google)**, Google App Engine, AWS Elastic Beanstalk. | **Google Workspace (Docs, Sheets)**, **Dropbox**, Microsoft 365, Gmail. |

> Contoh platform yang dicetak tebal berasal dari soal latihan Sesi 02. Kunci jawabannya: Google Workspace → **SaaS**, Azure Virtual Machines → **IaaS**, Heroku → **PaaS**, Dropbox → **SaaS**, AWS EC2 → **IaaS**, Firebase → **PaaS**.

#### 1.4 Shared Responsibility Model (Model Tanggung Jawab Bersama)

Sesi 02 membahas Shared Responsibility Model dengan diagram dari AWS, Azure, dan Google. Prinsipnya: **keamanan dan pengelolaan adalah tanggung jawab bersama** antara provider dan pelanggan. Sesi 01 juga menegaskan: *"On the cloud, the responsibility of enforcing security is on both parties."* Seberapa besar bagian masing-masing pihak bergantung pada model layanan.

| Lapisan | On-Premises | IaaS | PaaS | SaaS |
|---------|:-----------:|:----:|:----:|:----:|
| Data & akses pengguna | Pelanggan | Pelanggan | Pelanggan | Pelanggan |
| Aplikasi | Pelanggan | Pelanggan | Pelanggan | **Provider** |
| Runtime & middleware | Pelanggan | Pelanggan | **Provider** | **Provider** |
| Sistem operasi | Pelanggan | Pelanggan | **Provider** | **Provider** |
| Virtualisasi | Pelanggan | **Provider** | **Provider** | **Provider** |
| Server & storage | Pelanggan | **Provider** | **Provider** | **Provider** |
| Jaringan & data center fisik | Pelanggan | **Provider** | **Provider** | **Provider** |

Cara mengingat versi AWS: **provider bertanggung jawab atas keamanan "OF the cloud"** (hardware, data center, jaringan global), sedangkan **pelanggan bertanggung jawab atas keamanan "IN the cloud"** (data, identitas dan akses, konfigurasi, aplikasi). Pada model apa pun, **data dan pengaturan akses tetap tanggung jawab pelanggan**.

#### 1.5 Tipe Penggelaran (Deployment Types)

| Tipe | Penjelasan | Contoh / Cocok untuk |
|------|------------|----------------------|
| **Public Cloud** | Untuk semua orang. Provider pihak ketiga menyediakan resource (database, CPU, storage, VM) melalui internet. Bisa gratis, berlangganan, atau pay-as-you-go. Bersifat multi-tenant. | AWS, Google Cloud Platform (GCP), IBM Cloud, Microsoft Azure, Oracle Cloud. |
| **Private Cloud** | Server dan resource **khusus untuk satu pelanggan** (single tenant). Lebih aman. Bisa di data center milik organisasi sendiri atau di infrastruktur terpisah milik provider. | Organisasi dengan data sensitif: rekam medis, data keuangan. |
| **Hybrid Cloud** | **Gabungan public dan private cloud** dalam satu infrastruktur. Lebih fleksibel, lebih banyak opsi deployment, dan keamanan lebih baik. | Data sensitif di private cloud, beban publik di public cloud. |
| **Community Cloud [Tambahan]** | Infrastruktur dipakai bersama oleh **beberapa organisasi dengan kebutuhan yang sama** (misalnya regulasi atau misi yang sama). Biaya dibagi antar-anggota. | Konsorsium rumah sakit, universitas, atau bank. |

PPT Sesi 01 juga menyebut dua tipe lain:
- **Federal Cloud:** adopsi cloud untuk instansi pemerintah, dengan tujuan memperkuat keamanan siber dan meningkatkan layanan publik secara hemat biaya. Konsepnya mirip community cloud untuk sektor pemerintahan.
- **Multicloud:** memakai layanan dari **dua atau lebih provider berbeda** dalam satu aplikasi. Contoh: manajemen media di provider A, database di provider B.

---

### Soal 2: DevOps Lifecycle

#### 2.1 Pengertian

**DevOps** (*Development + Operations*) adalah budaya, praktik, dan tools yang menyatukan tim pengembang dan tim operasional. Menurut Sesi 02, DevOps Engineer membantu membangun sistem terdistribusi yang aman, tahan lama, dan berkembang cepat dalam skala besar, dengan memperkenalkan proses, tools, dan metodologi yang memudahkan tim mengembangkan, menguji, dan merawat software.

Siklus DevOps digambarkan sebagai **simbol tak hingga (∞)** karena berjalan terus-menerus. Siklus ini **tidak berhenti setelah deployment** (kunci soal latihan Sesi 03: pernyataan "The DevOps lifecycle ends after the application is deployed" bernilai **False**).

```
        Plan → Code → Build → Test
       ↗                          ↘
  Monitor                          Release
       ↖                          ↙
        Operate ← Deploy ←────────
           (DEV di kiri, OPS di kanan, berulang tanpa akhir)
```

#### 2.2 Urutan 8 Tahapan DevOps

| No | Tahap | Penjelasan | Contoh Tools **[Tambahan]** |
|----|-------|------------|-----------------------------|
| 1 | **Plan** | Menentukan fitur yang akan dibangun, memprioritaskan dan melacak tugas, serta membuat **backlog** berisi fitur bernilai dan bug yang perlu diperbaiki. | Jira, Trello, Azure Boards |
| 2 | **Code** | Developer menulis kode fitur baru berdasarkan item di backlog, lalu melakukan code review. Kode disimpan di version control. | Git, GitHub, GitLab, VS Code |
| 3 | **Build** | Kode baru **diintegrasikan** ke codebase yang ada lalu dikompilasi atau dipaketkan menjadi **artifact/executable**. | Maven, npm, Docker, GitHub Actions |
| 4 | **Test** | Artifact diuji otomatis (unit, integration, security test) untuk menemukan bug sedini mungkin. | PHPUnit, Selenium, JUnit |
| 5 | **Release** | Artifact yang lolos tes ditandai versinya dan disetujui untuk di-deploy. | Jenkins, GitLab CI, GitHub Releases |
| 6 | **Deploy** | Build output di-deploy ke lingkungan **development dan staging** terlebih dulu, baru ke **production**. Jika ada error di non-production, perubahan mudah di-**rollback** sebelum sampai ke pelanggan. | Kubernetes, Ansible, AWS CodeDeploy, cPanel |
| 7 | **Operate** | Aplikasi dijalankan dan dirawat di production: mengelola infrastruktur, konfigurasi, dan scaling. | Terraform, Kubernetes, AWS |
| 8 | **Monitor** | Memantau metrik seperti **performance** dan **availability**. Jika ada masalah, dibuat **incident** dan tim terkait memulihkan aplikasi. **Feedback** pelanggan (keluhan, permintaan fitur) dikumpulkan sebagai input untuk tahap Plan berikutnya. | Prometheus, Grafana, Datadog |

#### 2.3 Pemetaan ke Versi PPT Sesi 03

PPT Sesi 03 menyajikan siklus DevOps dalam **6 fase**. Hubungannya dengan 8 tahap di atas:

| Fase di PPT (Sesi 03) | Tahap pada model 8 tahap |
|-----------------------|--------------------------|
| **Planning** | Plan |
| **Development** (menulis, review, dan menguji kode) | Code |
| **Continuous Integration** (integrasi kode, testing, lalu menghasilkan artifact) | Build, Test, Release |
| **Deployment** (dev/staging lalu production, dengan rollback) | Deploy |
| **Monitoring** (performance, availability, incident) | Operate, Monitor |
| **Feedback** (masukan pelanggan dan permintaan fitur untuk rilis berikutnya) | Kembali ke Plan |

> Tips ujian: jika soal meminta urutan, tulis 8 tahap (Plan → Code → Build → Test → Release → Deploy → Operate → Monitor). Tambahkan bahwa Monitor menghasilkan feedback yang kembali ke Plan, sehingga siklus berulang.

#### 2.4 CI/CD: Urgensi dan Manfaat

**Pengertian:**
- **Continuous Integration (CI):** setiap perubahan kode langsung digabungkan ke codebase utama, lalu di-build dan dites otomatis. Hasilnya artifact yang siap di-deploy (Sesi 03).
- **Continuous Delivery (CD):** artifact yang lolos tes selalu siap rilis; deployment ke production biasanya butuh persetujuan manual.
- **Continuous Deployment [Tambahan]:** sama dengan Continuous Delivery, tetapi deployment ke production juga otomatis tanpa persetujuan manual.

Sesi 01 menyebut Continuous Delivery sebagai salah satu manfaat cloud: *tim dapat menyiapkan pipeline deployment otomatis sehingga setiap perubahan kecil sampai ke berbagai environment dan region tanpa build dan deploy manual.*

**Alur pipeline CI/CD:**

```
git push → [CI] Build → Unit Test → Integration Test → Artifact
        → [CD] Deploy ke Staging → Uji → Deploy ke Production → Monitor
```

**Mengapa CI/CD penting:**

| Manfaat | Penjelasan |
|---------|------------|
| **Bug ketahuan lebih awal** | Tes otomatis berjalan di setiap commit, sehingga bug ditemukan saat masih kecil dan murah diperbaiki. |
| **Rilis lebih cepat dan sering** | Perubahan kecil bisa dirilis kapan saja tanpa proses manual yang lama. |
| **Mengurangi human error** | Build dan deploy dilakukan oleh script yang sama setiap kali, bukan langkah manual. |
| **Rollback mudah** | Setiap versi tersimpan sebagai artifact, sehingga versi lama bisa dikembalikan dengan cepat jika ada masalah. |
| **Kualitas lebih konsisten** | Setiap perubahan melewati standar tes yang sama sebelum masuk production. |
| **Kolaborasi lebih baik** | Konflik kode antar-developer cepat terdeteksi karena kode sering diintegrasikan. |
| **Feedback loop cepat** | Fitur cepat sampai ke pengguna, sehingga masukan pengguna juga cepat diterima. |

**Contoh pipeline sederhana dengan GitHub Actions [Tambahan]** untuk aplikasi PHP:

```yaml
name: CI
on: [push]
jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
      - run: composer install
      - run: vendor/bin/phpunit
```

---

### Soal 3: Situs Static vs Dynamic

#### 3.1 Perbedaan Mendasar

Contoh dari PPT Sesi 04:

- **Static (`Hello_Static.php`)**: tanggal ditulis langsung (`7/10/2012`). Isinya **tidak pernah berubah** kecuali file diedit.
- **Dynamic (`Hello_Dynamic.php`)**: tanggal dihasilkan oleh `<?php echo date("l, d F Y"); ?>`. Isinya **berubah setiap kali halaman dibuka** karena diproses server.

```html
<!-- Static -->
<html>
<body>
    Hello world ! Today's date is the 7/10/2012
</body>
</html>

<!-- Dynamic -->
<html>
<body>
    Hello world ! Today's date is the <?php echo date("l, d F Y"); ?>
</body>
</html>
```

| Aspek | **Static Site** | **Dynamic Site** |
|-------|-----------------|------------------|
| **Konten** | Tetap. Semua pengunjung melihat isi yang sama. | Berubah sesuai data, waktu, atau pengguna (misalnya setelah login). |
| **Arsitektur** | Browser ↔ Web Server (hanya mengirim file). | Browser ↔ Web Server ↔ Interpreter (PHP) ↔ Database (MySQL). |
| **Pemrosesan** | Server hanya mengirim file apa adanya. Logika interaktif berjalan di **client** (JavaScript di browser). | Kode dijalankan di **server** (PHP), hasilnya HTML yang dikirim ke browser. Browser tidak melihat kode PHP. |
| **Server-side scripting** | Tidak ada. | Ada (PHP, sesuai PPT: *server-side scripting language*). |
| **Database** | Tidak memakai database. | Memakai database (MySQL) untuk menyimpan dan mengambil data. |
| **Teknologi** | HTML, CSS, JavaScript. | HTML, CSS, JS + PHP + MySQL. |
| **Ekstensi file umum** | `.html` | `.php` |
| **Kecepatan** | Lebih cepat; tidak ada pemrosesan server. | Sedikit lebih lambat karena ada eksekusi script dan query. |
| **Update konten** | Edit file HTML secara manual. | Ubah data di database atau lewat form/admin panel. |
| **Keamanan** | Celah lebih sedikit. | Perlu validasi input, sanitasi (`htmlspecialchars()`), dan prepared statement untuk mencegah SQL injection. |
| **Hosting** | Murah atau gratis (GitHub Pages, Netlify). | Butuh server yang mendukung PHP dan MySQL (XAMPP lokal, shared hosting dengan cPanel). |
| **Contoh** | Company profile, landing page, portofolio. | E-commerce, media sosial, sistem login, toko buku online. |

**Alur request:**

```
STATIC : Browser --(GET index.html)--> Web Server --(kirim file)--> Browser

DYNAMIC: Browser --(GET books.php)--> Web Server (Apache)
                                         |
                                     PHP Interpreter --(SQL query)--> MySQL
                                         |                <--(data)--
                                     (bentuk HTML)
         Browser <--(kirim HTML hasil proses)--
```

#### 3.2 Contoh Kode Static Site (HTML + CSS + JavaScript)

File: `index.html`. Bisa dibuka langsung di browser tanpa server.

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Static Site - Toko Buku</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f4f6f8; margin: 0; padding: 40px; }
        .card { max-width: 480px; margin: auto; background: #fff; padding: 24px;
                border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
        h1 { color: #2c3e50; }
        ul { padding-left: 20px; }
        button { background: #3498db; color: #fff; border: none; padding: 10px 16px;
                 border-radius: 4px; cursor: pointer; }
        button:hover { background: #2980b9; }
        #pesan { margin-top: 12px; color: #27ae60; font-weight: bold; }
    </style>
</head>
<body>
    <div class="card">
        <h1>Daftar Buku</h1>
        <!-- Data ditulis langsung (hardcoded), tidak dari database -->
        <ul>
            <li>Laskar Pelangi - Rp 85.000</li>
            <li>Bumi Manusia - Rp 95.000</li>
            <li>Negeri 5 Menara - Rp 78.000</li>
        </ul>
        <button id="btnSapa">Klik Saya</button>
        <p id="pesan"></p>
    </div>

    <script>
        // Interaksi DOM sederhana, berjalan di sisi CLIENT (browser)
        const tombol = document.getElementById('btnSapa');
        const pesan = document.getElementById('pesan');
        let jumlahKlik = 0;

        tombol.addEventListener('click', function () {
            jumlahKlik++;
            pesan.textContent = 'Terima kasih! Tombol diklik ' + jumlahKlik + ' kali.';
        });
    </script>
</body>
</html>
```

Poin penting: daftar buku **ditulis langsung** di HTML. Untuk menambah buku, file harus diedit. JavaScript hanya mengubah tampilan di browser; tidak ada data yang tersimpan di server.

#### 3.3 Contoh Kode Dynamic Site (PHP + MySQL)

Memakai desain database **bookstore** dari Sesi 07 (tabel `authors` dan `books`).

**Langkah 1: Buat database dan tabel** (jalankan di phpMyAdmin, `http://localhost/phpmyadmin`):

```sql
CREATE DATABASE bookstore;
USE bookstore;

CREATE TABLE authors (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);

CREATE TABLE books (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(150) NOT NULL,
  author_id INT,
  price DECIMAL(10,2),
  FOREIGN KEY (author_id) REFERENCES authors(id)
);

-- CREATE (insert data contoh)
INSERT INTO authors (name) VALUES ('Andrea Hirata'), ('Pramoedya Ananta Toer');
INSERT INTO books (title, author_id, price) VALUES
  ('Laskar Pelangi', 1, 85000),
  ('Sang Pemimpi', 1, 79000),
  ('Bumi Manusia', 2, 95000);
```

**Langkah 2: File `books.php`** (simpan di `C:\xampp\htdocs\bookstore\`, buka lewat `http://localhost/bookstore/books.php`):

```php
<?php
// 1. Koneksi ke database MySQL
$host = "localhost";
$user = "root";      // default XAMPP
$pass = "";          // default XAMPP: tanpa password
$db   = "bookstore";

$conn = mysqli_connect($host, $user, $pass, $db);
if (!$conn) {
    die("Koneksi gagal: " . mysqli_connect_error());
}

// 2. READ: ambil data buku beserta nama penulis (JOIN via foreign key)
$sql = "SELECT books.id, books.title, authors.name AS author, books.price
        FROM books
        JOIN authors ON books.author_id = authors.id
        ORDER BY books.title";
$result = mysqli_query($conn, $sql);
?>
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <title>Dynamic Site - Toko Buku</title>
    <style>
        body { font-family: Arial, sans-serif; padding: 40px; }
        table { border-collapse: collapse; width: 100%; max-width: 600px; }
        th, td { border: 1px solid #ccc; padding: 8px; text-align: left; }
        th { background: #2c3e50; color: #fff; }
    </style>
</head>
<body>
    <h1>Daftar Buku (dari Database)</h1>
    <p>Diakses pada: <?php echo date("l, d F Y H:i"); ?></p>

    <table>
        <tr><th>No</th><th>Judul</th><th>Penulis</th><th>Harga</th></tr>
        <?php
        // 3. Tampilkan setiap baris hasil query
        $no = 1;
        if (mysqli_num_rows($result) > 0) {
            while ($row = mysqli_fetch_assoc($result)) {
                echo "<tr>";
                echo "<td>" . $no++ . "</td>";
                echo "<td>" . htmlspecialchars($row['title']) . "</td>";
                echo "<td>" . htmlspecialchars($row['author']) . "</td>";
                echo "<td>Rp " . number_format($row['price'], 0, ',', '.') . "</td>";
                echo "</tr>";
            }
        } else {
            echo "<tr><td colspan='4'>Belum ada data buku.</td></tr>";
        }
        ?>
    </table>
</body>
</html>
<?php
// 4. Tutup koneksi
mysqli_close($conn);
?>
```

Poin penting: data diambil dari MySQL **setiap kali halaman dibuka**. Jika ada `INSERT` buku baru di database, halaman otomatis menampilkannya **tanpa mengedit file PHP**. Browser hanya menerima HTML hasil proses, bukan kode PHP-nya.

#### 3.4 Ringkasan CRUD MySQL (pendukung LO2)

| Operasi | Query SQL | Contoh |
|---------|-----------|--------|
| **Create** | `INSERT INTO` | `INSERT INTO books (title, author_id, price) VALUES ('Ayat-Ayat Cinta', 1, 90000);` |
| **Read** | `SELECT` | `SELECT * FROM books WHERE price < 90000;` |
| **Update** | `UPDATE ... SET` | `UPDATE books SET price = 80000 WHERE id = 2;` |
| **Delete** | `DELETE FROM` | `DELETE FROM books WHERE id = 3;` |

> Selalu pakai `WHERE` pada `UPDATE` dan `DELETE`. Tanpa `WHERE`, **semua baris** di tabel ikut berubah atau terhapus.

Untuk data yang berasal dari input pengguna (form `$_POST`), pakai **prepared statement** agar aman dari SQL injection:

```php
$stmt = mysqli_prepare($conn, "INSERT INTO books (title, author_id, price) VALUES (?, ?, ?)");
mysqli_stmt_bind_param($stmt, "sid", $_POST['title'], $_POST['author_id'], $_POST['price']);
mysqli_stmt_execute($stmt);
```

Konsep relasional dari Sesi 07:
- **Primary Key:** mengidentifikasi satu record secara unik (misalnya `id`).
- **Foreign Key:** menghubungkan record antar-tabel (misalnya `books.author_id` → `authors.id`).
- **Relasi:** One-to-One, One-to-Many (satu author punya banyak books), Many-to-Many (lewat tabel perantara/junction).

---

### Soal 4: Script Penulisan File di PHP (File Handling)

#### 4.1 Konsep Dasar File Handling

**File handling** adalah kemampuan PHP untuk membuat, membuka, membaca, menulis, dan menutup file di server. Kegunaannya antara lain menyimpan log, data guestbook (contoh latihan Sesi 06), konfigurasi, atau hasil export.

Fungsi dasar dari PPT Sesi 06:

| Fungsi | Kegunaan |
|--------|----------|
| `fopen($nama_file, $mode)` | Membuka file dan mengembalikan *file handle* (penunjuk file). |
| `fwrite($handle, $data)` | Menulis data ke file. Mengembalikan jumlah byte yang ditulis, atau `false` jika gagal. |
| `fread($handle, $panjang)` | Membaca sejumlah byte dari file. |
| `fclose($handle)` | Menutup file dan melepas resource. |

Fungsi tambahan yang sering dipakai **[Tambahan]**: `file_put_contents()`, `file_get_contents()`, `file_exists()`, `filesize()`, `is_writable()`, `unlink()` (menghapus file).

**Mode `fopen()`:**

| Mode | Arti | Jika file belum ada | Jika file sudah ada |
|------|------|---------------------|---------------------|
| `r` | Baca saja | Gagal | Pointer di awal file |
| `r+` | Baca dan tulis | Gagal | Pointer di awal file |
| `w` | Tulis saja | Dibuat | **Isi lama dihapus** (overwrite) |
| `w+` | Baca dan tulis | Dibuat | **Isi lama dihapus** |
| `a` | Tulis di akhir (append) | Dibuat | Isi lama tetap, data ditambah di akhir |
| `a+` | Baca dan append | Dibuat | Isi lama tetap |
| `x` | Buat file baru, tulis saja | Dibuat | **Gagal** (mencegah menimpa file) |
| `x+` | Buat file baru, baca dan tulis | Dibuat | **Gagal** |

> Jangan tertukar: `"x"` sebagai **mode** `fopen()` berbeda dengan file bernama `x.txt` pada soal.

#### 4.2 Hak Akses File (Permissions)

Di server Linux (misalnya shared hosting dengan cPanel), setiap file dan folder punya izin untuk tiga kelompok: **owner**, **group**, dan **others**. Masing-masing bisa punya izin **read (r = 4)**, **write (w = 2)**, dan **execute (x = 1)**.

| Kode | Arti | Penggunaan umum |
|------|------|-----------------|
| `644` | Owner: baca+tulis; group dan others: baca saja | File biasa (`.php`, `.html`, `.txt`) |
| `755` | Owner: semua; group dan others: baca+eksekusi | Folder |
| `600` | Hanya owner yang bisa baca+tulis | File rahasia (konfigurasi berisi password) |
| `777` | Semua orang boleh segalanya | **Hindari**; berbahaya untuk keamanan |

Hal yang perlu diperhatikan:
- PHP berjalan sebagai user web server (misalnya `www-data` atau user akun cPanel). User tersebut harus punya **izin write di folder target**, jika tidak `fopen()` atau `file_put_contents()` akan gagal dengan pesan *Permission denied*.
- Cek dengan `is_writable("folder_atau_file")` sebelum menulis.
- Izin bisa diubah lewat **File Manager cPanel** (klik kanan → *Change Permissions*), perintah `chmod 644 x.txt` di terminal, atau fungsi PHP `chmod("x.txt", 0644);`.
- Di Windows dengan XAMPP, folder `htdocs` biasanya sudah bisa ditulis, sehingga masalah permission jarang muncul saat latihan lokal.

#### 4.3 Metode Ringkas: `file_put_contents()`

`file_put_contents()` menggabungkan `fopen()`, `fwrite()`, dan `fclose()` dalam **satu baris**.

```php
<?php
$namaFile = "x.txt";
$isi = "Halo, ini data yang ditulis ke file x.txt\n";

// Menulis (menimpa isi lama). Mengembalikan jumlah byte, atau false jika gagal.
$hasil = file_put_contents($namaFile, $isi);

if ($hasil !== false) {
    echo "Berhasil menulis $hasil byte ke $namaFile";
} else {
    echo "Gagal menulis ke $namaFile. Periksa permission folder.";
}

// Menambah di akhir file tanpa menghapus isi lama (append),
// dengan LOCK_EX agar tidak bentrok jika ada dua request bersamaan.
file_put_contents($namaFile, "Baris tambahan\n", FILE_APPEND | LOCK_EX);

// Membaca kembali isinya untuk verifikasi
echo "<pre>" . htmlspecialchars(file_get_contents($namaFile)) . "</pre>";
?>
```

#### 4.4 Metode Konvensional: `fopen()`, `fwrite()`, `fclose()`

Sesuai contoh PPT Sesi 06 (`data.txt` diganti menjadi `x.txt`):

```php
<?php
$namaFile = "x.txt";

// 1. Buka file dalam mode "w" (write). File dibuat jika belum ada,
//    isi lama dihapus jika sudah ada.
$file = fopen($namaFile, "w");

// 2. Pastikan file berhasil dibuka
if ($file === false) {
    die("Tidak dapat membuka file $namaFile. Periksa permission.");
}

// 3. Tulis data ke file
fwrite($file, "Hello File\n");
fwrite($file, "Nama: Gary\n");
fwrite($file, "Tanggal: " . date("d-m-Y H:i:s") . "\n");

// 4. Tutup file untuk menyimpan perubahan dan melepas resource
fclose($file);

echo "Data berhasil ditulis ke $namaFile<br>";

// 5. Baca kembali (sesuai contoh PPT "Reading from a File")
$file = fopen($namaFile, "r");
echo nl2br(htmlspecialchars(fread($file, filesize($namaFile))));
fclose($file);
?>
```

**Versi append (menambah data tanpa menghapus isi lama):**

```php
<?php
$file = fopen("x.txt", "a");
fwrite($file, "Data baru ditambahkan pada " . date("H:i:s") . "\n");
fclose($file);
?>
```

**Contoh praktis: menyimpan input form ke `x.txt`** (gabungan Sesi 05 dan Sesi 06):

```php
<form method="post">
    Nama: <input type="text" name="nama">
    Komentar: <textarea name="komentar"></textarea>
    <input type="submit" value="Simpan">
</form>

<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    if (empty($_POST['nama']) || empty($_POST['komentar'])) {
        echo "Semua field wajib diisi.";
    } else {
        $nama = htmlspecialchars(trim($_POST['nama']));
        $komentar = htmlspecialchars(trim($_POST['komentar']));
        $baris = $nama . " : " . $komentar . "\n";

        $file = fopen("x.txt", "a");
        fwrite($file, $baris);
        fclose($file);

        echo "Komentar tersimpan.";
    }
}
?>
```

#### 4.5 Cara Kerja Penulisan Data ke `x.txt`

1. **`fopen("x.txt", "w")`**: PHP meminta sistem operasi membuka `x.txt` di folder yang sama dengan script. Jika file belum ada, file dibuat. Karena mode `"w"`, isi lama dikosongkan dan pointer berada di **awal file**. PHP mengembalikan *file handle* (`$file`), atau `false` jika gagal (misalnya tidak ada izin write).
2. **`fwrite($file, "Hello File\n")`**: string ditulis ke posisi pointer, lalu pointer maju ke akhir teks yang baru ditulis. Pemanggilan `fwrite()` berikutnya melanjutkan dari posisi tersebut. `"\n"` membuat baris baru.
3. **`fclose($file)`**: data yang masih di buffer memori dipastikan tersimpan ke disk, lalu handle dilepas. Tanpa `fclose()`, PHP biasanya menutup file otomatis di akhir script, tetapi menutup secara eksplisit adalah praktik yang benar.
4. **`file_put_contents("x.txt", $isi)`**: menjalankan langkah 1 sampai 3 sekaligus. Tambahkan flag `FILE_APPEND` untuk mode append.

**Isi `x.txt` setelah script 4.4 dijalankan:**

```
Hello File
Nama: Gary
Tanggal: 28-10-2026 14:35:00
```

**Perbandingan kedua metode:**

| Aspek | `file_put_contents()` | `fopen()` + `fwrite()` + `fclose()` |
|-------|-----------------------|-------------------------------------|
| Jumlah baris kode | 1 baris | Minimal 3 baris |
| Kemudahan | Sangat mudah | Lebih panjang |
| Kontrol | Terbatas (overwrite atau append) | Penuh (berbagai mode, banyak `fwrite()`, kontrol pointer) |
| Cocok untuk | Menulis data sekali jalan | Menulis bertahap, file besar, atau proses baris demi baris |

---

## 3. Ringkasan Cepat Sebelum Ujian

- **IaaS** = sewa infrastruktur (EC2, Azure VM); kontrol paling tinggi.
- **PaaS** = platform siap pakai untuk developer (Heroku, Firebase); fokus pada kode.
- **SaaS** = software siap pakai (Google Workspace, Dropbox); kontrol paling rendah.
- **Shared responsibility:** provider mengamankan cloud-nya; pelanggan selalu bertanggung jawab atas data dan akses.
- **Deployment:** Public (untuk umum), Private (single tenant), Hybrid (gabungan), Community (dipakai bersama beberapa organisasi). PPT juga menyebut Federal dan Multicloud.
- **DevOps:** Plan → Code → Build → Test → Release → Deploy → Operate → Monitor, lalu kembali ke Plan. Siklus **tidak berhenti** setelah deploy.
- **CI/CD:** integrasi, tes, dan deploy otomatis; bug cepat ketahuan, rilis cepat, rollback mudah.
- **Static** = konten tetap, tanpa server-side script dan database. **Dynamic** = diproses PHP di server dan mengambil data dari MySQL.
- **File PHP:** `fopen()` → `fwrite()` → `fclose()`, atau cukup `file_put_contents()`. Mode `"w"` menimpa, `"a"` menambah, `"x"` gagal jika file sudah ada. Folder harus punya izin write.

---

## 4. Catatan Sumber

- Beberapa slide di PPT berupa gambar tanpa teks (diagram Shared Responsibility Model AWS/Azure/Google di Sesi 02, diagram DevOps Lifecycle #4 di Sesi 03, serta screenshot cPanel dan instalasi XAMPP). Isi diagram tersebut tidak bisa dibaca langsung, jadi penjelasannya di panduan ini memakai versi standar dari masing-masing provider.
- **Community Cloud** tidak ada di PPT. PPT Sesi 01 menyebut Public, Private, Hybrid, Federal, dan Multicloud.
- Urutan **8 tahap DevOps** (Plan sampai Monitor) tidak tertulis persis di PPT. PPT Sesi 03 memakai 6 fase (Planning, Development, Continuous Integration, Deployment, Monitoring, Feedback); pemetaannya ada di bagian 2.3.
