# Materi Minggu 4: Konsistensi dan Replikasi pada Sistem Terdistribusi

### Identitas Mahasiswa
* **Nama:** Naisyah Izzatul Jannah K.
* **NIM:** 255410040
* **Prodi:** Informatika, Fakultas Teknologi Informasi, UTDI
* **Mata kuliah:** Sistem Terdistribusi dan Terdesentralisasi (FIF25002T, 3 SKS)
* **Dosen:** Dr. Bambang Purnomosidi D. P.
  
---

AI yang digunakan: Claude (Anthropic), model Claude Sonnet 5.5

Prompt: "Buatkan rangkuman ambil yang penting penting saja dari materi pada file PDF tersebut dalam format markdown untuk README.md. git hub saya"

Bagian yang disesuaikan dari keluaran prompt ada pada: Tidak ada 

---

# Konsistensi dan Replikasi pada Sistem Terdistribusi

## 1. Apa Itu Konsistensi?

Menjamin semua node memiliki data yang sama atau tampak sama pada waktu tertentu, meskipun data berada di banyak tempat dan bisa diubah bersamaan. Jika data diubah di satu server, server lain akhirnya harus punya data yang sama.

## 2. Mengapa Konsistensi Penting?

Tanpa konsistensi, pengguna bisa melihat versi data berbeda tergantung server yang dibaca.

Contoh: transfer dari rekening A ke B. Server 1 sudah mengurangi saldo A, tetapi server 2 belum menambah saldo B, sehingga B tidak bisa bertransaksi karena saldo belum bertambah. Sistem tidak konsisten.

## 3. Teorema CAP

- **Consistency**: semua node punya data yang sama.
- **Availability**: data tersedia saat dibutuhkan.
- **Partition Tolerance**: sistem tetap berjalan walau sebagian jaringan terputus.

Partisi jaringan hampir pasti terjadi, sehingga kondisi ideal CAP penuh tidak mungkin tercapai.

Contoh e-commerce (Server A melayani Indonesia barat, Server B Indonesia timur) saat jaringan antar wilayah terputus:

- **AP**: pengguna tetap bisa belanja di kedua wilayah, tetapi stok bisa sementara tidak sinkron.
- **CP**: transaksi ditolak sampai jaringan pulih agar stok konsisten (mengorbankan availability).

## 4. Model Konsistensi

Aturan tentang bagaimana dan kapan perubahan data terlihat oleh node atau klien lain.

| No | Model | Penjelasan | Contoh |
|---|---|---|---|
| 1 | Strong consistency | Setelah update, semua node langsung melihat versi terbaru. Biasanya memakai Two-Phase Commit atau Paxos. | Sistem perbankan |
| 2 | Eventual consistency | Data tidak langsung sama, tetapi akhirnya sama bila tidak ada update baru. Prinsip BASE. | DynamoDB, Cassandra |
| 3 | Causal consistency | Operasi yang saling bergantung dijaga urutannya; yang tidak berhubungan boleh dilihat berbeda urutan. | Komentar tidak muncul sebelum postingan |
| 4 | Read-your-writes | Setelah menulis, pembacaan oleh penulis yang sama melihat hasil tulisannya. | Pesan terkirim langsung muncul di layar pengirim |
| 5 | Monotonic read | Pengguna tidak akan melihat versi lebih lama dari yang sudah pernah dilihat. | Timeline tidak "mundur" setelah refresh |
| 6 | Sequential consistency | Semua node melihat urutan operasi yang sama, tidak harus real-time. | Urutan posting sama bagi semua pengguna |
| 7 | Linearizability | Varian terkuat; operasi tampak atomik dan langsung, sistem tampak seperti satu node. Dipakai etcd, ZooKeeper, Spanner. | Pengecekan keunikan username/ID |

**BASE:**

- **B**asically **A**vailable: data tetap bisa dibaca/ditulis saat kegagalan sebagian.
- **S**oft state: replika tidak harus konsisten sepanjang waktu.
- **E**ventual consistency: akhirnya konsisten bila tidak ada update baru.

## 5. Trade-off Latensi dengan Konsistensi

- Konsistensi naik → latensi naik.
- Konsistensi turun → latensi turun.

Strong consistency menunggu sinkronisasi antar server selesai sehingga respons lambat. Eventual consistency langsung merespons sehingga cepat, tetapi data bisa belum terbaru.

Contoh dua pusat data (Jakarta dan Yogyakarta): dengan strong consistency, sistem menunggu keduanya sama sebelum menjawab; dengan eventual consistency, sistem langsung menjawab dan mengirim update ke Yogyakarta belakangan.

## 6. Pengertian Replikasi Data

Membuat dan memelihara salinan data yang sama di beberapa node agar sistem tetap tersedia, andal, dan cepat meski satu node gagal. Contoh: database utama di Jakarta, salinan di Yogyakarta dan Denpasar.

**Tujuan:**

- **Availability**: jika satu server gagal, server lain masih punya salinan.
- **Performance**: pengguna membaca dari server terdekat.
- **Reliability**: risiko kehilangan data menurun.
- **Skalabilitas**: beban baca dibagi ke beberapa server.

