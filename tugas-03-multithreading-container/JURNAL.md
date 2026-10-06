# Jurnal Proses — Tugas 3

## Percobaan tanpa Lock
- Hasil `processed_count` yang didapat:

| Platform | Hasil
|---|---|
| Python | 42, 40, 41 |
| Docker | 56, 53, 55 | 

- Kenapa bisa meleset (jelaskan mekanisme race condition dengan kata sendiri): 

  - Meskipun 100 pesanan sudah diproses, counter hanya mencatat sekitar 40-56 karena sebagian penambahan saling menimpa saat dikerjakan oleh banyak thread secara bersamaan. `Jeda time.sleep(0.0005)` ditambahkan agar kejadian ini lebih mudah untuk dilihat.

## Percobaan dengan Lock
- Hasil `processed_count` setelah perbaikan:

| Platform | Hasil
|---|---|
| Python | 100, 100, 100 |
| Docker | 100, 100, 100 | 

## Kendala Docker
- Error yang ditemui saat `docker build`/`docker run` dan cara memperbaikinya: 

- Kendala: Muncul line bahwa docker tidak dapat dikenali
  - Penyebab: Docker belum terpasang di laptop.
  - Solusi: Memasang Docker Desktop dan menjalankannya.


## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |
