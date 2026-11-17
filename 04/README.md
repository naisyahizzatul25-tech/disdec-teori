


## 1. Konsep Konsistensi & Teorema CAP
* **Konsistensi Data:** Menjamin seluruh node memiliki atau menampilkan salinan data yang sama setelah terjadi perubahan[cite: 53].
* **Trade-off Latensi:** Semakin tinggi tingkat konsistensi yang dijamin (*Strong Consistency*), semakin tinggi latensi respons karena sistem menunggu sinkronisasi lintas jaringan selesai[cite: 69, 70].

## 2. Model Konsistensi (Kuat ke Lemah)
1. **Linearizability:** Varian terkuat; operasi tampak atomik dan langsung berlaku global secara *real-time* (contoh: `etcd`, ZooKeeper)[cite: 68].
2. **Sequential Consistency:** Semua node melihat urutan eksekusi operasi yang sama meskipun tidak harus *real-time*[cite: 67].
3. **Causal Consistency:** Operasi yang memiliki hubungan sebab-akibat harus terlihat berurutan[cite: 64].
4. **Read-Your-Writes:** Pengguna dijamin langsung melihat hasil pembaruan yang baru saja ditulisnya sendiri[cite: 65].
5. **Monotonic Read:** Pembacaan berulang tidak akan pernah menampilkan versi data yang lebih lama dari yang sudah pernah dibaca[cite: 66].
6. **Eventual Consistency:** Data tidak langsung seragam di semua node, namun pada akhirnya akan konsisten jika tidak ada pembaruan baru (prinsip **BASE**: *Basically Available, Soft-state, Eventual consistency*)[cite: 62, 63].

## 3. Replikasi Data & Penanganan Konflik
* **Jenis Replikasi:** *Master-Slave* (satu tulis, banyak baca) dan *Multi-Master* (semua node bisa baca dan tulis)[cite: 75].
* **Strategi Sinkronisasi:** *Synchronous* (menunggu semua replika commit), *Semi-synchronous* (menunggu minimal satu replika), dan *Asynchronous* (commit tanpa menunggu replika)[cite: 77].
* **Resolusi Konflik:** *Last Write Wins* (LWW berdasarkan timestamp), *Vector Clock*, **CRDT** (*Conflict-free Replicated Data Types*), dan **OT** (*Operational Transformation* untuk editor kolaboratif)[cite: 78].

## 4. Protokol Replikasi
* **Quorum-based:** Operasi tulis ($W$) dan baca ($R$) valid jika memenuhi syarat $W + R > N$ (jumlah total node)[cite: 83].
* **State Machine Replication:** Leader mengurutkan log perintah agar dieksekusi dengan urutan identik oleh seluruh replika[cite: 86].
* **Gossip Protocol:** Penyebaran pembaruan status secara berkala dan acak ke beberapa *peer* tetangga (contoh: Apache Cassandra)[cite: 87].
* **Chain Replication:** Pembaruan diteruskan secara berantai dari node *head* hingga *tail*[cite: 88].
