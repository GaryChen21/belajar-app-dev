# BELAJAR DEVOPS — Rangkuman Ujian (GitOps, Docker, Kubernetes)

> Rangkuman ini disusun dari slide kuliah **Session 02–06** (Software Development Operations in Cloud Environment).
> Fokus materi ujian: **GitOps**, **Docker**, dan **Kubernetes**.

---

## Daftar Isi

1. [GitOps](#1-gitops)
2. [Docker](#2-docker)
3. [Kubernetes](#3-kubernetes)
4. [Soal Latihan & Jawaban](#4-soal-latihan--jawaban)
5. [Cheat Sheet Perintah](#5-cheat-sheet-perintah)

---

## 1. GITOPS

### 1.1 Konsep Dasar: Source Code Management (SCM)

- Software development selalu butuh tempat menyimpan kode.
- Dua jenis kode:
  - **Source code** → ditulis dengan bahasa tingkat tinggi (human-readable).
  - **Binaries** → hasil kompilasi source code, siap dijalankan.
- **SCM tool** mengelola semua aspek source code. Manfaatnya: kolaborasi lebih baik, workflow efisien, *version control*, *repository management*, *branching & merging*, *change tracking*, *auditing*, serta kualitas & maintainability yang lebih baik.
- Contoh SCM: **Git**, Subversion, Mercurial, CVS.
- **Git = de facto standard** SCM saat ini (wajib dikuasai developer).

### 1.2 Git

- Git adalah **distributed version control system**.
  - Setiap Git repository adalah **copy dari repository asli** → bisa direplikasi ke remote.
  - **Remote repository** = repository pusat terpusat. Semua developer sync perubahan dari sini ke local repository masing-masing.

### 1.3 Apa itu GitOps?

> **GitOps** adalah metode penerapan DevOps di mana **Git menjadi single source of truth**.

Alih-alih menyimpan banyak script dan tooling, GitOps menuliskan **declarative code untuk segalanya**: infrastructure, configuration, dan application code. Artinya, seluruh sistem dapat dibangun "out of thin air" hanya dari Git repository.

**Manfaat GitOps:**
- Deploy software lebih cepat.
- Pemulihan (recovery) dari error lebih cepat.
- Manajemen kredensial lebih baik.
- Deployment **self-documenting** (terdokumentasi sendiri).
- Mendorong **shared ownership & knowledge**.

### 1.4 Empat Prinsip Utama GitOps

1. **Seluruh sistem dideklarasikan secara deklaratif (declarative).**
   Bukan langkah-langkah "how to", melainkan deklarasi **end state** yang diinginkan. Git selalu jadi single source of truth. Karena declarative bersifat **idempotent**, sistem akan *eventually consistent* dengan yang ada di Git.
2. **Versi state yang diinginkan dikelola menggunakan Git.**
   Git commit = deployment baru; Git revert = rollback. Tidak perlu memikirkan cara roll out/roll back secara manual.
3. **Tooling otomatis menerapkan perubahan yang sudah disetujui.**
   Tooling memantau perubahan di repository dan menerapkannya ke environment. Bisa pakai beberapa branch untuk environment berbeda + proses approval/gating berbasis **pull request**.
4. **Self-healing agent mengoreksi divergensi.**
   Agent memantau dan mengembalikan environment agar sesuai dengan Git. Contoh: jika seseorang menghapus container secara manual, agent akan alert ke tim dan **membuat ulang container** tersebut. Tidak ada jalan mem-bypass GitOps → **Git tetap single source of truth**.

### 1.5 GitOps Workflow: Push vs Pull Model

| Model | Karakteristik | Tool Contoh | Rekomendasi |
|-------|--------------|-------------|-------------|
| **Push Model** | Tidak sadar terhadap konfigurasi existing; hanya bereaksi saat ada perubahan ke Git repository. | Jenkins, CircleCI, Travis CI | **Tidak direkomendasikan**, tetapi tak terhindarkan untuk cloud provisioning (Terraform) & config management (Ansible) yang keduanya push-based. |
| **Pull Model** | Memantau dan bereaksi terhadap perubahan environment **dan** repository sekaligus. Perubahan yang tidak sesuai Git akan di-revert. Memberi notifikasi (email, tiket, Slack) untuk hal yang tak bisa diperbaiki. | Argo CD, Flux | **Direkomendasikan** untuk GitOps. |

### 1.6 Git vs GitOps

| Aspek | Git | GitOps |
|-------|-----|--------|
| **Definisi** | Distributed version control system untuk melacak perubahan source code & file lain. | Praktik/prinsip yang memakai Git sebagai single source of truth untuk mengotomasi deployment & operasi infrastructure dan aplikasi. |
| **Fokus** | Version control & kolaborasi source code. | Otomasi & manajemen deployment/operasi infrastruktur-aplikasi melalui DevOps workflow berbasis Git. |
| **Usage** | Digunakan developer untuk tracking, branch, merge kode. | Mendefinisikan & mengelola konfigurasi infrastructure/aplikasi secara deklaratif. Repository jadi hub untuk desired state & otomasi. |

### 1.7 Perbandingan Detail (Modern DevOps vs Traditional DevOps)

| Aspek | Traditional DevOps | Modern DevOps (GitOps) |
|-------|--------------------|------------------------|
| **Core Components** | Repositories, branches, commits, pull requests. | Git repositories, declarative config files (YAML), Kubernetes manifests, CI/CD pipelines, deployment tools seperti **Argo CD / Flux**. |
| **Workflow** | Developer clone → modify → commit → push. Kolaborasi via pull request & branch merge. | Konfigurasi infrastructure/aplikasi disimpan di Git. Perubahan memicu proses otomatis (CI/CD pipeline atau **reconciliation loop**) untuk apply ke environment. |
| **Benefits** | Version control, kolaborasi, manajemen kode efisien. | Infrastructure & application as code, versioning konfigurasi, declarative management. Deployment lebih sederhana & konsisten. |
| **Focus Area** | Source code management. | Infrastructure & application deployment and management. |
| **Contoh** | GitHub, Bitbucket, GitLab. | Argo CD, Flux, Jenkins X, Weave Flux. |

**Kata kunci yang sering keluar di ujian:**
- GitOps single source of truth = **Git repository**.
- Deployment model GitOps = **push model** & **pull model** (yang direkomendasikan = **pull model**).
- Branching strategy untuk aplikasi monolitik dengan banyak parallel development & rilis = **Gitflow**.
- Tool deployment GitOps = **Argo CD**, **Flux**.

---

## 2. DOCKER

### 2.1 Dasar Container

- **Docker container bersifat ephemeral (sementara).** Data pada filesystem container akan **hilang** saat container dihapus.
- Masalahnya: kebanyakan aplikasi nyata bersifat **stateful** → perlu menyimpan data melebihi siklus hidup container (data persistence).

### 2.2 Storage Drivers

| Driver | Keterangan |
|--------|-----------|
| **overlay2** | Production-ready, pilihan utama Docker. Bekerja di sebagian besar environment. |
| **devicemapper** | Dulu dipakai untuk **RHEL & CentOS 7 ke bawah** yang tidak mendukung overlay2. Cocok untuk workload write-intensive. |
| **btrfs & zfs** | Write-intensive, mendukung snapshot, hanya bisa dipakai jika host memakai filesystem btrfs/zfs. |
| **vfs** | Hanya jika tidak ada copy-on-write filesystem. **Sangat lambat** → jangan dipakai di production. |

### 2.3 Mounting Volumes

- Mount direktori host yang berisi file ke volume container yang kosong → container bisa membaca file host (**pre-populate** data).
- Jika direktori tidak ada di host, Docker akan **membuatnya otomatis**.
- Jika volume/host sudah berisi file, Docker akan **obscure** (menyembunyikan) file asli selama volume ter-mount. File **tidak terhapus** → bisa dipulihkan dengan *unmount*.

### 2.4 Docker Logging

Log management sangat penting karena sifat ephemeral Docker (log hilang jika container dihapus). Gunakan **log driver** untuk meng-export dan mempersist log.

**Logging Drivers Docker:**

| Driver | Fungsi |
|--------|--------|
| `none` | Tidak ada log. |
| `local` | Disimpan lokal, format custom, overhead minimal. |
| `json-file` | Format JSON. **Default driver Docker.** |
| `syslog` | Kirim ke syslog. |
| `journald` | Pakai journald (bisa dibaca via command line). |
| `gelf` | Kirim ke Graylog Extended Log Format (Graylog/Logstash). |
| `fluentd` | Kirim ke Fluentd. |
| `awslogs` | Kirim ke AWS CloudWatch. |
| `splunk` | Kirim ke Splunk (HTTP Event Collector). |
| `etwlogs` | Event Tracing for Windows (Windows only). |
| `gcplogs` | Google Cloud Logging. |
| `logentries` | Rapid7 Logentries. |

### 2.5 Docker Monitoring

- **Prometheus** = tools monitoring open-source populer untuk cloud-native. Punya dimensional data model, query language **PromQL**, time-series database efisien, alerting modern, serta banyak exporter (VM & container).
- Tools tradisional lain: **Nagios**.

**Challenges container monitoring:**
1. Container berperilaku seperti mini VM padahal sebenarnya **proses di server** → sering salah dipetakan ke satu VM.
2. Container **temporary** → saat di-recreate, **IP berubah** → membingungkan monitoring tradisional.
3. Container di cluster bisa **berpindah node** → monitoring harus tahu lokasi container untuk scrape metrics.

**Docker Container Metrics:**
- **Container CPU** – jumlah CPU yang dipakai container.
- **Throttled CPU time** – total waktu CPU di-throttle.
- **Container memory fail counters** – berapa kali container minta memori melebihi alokasi.
- **Container memory usage** – jumlah memori terpakai (untuk set memory limit).
- **Container swap** – container yang memakai swap, bukan RAM (indikasi memory-hungry).
- **Container disk I/O** – profil disk container.
- **Container network metrics** – bandwidth & pola traffic (deteksi spike / DoS).

### 2.6 Docker Images

> **Docker image = blueprint container**, mendefinisikan paket, source code, dependency, dan library yang dibutuhkan.

- Image terdiri dari serangkaian langkah di atas **base OS image**: install package/dependency, copy source code, build binary, dst.
- Image disimpan di **container registry** (tempat terpusat untuk pull image).
- **Container Layers:**
  - Contoh: base layer (Ubuntu OS) → Python → Python app → saat dijalankan, **writable filesystem** menjadi layer terakhir.
  - Cek layer dengan `docker history <image>`.

### 2.7 Dockerfile Directives

**CMD vs ENTRYPOINT:**
- Setiap container punya default ENTRYPOINT → `/bin/sh -c`.
- Argumen di `CMD` di-append setelah ENTRYPOINT.
  - `CMD ["nginx", "-g", "daemon off;"]` → menjadi `/bin/sh -c nginx -g daemon off;`.
- Jika pakai ENTRYPOINT custom, argumen saat `docker run` di-append setelah ENTRYPOINT.
  - `ENTRYPOINT ["nginx", "-g"]` + `docker run nginx daemon off;` → hasil serupa.
- Bisa juga langsung: `ENTRYPOINT ["nginx", "-g", "daemon off;"]`.

**RUN vs CMD:**
- **RUN** → dijalankan saat **build** image, hanya memodifikasi filesystem saat build. Boleh banyak RUN (setiap RUN bikin layer baru).
- **CMD** → dieksekusi di **writable container layer** setelah container **running**. Jika ada lebih dari satu CMD, **hanya CMD terakhir** yang dipakai.
- CMD bisa di-override dengan custom command di `docker run`.

**ENTRYPOINT vs CMD:**

| | ENTRYPOINT | CMD |
|---|-----------|-----|
| Fungsi | Menentukan executable utama container. | Memberi argumen/default command; mudah di-override. |
| Override | Butuh `--entrypoint`. | Bisa langsung dioverride argumen `docker run`. |

### 2.8 Build & Manage Docker Images — Best Practices

1. **Layer yang jarang berubah ditaruh di awal**, layer yang sering berubah di akhir. (Install package/dependency dulu → copy source code belakangan.)
2. **Gabungkan banyak command** agar layer sesedikit mungkin. Hindari banyak `RUN` berurutan → gabung pakai `&&`.
3. **Hanya tambahkan file yang diperlukan.** Container tidak butuh package manager berat / Go toolkit jika binary sudah dikompilasi (gunakan multi-stage build).
4. **Prune image secara berkala.**
5. Gunakan **base image kecil** (misal Alpine) bila memungkinkan.
6. Gunakan **multi-stage builds** untuk menambah hanya library/dependency yang diperlukan, buang toolkit berat.
7. Gunakan **distroless** base image bila memungkinkan.
8. Gunakan **semantic versioning** pada tag; **hindari tag `latest`**.
9. **Health check** sebaiknya di-bake ke dalam image.

**Tentang tag & pull:**
- Image biasanya dibuild di **developer machine** atau **CI/CD pipeline** → disimpan di registry → dideploy ke staging & production (bisa pakai Docker atau orchestrator seperti Kubernetes).
- Docker **pull image sekali**; jika versi tertentu sudah ada di mesin, Docker **tidak pull ulang** kecuali diminta eksplisit.

### 2.9 Docker Registries

- **Docker registry** = server-side application stateless & highly scalable untuk **store & distribute Docker images**. Open source (Apache license) → semacam "distribution site" image.
- Isi registry: banyak **Docker repository**. Satu repository = beberapa versi image tertentu.
  - Contoh: semua versi image nginx disimpan di repository `nginx` di Docker Hub.
- Default registry publik Docker = **Docker Hub**.

### 2.10 Docker Architecture (Ringkas)

Komponen utama:
- **Docker Client** – tempat user menjalankan perintah (`docker ...`).
- **Docker Daemon (dockerd)** – menjalankan container, mengelola image, network, volume.
- **Docker Registry** – menyimpan/distribusi image (Docker Hub, private registry).
- **Container Runtime** – menjalankan container dari image.
- Alur: Client → Daemon → (pull image dari Registry) → jalankan Container.

---

## 3. KUBERNETES

### 3.1 Apa itu Kubernetes & Kenapa Butuh?

> **Kubernetes = open-source container orchestrator**, awalnya dikembangkan engineer Google, lalu di-open-source ke **Cloud Native Computing Foundation (CNCF)**.

- Kubernetes mengubah cara pandang infrastruktur: server diperlakukan sebagai entitas dengan container runtime terpasang → **apa pun bisa dijalankan di cluster server**.
- Tidak perlu merancang HA/DR untuk setiap aplikasi secara terpisah.

**Fungsi Kubernetes:**
1. **Centralized control plane** – API server mengekspos API untuk berbagai fungsi Kubernetes.
2. **Interaksi dengan container runtime** untuk scheduling container.
3. **Menyimpan konfigurasi** pada key-value store → **etcd**.
4. **Network abstraction layer & service discovery** antar container.
5. **Interaksi dengan cloud provider** – memesan load balancer, persistent disk, dll.

### 3.2 Kubernetes Architecture

Cluster Kubernetes terdiri dari kumpulan **node** dengan dua peran:
- **Control Plane Node** – "otak" cluster: scheduling workload, menerima request, mengontrol cluster.
- **Worker Node** – menjalankan workload/container.

**Komponen Control Plane:**

| Komponen | Analogi Kapal | Peran |
|----------|---------------|-------|
| **API Server** | — | Mengekspos API untuk interaksi internal & eksternal Kubernetes. |
| **Controller Manager** | Executive Officer | Memastikan perintah "kapten" (desired state) dijalankan di cluster. |
| **Cloud Controller Manager** | — | Opsional; dijalankan bila Kubernetes berjalan di public cloud (AWS, Azure, GCP). |
| **etcd** | Log book kapal | Key-value store tempat seluruh konfigurasi (desired state) disimpan. |
| **Scheduler** | Boatswain | Mengatur "loading/unloading" container ke node. |

**Komponen Worker Node:**

| Komponen | Analogi Kapal | Peran |
|----------|---------------|-------|
| **kubelet** | Seamen | Menjalankan/menghentikan container dengan berinteraksi ke container runtime, atas instruksi scheduler. |
| **kube-proxy** | — | Berjalan di tiap worker node; menyediakan jaringan agar container bisa berkomunikasi di dalam & luar cluster; mendukung konsep **Service**. |

### 3.3 Installing Kubernetes (Dev Environment)

- **Minikube** – cara cepat memulai cluster Kubernetes single-node untuk development.
- **KinD (Kubernetes in Docker)** – menjalankan cluster **multi-node** di satu server. Caranya: **container Docker dijadikan sebagai node Kubernetes**.
  - Contoh: cluster 4 node → KinD membuat 4 container yang berperilaku sebagai 4 node.
  - ⚠️ **KinD TIDAK untuk production** (hanya dev/testing).

### 3.4 Kubernetes Pods

> **Pod = building block dasar aplikasi Kubernetes.**

- Satu pod berisi **satu atau lebih container**, dan semua container dalam pod **selalu di-schedule di host/node yang sama**.
- Umumnya satu pod = satu container, tetapi ada use case multi-container.
- Kubernetes memilih konsep **pod** (bukan container langsung) karena pod menyediakan shared context (network, storage) bagi container di dalamnya.

### 3.5 ReplicaSet

- **ReplicaSet** = resource Kubernetes untuk menjalankan **beberapa replica Pod** sekaligus.
- Memberikan **horizontal scaling** untuk workload container.
- Menentukan jumlah replica Pod; **controller** mempertahankan jumlah itu dan **membuat ulang Pod** jika ada yang mati.

### 3.6 Kubernetes Deployments

- **Deployment** mengelola deployment aplikasi container; biasanya untuk **stateless workloads**.
- Untuk aplikasi **stateful**, yang direkomendasikan adalah **StatefulSet** (bukan Deployment).

**Deployment Strategies:**

| Strategi | Keterangan |
|----------|-----------|
| **Recreate** | Hentikan semua instance lama, baru jalankan yang baru. Ada downtime. |
| **Rolling Update** | Ganti instance secara bertahap → zero/near-zero downtime. **Default**. |
| Ramped slow rollout | Rollout bertahap secara lambat. |
| Best-effort controlled rollout | Rollout dengan kontrol best-effort. |

> Update Deployment = tentukan **image container baru**. Karena itu image selalu dibuat berversi → memungkinkan roll out & roll back.

### 3.7 Kubernetes Services & Ingresses

**Kenapa butuh Service:**
- Pod bersifat transient → tidak bisa dijadikan endpoint dengan IP/hostname tetap (jika Pod dihancurkan, koneksi putus).
- Karena itu, **mengekspos Pod secara langsung bukan ide baik** → gunakan **Service**.

**Jenis Service untuk expose container:**

| Tipe | Kegunaan |
|------|----------|
| **ClusterIP** | Default. Hanya bisa diakses di dalam cluster. **Best practice mulai dari ClusterIP**, ubah tipe jika perlu. |
| **NodePort** | Mengekspos Service pada port di setiap node (dapat diakses dari luar via IP node). |
| **LoadBalancer** | Membuat load balancer eksternal (biasanya via cloud provider). |

**Ingress:**
- **Ingress = reverse proxy** ke dalam Kubernetes.
- Tidak perlu load balancer untuk setiap aplikasi; LB hanya meneruskan traffic & tidak butuh komputasi tinggi → tidak masuk akal bikin LB untuk semuanya.
- **Routing dalam Ingress:** *Simple*, *Path-based*, *Name-based*, *Complex*.
- **Workload Ingress:** utamanya **HTTP** (dan terkait TCP).

### 3.8 Dynamic Volume Provisioning

Resource terkait:
- **StorageClass** – mendefinisikan cara provisioning dinamis.
- **PersistentVolume (PV)** – resource storage.
- **PersistentVolumeClaim (PVC)** – permintaan/klaim storage oleh aplikasi.

**Untuk membuat dynamic volume provisioning**, definisikan: **StorageClass + PersistentVolumeClaim**.

### 3.9 Probes (Health Checks)

| Probe | Fungsi |
|-------|--------|
| **Startup probe** | Memeriksa apakah aplikasi sudah selesai startup. |
| **Liveness probe** | Memeriksa apakah aplikasi masih hidup; jika gagal → Pod di-restart. |
| **Readiness probe** | Memeriksa apakah aplikasi siap menerima traffic. |

> Kombinasi **Liveness + Readiness probe** memastikan aplikasi tetap andal meskipun ada masalah **intermittent** (kadang-kadang gagal).

### 3.10 Multi-container Patterns

| Pattern | Peran |
|---------|-------|
| **Sidecar** | Container pendamping yang memperluas/melengkapi container utama. |
| **Ambassador** | **Forward proxy** – container utama mengakses dunia luar lewat proxy ini. |
| **Adapter** | Menyesuaikan/menormalisasi output container utama ke format yang diharapkan. |
| **Init containers** | Container yang berjalan **sebelum** container utama (untuk setup/persiapan). |

### 3.11 Horizontal Scaling Parameters

Agar horizontal scaling lebih bermakna, gunakan parameter:
- **CPU**
- **Memory**
- **External metrics** (misal response time)

### 3.12 Port-forwarding Use Cases

`kubectl port-forward` berguna untuk:
- Troubleshooting Pod yang bermasalah.
- Mengakses system service seperti **Kubernetes dashboard**.
- (Bukan untuk mengekspos Service ke internet → itu tugas LoadBalancer/Ingress.)

---

## 4. SOAL LATIHAN & JAWABAN

### Session 02 — GitOps

1. **Pernyataan yang benar tentang Git (pilih 3):** It is a distributed SCM platform · It allows multiple developers to collaborate · It has commits and branches.
2. **`git checkout` artinya:** *Switch from one branch to another* (juga bisa restore file).
3. **GitOps single source of truth:** **The Git repository**.
4. **Deployment model GitOps (pilih 2):** Push model & Pull model.
5. **Branching strategy untuk monolitik dengan banyak parallel development & rilis:** **Gitflow**.
6. **Deployment model yang direkomendasikan GitOps:** **Pull model**.

### Session 03 — Docker I

1. *"You should use overlay2 for CentOS and RHEL 7 and below"* → **False** (yang benar devicemapper).
2. **Pernyataan benar (pilih 4):** tmpfs mounts use system memory · You can use bind mounts to mount host files to containers · You can use volume mounts for a multi-instance active-active configuration · (volumes umumnya menambah IOPS).
3. *"Changing the storage driver removes existing containers"* → **False**.
4. *"devicemapper is a better option than overlay2 for write-intensive containers"* → **True**.
5. **Logging drivers didukung Docker (pilih 4):** journald · Splunk · JSON files · Syslog.
6. *"Docker Compose is an imperative approach"* → umumnya dianggap **False** (Compose bersifat **declarative**).
7. **`docker run` configuration benar (pilih 3):** `docker run nginx` · `docker run --name nginx nginx:1.17.3` · `docker run -d --name nginx nginx`.

### Session 04 — Docker II

1. *"Docker images use a layered model"* → **True**.
2. *"You can delete an image if a container using it is running"* → **False**.
3. **Cara menghapus running container (pilih 2):** `docker rm -f <container_id>` · `docker stop <container_id> && docker rm <container_id>`.
4. **Build best practices (pilih 4):** Layer yang jarang berubah di awal · Gabungkan step untuk mengurangi layer · Hanya pakai file yang diperlukan · Gunakan semantic versioning, hindari tag `latest`.
5. *"Always flatten Docker images to a single layer"* → **False**.
6. *"A distroless container contains a shell"* → **False**.
7. **Cara meningkatkan efisiensi container (pilih 4):** Pakai base image kecil (Alpine) · Multi-stage builds · Distroless base image · Flatten image.
8. *"Best practice prune Docker images"* → **True**.
9. *"Health checks should always be baked into image"* → **True**.

### Session 05 — Kubernetes I

1. **Semua komunikasi dengan Kubernetes via:** **API server**.
2. **Memastikan cluster dalam desired state:** **Controller Manager**.
3. **Menyimpan desired state cluster:** **Etcd**.
4. *"A pod can contain more than one container"* → **True**.
5. **Port-forwarding use cases:** Troubleshooting Pod · Mengakses system service (dashboard).
6. **Kombinasi probe untuk aplikasi dengan masalah intermittent (pilih 2):** **Liveness probe + Readiness probe**.
7. *"We may use KinD in production"* → **False**.
8. **Multi-container pattern sebagai forward proxy:** **Ambassador**.

### Session 06 — Kubernetes II

1. *"A Deployment deletes old ReplicaSet when image is updated"* → umumnya **False** (ReplicaSet lama tetap disimpan untuk rollback; hanya jumlah replica-nya dikurangi).
2. **Deployment strategies Kubernetes:** Recreate · Rolling update · Ramped slow rollout · Best-effort controlled rollout.
3. **Resource untuk expose container eksternal:** NodePort Service · LoadBalancer Service · Ingress.
4. *"Best practice start with ClusterIP, change later"* → **True**.
5. *"Deployment resources suitable for stateful workloads"* → **False** (stateful → StatefulSet).
6. **Workload Ingress:** HTTP (dan TCP terkait).
7. **Resource untuk dynamic volume provisioning:** **StorageClass** + **PersistentVolumeClaim**.
8. **Parameter untuk scaling Pod:** CPU · Memory · External metrics.
9. **Routing dalam Ingress:** Simple · Path-based · Name-based · Complex.

---

## 5. CHEAT SHEET PERINTAH

### Git

```bash
git init                       # buat repository baru
git clone <url>                # clone repository
git status                     # lihat status
git add <file>                 # stage perubahan
git commit -m "pesan"          # commit
git push origin <branch>       # push ke remote
git pull                       # ambil & merge dari remote
git checkout <branch>          # pindah branch / restore file
git branch                     # lihat cabang
git merge <branch>             # merge cabang
```

### Docker

```bash
docker build -t <nama>:<tag> .         # build image dari Dockerfile
docker images                          # list image
docker history <image>                 # lihat layer image
docker run -d --name <nama> <image>    # jalankan container (detached)
docker run --name web nginx:1.17.3     # run dengan tag versi
docker ps                              # list container berjalan
docker ps -a                           # list semua container
docker stop <container_id>             # hentikan container
docker rm <container_id>               # hapus container
docker rm -f <container_id>            # paksa hapus container berjalan
docker rmi <image>                     # hapus image
docker pull <image>                    # pull image
docker push <image>                    # push image ke registry
docker system prune                    # bersihkan image/container tak terpakai
```

### Kubernetes (kubectl)

```bash
kubectl get nodes                      # lihat node cluster
kubectl get pods                       # lihat pod
kubectl get deployments                # lihat deployment
kubectl get svc                        # lihat service
kubectl get ingress                    # lihat ingress
kubectl apply -f <manifest>.yaml       # buat/update resource
kubectl delete -f <manifest>.yaml      # hapus resource
kubectl describe pod <pod>             # detail pod
kubectl logs <pod>                     # lihat log pod
kubectl exec -it <pod> -- /bin/sh      # masuk ke dalam pod
kubectl port-forward <pod> <l>:<r>     # port-forward untuk troubleshooting
kubectl scale deployment <name> --replicas=3   # scaling
```

---

## 6. KATA KUNCI CEPAT (Hafalan)

- **GitOps** → Git = single source of truth; prinsip: declarative, versioned, auto-apply, self-healing; model: **pull** (direkomendasikan) & push.
- **Docker** → image = blueprint; container = ephemeral; storagedriver: **overlay2** (default prod), devicemapper (RHEL/CentOS7); log default: **json-file**; registry default: **Docker Hub**; `RUN` = build, `CMD`/`ENTRYPOINT` = runtime.
- **Kubernetes** → orchestrator; node = control plane + worker; komponen: **API server, controller manager, cloud controller manager, etcd, scheduler, kubelet, kube-proxy**; pod = unit terkecil; **ReplicaSet** = scaling; **Deployment** = stateless; **StatefulSet** = stateful; Service: **ClusterIP / NodePort / LoadBalancer**; **Ingress** = reverse proxy; probes: **startup / liveness / readiness**.
