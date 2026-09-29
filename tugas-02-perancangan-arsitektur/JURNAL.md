# Jurnal Proses — Tugas 2

## [9/29/2026]
- Opsi arsitektur yang dipertimbangkan: 
Service-Oriented Architecture (SOA / microservice) untuk service inti + Publish-Subscribe untuk notifikasi dan koordinasi antar-tim.

- Kenapa akhirnya pilih [SOA/Pub-Sub]: Tim kami memutuskan untuk mengkombinasikan SOA dan Pub-Sub karena dua alasan:

    - Modul Pembayaran lebih cocok untuk menggunakan SOA karena butuh hasil langsung.
    - Modul pemberitahuan ke resto dan kurir lebih cocok menggunakan Pub-Sub karena modul bisa di-deploy ulang tanpa menganggu satu sama lain.


- Revisi diagram (versi 1 → versi 2, apa yang berubah dan kenapa): ...

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |
