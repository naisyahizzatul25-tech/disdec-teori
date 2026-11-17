
# Materi Minggu 3: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

AI yang digunakan: Claude (Anthropic), model Claude Sonnet 5.5

Prompt: "Buatkan rangkuman materi dari file PDF slide kuliah 'Sinkronisasi dan Koordinasi pada Sistem Terdistribusi' sesuai urutan agenda pembahasan, dalam format markdown untuk README.md." *(ganti dengan prompt yang benar-benar Anda tulis)*

Bagian yang disesuaikan dari keluaran prompt ada pada: *(isi sendiri)*

---

# Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

## Tujuan Pembelajaran

- Menjelaskan mengapa sinkronisasi diperlukan untuk koordinasi dan tantangannya.
- Menjelaskan sinkronisasi waktu fisik beserta algoritma dan protokolnya.
- Menjelaskan sinkronisasi waktu logis.
- Menjelaskan masalah mutual exclusion dan algoritmanya.
- Menjelaskan pemilihan koordinator dan contoh kasus industri.

## 1. Apa Itu Sinkronisasi?

Koordinasi proses, data, dan sumber daya di berbagai node agar operasi konsisten dan andal. Sinkronisasi menjaga konsistensi data, mengelola proses konkuren, dan mencegah data race.

**Contoh race condition (e-commerce):**

- Stok produk X = 10. User A dan B sama-sama membaca stok = 10, lalu masing-masing membeli 1.
- Proses A menulis stok = 9, proses B juga menulis stok = 9.
- Realitas: 2 pembelian, seharusnya stok = 8. Tercatat: stok = 9.

**Mengapa penting:**

- Integritas data (mencegah data race dan race condition).
- Sinkronisasi state di semua node (contoh: posisi pemain di game online, keranjang belanja lintas perangkat).
- Koordinasi task dan pengelolaan resource.
- Mekanisme recovery dengan memelihara state.
- Utilisasi efisien (mencegah operasi redundan), load balancing, mencegah deadlock, dan mendukung operasi yang scalable.

**Deadlock terdistribusi:** proses saling menunggu sumber daya yang dipegang proses lain sehingga tidak ada yang bisa maju.

## 2. Tantangan Sinkronisasi

- Network latency dan partitioning
- Skalabilitas
- Fault-tolerant system
- Concurrency control
- Data consistency
- Sinkronisasi waktu

## 3. Sinkronisasi Waktu Fisik

Menyelaraskan jam nyata sistem terdistribusi dengan standar umum. Penting untuk korelasi log, penjadwalan event, dan konsistensi sistem.

**Standar waktu:**

- GMT: sudah tidak digunakan.
- **UTC**: didefinisikan ITU, standar sejak 1963, berbasis TAI.
- **TAI** (International Atomic Time, dari bahasa Perancis *Temps Atomique International*).
- LTC (Coordinated Lunar Time): masih dikembangkan.

**Rantai sinkronisasi:**

Master atomic clock → atomic clock di satelit GPS → sinyal waktu GPS → GPS dongle → server NTP/PTP (Linux) → klien (Linux, Windows, Mac, ponsel).

**Sumber atomic clock:**

- Sekitar 31 satelit GPS di Medium Earth Orbit (dioperasikan U.S. Space Force).
- Deep Space Atomic Clock (DSAC) milik NASA, diluncurkan 2019.
- Misi ACES di ISS (cold-atom clock).
- Ground control (master clock): U.S. Naval Observatory (Washington, D.C.) dan Schriever Space Force Base (Colorado).

### Network Time Protocol (NTP)

Protokol sinkronisasi waktu melalui jaringan.

### Precision Time Protocol (PTP)

- Lebih presisi daripada NTP; standar IEEE 1588 (2002, 2008 / PTP v2, 2019).
- IEEE 802.1AS (gPTP) adalah adaptasi untuk Audio Video Bridging (AVB) dan Time-Sensitive Networking (TSN).
- Syarat: NIC harus mendukung **hardware timestamping**.
- Implementasi Linux: linuxptp.

## 4. Sinkronisasi Waktu Logis

Menetapkan **urutan sebab-akibat** kejadian (causal order), tidak terkait waktu fisik. Sifat transitif: jika A sebelum B dan B sebelum C, maka A sebelum C.

Lamport clock dibahas di materi minggu 2.

### Vector Clocks

Setiap proses menyimpan vektor dengan satu posisi per proses (contoh 3 proses A, B, C: `[0,0,0]`).

Aturan:

- Setiap event (internal, kirim, terima), proses menambah 1 pada posisinya sendiri.
- Saat mengirim, sertakan seluruh vektor.
- Saat menerima, ambil nilai maksimum per elemen (element-wise max), lalu tambah 1 pada posisi sendiri.

Contoh akhir: A = `[2,0,0]`, B = `[2,1,0]`, C = `[2,1,2]`.

Perbandingan:

- **A terjadi sebelum B** jika semua elemen vektor A ≤ elemen vektor B dan minimal satu lebih kecil.
- **Concurrent (independen)** jika tidak memenuhi kondisi di atas.

Dari contoh: `[2,0,0]` vs `[2,1,0]` berarti A lebih dulu. `[2,1,2]` vs `[2,0,0]` berarti independen. Implementasi: `vclocks.py`.

## 5. Tantangan Mutual Exclusion

Hanya satu proses yang boleh mengakses sumber daya bersama (printer, file, database) pada satu waktu. Bagian kode yang mengakses sumber daya bersama disebut **critical section (CS)**.

**Prinsip:**

- **Mutual exclusion**: hanya satu proses di CS.
- **Progress**: jika CS bebas, sumber daya bisa diakses.
- **Bounded waiting**: tidak ada proses yang menunggu selamanya.
- **Fairness**: permintaan dilayani dalam urutan yang wajar.

**Pendekatan solusi:**

| Algoritma | Cara kerja | Kelebihan | Kekurangan |
|---|---|---|---|
| Centralized | Satu node koordinator memberi izin dan menerima pelepasan CS | Sederhana, biaya rendah | Single point of failure, bottleneck |
| Ricart-Agrawala | Tanpa koordinator; setiap proses kirim request ber-timestamp, masuk CS bila semua membalas OK. Butuh 2 × (N − 1) pesan per CS | Fault tolerant | Overhead pesan tinggi |
| Token-based | Satu token beredar; hanya pemegang token boleh masuk CS | Hanya 1 pesan per entri CS | Deteksi token hilang sulit, perlu mekanisme pemulihan |

## 6. Pemilihan Koordinator

Node mungkin memerlukan koordinator untuk mengelola sumber daya bersama, menyinkronkan operasi, dan membuat keputusan global. Jika koordinator gagal atau node baru bergabung, sistem memilih pemimpin baru secara otomatis.

**Algoritma:**

- **Bully algorithm**: node dengan ID tertinggi yang masih hidup menjadi koordinator.
- **Ring algorithm**: node tersusun melingkar, pesan election diteruskan sepanjang cincin.
- **Raft atau Paxos** (berbasis konsensus).

> Catatan: slide hanya menyebut nama Bully dan Ring; deskripsi singkat di atas berasal dari pengetahuan umum, bukan dari slide.

**Contoh industri:**

- Apache ZooKeeper memakai Zab (ZooKeeper Atomic Broadcast), mirip Paxos.
- Kubernetes memakai Raft (melalui etcd).
