# Tugas 2 (Pekan 2) — Perancangan Arsitektur untuk FoodGo

**Materi terkait:** Architectural style (Layered, SOA, Peer-to-Peer, Publish-Subscribe).

## Studi Kasus

Melanjutkan Tugas 1: FoodGo butuh sistem yang **decoupled** agar tim kurir dan tim resto tidak saling mengganggu ketika salah satu modul diperbarui/deploy ulang. Saat ini semua modul (pesanan, pembayaran, notifikasi kurir, katalog resto) berjalan sebagai satu aplikasi monolitik — sekali deploy, semua modul ikut restart dan berisiko downtime total.

## Tugas Kelompok

1. Pilih **satu** gaya arsitektur utama: **Service-Oriented Architecture (SOA)** atau **Publish-Subscribe**. Boleh dikombinasikan (mis. SOA untuk service inti + Pub-Sub untuk notifikasi), tapi harus dijustifikasi kenapa kombinasi ini yang dipilih.
2. Gambarkan minimal 4 komponen berikut dan interaksinya: modul Pesanan, modul Pembayaran, modul Kurir/Notifikasi, modul Katalog Resto (dan message broker/API gateway jika relevan).
3. Jelaskan alur satu skenario penuh secara end-to-end di diagram (misalnya: pelanggan buat pesanan → bayar → resto terima notifikasi → kurir ditugaskan) — tunjukkan komponen mana berkomunikasi dengan siapa, dan **jenis komunikasinya** (sinkron/asinkron, request-response/event).
4. Analisis tertulis: kenapa gaya ini mengatasi masalah *coupling* dari Tugas 1, dan apa trade-off-nya (mis. Pub-Sub menambah kompleksitas debugging karena alur tidak linear).


## Tugas 2 — Perancangan Arsitektur FoodGo

### 1. Masalah yang Diselesaikan

FoodGo berjalan sebagai **satu aplikasi monolitik** (pola MVC): modul Pesanan, Pembayaran, Notifikasi Kurir, dan Katalog Resto berada dalam satu unit deploy. Akibatnya:

- Satu modul diperbarui → **semua modul ikut restart** → risiko downtime total.
- Modul saling bergantung erat (*tightly coupled*): jika satu modul jebol, modul lain ikut jebol.
- Tim kurir dan tim resto tidak bisa deploy secara independen.

Target desain: **decoupled**, sehingga tiap modul bisa di-deploy, di-*scale*, dan gagal secara terpisah.

### 2. Gaya Arsitektur yang Dipilih

**Kombinasi: Service-Oriented Architecture (SOA / microservice) untuk service inti + Publish-Subscribe untuk notifikasi dan koordinasi antar-tim.**

| Bagian sistem | Gaya | Komunikasi | Alasan |
|---|---|---|---|
| Client → API Gateway → Service | SOA (REST API) | Sinkron, request-response | Klien butuh jawaban langsung (menu, status bayar) |
| Order → Payment | SOA | Sinkron, request-response | Pesanan tidak boleh lanjut sebelum hasil pembayaran pasti |
| Order → Katalog (cek menu/harga) | SOA | Sinkron, request-response | Validasi harus selesai sebelum pesanan dibuat |
| Order → Resto, Kurir, Notifikasi pelanggan | Publish-Subscribe | Asinkron, event | Order tidak perlu tahu siapa penerimanya dan tidak boleh gagal hanya karena penerima sedang down |

#### Justifikasi kenapa memilih arsitektur kombinasi

- **Opsi 1: SOA:** Jika Order memanggil Kurir dan Resto secara sinkron, Order tetap *coupled* ke keduanya: saat service Kurir sedang deploy ulang, pesanan pelanggan ikut gagal. Ini mengulang masalah monolit dalam bentuk lain.
- **Opsi 2: Pub-Sub.** Pembayaran butuh kepastian hasil sebelum pesanan dilanjutkan. Kalau dibuat event murni, alur menjadi tidak linear dan sulit dikontrol tepat di titik yang paling sensitif (uang).
- **Kombinasi:** alur yang butuh jawaban langsung memakai request-response (SOA); alur yang berupa "beri tahu pihak lain bahwa sesuatu terjadi" memakai event (Pub-Sub). Publisher hanya mengirim ke *channel* di message broker, sehingga penambahan subscriber baru (mis. layanan promo) tidak mengubah Service Pesanan.

Pemisahan modul mengikuti prinsip microservice: satu aplikasi besar dipecah menjadi layanan kecil yang terpisah, terhubung lewat konektor (*bridge*/API/broker). Jika satu layanan jebol, layanan lain tetap berjalan.


## Cara Membuat Diagram (Gratis, Cukup Laptop)

Tidak perlu software berbayar. Dua opsi:

**Opsi A — Mermaid di dalam Markdown (disarankan).** Ditulis sebagai teks biasa di `README.md`, otomatis dirender jadi diagram oleh GitHub — tidak perlu install apa pun.

````markdown
```mermaid
graph LR
  Client[Pelanggan] -->|HTTP request pesan| OrderSvc[Service Pesanan]
  OrderSvc -->|RPC sinkron| PaymentSvc[Service Pembayaran]
  OrderSvc -->|publish event OrderCreated| Broker[(Message Broker)]
  Broker -->|subscribe| NotifSvc[Service Notifikasi Kurir]
  Broker -->|subscribe| RestoSvc[Service Katalog Resto]
```
````

**Opsi B — draw.io / diagrams.net** (gratis, jalan di browser tanpa akun, atau app desktop offline di [app.diagrams.net](https://app.diagrams.net/)). Ekspor sebagai `.png` dan simpan di folder `diagram/`.

## Struktur Submission

```
tugas-02-perancangan-arsitektur/
├── README.md          # Analisis + diagram Mermaid (jika Opsi A) atau referensi ke diagram/
├── JURNAL.md
└── diagram/            # File .png/.drawio jika pakai Opsi B
```

## Rubrik Penilaian (Tugas 2)

| Komponen | Bobot | Kriteria |
|---|---|---|
| Ketepatan pemilihan gaya arsitektur | 20% | Justifikasi SOA/Pub-Sub sesuai kebutuhan *decoupling* di skenario |
| Kelengkapan & kejelasan diagram | 30% | Semua komponen kunci ada, jenis komunikasi (sinkron/asinkron) jelas ditandai |
| Analisis trade-off | 30% | Bukan hanya kelebihan — kekurangan/kompleksitas baru juga dibahas |
| Proses & kontribusi kelompok | 20% | `JURNAL.md`, commit history |

## Batasan Penggunaan AI (Level 2)

Kebijakan **Level 2 (AI Assisted Idea Generation & Structuring)** berlaku — lihat [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Boleh memakai AI untuk brainstorming komponen apa saja yang umum ada di gaya arsitektur SOA/Pub-Sub; **tidak boleh** meminta AI menggambar diagram final atau menuliskan analisis trade-off yang tinggal ditempel. Catat pemakaian AI di "Log Penggunaan AI" pada `JURNAL.md`.

- Diagram Mermaid/draw.io yang "terlalu generik" (identik dengan contoh tutorial di internet tanpa penyesuaian ke kasus FoodGo) akan dinilai rendah pada komponen kelengkapan & kejelasan diagram.
