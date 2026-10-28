# JAWABAN UTS APP DEV (VERSI SINGKAT)

## 1. Model Layanan Cloud: IaaS, PaaS, SaaS

- **IaaS (Infrastructure as a Service):** sewa infrastruktur IT (server, storage, VM, jaringan), bayar sesuai pemakaian. Pengguna mengelola OS, runtime, aplikasi, dan data sendiri.
- **PaaS (Platform as a Service):** perluasan IaaS. Provider menyediakan platform lengkap (OS, runtime, tools build/test/deploy). Developer cukup fokus pada kode aplikasi.
- **SaaS (Software as a Service):** provider mengelola seluruh stack. Pengguna tinggal memakai aplikasi lewat internet, biasanya berlangganan.

| Aspek | IaaS | PaaS | SaaS |
|---|---|---|---|
| Yang disediakan | Server, storage, jaringan, VM | IaaS + OS, runtime, tools developer | Aplikasi siap pakai |
| Dikelola pengguna | OS, runtime, aplikasi, data | Aplikasi, data | Hanya pemakaian dan data |
| Kontrol pengguna | Paling tinggi | Sedang | Paling rendah |
| Pengguna utama | SysAdmin / DevOps | Developer | End user |
| Kelebihan | Scaling dinamis, hemat biaya, reliable | Platform lengkap, kolaborasi mudah, produktif | Langganan murah, tanpa maintenance, akses dari mana saja |
| Kekurangan | Keamanan aplikasi tanggung jawab sendiri, bergantung pada provider | Bergantung pada provider, risiko keamanan data | Kerahasiaan data, wajib ada internet |
| Contoh | AWS EC2, Azure VM | Heroku, Firebase, Google App Engine | Google Workspace, Dropbox, Gmail |

---

## 2. DevOps Lifecycle

DevOps menggabungkan Development dan Operations dalam siklus yang berulang terus (simbol ∞). Siklusnya **tidak berhenti** setelah deploy.

1. **Plan:** menentukan fitur, menyusun prioritas, membuat backlog fitur dan bug.
2. **Code:** menulis kode dan code review, disimpan di Git.
3. **Build:** mengintegrasikan kode ke codebase, lalu dikompilasi menjadi artifact.
4. **Test:** menguji artifact secara otomatis (unit test, integration test).
5. **Release:** memberi versi pada artifact yang lolos tes dan menyetujuinya untuk rilis.
6. **Deploy:** memasang ke staging dulu, lalu ke production. Kalau ada error, rollback.
7. **Operate:** menjalankan dan merawat aplikasi di production.
8. **Monitor:** memantau performa dan availability, menangani insiden, mengumpulkan feedback pengguna. Feedback masuk lagi ke tahap **Plan**.

Build, Test, dan Release adalah inti **CI/CD**: integrasi, pengujian, dan deploy berjalan otomatis. Hasilnya bug cepat ketahuan, rilis lebih cepat, dan rollback lebih mudah.

---

## 3. Static vs Dynamic

| Static | Dynamic |
|---|---|
| Konten tetap, sama untuk semua pengunjung | Konten berubah sesuai data, waktu, atau pengguna |
| Server hanya mengirim file apa adanya | Kode diproses di server (PHP), hasilnya HTML |
| Tanpa database | Pakai database (MySQL) |
| HTML, CSS, JS (JS jalan di browser) | HTML, CSS, JS + PHP + MySQL |
| Ubah konten = edit file manual | Ubah konten = ubah data di database |
| Contoh: landing page, portofolio | Contoh: e-commerce, sistem login, media sosial |

### Contoh Static (HTML + CSS + JS)

```html
<!DOCTYPE html>
<html>
<head>
  <title>Static Site</title>
  <style>
    body { font-family: Arial; background: #f4f4f4; padding: 20px; }
    h1 { color: #2c3e50; }
    button { background: #3498db; color: white; border: none; padding: 8px 14px; }
  </style>
</head>
<body>
  <h1>Daftar Buku</h1>
  <!-- Data ditulis langsung, tidak dari database -->
  <ul>
    <li>Laskar Pelangi</li>
    <li>Bumi Manusia</li>
  </ul>
  <p>Hari ini: 28/10/2026</p>
  <button onclick="sapa()">Klik</button>
  <p id="pesan"></p>

  <script>
    // JS berjalan di browser (client-side)
    function sapa() {
      document.getElementById("pesan").innerHTML = "Halo, selamat datang!";
    }
  </script>
</body>
</html>
```

### Contoh Dynamic (PHP + MySQL)

```php
<!DOCTYPE html>
<html>
<head>
  <title>Dynamic Site</title>
  <style>
    body { font-family: Arial; padding: 20px; }
  </style>
</head>
<body>
  <h1>Daftar Buku</h1>
  <!-- Tanggal dibuat server, berubah setiap kali dibuka -->
  <p>Hari ini: <?php echo date("l, d F Y"); ?></p>

  <ul>
  <?php
    // Data diambil dari database
    $conn = mysqli_connect("localhost", "root", "", "bookstore");
    $result = mysqli_query($conn, "SELECT title FROM books");
    while ($row = mysqli_fetch_assoc($result)) {
      echo "<li>" . htmlspecialchars($row['title']) . "</li>";
    }
    mysqli_close($conn);
  ?>
  </ul>
</body>
</html>
```

---

## 4. Menulis ke File `x.txt` di PHP

### Cara 1: `fopen()`, `fwrite()`, `fclose()`

```php
<?php
$file = fopen("x.txt", "w");      // buka file; dibuat jika belum ada
fwrite($file, "Hello File\n");    // tulis data
fclose($file);                    // tutup file
echo "Berhasil menulis ke x.txt";
?>
```

### Cara 2: `file_put_contents()` (satu baris)

```php
<?php
file_put_contents("x.txt", "Hello File\n");                 // menimpa isi lama
file_put_contents("x.txt", "Baris baru\n", FILE_APPEND);    // menambah di akhir
?>
```

### Mode `fopen()` yang penting

| Mode | Arti |
|---|---|
| `"w"` | Tulis; isi lama dihapus, file dibuat jika belum ada |
| `"a"` | Tambah di akhir file; isi lama tetap |
| `"r"` | Baca saja |
| `"x"` | Buat file baru; gagal jika file sudah ada |

Folder tempat `x.txt` disimpan harus punya izin **write** untuk web server, kalau tidak penulisan gagal (*Permission denied*).
