# Jurnal Proses — Tugas 4

## Jalur yang dipilih
- Keduanya (kombinasi RPC dan MQ)
- Alasan: Karena cek saldo dan proses pembayaran membutuhkan hasil langsung untuk melanjutkan alur, yang bisa didapatkan dengan metode RPC dan tidak bisa didapatkan pada metode MQ. Sedangkan notifikasi ke kurir hanya perlu memberitahuan ke pihak lain tanpa membutuhkan balasan yang bisa didapatkan dengan metode MQ dan tidak bisa didapatkan pada metode RPC.
- Jika RPC dipakai untuk notifikasi kurir dan kondisi kurir sibuk/lambat, pembayaran ikut menunggu sampai kurir membalas sehingga waktu respon pembayaran pun menjadi lama sesuai lambatnya kurir, padahal tugas pembayaran sudah selesai. Atau ketika menggunakan server RPC satu threads atau bahkan "service" kurir mati. Hal tersebut dapat menyebabkan satu permintaan diproses pada satu waktu atau koneksi koneksi tersambung tapi tidak dibalas tanpa ada batasan secara default untuk RPC. Dengan kata lain, jika RPC digunakan untuk notifikasi kurir, akan terjadi efek domino jika terjadi masalah di kurir yang berefek muncul masalah di pembayaran dan pesanan. Belum lagi terjadi data yang tidak konsisten yaitu ketika saldo pengguna sudah terpotong, tetapi notifikasi ke kurir gagal dan pesanan tidak pernah diantar.
- Sedangkan jika MQ dipakai untuk cek_saldo, permintaan masuk antrean dan jawaban harus dikirim lewat antrean balasan. Padahal cek_saldo membutuhkan jawaban saat itu juga. Belum lagi karena kompleksitasnya, membuat cek_saldo harus memiliki latensi yang lebih besar tanpa ada manfaatnya.  

## Kendala teknis
- Error saat setup (mis. koneksi RabbitMQ ditolak, port bentrok): ...

## Uji "pesan tidak hilang" (khusus Jalur B)
- Langkah uji: matikan consumer → jalankan publisher → nyalakan consumer
- Hasil yang diamati: ...

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |
