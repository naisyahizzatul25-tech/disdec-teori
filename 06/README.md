# Materi Minggu 6: Distributed File System

### Identitas Mahasiswa
* **Nama:** Naisyah Izzatul Jannah K.
* **NIM:** 255410040
* **Prodi:** Informatika, Fakultas Teknologi Informasi, UTDI
* **Mata kuliah:** Sistem Terdistribusi dan Terdesentralisasi (FIF25002T, 3 SKS)
* **Dosen:** Dr. Bambang Purnomosidi D. P.

---

# Distributed File System

## 1. Pengertian & Jenis File System
**File System (FS)** merupakan metode yang digunakan oleh sistem operasi untuk mengatur dan mengelola file pada media penyimpanan seperti harddisk, flashdisk, maupun SSD.

Contoh file system berdasarkan sistem operasinya:
* **Linux:** ext4, XFS, btrfs, ReiserFS.
* **DOS/Windows:** FAT16, FAT32, NTFS.
* **macOS:** APFS (Apple File System).

## 2. Cara Kerja File System
* FS diakses lewat *driver file system* yang dikelola oleh kernel. Kernel menyediakan *Virtual File System* (VFS) agar bisa diakses oleh aplikasi di *user space*.
* **Alur akses data:** Aplikasi → System Call Interface → Virtual FS → Driver Kernel → Hardware (media penyimpan).
* **FS vs Hirarki FS:** Keduanya berbeda. Hirarki FS adalah pengaturan struktur folder dan file di OS (contohnya `/bin`, `/etc`, `/home`, `/usr`, `/var` sesuai standar FHS. Pengguna cukup melihat hirarki ini tanpa perlu memikirkan jenis FS di baliknya[cite: 4].

## 3. Pengertian Distributed File System (DFS)
* DFS bukan sekadar file system yang tersebar di lebih dari satu mesin. Secara konsep, DFS adalah sistem abstraksi terkoordinasi dari sekumpulan media penyimpan yang menutupi perbedaan hardware (heterogenitas), kegagalan sistem, dan konkurensi
* Hasilnya adalah jaringan penyimpanan yang terintegrasi, andal, dan berkinerja tinggi, di mana banyak media penyimpanan fisik terlihat seperti **satu media penyimpan tunggal** di sisi pengguna
  
**Perbedaan Arsitektur Penyimpanan:**
* **DAS (Direct-Attached Storage):** Penyimpanan yang terpasang langsung ke satu mesin lokal (misalnya SSD NVMe di laptop yang dicek pakai perintah `df -h`)
* **NAS (Network-Attached Storage):** Penyimpanan bersama lewat jaringan yang menyajikan layanan tingkat *file system* dengan manajemen yang lebih mudah
* **SAN (Storage Area Network):** Penyimpanan bersama lewat jaringan khusus berkecepatan tinggi dalam bentuk *block storage* (cepat namun mahal, server harus memformatnya dengan FS sendiri)
* **DFS:** Abstraksi di level *software* yang mengelola data menjadi file dan direktori yang tersebar di banyak node (umumnya memanfaatkan DAS di tiap node)

## 4. Contoh & Cara Kerja DFS
* **Contoh DFS populer:** Google File System (GFS), Colossus (pengganti GFS untuk YouTube, Gmail, Google Docs), CephFS, Gluster, HDFS, dan Lustre
* **Cara Kerja:** DFS menyediakan **Global Namespace** yang menggabungkan berbagai *namespace* dari sumber berbeda (misalnya NAS di data center, Linux NFS, Windows SMB, hingga public cloud) menjadi satu tampilan terpadu bagi pengguna dan aplikasi
  
## 5. Studi Kasus: HDFS (Hadoop Distributed File System)
* HDFS adalah DFS *open-source*, *scalable*, dan *fault-tolerant* yang dirancang khusus untuk menyimpan dataset berukuran sangat besar di klaster *commodity hardware* (perangkat keras massal yang murah dan mudah diganti)
* File dipecah menjadi blok-blok kecil, didistribusikan ke banyak server, lalu direplikasi demi menjamin *high-availability*
  
* **Arsitektur Utama HDFS:**
  * **NameNode (Master):** Bertugas menyimpan metadata file (nama file, jumlah replika, dan lokasi blok)
  * **DataNode (Slave):** Bertugas menyimpan blok data fisik yang tersebar di berbagai *rack*
  * **Alur Operasi:** Client menghubungi NameNode untuk urusan metadata, lalu melakukan baca/tulis (*read/write*) data secara langsung ke DataNode[cite: 15]. Blok data otomatis direplikasi antar DataNode lintas *rack*
* **Catatan Praktik:** Mengikuti panduan *"Mulai Menggunakan Apache Hadoop - HDFS"* menggunakan Apache Hadoop 3.5.0, JDK 17/21, `pdsh`, serta layanan `ssh`/`sshd` yang aktif
  
## 6. Cloud Storage & Relasinya dengan DFS
* **Cloud Storage:** Layanan penyimpanan online yang dikelola secara remote dalam satu *pool* logis melalui internet (contoh: Amazon S3, Azure Blob Storage, Google Cloud Storage)
* **Hubungan dengan DFS:** Cloud storage berperan sebagai tempat penyimpanan fisik/objeknya, sedangkan DFS bertugas mengatur pengorganisasian sistem filenya. Contohnya, HDFS dapat menggunakan Amazon S3 sebagai penyimpanannya melalui pengaturan pada file `core-site.xml` (`fs.s3a.access.key`, `fs.s3a.secret.key`, dan `fs.s3a.endpoint`)

## 7. Tipe Cloud Storage & Studi Kasus
**Tiga Tipe Utama Cloud Storage:**
* **Block Storage:** Menggunakan jalur FC atau iSCSI dengan antarmuka Direct Attached atau SAN Sangat rendah latensi sehingga cocok untuk database dan data terstruktur
* **File Storage:** Berjalan di atas TCP/IP menggunakan protokol NFS atau SMB. Cocok untuk berbagi file bersama dan *global file locking*
* **Object Storage:** Berjalan di atas TCP/IP menggunakan antarmuka HTTP/REST. Sangat mudah di-*scale* tanpa batas dan bisa diakses lintas LAN maupun WAN

**Studi Kasus Implementasi Cloud Storage:**
* **Ceph:** Sistem penyimpanan terdistribusi *open-source* yang mendukung file, block, dan object storage sekaligus
* **CubeFS:** Penyimpanan *cloud-native* dari CNCF yang mendukung protokol S3, HDFS, dan POSIX (terdiri dari komponen Master, Meta Node, Data/Blob Node, dan Object Node)
* **RustFS:** *Object storage* berperforma tinggi yang dirancang sebagai pengganti MinIO dan S3
* **Silo (PGSTY):** *Fork* komunitas dari MinIO yang kompatibel dengan API S3
* **Rook:** Orkestrator penyimpanan *cloud-native* *open-source* khusus untuk lingkungan Kubernetes

  
