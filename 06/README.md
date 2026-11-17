# Materi Minggu 6: Distributed File System

### Identitas Mahasiswa
* **Nama:** Naisyah Izzatul Jannah K.
* **NIM:** 255410040
* **Prodi:** Informatika, Fakultas Teknologi Informasi, UTDI
* **Mata kuliah:** Sistem Terdistribusi dan Terdesentralisasi (FIF25002T, 3 SKS)
* **Dosen:** Dr. Bambang Purnomosidi D. P.
  
---

AI yang digunakan: Claude (Anthropic), model Claude Sonnet 5.5

Prompt: "Buatkan rangkuman materi dari file PDF slide kuliah 'Distributed File System' sesuai urutan agenda pembahasan, dalam format markdown untuk README.md." *(ganti dengan prompt yang benar-benar Anda tulis)*

Bagian yang disesuaikan dari keluaran prompt ada pada: *(isi sendiri)*

---

# Distributed File System

## Tujuan Pembelajaran

- Menjelaskan file system (FS), jenis, dan cara kerjanya.
- Menjelaskan distributed file system (DFS), jenis, dan cara kerjanya.
- Melakukan instalasi salah satu DFS (praktik HDFS).
- Menjelaskan cloud storage dan hubungannya dengan DFS.
- Melakukan instalasi salah satu object storage sebagai cloud storage.

## 1. Pengertian File System

Metode yang dipakai sistem operasi untuk mengelola dan mengatur file pada media penyimpan (harddisk, flashdisk, SSD, dan sebagainya). Disingkat FS.

## 2. Berbagai File System

| Sistem operasi | File system |
|---|---|
| Linux | ext4, XFS, btrfs, ReiserFS, dan lain-lain |
| DOS/Windows | FAT16, FAT32, NTFS |
| macOS | APFS (Apple File System) |

## 3. Cara Kerja File System

- FS diakses melalui **driver file system** yang dikelola kernel. Kernel menyediakan **virtual file system** untuk aplikasi di user space.
- Alur: Aplikasi → System Call Interface → virtual FS → driver kernel → hardware (media penyimpan).
- **FS berbeda dengan hirarki FS.** Hirarki FS adalah pengaturan file dan direktori di OS (misalnya `/bin`, `/etc`, `/home`, `/usr`, `/var` menurut Filesystem Hierarchy Standard). Pengguna hanya melihat hirarki, tanpa peduli FS di baliknya.

## 4. Pengertian Distributed File System (DFS)

- DFS bukan sekadar "FS yang tersebar di lebih dari satu mesin". Secara konseptual, DFS adalah abstraksi terkoordinasi dari sekumpulan media penyimpan yang menutupi heterogenitas, kegagalan, dan konkurensi, sehingga menjadi jaringan penyimpanan yang terintegrasi, andal, dan berkinerja tinggi.
- Seperti sistem terdistribusi lain, komponennya banyak, tetapi pengguna melihatnya sebagai satu media penyimpan.

### Perbandingan DAS, NAS, SAN, dan DFS

| Istilah | Penjelasan |
|---|---|
| **DAS** (Direct-Attached Storage) | Penyimpanan terpasang langsung ke satu mesin (contoh: disk NVMe lokal yang terlihat lewat `df -h`). |
| **NAS** (Network-Attached Storage) | Penyimpanan bersama lewat jaringan bersama, menyajikan file system, manajemen lebih mudah. |
| **SAN** (Storage Area Network) | Penyimpanan bersama lewat jaringan khusus, berupa block storage; cepat tetapi mahal. Server memformatnya dengan FS sendiri. |
| **DFS** | Abstraksi level software yang mengelola data sebagai file dan direktori yang tersebar di banyak node. |

Catatan dari slide: SAN secara konsep bisa menjadi storage DFS meskipun tidak disarankan; penggunaan umum SAN adalah backend database besar, video/audio editing, dan virtualisasi skala besar. DFS lebih mengarah ke DAS di setiap node.

