Materi Minggu 2: Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

AI yang digunakan: Claude (Anthropic), model Claude Sonnet 5.5

Prompt: "Buatkan rangkuman materi dari file PDF slide kuliah 'Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi' sesuai urutan agenda pembahasan, dalam format markdown untuk README.md." *(ganti dengan prompt yang benar-benar Anda tulis)*

Bagian yang disesuaikan dari keluaran prompt ada pada: *(isi sendiri)*

---

# Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

## Tujuan Pembelajaran

- Menjelaskan konsep proses pada sistem terdistribusi.
- Menjelaskan berbagai jenis komunikasi antar proses (IPC).
- Menganalisis isu sinkronisasi, reliabilitas, dan keamanan.
- Mengaitkan konsep dengan contoh nyata: microservices, cloud, distributed messaging.

## 1. Apa Itu Proses?

Proses adalah program yang sedang dijalankan oleh sistem operasi. Manajemen proses mencakup:

- **Process creation**: pembuatan proses.
- **Process migration**: perpindahan proses antar node.
- **Process monitoring**: pemantauan proses.

## 2. Sistem Terdistribusi dan Komunikasi

- Sistem terdistribusi adalah sekumpulan node independen yang bekerja bersama dalam satu koordinasi sebagai satu kesatuan logis.
- Setiap node menjalankan proses, sehingga antar proses di berbagai node harus saling berkomunikasi.

Contoh:

1. Multiplayer games.
2. Gojek/Grab: antarmuka pengguna berkomunikasi dengan layanan driver, pelacakan lokasi, perhitungan biaya, dan sebagainya.
3. Chat/video call WhatsApp: komunikasi point-to-point pada infrastruktur terdistribusi.

## 3. Model Komunikasi Pesan

- **Langsung**: menggunakan socket.
- **Tidak langsung**: melalui mailbox / message queue (misalnya perintah `write` di Linux).
- **Pub/Sub** (publish/subscribe): pengirim menerbitkan pesan ke topik, penerima yang berlangganan topik tersebut menerimanya.

## 4. Proses pada Sistem Terdistribusi

Proses bisa berada di node berbeda dan **tidak ada shared memory** antar node. Karena itu diperlukan IPC khusus untuk sistem terdistribusi.

## 5. IPC (Inter-Process Communication)

Ada 4 jenis dasar:

| Jenis | Keterangan |
|---|---|
| Shared Memory | Tidak cocok untuk sistem terdistribusi karena memori antar node tidak dapat diakses langsung. |
| Message Passing | Proses saling mengirim pesan. |
| Remote Procedure Call (RPC) | Memanggil prosedur di node lain seolah-olah lokal. |
| Middleware | Lapisan perantara komunikasi, misalnya MOM. |

### Message Passing

- Standar: **MPI (Message Passing Interface)**, versi terakhir 5.0 (sampai Oktober 2026).
- Implementasi Python: `mpi4py`.

### RPC (Remote Procedure Call)

Contoh: **gRPC**.

### MOM (Message-Oriented Middleware)

- Infrastruktur yang memfasilitasi pertukaran data melalui pesan, yaitu unit informasi mandiri.
- Pesan dikirim dan diterima secara **asinkron**.

Standar dan protokol MOM:

1. XATMI (X/Open group).
2. AMQP (Advanced Message Queuing Protocol), biasanya untuk aplikasi enterprise.
3. HLA (IEEE 1516).
4. MQTT, biasanya untuk perangkat dengan sumber daya terbatas.
5. DDS (Data Distribution Service) dari OMG.
6. XMPP.

## 6. Studi Kasus: Microservices

Aplikasi dipecah menjadi layanan-layanan kecil yang independen dan saling berkomunikasi lewat jaringan (API, message queue, dan sebagainya).

## 7. Komunikasi Sinkron dan Asinkron

- **Sinkron**: pengirim menunggu respons sebelum melanjutkan.
- **Asinkron**: pengirim tidak menunggu respons, misalnya lewat message queue.

## 8. Berbagai Masalah pada Komunikasi Antar Proses

- **Reliability**: pesan harus terkirim lengkap dan berurutan walau ada packet loss atau crash. Solusi: acknowledgement, transmisi ulang, protokol andal seperti TCP.
- **Ordering**: pesan diproses dalam urutan logis. Solusi: pipes (FIFO), message queues, shared memory, sockets dan message passing, RPC.
- **Security**: enkripsi dan autentikasi.
- **Performance**: latency dan bandwidth.

## 9. Protokol Komunikasi

- Protokol dasar: TCP, UDP, HTTP.
- Protokol yang berjalan di atas HTTP: gRPC, GraphQL, REST.

## 10. Konsep Sinkronisasi

- Setiap node punya clock lokal sendiri. Karena latensi jaringan dan pergeseran jam hardware (*clock drift*), clock lokal bisa menyimpang.
- Sinkronisasi clock menyelaraskan clock beberapa komputer ke waktu yang sama. Ini penting untuk pengurutan kejadian, konsistensi data, dan operasi terkoordinasi.
- Metode umum: sinkronisasi eksternal dengan atomic clock (TAI), GPS, dan UTC, lalu protokol **NTP** atau **PTP** (PTP lebih presisi daripada NTP).

## 11. Logical Clock

### Lamport Logical Clock

Contoh: Sales Service, Product Service, dan Invoice Service harus tercatat berurutan. Jika memakai timestamp jam nyata, urutan bisa bergeser karena latensi, masalah jaringan, atau hardware.

Aturan Lamport (logical clock tidak terkait jam nyata):

1. Setiap ada kejadian, angka logical clock bertambah 1.
2. Saat mengirim pesan, sertakan angka logical clock.
3. Saat menerima pesan, ambil nilai terbesar antara clock lokal dan clock pesan, lalu tambah 1.

Hasil pada contoh logging: Sales Service (1), Product Service (2), Invoice Service (3), sehingga urutan benar.

### Pengembangan Setelah Lamport

Lamport masih berupa *scalar clock*. Pengembangannya:

1. Vector clocks: Fidge dan Mattern (1988)
2. Plausible clocks: Torres-Rojas dan Ahamad (1999)
3. Chain clocks: Agarwal dan Garg (2005)
4. Interval tree clocks: Almeida dkk. (2008)
5. Blum clocks: Lum Ramabaja (2019)

## 12. Koordinasi Proses

Mekanisme yang membuat banyak proses independen di berbagai komputer bekerja sama tanpa konflik seperti deadlock atau race condition, dengan mengelola komunikasi, sinkronisasi, dan akses ke sumber daya bersama.

Contoh: Apache ZooKeeper, etcd, Consul, ClickHouse Keeper, Oxia, KRaft.

## 13. Tantangan dan Solusi

| Tantangan | Solusi |
|---|---|
| Message loss | Retransmission dan acknowledgement |
| Deadlock | Timeout, algoritma deteksi |
| Data inconsistency | Protokol konsensus (Raft, Paxos) |
| Security | SSL/TLS, OAuth2 |

## 14. Current Issues

- Edge dan Fog Computing
- Observability dan tracing (telemetry)
- Container orchestration: Kubernetes, Docker Swarm

## 15. Diskusi

1. Apakah GraphQL merupakan contoh RPC?
2. Apakah REST merupakan contoh RPC?
3. Apakah telemetry merupakan solusi etis?
