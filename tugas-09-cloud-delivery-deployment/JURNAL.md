# Jurnal Proses — Tugas 9

## [11/23/2026 - Sesi 1]

- Peserta: [Moch. Andy Yusuf Hendrawan E P, Bezaliel Agung Trilaksana, Heilyn Alfreda Aritonang]
- Poin diskusi: Penentuan deployment model untuk Sistem Data Kependudukan
- Perbedaan pendapat (jika ada):
  - `Bezaliel` berpendapat bahwa **Private Cloud** paling sesuai karena:
    - Kerahasiaan data pribadi warga negara terjaga karena dikelola eksklusif oleh negara
    - Risiko serangan lebih kecil karena isolasi yang ketat

  - Di sisi lain `Heilyn` berpendapat bahwa **Community Cloud** lebih sesuai karena:
    - Data inti tetap aman dengan standar keamanan dan regulasi yang seragam
    - Efisiensi biaya karena infrastruktur ditanggung bersama

  - `Andy` berpendapat bahwa **Hybrid Cloud** paling sesuai karena:
    - Data inti tetap di _private_, tetapi lonjakan akses bisa ditampung _public cloud_
    - Private murni tidak punya _rapid elasticity_ yang memadai

## [11/23/2026 - Sesi 2]

- Peserta: [Moch. Andy Yusuf Hendrawan E P, Bezaliel Agung Trilaksana, Heilyn Alfreda Aritonang]
- Poin diskusi: Penentuan service model untuk komponen-komponen berbeda dari sistem ini
- Perbedaan pendapat (jika ada):
  - `Bezaliel` berpendapat bahwa: Semua komponen menggunakan **IaaS** untuk keamanan ketat dan kendali penuh
  - `Heilyn` berpendapat bahwa: Data inti menggunakan **IaaS**, tetapi API verifikasi KYC menggunakan **PaaS** agar gampang scaling saat trafik tinggi
  - `Andy` berpendapat bahwa: menggunakan **SaaS** pada layanan publik agar lebih praktis

## Diskusi deployment model

- Opsi yang dipertimbangkan dan kenapa akhirnya memilih salah satu:
  - Opsi yang dipertimbangkan terdapat pada Diskusi Sesi 1. Berdasarkan hasil diskusi, deployment model yang dipilih ialah **Hybrid Cloud** karena mampu menyeimbangkan kerahasiaan data dan kebutuhan skalabilitas sistem. Data rahasia warga negara disimpan sepenuhnya di Private Cloud milik negara dengan sumber daya yang terisolasi (_resource pooling_) dan terpantau (_measured service_). Layanan untuk menangani lonjakan akses musiman dijalankan di Public Cloud agar mudah diakses banyak instansi (_broad network access_), bisa diatur kapasitasnya secara mandiri (_on-demand self-service_), serta mampu menyesuaikan kapasitas secara otomatis (_rapid elasticity_) agar tidak terjadi downtime.

  - **Risiko dan mitigasi:**

  | Risiko                                               | Mitigasi                                                                                        |
  | ---------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
  | Sinkronisasi data _private_ ↔ _public_ (status basi) | _Cache_ status non-sensitif dengan TTL pendek, data otoritatif tetap di _private_               |
  | Kebocoran data lewat segmen _public_                 | Tanpa replikasi data mentah ke _public_, VPN dan _mutual TLS_, segmentasi jaringan, _audit log_ |
  | Latensi antar segmen                                 | _Timeout_, _retry_ terbatas, _circuit breaker_                                                  |
  | _Vendor lock-in_ pada PaaS                           | Kontainer dan standar terbuka                                                                   |
  - **Alternatif yang ditolak: Public Cloud murni.** Data NIK dan biometrik akan berada di infrastruktur yang tidak dikendalikan penuh negara, bertentangan dengan syarat studi kasus dan UU Perlindungan Data Pribadi. Selain itu, _multi-tenancy_ meningkatkan risiko kebocoran lintas tenant. Private Cloud murni juga dianggap kurang skalabel karena harus menyediakan kapasitas untuk puncak pemilu.

## Diskusi service model per komponen

- Diskusi terdapat pada Diskusi Sesi 2. Hasilnya ialah:
  - Menggunakan **IaaS** pada data inti (nama, NIK, alamat, data biometrik) di Private Cloud agar pemerintah memegang kendali penuh atas keamanan dan enkripsi.
  - Menggunakan **PaaS** pada API Gateway dan Layanan Verifikasi KYC agar proses scaling saat lonjakan trafik banyak instansi berjalan otomatis dan efisien.
  - Menggunakan **SaaS** pada Portal Publik yang terpisah dan tidak menyimpan data sensitif warga secara langsung.

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal    | Tool AI  | Prompt yang diberikan                                                                                                                                                                                           | Ringkasan saran/ide                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Bagaimana diolah jadi tulisan/kode sendiri                                        |
| ---------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| 23/11/2026 | GeminiAI | Jelaskan secara sederhana serta kelebihan dan kekurangan mengenai Karakteristik esensial cloud computing (NIST SP 800-145), Deployment Model (Public/Private/Hybrid/Community), Service Model (IaaS/PaaS/SaaS). | Standar NIST SP 800-145 menetapkan lima karakteristik utama yang mendefinisikan layanan cloud: On-demand self-service: Pengguna dapat mengatur kapasitas komputasi (seperti waktu server atau penyimpanan jaringan) secara otomatis sesuai kebutuhan. Broad network access: Layanan tersedia melalui jaringan dan dapat diakses dari berbagai perangkat. Resource pooling: Sumber daya fisik penyedia (penyimpanan, pemrosesan, memori, jaringan) digabung dalam satu kolam dan dibagikan ke banyak pengguna. Rapid elasticity: Kapasitas dapat ditambah atau dikurangi dengan cepat—bahkan otomatis— saat lonjakan. Measured service: Penggunaan sumber daya dipantau, dikontrol, dan dilaporkan secara terukur secara otomatis. Public Cloud: Infrastruktur dimiliki dan dioperasikan penyedia pihak ketiga lalu diakses publik lewat internet. Private Cloud: Infrastruktur khusus untuk satu organisasi saja, bisa di-host di lokasi sendiri atau pihak ketiga. Hybrid Cloud: Gabungan public cloud dan private cloud yang terhubung agar data dan aplikasi bisa saling berpindah. Community Cloud: Infrastruktur dipakai bersama oleh beberapa organisasi dengan tujuan atau kebutuhan serupa (misalnya sektor kesehatan atau pemerintahan). Infrastructure as a Service (IaaS): Menyewa komponen dasar infrastruktur IT seperti server virtual, penyimpanan, dan jaringan. Platform as a Service (PaaS): Menyediakan lingkungan siap pakai (runtime, database, alat pengembangan/deployment) sehingga developer cukup fokus menulis kode dan menjalankan aplikasi tanpa mengurus server atau OS. Software as a Service (SaaS): Aplikasi utuh siap pakai yang diakses langsung lewat browser atau aplikasi klien. | dipahami dan dipikirkan jawaban yang tepat berdasarkan studi kasus yang tersedia. |