## 7. Jenis Replikasi

- **Master-Slave (Primary-Secondary)**: satu master menulis, slave hanya membaca. Konsistensi kuat, tetapi master jadi bottleneck. Contoh: PostgreSQL streaming replication.
- **Multi-Master (Peer-to-Peer)**: semua node bisa menulis dan membaca. Fleksibel, tetapi rawan konflik. Contoh: PostgreSQL logical replication, Cassandra, MongoDB, Apache Ignite.
- **Synchronous**: update ditulis ke semua node sebelum dianggap selesai. Konsisten tetapi lambat. Contoh: Google Spanner.
- **Asynchronous**: update dikirim setelah transaksi selesai di master. Cepat tetapi bisa tertunda. Contoh: MySQL mode async, DynamoDB.

## 8. Strategi Sinkronisasi Replika

Dikelompokkan berdasarkan:

- **Waktu sinkronisasi**: synchronous, asynchronous, semi-synchronous.
- **Arah replikasi**: master-slave, master-master, master-master asimetris.
- **Cara sinkronisasi**: pull-based, push-based, hybrid.
- **Penanganan konflik**: LWW, vector clock, CRDT, Operational Transformation.

### Waktu Sinkronisasi

- **Asynchronous**: commit tanpa menunggu replika; tercepat, risiko kehilangan data tertinggi.
- **Semi-synchronous**: menunggu satu replika menerima data; seimbang (dipakai MySQL).
- **Synchronous**: menunggu semua replika commit; konsistensi tertinggi, performa terendah.

### Cara Sinkronisasi

- **Pull-based**: replika memeriksa update ke source secara periodik.
- **Push-based**: source mengirim update ke replika saat terjadi perubahan, memakai CDC (Change Data Capture) dan WebSockets atau SSE.
- **Hybrid**: campuran keduanya.

### Penanganan Konflik

- **LWW (Last Write Wins)**: timestamp terbaru yang dipakai, lainnya diabaikan.
- **Vector clock**: pengurutan memakai vector clock.
- **CRDT (Conflict-free Replicated Data Type)**: struktur data di tiap node yang bisa digabung otomatis tanpa konflik. Contoh: Yjs (editor kolaboratif).
- **OT (Operational Transformation)**: operasi ditransformasi berdasarkan informasi lokal.

**Contoh CRDT (penghitung pengunjung):** dua pengguna mencatat hitungan di map lokal masing-masing: `{Agnes: 5, Bambang: 0}` dan `{Agnes: 0, Bambang: 3}`. Saat sinkronisasi, tiap entri diambil nilai maksimumnya: `{Agnes: 5, Bambang: 3}`. Total = 5 + 3 = 8.

**Contoh OT (teks "abc"):** Agnes menyisipkan "x" di indeks 0 (menjadi "xabc"), Bambang menghapus "c" di indeks 2 (menjadi "ab"). Tanpa transformasi, hasil berbeda ("xac" vs "xab"). Dengan OT, operasi "hapus indeks 2" ditransformasi menjadi "hapus indeks 3" di sisi Agnes sehingga keduanya menghasilkan "xab".

## 9. Protokol Replikasi

1. **Primary-based (master-slave)**: satu node write-read, lainnya read.
2. **Quorum-based**: operasi sukses jika dikonfirmasi sejumlah node tertentu.
3. **State machine replication**.
4. **Gossip protocol**.
5. **Chain replication**.

### Quorum-based

Syarat: **W + R > N** (W = minimal node untuk write, R = minimal node untuk read, N = total node).

Contoh N = 3, W = 2, R = 2 (2 + 2 > 3):

- **Write** `x = 100` dikirim ke A, B, C paralel. A dan B membalas ACK, C lambat. Dengan 2 ACK (W terpenuhi), write dinyatakan berhasil.
- **Read** meminta minimal 2 node, misalnya A dan C. A punya versi terbaru, C mungkin versi lama. Karena irisan write dan read pasti memuat minimal satu node dengan data terbaru, versi terbaru pasti terbaca.

### State Machine Replication

Beberapa replika mengeksekusi perintah yang sama dalam urutan yang sama. Contoh: A, B, C punya `x = 10`. Klien kirim `SET x = 20` ke leader (A). A memberi nomor urut, menambah ke log, mengirim ke B dan C. Setelah B dan C mengonfirmasi, perintah "dikomit", semua replika mengeksekusinya (`x = 20`), lalu leader mengirim hasil ke klien.

### Gossip Protocol

Dipakai di Apache Cassandra untuk memeriksa keberadaan node dan replikasi data. Tiap detik sebuah node memilih 1-3 peer acak dan membagikan statusnya; peer tersebut menyebarkan lagi ke peer lain, sehingga seluruh cluster cepat mengetahui perubahan.

### Chain Replication

Replika disusun berurutan seperti rantai: write masuk di kepala rantai dan diteruskan hingga ekor, read dilayani dari ekor (contoh pada slide: penyimpanan Azure).

(Lihat lecture notes dan source code yang menyertai materi.)

