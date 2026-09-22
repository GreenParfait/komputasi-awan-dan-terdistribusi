# Tugas 1 — Analisis Pitfall FoodGo

**Kelompok:** [nama kelompok]

| Nama | NIM | Kontribusi |
|---|---|---|
| [nama 1] | [nim] | [pitfall/bagian yang dikerjakan] |
| Ighfir Maulana | 103072400029 | Latency is Zero |
| [nama 3] | [nim] | [pitfall/bagian yang dikerjakan] |

## Pitfall 1: [nama pitfall] — ditulis oleh [nama]

**Bukti di skenario:** [kutip/paraphrase bagian skenario]

**Kenapa ini keliru:** [penjelasan]

**Dampak ke FoodGo:** [mekanisme kegagalan konkret]

**Solusi desain awal:** [usulan solusi]

**Trade-off:** [apa yang dikorbankan/risiko dari solusi ini]

---

## Pitfall 2: Latency is Zero — ditulis oleh Ighfir Maulana

**Bukti di skenario:** Aplikasi FoodGo mengalami kegagalan sistem saat pesanan melonjak salah satunya yaitu aplikasi menjadi sangat lambat dan beberapa permintaan *timeout*.

**Kenapa ini keliru:** Karena dalam dunia jaringan, perangkat klien dan *server* berkomunikasi menggunakan jaringan komputer. Klien mengirimkan permintaan data, dan server mengirimkan *respons data*, yang mana pengirimannya dalam bentuk paket data kecil sehingga permintaan dan respons data melompat dari satu perangkat ke perangkat lain melalui medium pengirim paket dalam jaringan seperti *router*, modem, kabel serat optik, media transmisi nirkabel hingga sampai ke tujuan (sumber: [AWS](https://aws.amazon.com/id/what-is/latency/)). Sedangkan medium-medium tersebut dapat mengalami gangguan dari luar seperti hujan yang dapat mengganggu jaringan nirkabel karena air dapat menyerap dan memantulkan sinyal radio (sumber: [Vinotek](https://vinotek.id/article/koneksi-internet-sering-memburuk-saat-hujan-apa-penyebabnya)). Itu baru faktor/gangguan eksternal, belum lagi faktor/gangguan internal yang terjadi pada *case* aplikasi FoodGo ini. 

Seperti yang sudah kami jelaskan sebelumnya, dalam pengirimannya, data "melewati" server, yang mana dalam satu aplikasi seperti FoodGo memiliki beberapa *service*. Padahal pada suatu aplikasi, data akan berpindah berurutan dari *service* satu ke *service* yang lain. Dengan kata lain, kalau suatu *service* sedang bermasalah/terhambat, maka datanya juga tertunda/terhambat sehingga menyebabkan latensi. Terlebih lagi jika terjadi kesalahan dalam memanajemen aplikasi salah satunya salah dalam menentukan arsitekturnya.

Contoh: ketika *service* pesanan memanggil modul pembayaran dan ternyata terdapat lonjakan* traffic*, sehingga *sevice* pesanan menunggu respon tanpa batas waktu karena tidak dipasangi *timeout*. Karena tidak dipasangi *timeout*, *server* "terpaksa" menahan *resource* agar tidak digunakan pada proses/*service* lain sehingga menyebabkan latensi aplikasi semakin besar. Hal ini, kontradiksi dengan *Latency is Zero* karena memperlakukan komunikasi *service* satu dengan *service* yang lain seperti pemanggilan fungsi lokal yang instan.

**Dampak ke FoodGo:** Ketika *service* pembayaran melambat akibat lonjakan trafik, setiap *request* baru dari *service* pesanan menumpuk dan menunggu tanpa batas yang dapat menyebabkan *resource server* habis. Yang pada akhirnya menyebabkan "server backend kadang crash total dan perlu di-restart manual".

**Solusi desain awal:** Menetapkan *timeout* di setiap komunikasi/pemanggilan antar *service*. Serta membuat pesan bahwa suatu *sevice* sedang bermasalah dan tidak menerima *request* baru dengan langsung membuat *request* tersebut gagal/batal.

**Trade-off:** *Time out* yang terlalu ketat bisa membatalkan *request* yang sebenarnya masih bisa berhasil jika ditunggu sedikit lebih lama, sehingga membuat order gagal menjadi banyak, yang dapat menyebabkan pengguna menganggap bahwa aplikasi sedang *down* atau rusak.

---

## Pitfall 3: [nama pitfall] — ditulis oleh [nama]

(ulangi struktur di atas)

---

## Kesimpulan Kelompok

[Ringkasan: jika FoodGo memperbaiki ketiga pitfall ini, apa arsitektur yang disarankan secara garis besar? Kaitkan dengan Tugas 2.]
