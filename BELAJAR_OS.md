# BELAJAR SISTEM OPERASI — Kisi-Kisi UTS

> Materi ini disusun dari kisi-kisi yang diberikan dan konsep standar mata kuliah **Operating Systems**. Fokusnya adalah jawaban esai, pemahaman konsep, simulasi, dan hafalan cepat.
>
> **Catatan istilah:** tulisan `FSFS` pada kisi-kisi kemungkinan besar berarti **FCFS (First-Come, First-Served)**.

---

## Daftar Isi

1. [Process Control dan Process Description](#1-process-control-dan-process-description)
2. [Thread dan Manajemen Proses](#2-thread-dan-manajemen-proses)
3. [Multiprocessor, Multicore, dan Embedded System](#3-multiprocessor-multicore-dan-embedded-system)
4. [Process Scheduling](#4-process-scheduling)
5. [Concurrency](#5-concurrency)
6. [Ringkasan Hafalan Cepat](#6-ringkasan-hafalan-cepat)
7. [Template Jawaban Esai](#7-template-jawaban-esai)
8. [Latihan Soal dan Jawaban](#8-latihan-soal-dan-jawaban)

---

# 1. Process Control dan Process Description

## 1.1 Pengertian Program dan Process

### Program

**Program** adalah kumpulan instruksi yang tersimpan secara pasif di media penyimpanan, misalnya file aplikasi di SSD. Program belum melakukan pekerjaan selama belum dijalankan.

### Process

**Process (proses)** adalah **program yang sedang dieksekusi**. Proses bersifat aktif karena memiliki keadaan eksekusi, sumber daya, dan urutan instruksi yang sedang dikerjakan.

Satu program dapat menghasilkan beberapa proses. Contohnya, membuka aplikasi kalkulator tiga kali dapat menghasilkan tiga proses terpisah dari program yang sama.

| Program | Process |
|---|---|
| Bersifat pasif | Bersifat aktif |
| Berupa file atau kumpulan instruksi | Program yang sedang dijalankan |
| Tidak memiliki state eksekusi | Memiliki state, program counter, register, dan resource |
| Tersimpan di media penyimpanan | Berada di memori dan dikelola sistem operasi |

> **Jawaban singkat untuk hafalan:** program adalah kode pasif, sedangkan proses adalah program aktif yang sedang dieksekusi beserta seluruh konteks dan sumber dayanya.

## 1.2 Elemen-Elemen Process

Ketika sebuah program dijalankan, sistem operasi membentuk **process image**, yaitu seluruh informasi yang diperlukan untuk menjalankan dan mengelola proses.

### Isi process image

| Elemen | Fungsi |
|---|---|
| **Program code / text** | Instruksi mesin yang akan dieksekusi CPU. |
| **Data section** | Variabel global dan data statis milik proses. |
| **Heap** | Memori dinamis yang dialokasikan saat program berjalan. |
| **Stack** | Menyimpan parameter fungsi, variabel lokal, alamat kembali, dan pemanggilan fungsi. |
| **Process Control Block (PCB)** | Catatan milik OS yang berisi identitas dan konteks pengelolaan proses. |

Gambaran sederhana:

```text
Alamat memori tinggi
+-------------------------+
| Stack                   |  parameter, local variable, return address
|            ↓            |
|                         |
|            ↑            |
| Heap                    |  alokasi memori dinamis
+-------------------------+
| Data                    |  global dan static variable
+-------------------------+
| Program Code / Text     |  instruksi program
+-------------------------+
Alamat memori rendah

PCB disimpan dan dilindungi oleh kernel.
```

## 1.3 Process Control Block (PCB)

**PCB** adalah struktur data milik sistem operasi yang menyimpan seluruh informasi penting tentang satu proses. PCB memungkinkan OS menghentikan proses sementara dan melanjutkannya kembali dari posisi terakhir.

Informasi utama dalam PCB:

| Informasi | Isi / Contoh |
|---|---|
| **Process Identifier (PID)** | Nomor unik proses, termasuk parent process ID bila diperlukan. |
| **Process State** | New, Ready, Running, Blocked, Exit, atau state suspend. |
| **Program Counter (PC)** | Alamat instruksi berikutnya yang akan dieksekusi. |
| **CPU Registers** | Isi register umum, stack pointer, status register, dan register CPU lainnya. |
| **Scheduling Information** | Prioritas, posisi dalam antrean, time quantum, dan parameter scheduling. |
| **Memory Management Information** | Base/limit register, page table, segment table, dan alamat ruang memori. |
| **I/O Status Information** | Perangkat yang digunakan, permintaan I/O, dan daftar file terbuka. |
| **Accounting Information** | Waktu CPU yang dipakai, pemilik proses, batas waktu, dan statistik penggunaan. |
| **IPC / Security Information** | Hak akses, sinyal, semaphore, dan hubungan komunikasi bila ada. |

### Mengapa PCB penting?

Saat terjadi **context switch**, OS menyimpan program counter, register, dan keadaan proses lama ke PCB. OS kemudian mengambil konteks proses baru dari PCB-nya. Tanpa PCB, proses tidak dapat dilanjutkan dengan benar.

## 1.4 Process Control dan Operasi pada Process

**Process control** adalah kegiatan sistem operasi untuk membuat, menjalankan, menghentikan sementara, melanjutkan, dan mengakhiri proses.

Operasi utamanya meliputi:

1. **Create:** membuat PID, PCB, address space, dan resource awal.
2. **Dispatch:** memberikan CPU kepada proses yang berada pada Ready state.
3. **Block / Wait:** menghentikan eksekusi sampai event tertentu terjadi.
4. **Wake up:** memindahkan proses dari Blocked ke Ready setelah event selesai.
5. **Preempt:** mengambil CPU dari proses, biasanya karena quantum habis atau ada proses berprioritas lebih tinggi.
6. **Suspend:** menghentikan proses untuk jangka waktu yang lebih panjang, sering disertai pemindahan dari RAM.
7. **Resume / Activate:** mengaktifkan kembali proses yang suspended.
8. **Terminate:** mengakhiri proses dan membebaskan resource-nya.

## 1.5 Model Process Dua State

Model paling sederhana hanya memiliki dua keadaan:

1. **Running:** proses sedang menggunakan CPU.
2. **Not Running:** proses tidak sedang menggunakan CPU.

```text
                  dispatch
+-------------+ -------------> +-------------+
| Not Running |                |   Running   |
+-------------+ <------------- +-------------+
                  pause
```

### Alur model dua state

- Proses baru masuk ke keadaan **Not Running**.
- Dispatcher memilih satu proses untuk masuk ke **Running**.
- Proses kembali ke Not Running jika dihentikan, menunggu sesuatu, atau jatah CPU-nya habis.

### Kekurangan model dua state

State **Not Running** mencampur dua kondisi yang berbeda:

- proses sudah siap dan hanya menunggu CPU; dan
- proses belum bisa berjalan karena menunggu I/O atau event.

Akibatnya, dispatcher dapat memeriksa proses yang sebenarnya belum siap. Kelemahan ini diperbaiki oleh model lima state.

## 1.6 Model Process Lima State

Model lima state membedakan proses yang siap menggunakan CPU dari proses yang masih menunggu event.

| State | Penjelasan |
|---|---|
| **New** | Proses sedang dibuat, tetapi belum masuk antrean Ready. |
| **Ready** | Proses sudah berada di memori dan siap berjalan, tetapi sedang menunggu CPU. |
| **Running** | Instruksi proses sedang dieksekusi CPU. Pada satu core, hanya satu proses dapat Running pada satu waktu. |
| **Blocked / Waiting** | Proses tidak dapat berjalan sampai event terjadi, misalnya operasi I/O selesai. |
| **Exit / Terminated** | Eksekusi proses selesai atau dihentikan; OS membersihkan resource-nya. |

### Diagram transisi lima state

```text
                 admit                 dispatch
+-----+ ------------------> +-------+ -----------> +---------+
| New |                     | Ready |              | Running |
+-----+                     +-------+ <----------- +---------+
                               ^       timeout /       |   |
                               |       preemption      |   | release
                               |                       |   v
                               | event occurs          | +------+
                               |                       | | Exit |
                            +---------+ <--------------+ +------+
                            | Blocked |   event wait
                            +---------+
```

### Penjelasan setiap transisi

| Transisi | Penyebab |
|---|---|
| **New → Ready** | OS menerima atau mengadmit proses ke memori. |
| **Ready → Running** | Short-term scheduler memilih proses; dispatcher memberi CPU. |
| **Running → Ready** | Quantum habis, proses di-preempt, atau proses berprioritas lebih tinggi datang. |
| **Running → Blocked** | Proses meminta I/O, menunggu lock, atau menunggu event. |
| **Blocked → Ready** | I/O atau event yang ditunggu sudah selesai. Proses belum langsung Running karena harus menunggu CPU. |
| **Running → Exit** | Proses selesai normal, mengalami error fatal, atau dihentikan. |

> **Poin penting:** proses yang selesai menunggu I/O berpindah dari **Blocked ke Ready**, bukan langsung ke Running.

## 1.7 Perbedaan Blocked dan Suspended

Keduanya sama-sama tidak sedang memakai CPU, tetapi penyebab dan kondisi memorinya berbeda.

| Blocked | Suspended |
|---|---|
| Menunggu event, I/O, atau resource. | Dihentikan secara sengaja oleh OS, user, atau parent process. |
| Biasanya masih berada di RAM. | Dapat dipindahkan dari RAM ke secondary storage melalui swapping. |
| Event yang selesai membuatnya Ready. | Event selesai belum cukup; proses juga harus diaktifkan kembali. |
| Tidak dapat berjalan walau diberi CPU sebelum event selesai. | Dapat saja sudah siap secara logis, tetapi dilarang berjalan sampai di-resume. |

> **Kalimat hafalan:** blocked menunggu kejadian; suspended menunggu keputusan untuk diaktifkan kembali.

## 1.8 Dukungan Satu Suspend State

Jika memori penuh, OS dapat memindahkan proses ke disk. Model awal dapat menambahkan satu state umum bernama **Suspend**.

Masalahnya, satu Suspend state masih mencampur dua kondisi:

1. proses yang sebenarnya siap tetapi dikeluarkan dari RAM; dan
2. proses yang selain dikeluarkan dari RAM juga masih menunggu event.

Karena kebutuhan keduanya berbeda, model yang lebih jelas menggunakan **dua suspend state**.

## 1.9 Dua Suspend State: Model Tujuh State

Dua state tambahan adalah:

| Suspend State | Penjelasan |
|---|---|
| **Ready/Suspend** | Proses siap dieksekusi, tetapi sedang suspended dan biasanya tidak berada di RAM. Agar berjalan, proses harus dibawa kembali ke RAM. |
| **Blocked/Suspend** | Proses sedang suspended dan masih menunggu event. Walaupun dikembalikan ke RAM, proses belum dapat berjalan sebelum event selesai. |

Model ini sering disebut **seven-state process model** karena berisi New, Ready, Running, Blocked, Exit, Ready/Suspend, dan Blocked/Suspend.

```text
Alur utama:
New --admit--> Ready --dispatch--> Running --release--> Exit
                 ^                    |
                 |                    | timeout / preemption
                 +--------------------+
                 ^                    |
                 | event occurs       | event wait
                 |                    v
              Blocked <---------------+

Jalur suspend:
Ready   --suspend--> Ready/Suspend   --activate--> Ready
Blocked --suspend--> Blocked/Suspend --activate--> Blocked
Running --suspend--> Ready/Suspend

Blocked/Suspend --event occurs--> Ready/Suspend
```

### Transisi penting pada dua suspend state

| Transisi | Arti |
|---|---|
| **Ready → Ready/Suspend** | Proses siap dipindahkan keluar dari memori. |
| **Blocked → Blocked/Suspend** | Proses yang menunggu event dipindahkan keluar dari memori. Ini sering dipilih karena proses memang belum bisa berjalan. |
| **Blocked/Suspend → Ready/Suspend** | Event selesai ketika proses masih berada di luar memori. Proses kini siap secara logis, tetapi masih suspended. |
| **Ready/Suspend → Ready** | OS mengaktifkan dan membawa proses kembali ke RAM. |
| **Blocked/Suspend → Blocked** | Proses dibawa kembali ke RAM, tetapi event yang ditunggu belum selesai. |
| **Running → Ready/Suspend** | OS dapat langsung menangguhkan proses yang sedang berjalan pada kondisi tertentu. |

### Alasan terjadinya suspend

| Alasan | Penjelasan |
|---|---|
| **Swapping / kekurangan memori** | OS mengosongkan RAM agar proses lain dapat masuk atau untuk mengurangi thrashing. |
| **Keputusan sistem operasi** | OS mencurigai proses menyebabkan masalah, memakai resource berlebihan, atau perlu menjaga kestabilan sistem. |
| **Permintaan user interaktif** | User melakukan pause, debugging, atau ingin menghentikan sementara aplikasi. |
| **Timing** | Proses periodik dihentikan sampai waktu eksekusi berikutnya. |
| **Permintaan parent process** | Parent menangguhkan child untuk memeriksa, mengoordinasikan, atau mengubah aktivitasnya. |
| **Alasan keamanan atau administrasi** | Proses dihentikan sementara karena kebijakan akses, maintenance, atau investigasi. |

### Contoh alur suspend

1. Proses A sedang menunggu data dari disk sehingga state-nya **Blocked**.
2. RAM hampir penuh. OS memindahkan A ke disk sehingga menjadi **Blocked/Suspend**.
3. Operasi disk selesai saat A masih di luar RAM. State A menjadi **Ready/Suspend**.
4. Ketika tersedia ruang RAM, OS mengaktifkan A menjadi **Ready**.
5. Short-term scheduler kemudian dapat memilih A menjadi **Running**.

---

# 2. Thread dan Manajemen Proses

## 2.1 Pengertian Thread

**Thread** adalah unit eksekusi terkecil yang dapat dijadwalkan di dalam sebuah proses. Satu proses minimal memiliki satu thread.

Dua peran penting:

- **Process** adalah unit kepemilikan resource dan perlindungan.
- **Thread** adalah unit eksekusi atau dispatch yang menggunakan resource milik proses.

Dengan kata lain, proses menyediakan tempat dan resource, sedangkan thread melakukan pekerjaan di dalam tempat tersebut.

## 2.2 Elemen Thread

Setiap thread memiliki konteks eksekusinya sendiri:

- Thread ID.
- Program counter.
- CPU registers.
- Stack.
- State thread, misalnya Ready, Running, atau Blocked.
- Informasi scheduling dan Thread Control Block (TCB).
- Thread-local storage bila digunakan.

Thread-thread dalam satu proses berbagi:

- program code;
- data global;
- heap dan address space;
- file yang terbuka;
- socket dan resource proses lainnya.

```text
SATU PROCESS
+------------------------------------------------------+
| Resource yang dibagi: code, data, heap, files, socket|
|                                                      |
| +---------------+ +---------------+ +---------------+|
| | Thread 1      | | Thread 2      | | Thread 3      ||
| | PC, register  | | PC, register  | | PC, register  ||
| | stack sendiri | | stack sendiri | | stack sendiri ||
| +---------------+ +---------------+ +---------------+|
+------------------------------------------------------+
```

> **Hafalan:** thread berbagi **code, data, heap, dan files**, tetapi setiap thread memiliki **program counter, register, dan stack sendiri**.

## 2.3 Keterkaitan Process dan Thread

| Aspek | Process | Thread |
|---|---|---|
| Pengertian | Program yang sedang berjalan beserta resource-nya. | Alur eksekusi di dalam process. |
| Address space | Umumnya terpisah dari proses lain. | Berbagi address space dengan thread lain dalam proses yang sama. |
| Resource | Memiliki resource seperti memory dan open files. | Menggunakan resource milik proses. |
| Komunikasi | Memerlukan IPC karena ruang alamat terpisah. | Dapat berkomunikasi lewat data bersama dalam proses. |
| Pembuatan dan pergantian | Lebih berat. | Umumnya lebih ringan. |
| Isolasi gangguan | Lebih kuat; crash satu proses biasanya tidak merusak proses lain. | Lebih lemah; satu thread merusak memori dapat menjatuhkan seluruh proses. |

### Mengapa thread disebut lightweight process?

Pembuatan dan context switch thread biasanya lebih murah daripada proses karena thread dalam satu proses tidak perlu mengganti seluruh address space dan tidak membuat salinan semua resource. Namun, thread bukan proses mandiri karena tetap bergantung pada proses pemiliknya.

## 2.4 Simulasi Hubungan Process dan Thread

Misalkan terdapat satu proses aplikasi editor dokumen:

- **Thread 1 — UI:** menerima input keyboard dan menggambar tampilan.
- **Thread 2 — Autosave:** menyimpan dokumen secara berkala.
- **Thread 3 — Spell check:** memeriksa ejaan di latar belakang.

Contoh kondisi pada satu saat:

| Thread | State | Kejadian |
|---|---|---|
| UI | Running | Sedang memproses karakter yang diketik user. |
| Autosave | Blocked | Menunggu timer atau operasi disk selesai. |
| Spell check | Ready | Siap bekerja, tetapi menunggu CPU. |

Pada CPU satu core, thread tidak benar-benar berjalan bersamaan. Scheduler menggilir ketiganya dengan cepat sehingga tampak bersamaan. Pada CPU multicore, UI dan spell check dapat benar-benar berjalan paralel pada core berbeda.

Semua thread tersebut dapat membaca dokumen yang sama karena berbagi memori proses. Konsekuensinya, akses bersamaan harus disinkronkan agar autosave tidak membaca data ketika UI sedang mengubah struktur dokumen.

## 2.5 Single-Threaded Process

**Single-threaded process** hanya mempunyai satu alur eksekusi.

```text
PROCESS A
+-----------------------------------+
| Code, Data, Heap, Files           |
| +-------------------------------+ |
| | Satu thread: PC, register,    | |
| | stack, dan satu alur eksekusi | |
| +-------------------------------+ |
+-----------------------------------+
```

### Karakteristik

- Lebih mudah dibuat dan diuji.
- Tidak memerlukan sinkronisasi antarthread dalam proses yang sama.
- Jika thread menunggu operasi I/O yang blocking, seluruh proses tidak dapat melakukan pekerjaan lain.
- Kurang mampu memanfaatkan banyak core untuk satu proses.

Contoh: program sederhana yang membaca input, menghitung hasil, lalu menampilkannya secara berurutan.

## 2.6 Multithreaded Process

**Multithreaded process** memiliki dua atau lebih thread dalam satu proses.

```text
PROCESS B
+------------------------------------------------+
| Shared: Code, Data, Heap, Open Files           |
|                                                |
| Thread 1          Thread 2          Thread 3   |
| PC + Registers    PC + Registers    PC + Reg.  |
| Stack 1           Stack 2           Stack 3    |
+------------------------------------------------+
```

### Keuntungan multithreading

1. **Responsiveness:** aplikasi tetap responsif ketika thread lain menunggu I/O.
2. **Resource sharing:** thread dapat memakai data dan resource proses yang sama.
3. **Economy:** membuat dan mengganti thread lebih murah daripada proses.
4. **Scalability / parallelism:** beberapa thread dapat berjalan pada core berbeda.
5. **Struktur program:** pekerjaan dapat dibagi menurut tugas, misalnya UI, network, dan background worker.

### Kekurangan dan risiko

1. **Race condition:** hasil bergantung pada urutan eksekusi thread.
2. **Deadlock:** thread saling menunggu resource tanpa akhir.
3. **Sulit diuji:** bug concurrency sering tidak konsisten dan sulit direproduksi.
4. **Shared failure:** kerusakan memori oleh satu thread dapat merusak seluruh proses.
5. **Overhead:** terlalu banyak thread menambah context switch dan penggunaan stack.

## 2.7 Model Implementasi Thread

### User-Level Thread (ULT)

Thread dikelola library di user space dan kernel tidak mengetahui setiap thread.

**Kelebihan:** pembuatan dan pergantian cepat; dapat memakai kebijakan sendiri.

**Kekurangan:** pada model many-to-one, satu system call blocking dapat memblokir semua thread dan thread-thread tersebut tidak bisa berjalan paralel di beberapa core.

### Kernel-Level Thread (KLT)

Thread dikelola langsung oleh kernel dan dapat dijadwalkan satu per satu.

**Kelebihan:** jika satu thread blocked, thread lain tetap dapat berjalan; mendukung parallelism multicore.

**Kekurangan:** operasi thread melibatkan kernel sehingga overhead lebih tinggi daripada ULT murni.

### Pemetaan thread

| Model | Pemetaan | Inti karakteristik |
|---|---|---|
| **Many-to-One** | Banyak user thread → satu kernel thread | Cepat, tetapi tidak paralel dan satu blocking call dapat memblokir semuanya. |
| **One-to-One** | Satu user thread → satu kernel thread | Mendukung parallelism, tetapi jumlah thread menambah overhead kernel. |
| **Many-to-Many** | Banyak user thread → sejumlah kernel thread | Fleksibel, tetapi implementasinya lebih kompleks. |

## 2.8 Context Switch Process dan Thread

**Context switch** terjadi ketika CPU berhenti menjalankan satu unit eksekusi dan beralih ke unit lain.

- **Process switch** menyimpan konteks CPU dan biasanya mengganti address space serta informasi memory mapping.
- **Thread switch dalam proses yang sama** menyimpan PC, register, dan stack thread, tetapi address space tetap sama.

Karena itu, pergantian thread dalam satu proses umumnya lebih ringan daripada pergantian antarprocess. Context switch tetap merupakan overhead karena selama pergantian CPU tidak mengerjakan instruksi aplikasi.

## 2.9 Manajemen Process oleh Sistem Operasi

Sistem operasi bertanggung jawab atas:

### 1. Pembuatan process

OS membuat PID dan PCB, mengalokasikan address space, memuat program, menyiapkan resource, dan menempatkan proses ke antrean Ready. Sebuah proses dapat membuat **child process**, sehingga terbentuk hubungan parent–child.

### 2. Penjadwalan dan dispatch

OS memilih proses atau thread yang mendapat CPU berdasarkan algoritma scheduling. Dispatcher melakukan perpindahan konteks dan menyerahkan kontrol CPU.

### 3. Pemeliharaan state dan antrean

OS memindahkan proses di antara Ready, Running, Blocked, dan state suspend sesuai kejadian.

### 4. Alokasi resource

OS mengatur CPU, memori, file, perangkat I/O, dan resource lain. OS juga memastikan proses tidak mengakses memori proses lain tanpa izin.

### 5. Inter-Process Communication (IPC)

Proses yang address space-nya terpisah dapat berkomunikasi melalui:

- shared memory;
- message queue;
- pipe;
- socket;
- signal.

Shared memory cepat, tetapi memerlukan sinkronisasi. Message passing lebih terisolasi, tetapi melibatkan pengiriman dan penerimaan pesan.

### 6. Sinkronisasi

OS menyediakan mutex, semaphore, monitor, atau mekanisme lain untuk mengatur akses ke resource bersama.

### 7. Penanganan deadlock

OS atau aplikasi dapat mencegah, menghindari, mendeteksi, atau memulihkan deadlock.

### 8. Terminasi dan cleanup

Ketika proses berakhir, OS mencatat status keluaran, menutup file, membebaskan memori, dan mengembalikan resource. Parent dapat mengambil exit status child.

---

# 3. Multiprocessor, Multicore, dan Embedded System

## 3.1 Multiprocessor

**Multiprocessor system** adalah satu sistem komputer yang memiliki dua atau lebih processor/CPU yang bekerja di bawah kendali satu sistem operasi dan biasanya berbagi memori serta perangkat I/O.

Tujuannya:

- meningkatkan throughput dan kinerja;
- menjalankan pekerjaan secara paralel;
- meningkatkan reliability, karena beban dapat dialihkan ketika satu unit gagal pada desain tertentu;
- membagi resource bersama sehingga lebih ekonomis daripada sistem terpisah.

### Jenis multiprocessor berdasarkan kendali

#### Asymmetric Multiprocessing (AMP)

- Setiap processor dapat memiliki tugas khusus.
- Satu master processor mengontrol sistem dan memberikan pekerjaan kepada processor lain.
- Desain lebih sederhana, tetapi master dapat menjadi bottleneck atau single point of failure.

#### Symmetric Multiprocessing (SMP)

- Semua processor sederajat.
- Setiap processor menjalankan salinan OS yang sama atau kernel bersama.
- Semua processor dapat menjadwalkan proses dan mengakses memori bersama.
- Lebih fleksibel dalam pembagian beban, tetapi sinkronisasi lebih kompleks.

### Organisasi akses memori

| Model | Penjelasan |
|---|---|
| **UMA (Uniform Memory Access)** | Waktu akses ke seluruh memori relatif sama bagi semua processor. Umum pada SMP berskala kecil. |
| **NUMA (Non-Uniform Memory Access)** | Akses ke memori lokal processor lebih cepat daripada memori milik node lain. Skalabilitas lebih baik, tetapi OS harus menempatkan thread dan data dengan tepat. |

## 3.2 Multicore

**Multicore processor** adalah satu chip atau satu paket CPU fisik yang di dalamnya terdapat dua atau lebih core pemrosesan. Setiap core dapat mengeksekusi alur instruksi sendiri.

Contoh susunan umum:

```text
SATU CHIP CPU
+---------------------------------------------+
| Core 1 | Core 2 | Core 3 | Core 4           |
| L1/L2  | L1/L2  | L1/L2  | L1/L2            |
|---------------------------------------------|
| Shared Last-Level Cache dan Memory Control  |
+---------------------------------------------+
```

Keuntungan multicore:

- kinerja paralel meningkat;
- lebih hemat daya dan ruang daripada terus menaikkan clock satu core;
- beberapa thread atau proses dapat berjalan pada core berbeda;
- komunikasi antarcore dapat lebih cepat karena berada dalam chip yang sama.

Aplikasi harus memiliki beberapa pekerjaan yang dapat dijalankan bersamaan agar mendapat manfaat penuh. Program single-thread tidak otomatis memakai semua core untuk satu alur instruksi.

## 3.3 Perbedaan Multiprocessor dan Multicore

| Aspek | Multiprocessor | Multicore |
|---|---|---|
| Bentuk fisik | Dua atau lebih CPU/socket pemrosesan dalam satu sistem. | Dua atau lebih core dalam satu chip/paket CPU. |
| Komunikasi | Biasanya melalui interconnect antarsocket; dapat lebih lambat. | Antarcore berada pada chip yang sama; umumnya lebih dekat. |
| Cache/memori | Setiap processor dapat memiliki cache dan memori lokal sendiri. | Core sering mempunyai cache privat dan berbagi last-level cache. |
| Daya dan biaya | Cenderung lebih tinggi. | Biasanya lebih hemat daya dan biaya untuk jumlah core yang sama. |
| Pandangan OS | OS melihat beberapa logical CPU. | OS juga melihat beberapa logical CPU/core. |
| Kesamaan | Keduanya memungkinkan parallel execution dan memerlukan scheduling serta sinkronisasi. | Keduanya memungkinkan parallel execution dan memerlukan scheduling serta sinkronisasi. |

Sistem modern dapat sekaligus **multiprocessor dan multicore**, misalnya server dengan dua socket CPU dan setiap CPU memiliki delapan core.

## 3.4 Multicore Bukan Sama dengan Multithreading

- **Multicore** adalah karakteristik hardware: tersedia beberapa core fisik.
- **Multithreading** adalah pembagian software menjadi beberapa alur eksekusi.
- **Simultaneous Multithreading (SMT)** memungkinkan satu core fisik menampilkan lebih dari satu logical processor kepada OS. SMT tidak menggandakan semua resource fisik core.

Multithreaded software dapat memanfaatkan multicore untuk parallelism. Namun, thread tetap dapat digunakan pada single-core melalui time sharing untuk meningkatkan responsiveness.

## 3.5 Concurrency dan Parallelism pada Banyak Core

| Concurrency | Parallelism |
|---|---|
| Beberapa tugas mengalami kemajuan dalam rentang waktu yang bertumpang tindih. | Beberapa tugas benar-benar dieksekusi pada saat yang sama. |
| Dapat terjadi pada satu core melalui interleaving. | Memerlukan lebih dari satu execution unit/core. |
| Fokus pada pengelolaan banyak aktivitas. | Fokus pada pengerjaan serentak untuk mempercepat pekerjaan. |

## 3.6 Tantangan OS pada Multiprocessor dan Multicore

1. **Scheduling:** membagi beban secara seimbang ke semua core.
2. **Processor affinity:** menjaga thread pada core yang sama agar cache tetap bermanfaat.
3. **Load balancing:** mencegah satu core sibuk ketika core lain menganggur.
4. **Synchronization:** melindungi data kernel dan aplikasi yang diakses bersamaan.
5. **Cache coherence:** menjaga agar salinan data pada cache berbeda tetap konsisten.
6. **Memory locality:** terutama pada NUMA, thread sebaiknya dekat dengan data yang dipakainya.
7. **Scalability:** lock global dapat menjadi bottleneck ketika jumlah core bertambah.

## 3.7 Embedded System

**Embedded system** adalah sistem komputer yang ditanamkan ke dalam perangkat yang lebih besar untuk menjalankan fungsi khusus. Sistem ini tidak dirancang sebagai komputer serbaguna.

Contoh:

- kontrol mesin cuci;
- sistem pengereman ABS pada mobil;
- router dan access point;
- alat kesehatan;
- smart TV dan kamera digital;
- sensor IoT;
- kontrol mesin industri.

### Komponen umum

```text
Sensor/Input → Processor/Microcontroller → Actuator/Output
                       |
                Memory + Firmware
                       |
              Embedded OS / RTOS
```

Tidak semua embedded system memakai OS. Perangkat sangat sederhana dapat menjalankan firmware langsung pada microcontroller. Sistem yang lebih kompleks biasanya memakai embedded OS atau RTOS.

### Karakteristik embedded system

| Karakteristik | Penjelasan |
|---|---|
| **Fungsi khusus** | Dirancang untuk satu tugas atau kelompok tugas tertentu. |
| **Resource terbatas** | CPU, RAM, storage, dan daya lebih terbatas daripada PC umum. |
| **Ukuran dan biaya** | Harus kecil dan ekonomis sesuai produk. |
| **Reliability** | Sering harus bekerja terus-menerus tanpa kegagalan. |
| **Power-aware** | Banyak perangkat menggunakan baterai sehingga konsumsi daya penting. |
| **Interaksi hardware langsung** | Membaca sensor dan mengendalikan actuator melalui interrupt atau I/O khusus. |
| **Respons waktu** | Beberapa sistem wajib memberi respons dalam batas waktu tertentu. |

## 3.8 Real-Time System dan RTOS

**Real-time system** adalah sistem yang kebenarannya ditentukan oleh hasil **dan waktu** saat hasil diberikan. Real-time tidak selalu berarti paling cepat; artinya respons harus dapat diprediksi dan memenuhi deadline.

### Hard real-time

Deadline wajib dipenuhi. Kegagalan memenuhi deadline dapat menyebabkan kerusakan serius.

Contoh: airbag, alat pacu jantung, dan kontrol penerbangan.

### Soft real-time

Deadline penting, tetapi keterlambatan sesekali hanya menurunkan kualitas layanan.

Contoh: audio/video streaming dan sistem multimedia.

### Ciri RTOS

- scheduling berbasis prioritas dan bersifat preemptive;
- interrupt latency rendah dan dapat diprediksi;
- context switch cepat;
- penggunaan memori terkendali;
- mekanisme sinkronisasi yang mempertimbangkan masalah seperti priority inversion;
- reliability tinggi.

## 3.9 General-Purpose OS vs Embedded OS/RTOS

| Aspek | General-Purpose OS | Embedded OS / RTOS |
|---|---|---|
| Tujuan | Menjalankan banyak jenis aplikasi. | Menjalankan fungsi perangkat tertentu. |
| Prioritas | Throughput, fairness, kenyamanan user. | Prediktabilitas, deadline, resource terbatas. |
| Hardware | Umumnya lebih besar dan bertenaga. | Sering kecil, hemat daya, dan spesifik. |
| Respons | Rata-rata cepat sudah cukup untuk banyak tugas. | Pada RTOS, worst-case response harus terkendali. |
| Contoh pemakaian | Laptop, desktop, server. | Kendaraan, alat industri, sensor, perangkat medis. |

---

# 4. Process Scheduling

## 4.1 Pengertian Process Scheduling

**Process scheduling** adalah kegiatan sistem operasi memilih proses atau thread yang boleh masuk ke sistem, berada di memori, atau menggunakan CPU.

Tujuan scheduling biasanya mencakup:

- menjaga CPU tetap sibuk;
- meningkatkan throughput;
- mengurangi waiting, turnaround, dan response time;
- memberikan fairness;
- memenuhi prioritas atau deadline;
- menjaga sistem tetap responsif.

## 4.2 Long-Term, Medium-Term, dan Short-Term Scheduling

```text
Job baru di disk
      |
      | Long-term scheduler: admit
      v
Ready queue di RAM <----------------------------+
      |                                         |
      | Short-term scheduler: pilih CPU         | I/O selesai
      v                                         |
   Running ----------> Blocked / I/O queue ------+
      |
      | Medium-term scheduler: suspend / swap out
      v
Ready/Suspend atau Blocked/Suspend di disk
      |
      | swap in / activate
      +---------------------> Ready/Blocked di RAM
```

### Long-Term Scheduler (Job Scheduler)

Memilih job dari secondary storage yang boleh masuk ke memori dan menjadi proses Ready.

- Mengontrol **degree of multiprogramming**, yaitu jumlah proses yang berada di memori.
- Menjaga campuran proses CPU-bound dan I/O-bound.
- Dipanggil relatif jarang sehingga boleh mengambil keputusan lebih lambat.
- Tidak selalu ada secara jelas pada sistem interaktif modern.

> **Kata kunci:** long-term menentukan **siapa yang masuk ke sistem/RAM**.

### Medium-Term Scheduler (Swapper)

Menangguhkan dan mengaktifkan kembali proses melalui mekanisme **swap out** dan **swap in**.

- Mengurangi degree of multiprogramming sementara.
- Membebaskan RAM.
- Membantu mengatasi memory pressure dan thrashing.
- Mengelola Ready/Suspend serta Blocked/Suspend.
- Dipanggil lebih sering daripada long-term, tetapi tidak sesering short-term.

> **Kata kunci:** medium-term menentukan **siapa yang dikeluarkan atau dimasukkan kembali ke RAM**.

### Short-Term Scheduler (CPU Scheduler)

Memilih satu proses atau thread dari Ready queue untuk menggunakan CPU.

- Dipanggil sangat sering, misalnya ketika quantum habis, proses blocked, atau proses selesai.
- Harus sangat cepat karena keputusan yang terlalu lama menjadi overhead.
- Menerapkan algoritma seperti FCFS, SJF/SRTF, Priority, dan Round Robin.

> **Kata kunci:** short-term menentukan **siapa yang memakai CPU berikutnya**.

### Tabel perbandingan tiga scheduler

| Aspek | Long-Term | Medium-Term | Short-Term |
|---|---|---|---|
| Nama lain | Job scheduler | Swapper | CPU scheduler |
| Keputusan | Admit job ke sistem | Suspend/swap out dan activate/swap in | Pilih proses Ready untuk CPU |
| Perpindahan umum | New → Ready | Ready/Blocked ↔ Suspend | Ready → Running |
| Frekuensi | Paling jarang | Sedang | Paling sering |
| Kecepatan harus | Tidak terlalu kritis | Cukup cepat | Sangat cepat |
| Tujuan utama | Kontrol degree of multiprogramming | Kontrol memori dan beban | Alokasi CPU |

## 4.3 Scheduler dan Dispatcher

Keduanya berbeda:

- **Scheduler** memilih proses yang akan berjalan.
- **Dispatcher** benar-benar menyerahkan CPU kepada proses terpilih dengan melakukan context switch, berpindah ke user mode, lalu melompat ke instruksi yang tepat.

Waktu yang dibutuhkan dispatcher disebut **dispatch latency**.

## 4.4 Istilah dan Rumus Scheduling

| Istilah | Arti |
|---|---|
| **Arrival Time (AT)** | Waktu proses masuk ke Ready queue. |
| **Burst Time (BT)** | Lama waktu CPU yang dibutuhkan proses. |
| **Completion Time (CT)** | Waktu proses selesai. |
| **Turnaround Time (TAT)** | Total waktu sejak datang sampai selesai. |
| **Waiting Time (WT)** | Total waktu menunggu di Ready queue. |
| **Response Time (RT)** | Waktu sejak datang sampai pertama kali memperoleh CPU. |
| **Throughput** | Jumlah proses yang selesai per satuan waktu. |
| **CPU Utilization** | Persentase waktu CPU mengerjakan pekerjaan berguna. |

Rumus:

```text
TAT = CT - AT
WT  = TAT - BT
RT  = waktu pertama mendapat CPU - AT
```

Response time tidak selalu sama dengan waiting time. Pada algoritma preemptive, proses dapat menerima respons awal lalu kembali menunggu beberapa kali.

## 4.5 Preemptive dan Non-Preemptive

### Non-preemptive

Setelah mendapat CPU, proses terus berjalan sampai selesai atau blocked secara sukarela.

- Lebih sederhana dan context switch lebih sedikit.
- Proses panjang dapat membuat proses pendek menunggu.
- Contoh: FCFS dan SJF non-preemptive.

### Preemptive

OS dapat mengambil CPU dari proses yang sedang Running.

- Lebih responsif untuk sistem interaktif.
- Memerlukan timer interrupt dan menambah context-switch overhead.
- Akses data bersama lebih kompleks.
- Contoh: Round Robin dan SRTF.

## 4.6 FCFS (First-Come, First-Served)

**FCFS** menjalankan proses berdasarkan urutan kedatangannya. Proses yang datang pertama dilayani pertama.

Karakteristik:

- menggunakan antrean FIFO;
- biasanya non-preemptive;
- sederhana dan mudah diterapkan;
- tidak menyebabkan starvation berdasarkan urutan kedatangan;
- dapat mengalami **convoy effect**, yaitu banyak proses pendek menunggu satu proses panjang.

Analogi: antrean kasir biasa.

## 4.7 SJF (Shortest Job First)

**SJF** memilih proses Ready dengan CPU burst paling pendek.

Karakteristik:

- versi dasar bersifat non-preemptive;
- menghasilkan rata-rata waiting time minimum jika burst berikutnya diketahui;
- sulit diterapkan sempurna karena OS biasanya tidak mengetahui burst masa depan;
- proses panjang dapat mengalami starvation jika proses pendek terus datang.

### SRTF

**Shortest Remaining Time First (SRTF)** adalah versi preemptive dari SJF. Jika proses baru datang dengan remaining time lebih pendek, proses yang sedang berjalan dapat di-preempt.

## 4.8 Round Robin (RR)

**Round Robin** membagikan CPU secara bergiliran. Setiap proses menerima jatah waktu bernama **time quantum**.

Alur:

1. Ambil proses dari depan Ready queue.
2. Jalankan maksimal selama satu quantum.
3. Jika selesai, keluarkan dari sistem.
4. Jika belum selesai, preempt dan masukkan ke belakang Ready queue.

Karakteristik:

- preemptive;
- cocok untuk time-sharing dan sistem interaktif;
- memberi response time yang baik dan relatif adil;
- besar quantum sangat memengaruhi performa.

### Pengaruh time quantum

- Quantum terlalu besar: RR mendekati FCFS.
- Quantum terlalu kecil: respons cepat, tetapi terlalu banyak context switch.
- Quantum yang baik harus menyeimbangkan responsiveness dan overhead.

## 4.9 Simulasi FCFS, SJF, dan Round Robin

Diketahui semua proses datang pada waktu 0:

| Process | Arrival Time | Burst Time |
|---|---:|---:|
| P1 | 0 | 6 |
| P2 | 0 | 2 |
| P3 | 0 | 4 |

Jika ada nilai yang sama, gunakan urutan awal P1, P2, lalu P3.

### A. FCFS

Urutan: P1 → P2 → P3

```text
0          6    8        12
|    P1    | P2 |   P3    |
```

| Process | CT | TAT = CT − AT | WT = TAT − BT | RT |
|---|---:|---:|---:|---:|
| P1 | 6 | 6 | 0 | 0 |
| P2 | 8 | 8 | 6 | 6 |
| P3 | 12 | 12 | 8 | 8 |
| **Rata-rata** |  | **8,67** | **4,67** | **4,67** |

### B. SJF Non-Preemptive

Urutan burst terpendek: P2 → P3 → P1

```text
0    2        6          12
| P2 |   P3    |    P1    |
```

| Process | CT | TAT | WT | RT |
|---|---:|---:|---:|---:|
| P1 | 12 | 12 | 6 | 6 |
| P2 | 2 | 2 | 0 | 0 |
| P3 | 6 | 6 | 2 | 2 |
| **Rata-rata** |  | **6,67** | **2,67** | **2,67** |

Pada data ini, SJF menghasilkan rata-rata waiting time lebih kecil daripada FCFS.

### C. Round Robin, Quantum = 2

Ready queue awal: P1, P2, P3.

```text
0    2    4    6    8    10   12
| P1 | P2 | P3 | P1 | P3 | P1 |
```

Rincian:

- P1 berjalan 0–2, sisa 4.
- P2 berjalan 2–4, selesai.
- P3 berjalan 4–6, sisa 2.
- P1 berjalan 6–8, sisa 2.
- P3 berjalan 8–10, selesai.
- P1 berjalan 10–12, selesai.

| Process | CT | TAT | WT = TAT − BT | RT |
|---|---:|---:|---:|---:|
| P1 | 12 | 12 | 6 | 0 |
| P2 | 4 | 4 | 2 | 2 |
| P3 | 10 | 10 | 6 | 4 |
| **Rata-rata** |  | **8,67** | **4,67** | **2,00** |

RR pada contoh ini tidak memberikan waiting time terkecil, tetapi memberikan kesempatan CPU lebih awal kepada semua proses. Inilah alasan RR cocok untuk sistem interaktif.

## 4.10 Perbandingan Algoritma Scheduling

| Aspek | FCFS | SJF | Round Robin |
|---|---|---|---|
| Dasar pemilihan | Urutan kedatangan | Burst terpendek | Giliran dengan quantum |
| Preemptive? | Tidak | Tidak; SRTF adalah versi preemptive | Ya |
| Kelebihan utama | Sederhana dan overhead rendah | Rata-rata waiting time optimal jika burst diketahui | Responsif dan fair untuk time-sharing |
| Kekurangan utama | Convoy effect | Burst sulit diprediksi, starvation proses panjang | Context switch dan pemilihan quantum |
| Cocok untuk | Batch sederhana | Job dengan burst yang dapat diperkirakan | Sistem interaktif |
| Risiko starvation | Umumnya tidak | Ada untuk job panjang | Umumnya tidak bila antrean berjalan normal |

## 4.11 Tambahan: Fair-Share Scheduling

Jika istilah **fair-share scheduling** muncul dalam soal, artinya CPU dibagi secara adil berdasarkan **user, group, atau kelompok proses**, bukan hanya per proses.

Contoh:

- User A menjalankan 10 proses.
- User B menjalankan 1 proses.
- Jika setiap proses mendapat jatah sama, A dapat menguasai sekitar 10/11 CPU.
- Fair-share scheduling dapat memberi A dan B masing-masing sekitar 50% sesuai kebijakan, lalu jatah A dibagi lagi ke 10 prosesnya.

Tujuannya mencegah user yang membuat banyak proses memperoleh bagian CPU tidak adil.

---

# 5. Concurrency

## 5.1 Pengertian Concurrency

**Concurrency** adalah keadaan ketika beberapa proses atau thread mengalami kemajuan dalam rentang waktu yang saling bertumpang tindih.

Concurrency dapat terjadi:

- pada **single-core**, dengan pergantian cepat atau interleaving;
- pada **multicore**, dengan interleaving sekaligus parallel execution.

```text
Single-core (concurrent, tidak paralel):
Waktu → | A | B | A | C | B | A |

Dua core (dapat concurrent dan paralel):
Core 1 → | A | A | B | B |
Core 2 → | C | C | A | C |
```

Concurrency dibutuhkan agar sistem responsif, CPU tidak menganggur saat I/O, dan banyak user atau aplikasi dapat dilayani. Namun, urutan eksekusi menjadi tidak selalu dapat diprediksi.

## 5.2 Interaksi Antarprocess atau Antarthread

Aktivitas concurrent dapat:

1. **Tidak saling mengetahui:** proses bersaing memakai CPU, memory, atau I/O.
2. **Mengetahui secara tidak langsung:** proses berbagi objek, file, atau database.
3. **Mengetahui secara langsung:** proses berkomunikasi dan bekerja sama melalui IPC.

Masalah utama yang perlu dikendalikan adalah perebutan resource, komunikasi, sinkronisasi, dan urutan operasi.

## 5.3 Race Condition

**Race condition** terjadi ketika hasil program bergantung pada urutan atau timing eksekusi beberapa thread/process yang mengakses data bersama.

Misalkan `counter = 5`. Thread A dan B sama-sama menjalankan `counter = counter + 1`.

Operasi tersebut sebenarnya terdiri dari:

```text
1. LOAD counter ke register
2. ADD 1
3. STORE hasil ke counter
```

Kemungkinan interleaving yang salah:

```text
A: LOAD 5
B: LOAD 5
A: ADD 1  → 6
B: ADD 1  → 6
A: STORE 6
B: STORE 6
```

Hasil akhir menjadi 6, padahal seharusnya 7. Operasi gabungan ini tidak atomic.

## 5.4 Critical Section

**Critical section** adalah bagian program yang membaca atau mengubah data/resource bersama dan karena itu tidak boleh dieksekusi bersamaan secara sembarangan.

Struktur umum:

```text
ENTRY SECTION       meminta izin masuk
CRITICAL SECTION    mengakses resource bersama
EXIT SECTION        melepas izin
REMAINDER SECTION   pekerjaan lain
```

Solusi critical-section yang baik memenuhi:

1. **Mutual exclusion:** hanya satu proses/thread berada di critical section untuk resource yang sama.
2. **Progress:** jika critical section kosong, pemilihan proses berikutnya tidak boleh ditunda tanpa alasan.
3. **Bounded waiting:** ada batas berapa lama proses menunggu sehingga tidak starvation.

## 5.5 Atomic Operation

Operasi **atomic** terjadi sebagai satu kesatuan yang tidak dapat diamati dalam keadaan setengah selesai. Operasi atomic menjadi dasar implementasi lock, misalnya dengan instruksi hardware seperti test-and-set atau compare-and-swap.

Atomic bukan berarti seluruh program berjalan tanpa interupsi. Hanya operasi tertentu yang dijamin tidak terpecah dari sudut pandang proses lain.

## 5.6 Mutex

**Mutex (mutual exclusion lock)** adalah kunci yang memastikan hanya satu thread memasuki critical section pada satu waktu.

```text
lock(mutex)
    // critical section
unlock(mutex)
```

- Jika mutex tersedia, thread mengambilnya dan masuk.
- Jika mutex sudah dimiliki thread lain, thread harus menunggu.
- Secara konsep, thread yang mengunci mutex harus membuka mutex tersebut.

Mutex cocok untuk melindungi satu resource bersama, tetapi penggunaan urutan lock yang salah dapat menyebabkan deadlock.

## 5.7 Semaphore

**Semaphore** adalah variabel sinkronisasi integer yang diubah hanya melalui operasi atomic:

- `wait()` / `P()` / `down()`: mencoba mengurangi nilai; proses menunggu jika resource tidak tersedia.
- `signal()` / `V()` / `up()`: menambah nilai dan dapat membangunkan proses yang menunggu.

### Binary semaphore

Nilainya 0 atau 1 dan dapat dipakai untuk mutual exclusion atau signaling.

### Counting semaphore

Nilainya dapat lebih dari 1 untuk menghitung jumlah resource identik yang tersedia.

Contoh: tersedia tiga koneksi database. Semaphore diinisialisasi dengan nilai 3. Tiga thread dapat memakai koneksi; thread keempat harus menunggu sampai salah satu melakukan `signal()`.

### Mutex vs semaphore

| Mutex | Semaphore |
|---|---|
| Berfokus pada kepemilikan lock dan mutual exclusion. | Berfokus pada signaling atau menghitung resource. |
| Umumnya hanya owner yang membuka lock. | Tidak selalu memiliki konsep owner. |
| Biasanya bernilai locked/unlocked. | Bisa binary atau counting. |

## 5.8 Monitor dan Message Passing

### Monitor

**Monitor** adalah abstraksi tingkat tinggi yang menggabungkan data bersama, operasi terhadap data, dan mutual exclusion. Hanya satu thread dapat menjalankan prosedur monitor pada satu waktu. **Condition variable** memungkinkan thread menunggu kondisi tertentu dan dibangunkan kembali.

### Message passing

Process/thread berkomunikasi melalui operasi `send` dan `receive`, tanpa harus mengubah data bersama secara langsung.

- Cocok untuk proses dengan address space berbeda dan distributed system.
- Dapat bersifat blocking/synchronous atau non-blocking/asynchronous.
- Mengurangi shared-memory race, tetapi protokol pesan tetap harus dirancang dengan benar.

## 5.9 Contoh Producer–Consumer

- **Producer** menghasilkan data dan memasukkannya ke buffer.
- **Consumer** mengambil data dari buffer.
- Producer harus menunggu bila buffer penuh.
- Consumer harus menunggu bila buffer kosong.
- Keduanya tidak boleh mengubah struktur buffer secara bersamaan.

Untuk buffer berukuran `N`:

```text
semaphore empty = N
semaphore full  = 0
semaphore mutex = 1
```

Pseudocode producer:

```text
while true:
    item = produce()
    wait(empty)
    wait(mutex)
        insert(item)
    signal(mutex)
    signal(full)
```

Pseudocode consumer:

```text
while true:
    wait(full)
    wait(mutex)
        item = remove()
    signal(mutex)
    signal(empty)
    consume(item)
```

Perannya:

- `mutex` melindungi critical section buffer;
- `empty` menghitung slot kosong;
- `full` menghitung slot berisi.

## 5.10 Deadlock

**Deadlock** adalah keadaan ketika sekumpulan proses/thread saling menunggu resource atau event yang hanya dapat diberikan oleh anggota lain dalam kumpulan itu. Akibatnya, tidak ada yang dapat melanjutkan.

Contoh:

```text
Thread A memegang Lock 1, menunggu Lock 2.
Thread B memegang Lock 2, menunggu Lock 1.
```

### Empat syarat Coffman

Deadlock dapat terjadi jika keempat kondisi berikut ada secara bersamaan:

1. **Mutual Exclusion:** setidaknya satu resource hanya dapat dipakai satu proses pada satu waktu.
2. **Hold and Wait:** proses memegang resource sambil menunggu resource lain.
3. **No Preemption:** resource tidak dapat diambil paksa; harus dilepas sukarela.
4. **Circular Wait:** terbentuk rantai melingkar proses yang saling menunggu.

Jika minimal satu kondisi dicegah, deadlock tidak dapat terbentuk.

### Penanganan deadlock

| Strategi | Penjelasan |
|---|---|
| **Prevention** | Merancang sistem agar salah satu syarat Coffman tidak pernah terpenuhi, misalnya semua lock wajib diambil dalam urutan tetap. |
| **Avoidance** | Hanya mengabulkan permintaan jika sistem tetap dalam safe state, misalnya konsep Banker’s Algorithm. |
| **Detection** | Mengizinkan deadlock, lalu mencari siklus atau proses yang tidak dapat maju. |
| **Recovery** | Menghentikan proses, mengambil resource, rollback, atau me-restart komponen setelah deadlock terdeteksi. |
| **Ignore** | Sistem umum kadang membiarkan aplikasi menangani kasus langka karena pencegahan penuh mahal. |

## 5.11 Starvation, Deadlock, dan Livelock

| Masalah | Kondisi | Apakah aktivitas terlihat? |
|---|---|---|
| **Deadlock** | Semua pihak dalam siklus menunggu dan tidak dapat maju. | Tidak ada kemajuan. |
| **Starvation** | Satu proses terus tidak mendapat CPU/resource karena proses lain selalu didahulukan. | Sistem lain dapat tetap maju. |
| **Livelock** | Proses tidak blocked dan terus bereaksi, tetapi tidak menyelesaikan pekerjaan. | Banyak aktivitas, tanpa kemajuan nyata. |

Contoh livelock: dua orang berpapasan dan terus berpindah ke sisi yang sama untuk saling memberi jalan.

**Aging** dapat mengurangi starvation dengan menaikkan prioritas proses semakin lama proses menunggu.

## 5.12 Masalah Concurrency Klasik

| Masalah | Fokus |
|---|---|
| **Producer–Consumer** | Sinkronisasi buffer penuh/kosong dan mutual exclusion. |
| **Readers–Writers** | Banyak reader boleh membaca bersamaan, tetapi writer perlu akses eksklusif. |
| **Dining Philosophers** | Perebutan beberapa resource dan risiko deadlock/starvation. |

## 5.13 Pedoman Aman Menggunakan Lock

1. Lindungi semua akses ke data bersama dengan mekanisme yang konsisten.
2. Jaga critical section tetap pendek.
3. Ambil beberapa lock dalam urutan global yang sama.
4. Jangan melakukan operasi blocking yang lama sambil memegang lock jika dapat dihindari.
5. Selalu lepaskan lock pada semua jalur error.
6. Gunakan abstraksi tingkat tinggi jika tersedia.
7. Jangan menganggap program benar hanya karena sekali dijalankan tanpa error; jadwal thread dapat berubah.

---

# 6. Ringkasan Hafalan Cepat

## 6.1 Process dan PCB

- **Program:** instruksi pasif.
- **Process:** program aktif yang sedang dieksekusi.
- **Process image:** code, data, heap, stack, PCB.
- **PCB:** PID, state, program counter, register, scheduling, memory, I/O, accounting.
- **Context switch:** simpan konteks lama ke PCB, muat konteks baru dari PCB.

## 6.2 State Process

### Dua state

```text
Running ↔ Not Running
```

Kelemahannya: Ready dan Blocked tercampur dalam Not Running.

### Lima state

```text
New → Ready → Running → Exit
        ↑       |
        +Blocked+
```

Transisi wajib hafal:

- Ready → Running = dispatch.
- Running → Ready = timeout/preemption.
- Running → Blocked = menunggu event/I/O.
- Blocked → Ready = event/I/O selesai.

### Dua suspend

- **Ready/Suspend:** siap, tetapi belum berada di RAM atau belum diaktifkan.
- **Blocked/Suspend:** suspended dan masih menunggu event.
- Event selesai saat Blocked/Suspend → **Ready/Suspend**, bukan Ready.

Alasan suspend: swapping, keputusan OS, permintaan user, timing, parent, keamanan/administrasi.

## 6.3 Process dan Thread

```text
Process = pemilik resource
Thread  = pelaksana instruksi
```

Berbagi dalam satu proses:

```text
Code + Data + Heap + Files
```

Milik masing-masing thread:

```text
Program Counter + Registers + Stack
```

Single-thread sederhana, tetapi satu blocking operation dapat menghentikan seluruh pekerjaan. Multithread lebih responsif dan dapat paralel, tetapi membutuhkan sinkronisasi.

## 6.4 Scheduler

```text
LONG   = memilih job masuk
MEDIUM = swap/suspend
SHORT  = memilih pemakai CPU
```

Urutan frekuensi dari paling sering:

```text
Short-term > Medium-term > Long-term
```

## 6.5 Algoritma Scheduling

```text
FCFS = yang datang dulu dilayani dulu
SJF  = burst paling pendek dulu
RR   = bergiliran memakai time quantum
```

- FCFS: sederhana, convoy effect.
- SJF: rata-rata waiting minimum jika burst diketahui, risiko starvation.
- RR: responsif dan fair, tetapi quantum kecil menambah context switch.

Rumus:

```text
TAT = CT - AT
WT  = TAT - BT
RT  = First Run - AT
```

## 6.6 Multiprocessor, Multicore, Embedded

- **Multiprocessor:** beberapa CPU/processor dalam satu sistem.
- **Multicore:** beberapa core di dalam satu chip CPU.
- **SMP:** processor sederajat.
- **AMP:** master mengatur processor lain atau tugas processor berbeda.
- **Concurrency:** tugas overlap dalam waktu.
- **Parallelism:** tugas benar-benar berjalan bersamaan.
- **Embedded system:** komputer khusus di dalam perangkat, resource terbatas.
- **Hard real-time:** deadline wajib.
- **Soft real-time:** terlambat menurunkan kualitas, tetapi tidak selalu fatal.

## 6.7 Concurrency

- **Race condition:** hasil bergantung pada timing/interleaving.
- **Critical section:** bagian kode yang mengakses resource bersama.
- **Mutual exclusion:** hanya satu pihak masuk critical section.
- **Mutex:** lock dengan kepemilikan.
- **Semaphore:** signaling atau penghitung resource.
- **Deadlock:** saling menunggu tanpa akhir.
- **Starvation:** satu proses terus tidak kebagian.
- **Livelock:** terus aktif, tetapi tidak maju.

Empat syarat deadlock:

```text
Mutual Exclusion
Hold and Wait
No Preemption
Circular Wait
```

---

# 7. Template Jawaban Esai

## 7.1 Jelaskan Process dan Elemen Process

> Process adalah program yang sedang dieksekusi beserta konteks dan resource-nya. Process image terdiri atas program code, data, heap, stack, dan PCB. PCB menyimpan PID, state, program counter, register CPU, informasi scheduling, memory management, I/O, serta accounting. Informasi tersebut memungkinkan OS mengelola proses dan melakukan context switch.

## 7.2 Jelaskan Model Lima State dan Suspend

> Model lima state terdiri atas New, Ready, Running, Blocked, dan Exit. Proses di-admit dari New ke Ready, di-dispatch ke Running, kembali ke Ready saat di-preempt, masuk Blocked saat menunggu event, kembali ke Ready setelah event selesai, dan masuk Exit setelah berakhir. Untuk mendukung swapping ditambahkan Ready/Suspend dan Blocked/Suspend. Ready/Suspend berarti proses siap tetapi suspended, sedangkan Blocked/Suspend berarti proses suspended sekaligus masih menunggu event. Suspend dapat terjadi karena kekurangan memori, keputusan OS, permintaan user atau parent, timing, dan alasan administrasi.

## 7.3 Jelaskan Hubungan Process dan Thread

> Process merupakan unit kepemilikan resource, sedangkan thread merupakan unit eksekusi di dalam process. Thread-thread pada process yang sama berbagi code, data, heap, dan file, tetapi masing-masing mempunyai program counter, register, dan stack. Single-thread hanya mempunyai satu alur eksekusi. Multithread memungkinkan responsiveness dan parallelism yang lebih baik, tetapi dapat menimbulkan race condition dan deadlock sehingga memerlukan sinkronisasi.

## 7.4 Jelaskan Multiprocessor dan Multicore

> Multiprocessor adalah sistem dengan dua atau lebih processor/CPU yang bekerja di bawah satu OS dan umumnya berbagi resource. Multicore adalah satu chip CPU yang memiliki beberapa core. Keduanya memungkinkan parallel execution. Multiprocessor menekankan banyak unit processor atau socket, sedangkan multicore menempatkan beberapa core dalam satu chip. OS harus menangani scheduling, load balancing, cache coherence, processor affinity, dan sinkronisasi.

## 7.5 Jelaskan Embedded System

> Embedded system adalah sistem komputer yang ditanamkan dalam perangkat untuk menjalankan fungsi khusus. Ciri-cirinya adalah resource terbatas, hemat daya, berhubungan erat dengan hardware, serta membutuhkan reliability tinggi. Sebagian embedded system menggunakan RTOS. Pada hard real-time, deadline wajib dipenuhi, sedangkan pada soft real-time keterlambatan biasanya hanya menurunkan kualitas layanan.

## 7.6 Jelaskan Tiga Tingkat Scheduling

> Long-term scheduler memilih job yang boleh masuk ke sistem dan mengontrol degree of multiprogramming. Medium-term scheduler melakukan suspend, swap out, dan swap in untuk mengatur penggunaan memori. Short-term scheduler memilih proses dari Ready queue untuk menggunakan CPU dan karena dipanggil sangat sering harus bekerja sangat cepat.

## 7.7 Bandingkan FCFS, SJF, dan Round Robin

> FCFS melayani proses berdasarkan urutan kedatangan, sederhana tetapi mengalami convoy effect. SJF memilih burst terpendek dan menghasilkan rata-rata waiting time minimum jika burst diketahui, tetapi proses panjang dapat starvation. Round Robin membagi CPU secara bergiliran memakai time quantum, cocok untuk sistem interaktif, tetapi quantum yang terlalu kecil menambah context-switch overhead dan quantum yang terlalu besar membuatnya mendekati FCFS.

## 7.8 Jelaskan Concurrency

> Concurrency adalah kemajuan beberapa proses atau thread dalam waktu yang bertumpang tindih. Pada satu core concurrency terjadi melalui interleaving, sedangkan pada multicore aktivitas dapat benar-benar paralel. Akses data bersama dapat menimbulkan race condition sehingga critical section harus dilindungi dengan mutex, semaphore, monitor, atau message passing. Pengelolaan yang salah dapat menyebabkan deadlock, starvation, atau livelock.

---

# 8. Latihan Soal dan Jawaban

## Soal 1

**Apa perbedaan program dan process?**

**Jawaban:** Program adalah kumpulan instruksi pasif di penyimpanan. Process adalah program yang sedang dieksekusi beserta state, konteks CPU, memori, dan resource-nya.

## Soal 2

**Sebutkan lima komponen process image.**

**Jawaban:** Program code/text, data, heap, stack, dan PCB.

## Soal 3

**Mengapa PCB diperlukan ketika context switch?**

**Jawaban:** PCB menyimpan konteks proses lama, termasuk program counter dan register, agar proses dapat dilanjutkan tepat dari keadaan terakhir. PCB proses baru juga menyediakan konteks yang harus dimuat CPU.

## Soal 4

**Apa kelemahan model dua state?**

**Jawaban:** State Not Running mencampur proses Ready dengan proses Blocked, padahal Ready dapat memakai CPU sedangkan Blocked masih menunggu event.

## Soal 5

**Ke mana proses berpindah setelah operasi I/O selesai?**

**Jawaban:** Dari Blocked ke Ready, bukan langsung ke Running, karena proses masih harus dipilih oleh scheduler.

## Soal 6

**Apa perbedaan Ready/Suspend dan Blocked/Suspend?**

**Jawaban:** Ready/Suspend sudah siap secara logis tetapi masih suspended atau di luar RAM. Blocked/Suspend masih menunggu event sekaligus suspended.

## Soal 7

**Jika I/O selesai ketika proses berada pada Blocked/Suspend, state berikutnya apa?**

**Jawaban:** Ready/Suspend. Event sudah selesai, tetapi proses belum diaktifkan atau dimasukkan kembali ke RAM.

## Soal 8

**Sebutkan minimal empat alasan suspend.**

**Jawaban:** Swapping karena kekurangan memori, keputusan OS, permintaan user, kebutuhan timing, permintaan parent, atau alasan keamanan/administrasi.

## Soal 9

**Apa yang dibagi dan tidak dibagi oleh thread dalam process yang sama?**

**Jawaban:** Thread berbagi code, data, heap, address space, dan open files. Setiap thread memiliki program counter, register, stack, ID, dan state sendiri.

## Soal 10

**Mengapa thread switch biasanya lebih ringan daripada process switch?**

**Jawaban:** Thread dalam process yang sama tetap memakai address space dan resource yang sama, sehingga OS terutama mengganti konteks eksekusi thread, bukan seluruh konteks memori process.

## Soal 11

**Apa hubungan multicore dan multithreading?**

**Jawaban:** Multicore menyediakan beberapa core hardware. Multithreading menyediakan beberapa alur eksekusi software. Thread-thread dapat ditempatkan pada core berbeda agar benar-benar berjalan paralel.

## Soal 12

**Bedakan AMP dan SMP.**

**Jawaban:** Pada AMP, processor memiliki peran berbeda atau dikendalikan master. Pada SMP, semua processor sederajat dan dapat menjalankan pekerjaan serta mengakses resource bersama.

## Soal 13

**Mengapa real-time tidak selalu berarti “secepat mungkin”?**

**Jawaban:** Real-time menekankan respons yang dapat diprediksi dan selesai sebelum deadline. Respons yang sangat cepat tetapi kadang melewati deadline tetap tidak memenuhi kebutuhan real-time.

## Soal 14

**Apa perbedaan tugas long-, medium-, dan short-term scheduler?**

**Jawaban:** Long-term mengadmit job, medium-term melakukan suspend/swapping, dan short-term memilih proses Ready untuk memakai CPU.

## Soal 15

**Apa perbedaan scheduler dan dispatcher?**

**Jawaban:** Scheduler memilih proses. Dispatcher melakukan context switch dan menyerahkan CPU kepada proses terpilih.

## Soal 16

**Apa kelemahan utama FCFS?**

**Jawaban:** Convoy effect, yaitu proses pendek menunggu lama di belakang proses dengan burst panjang.

## Soal 17

**Mengapa SJF sulit diterapkan secara sempurna?**

**Jawaban:** Karena OS biasanya tidak mengetahui dengan pasti panjang CPU burst berikutnya dan hanya dapat memperkirakannya.

## Soal 18

**Apa yang terjadi jika quantum Round Robin terlalu besar atau terlalu kecil?**

**Jawaban:** Quantum terlalu besar membuat RR mendekati FCFS. Quantum terlalu kecil menyebabkan terlalu banyak context switch dan overhead.

## Soal 19

**Apa perbedaan waiting time dan response time?**

**Jawaban:** Waiting time adalah total waktu proses menunggu di Ready queue. Response time adalah waktu dari kedatangan sampai pertama kali memperoleh CPU.

## Soal 20

**Apa itu race condition?**

**Jawaban:** Kondisi ketika hasil akses data bersama bergantung pada urutan atau timing eksekusi concurrent, sehingga hasil dapat salah atau tidak konsisten.

## Soal 21

**Sebutkan tiga syarat solusi critical section yang baik.**

**Jawaban:** Mutual exclusion, progress, dan bounded waiting.

## Soal 22

**Bedakan mutex dan counting semaphore.**

**Jawaban:** Mutex adalah lock dengan kepemilikan untuk akses eksklusif. Counting semaphore menghitung sejumlah resource identik dan dapat mengizinkan beberapa pengguna sesuai nilainya.

## Soal 23

**Sebutkan empat syarat Coffman untuk deadlock.**

**Jawaban:** Mutual exclusion, hold and wait, no preemption, dan circular wait.

## Soal 24

**Bedakan deadlock, starvation, dan livelock.**

**Jawaban:** Deadlock berarti pihak-pihak saling menunggu dan berhenti maju. Starvation berarti satu pihak terus tidak memperoleh resource sementara pihak lain maju. Livelock berarti pihak-pihak terus aktif bereaksi tetapi tidak menghasilkan kemajuan.

## Soal 25

**Apakah concurrency selalu berarti parallelism?**

**Jawaban:** Tidak. Concurrency dapat terjadi pada satu core melalui interleaving. Parallelism berarti beberapa pekerjaan benar-benar dieksekusi pada saat yang sama dan memerlukan lebih dari satu execution unit.

---

## Checklist Belajar Sebelum UTS

- [ ] Dapat menggambar dan menjelaskan model dua state.
- [ ] Dapat menggambar lima state beserta semua transisinya.
- [ ] Dapat membedakan Blocked, Ready/Suspend, dan Blocked/Suspend.
- [ ] Hafal isi utama PCB dan fungsi context switch.
- [ ] Dapat membedakan resource process dengan konteks milik thread.
- [ ] Dapat memberi simulasi single-thread dan multithread.
- [ ] Dapat membedakan multiprocessor, multicore, multithreading, concurrency, dan parallelism.
- [ ] Dapat menjelaskan embedded system serta hard/soft real-time.
- [ ] Hafal tugas long-, medium-, dan short-term scheduler.
- [ ] Dapat menggambar Gantt chart FCFS, SJF, dan Round Robin.
- [ ] Dapat menghitung CT, TAT, WT, dan RT.
- [ ] Dapat menjelaskan race condition, critical section, mutex, dan semaphore.
- [ ] Hafal empat syarat Coffman untuk deadlock.
- [ ] Dapat membedakan deadlock, starvation, dan livelock.