## 5. Berbagai DFS yang Umum

- Google File System
- Colossus (pengganti GFS; dipakai di YouTube, Gmail, Google Docs, dll.)
- CephFS
- Gluster
- HDFS (Hadoop Distributed File System)
- Lustre

## 6. Cara Kerja DFS

DFS menyediakan **global namespace** yang menyatukan beberapa namespace dari berbagai sumber (NAS di data center, Linux NFS dan Windows SMB di lokasi remote, public cloud), sehingga pengguna dan aplikasi mengakses data lewat satu tampilan terpadu.

## 7. Studi Kasus: HDFS

- FS open source, scalable, terdistribusi, dan fault-tolerant untuk menyimpan dataset besar di klaster *commodity hardware* (perangkat keras massal, murah, mudah dipertukarkan).
- Komponen utama Apache Hadoop; file dibagi menjadi **blok**, didistribusikan ke beberapa server, dan **direplikasi** untuk high availability.
- Apache Hadoop 3.5.0 dikompilasi dengan JDK 17; server berjalan di Java 17, client di Java 17 atau 21.

**Arsitektur:**

- **NameNode** (master): menyimpan metadata (nama file, jumlah replika, lokasi blok).
- **DataNode** (slave): menyimpan blok data; tersebar di beberapa rack.
- Alur: client melakukan *metadata ops* ke NameNode; *read* dan *write* data langsung ke DataNode; blok direplikasi antar DataNode (antar rack).
- Pada ekosistem Hadoop juga ada Resource Manager dan Node Manager (untuk Map dan Reduce).

**Praktik:** ikuti dokumen "Mulai Menggunakan Apache Hadoop - HDFS" di EL-UTDI. Persyaratan: JDK 17/21, Apache Hadoop 3.5.0, `pdsh`, `ssh`, dan `sshd` yang sudah berjalan.

## 8. Pengertian Cloud Storage

- Layanan online untuk menyimpan dan mengakses data di server jarak jauh lewat internet, bukan di perangkat lokal.
- Model penyimpanan yang dikelola secara remote dalam *pool* logis. Penyimpanan fisik mencakup banyak server (kadang banyak lokasi) yang dimiliki dan dikelola penyedia cloud.
- Contoh: Amazon S3, Azure Blob Storage, Google Cloud Storage (GCS).

## 9. Relasi Cloud Storage dengan DFS

Cloud storage adalah **tempat penyimpanan data**, sedangkan **pengorganisasiannya** dilakukan oleh DFS. Contoh: S3 (cloud storage) bisa dipakai oleh HDFS (yang melakukan operasi FS) lewat konfigurasi `core-site.xml`, antara lain properti `fs.s3a.access.key`, `fs.s3a.secret.key`, dan `fs.s3a.endpoint`.

## 10. Tipe Cloud Storage

| Tipe | Transport | Interface | Kegunaan |
|---|---|---|---|
| Block storage | FC atau iSCSI | Direct attached atau SAN | Latensi rendah, cocok untuk data terstruktur |
| File storage | TCP/IP | NFS, SMB | Performa baik, berbagi file, global file locking |
| Object storage | TCP/IP | HTTP, REST | Scaling mudah tanpa batas, diakses lintas LAN dan WAN |

## 11. Studi Kasus Cloud Storage

- **Ceph**: sistem penyimpanan terdistribusi open source (dukungan file, block, object).
- **CubeFS**: storage cloud-native open source dari CNCF; mendukung protokol S3, HDFS, dan POSIX. Arsitektur: Master, Meta Node (metadata subsystem), Data Node dan Blob Node (data subsystem, termasuk erasure code), serta Object Node (object subsystem).
- **RustFS**: object storage yang diposisikan sebagai pengganti MinIO dan S3.
- **Silo (PGSTY)**: fork MinIO yang kompatibel dengan S3, dipelihara oleh PGSTY.
- **Rook**: storage cloud-native open source untuk Kubernetes (mengelola file, block, dan object storage).
