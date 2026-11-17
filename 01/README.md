
# Materi Minggu 1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi
### Identitas Mahasiswa
* **Nama:** Naisyah Izzatul Jannah K.
* **NIM:** 255410040
* **Prodi:** Informatika, Fakultas Teknologi Informasi, UTDI
* **Mata kuliah:** Sistem Terdistribusi dan Terdesentralisasi (FIF25002T, 3 SKS)
* **Dosen:** Dr. Bambang Purnomosidi D. P.

---

## Pernyataan Penggunaan AI

**AI yang digunakan:** Claude (Anthropic), model Claude Sonnet 5.5

**Prompt:**
> Dari file PDF tersebut buatkan rangkuman materi dalam format markdown untuk README.md.

**Bagian yang disesuaikan dari keluaran prompt ada pada:**
> _(Yang diubah hanya menghapus bagian tujuan, diskusi, dan ringkasan saja)_

---

## Daftar Isi

1. [Arti Penting](#1-arti-penting-sistem-terdistribusi-dan-terdesentralisasi)
2. [Pengertian Sistem Terdistribusi](#2-pengertian-sistem-terdistribusi)
3. [Arsitektur Sistem Dasar](#3-arsitektur-sistem-dasar)
4. [Karakteristik Sistem Terdistribusi](#4-karakteristik-sistem-terdistribusi)
5. [Pengertian Sistem Terdesentralisasi](#5-pengertian-sistem-terdesentralisasi)
6. [Terdistribusi vs Terdesentralisasi](#6-sistem-terdistribusi-vs-sistem-terdesentralisasi)
7. [Studi Kasus Sistem Terdistribusi](#7-studi-kasus-sistem-terdistribusi)
8. [Studi Kasus Sistem Terdesentralisasi](#8-studi-kasus-sistem-terdesentralisasi)
9. [CAP Theorem](#9-cap-theorem)
10. [Konsistensi Data](#10-konsistensi-data)
11. [Tantangan](#11-tantangan)
12. [Tren Masa Depan](#12-tren-masa-depan)
---

## 1. Arti Penting Sistem Terdistribusi dan Terdesentralisasi

Layanan digital modern tidak mungkin berjalan di satu mesin saja. Alasannya:

- Pertumbuhan data dan jumlah pengguna sangat besar.
- Layanan modern memerlukan **skalabilitas**.
- Sistem harus **fault-tolerant**.
- Performa dan ketersediaan harus terjaga 24/7.

Tanpa sistem terdistribusi dan terdesentralisasi, layanan seperti Google, Netflix, dan Blockchain tidak mungkin ada.

**Catatan istilah:**

- **Skalabilitas**: kemampuan sistem, jaringan, proses, atau organisasi untuk berkembang dan menangani peningkatan beban kerja, pengguna, atau permintaan tanpa mengorbankan kinerja atau kualitas.
- **Sistem fault-tolerant**: sistem yang tetap beroperasi normal meskipun sebagian komponennya (hardware maupun software) gagal atau mengalami malfungsi.

## 2. Pengertian Sistem Terdistribusi

> *"A collection of independent computers that appears to its users as a single coherent system"*
> (Andrew S. Tanenbaum dan Maarten van Steen, 2007)

Ciri utama:

- Komponen bersifat **independen**.
- Berkomunikasi melalui **jaringan**.
- Memberikan **transparansi**: lokasi, akses, replikasi, dan kegagalan (pengguna tidak perlu tahu di mana data berada, bagaimana diakses, berapa salinannya, atau bahwa ada komponen yang sedang gagal).

Contoh arsitektur terdistribusi (microservices e-commerce): aplikasi mobile dan browser mengakses **API Gateway** dan **Storefront WebApp**, yang memanggil layanan terpisah (*Account*, *Inventory*, *Shipping Service*) dengan basis data masing-masing melalui REST API.

## 3. Arsitektur Sistem Dasar

| Arsitektur | Penjelasan | Contoh |
|---|---|---|
| **Client-Server** | Banyak client terhubung melalui internet ke server yang menyediakan layanan. Server menjadi pusat layanan. | Web, aplikasi mobile |
| **Peer-to-Peer (P2P)** | Setiap node setara (bisa menjadi client sekaligus server) dan saling terhubung langsung. | BitTorrent, Bitcoin, Ethereum, dan kripto lain |
| **Hybrid** | Gabungan: ada *supernode* yang mengoordinasikan sekelompok node, sementara pertukaran data dapat dilakukan secara P2P. | WhatsApp, Signal, Telegram (sesuai ilustrasi di slide) |

## 4. Karakteristik Sistem Terdistribusi

- **Transparansi lokasi**: pengguna tidak perlu tahu lokasi fisik sumber daya.
- **Skalabilitas**: mampu menangani beban yang bertambah.
- **Fault tolerance**: tetap berjalan walau ada komponen gagal.
- **Kinerja tinggi**: beban dibagi ke banyak node.
- **Heterogenitas**: node dapat berbeda hardware, OS, dan jaringan.

## 5. Pengertian Sistem Terdesentralisasi

- **Sentralisasi**: satu titik kontrol.
- **Desentralisasi**: kontrol didistribusikan, **tidak ada single point of failure**.
- Keputusan diambil secara kolektif melalui **konsensus**.
- Contoh: Blockchain, DNS, BitTorrent.

**Tiga topologi jaringan (Baran, 1964):**

| Tipe | Gambaran |
|---|---|
| **Centralized (A)** | Semua station terhubung ke satu pusat. Jika pusat gagal, seluruh jaringan lumpuh. |
| **Decentralized (B)** | Ada beberapa hub; tiap hub melayani sekelompok station. |
| **Distributed (C)** | Jaringan mesh; tiap node terhubung ke beberapa node lain, tanpa hub dominan. |

## 6. Sistem Terdistribusi vs Sistem Terdesentralisasi

| Aspek | Terdistribusi | Terdesentralisasi |
|---|---|---|
| Fokus | Pembagian **beban kerja** | Pembagian **kontrol** |
| Contoh | Google File System, MinIO Object Storage (kini sudah di-fork ke Silo) | Blockchain |

Keduanya saling terkait tetapi berbeda fokus. Sebuah sistem bisa terdistribusi namun tetap dikendalikan satu organisasi (terpusat kontrolnya).

## 7. Studi Kasus Sistem Terdistribusi

- **Google File System / HDFS**: penyimpanan data berskala besar.
- **Apache Kafka dan Apache Pulsar**: messaging dan streaming.
- **Netflix, Twitch, YouTube**: streaming video.
- **Instagram, Threads, Facebook, TikTok**: media sosial.

## 8. Studi Kasus Sistem Terdesentralisasi

- **Blockchain**: Bitcoin, Ethereum, Algorand, Sui, Near, Aptos, dan lainnya.
- **DNS (Domain Name System)**.
- **BitTorrent**.

## 9. CAP Theorem

Disebut juga **Brewer's Theorem** (Eric Brewer, tahun 2000).

| Huruf | Properti | Arti |
|---|---|---|
| **C** | Consistency | Setiap operasi baca mengembalikan data hasil penulisan terbaru, atau error. |
| **A** | Availability | Setiap permintaan ke node yang tidak gagal mendapat respons non-error, meskipun bukan data terbaru. |
| **P** | Partition Tolerance | Sistem tetap berfungsi walau komunikasi antar node terputus atau terfragmentasi. |

**Inti teorema:** pada *distributed data store*, Consistency dan Availability tidak dapat dipenuhi sekaligus karena partisi jaringan pasti bisa terjadi. Ketika terjadi partisi, perancang sistem harus memilih salah satu (trade-off), meskipun sistem dapat pulih otomatis (*auto healing*) setelah partisi selesai:

- **AP (Availability + Partition Tolerance)**: permintaan data selalu dilayani, dengan risiko data tidak sinkron (mengorbankan konsistensi). Contoh: **MongoDB**.
- **CP (Consistency + Partition Tolerance)**: permintaan baca maupun tulis ditolak untuk menjaga data tetap konsisten (mengorbankan availability). Contoh: **Apache Cassandra** (sebagaimana disebut di slide).

## 10. Konsistensi Data

- **Strong consistency**: hasil selalu sama di semua node.
- **Eventual consistency**: data awalnya mungkin berbeda, tetapi akhirnya menjadi konsisten.

| Kebutuhan | Contoh |
|---|---|
| Strong consistency | Bank (saldo harus akurat setiap saat) |
| Eventual consistency | Media sosial (jumlah like boleh terlambat sinkron) |

## 11. Tantangan

- **Latency dan throughput**
  - Latency: seberapa lama suatu permintaan diproses.
  - Throughput: seberapa banyak permintaan yang diproses.
- **Keamanan**: DDoS, Sybil attack.
- **Kompleksitas manajemen node**.
- **Governance (tata kelola)** pada sistem terdesentralisasi.

**Sybil Attack:** serangan siber dengan membuat banyak identitas palsu (akun, node, atau pseudonim, hingga ribuan) sekaligus untuk mendominasi pengaruh, suara, atau reputasi dalam jaringan. Umumnya menarget sistem terdesentralisasi seperti P2P atau blockchain. Dengan menyamar sebagai banyak pengguna sah, penyerang dapat memanipulasi sistem, merusak integritasnya, melakukan tindakan tidak sah, dan mengakses informasi sensitif. Nama "Sybil" berasal dari nama perempuan dengan *identity disorder* dalam buku tahun 1973.

## 12. Tren Masa Depan

- **Cloud-native**: Kubernetes, Docker.
- **Edge dan Fog Computing**.
- **Web3 dan DeFi**.
- **Federated Learning** (AI terdistribusi).

---

**Sumber:** Slide kuliah "Pengenalan Sistem Terdistribusi dan Terdesentralisasi", Dr. Bambang Purnomosidi D. P., UTDI.
