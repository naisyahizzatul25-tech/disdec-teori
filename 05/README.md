# Materi Minggu 5: Fault Tolerance

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

# Fault Tolerance

## 1. Pengertian Fault Tolerance

Kemampuan sistem mempertahankan operasi dengan tepat meskipun satu atau lebih komponennya mengalami kegagalan atau kesalahan. Penting untuk sistem *high availability*, *mission critical*, atau *life-critical*.

## 2. Arti Penting pada Sistem Terdistribusi-Terdesentralisasi

Sistem ini terdiri dari banyak komponen, dan koneksi antar komponen bisa bermasalah. Semakin kritis aplikasi, semakin tinggi tuntutan untuk tetap berfungsi (high availability), sehingga kegagalan harus diantisipasi.

## 3. Jenis Fault

| Jenis | Penjelasan | Contoh |
|---|---|---|
| Transient | Sementara | Gangguan karena petir |
| Intermittent | Muncul dan hilang berulang | Hardware overheat |
| Permanent | Bersifat tetap | Komponen rusak permanen |
| Byzantine | Komponen memberi hasil acak/tidak konsisten (mis. node "pengkhianat" yang mengirim informasi berbeda) | Node berbahaya di jaringan blockchain |

## 4. Klasifikasi Fault Berdasarkan Sumber

- **Hardware faults**: disk crash, power failure.
- **Software faults**: bug, memory leak, race condition.
- **Communication faults**: packet loss, network partition.

## 5. Teknik / Design Pattern Mencapai Fault Tolerance

Lima teknik utama: redundansi dan replikasi, load balancing, checkpointing dan rollback recovery, heartbeat dan failure detection, serta consensus dan coordination.

### 5.1 Redundansi dan Replikasi

Resource direplikasi ke banyak node (redundansi) agar tidak ada *single point of failure*. (Lihat materi minggu 4.)

### 5.2 Load Balancing

Aplikasi di-scale menjadi lebih dari satu instance (satu instance = satu proses aplikasi penuh yang aktif), lalu lalu lintas dibagi oleh load balancer (HAProxy, nginx, dll.) ke beberapa instance.

### 5.3 Checkpointing and Rollback Recovery

- **Checkpointing**: menyimpan status sistem secara berkala ke media penyimpan.
- **Rollback recovery**: mengembalikan sistem ke checkpoint terakhir setelah kegagalan.
- Sistem pulih dari status tersimpan (bukan dari awal), sehingga kehilangan data dan waktu pemrosesan minimal.

### 5.4 Heartbeat and Failure Detection

- **Heartbeat**: sinyal periodik antar komponen sebagai tanda masih aktif.
- **Failure detection**: jika komponen pemantau tidak menerima heartbeat dalam jangka waktu tertentu, komponen tersebut dianggap gagal.
- Pola ini krusial agar kegagalan terdeteksi dan ditangani tepat waktu.
- Praktik: skrip `check-server.py` memeriksa apakah server pada host dan port tertentu hidup (output "Server up" atau "Server down").

**Retry dengan library `tenacity`:** karena koneksi ke komponen luar selalu mungkin gagal, `tenacity` mengulang percobaan secara otomatis. Pada contoh `check-retry.py`, setelah beberapa kali percobaan gagal, muncul `RetryError`.

**Circuit breaker:** pola desain pada microservices untuk mencegah kegagalan berantai dengan memblokir sementara panggilan ke layanan yang gagal. Bekerja seperti state machine dengan tiga status:

| Status | Perilaku |
|---|---|
| Closed | Permintaan diizinkan dan dipantau. Jika ambang kegagalan tercapai, pindah ke open. |
| Open | Permintaan langsung ditolak agar layanan punya waktu pulih. Setelah batas waktu, pindah ke half-open. |
| Half-open | Sejumlah terbatas permintaan uji diizinkan. Berhasil → closed; gagal → kembali open. |

Contoh Python memakai `pybreaker`:

```python
import pybreaker
import requests

breaker = pybreaker.CircuitBreaker(fail_max=4, reset_timeout=20)

@breaker
def make_http_request(url):
    print(f"Attempting to access {url}...")
    response = requests.get(url)
    response.raise_for_status()  # exception untuk status 4xx atau 5xx
    print(f"Successfully accessed {url}")
    return response.text

try:
    content = make_http_request("http://localhost:44777")
except Exception as e:
    print(e)
```

Artinya: setelah 4 kegagalan beruntun, breaker membuka sirkuit; setelah 20 detik ia mencoba kembali (half-open).

### 5.5 Consensus and Coordination

- **Consensus**: beberapa node menyepakati satu nilai, status, atau urutan operasi, meski ada kegagalan dan penundaan jaringan.
- **Coordination**: pengelolaan interaksi antar node agar bekerja sama secara efektif dan konsisten; sering melibatkan consensus.

Contoh:

- **Database leader election**: memilih leader untuk menangani write, memakai algoritma seperti Raft.
- **Cloud storage file update** (mis. Google Drive): perubahan di-commit ke mayoritas server, lalu direplikasi ke semua server.
- **Blockchain**: consensus untuk validasi dan persetujuan transaksi.

## 6. Actor Model untuk Supervisi Proses

- Dalam actor model, setiap *actor* adalah unit komputasi independen dengan **mailbox** sendiri; actor berkomunikasi hanya lewat pengiriman pesan.
- Actor disusun dalam hierarki **supervisor-worker**: *top level supervisor* memeriksa apakah actor di bawahnya hidup ("check if alive"), dan jika sebuah actor gagal, supervisor me-*restart*-nya.

## 7. Implementasi pada Berbagai Bahasa dan Peranti

- **Erlang/Elixir**: actor model dan supervisor-worker sudah bawaan (builtin).
- **Pony**: bahasa pemrograman yang berfokus pada actor model.
- **Python**: Xoscar, tenacity, pybreaker (circuit breaker), dan lain-lain.
- **Java**: Apache Pekko (actor model dan supervisor-worker), Resilience4j (fault tolerance).
- Bahasa lain biasanya memakai library pihak ketiga; cari library fault tolerance di npm (JavaScript), crates.io (Rust), dan lainnya.
