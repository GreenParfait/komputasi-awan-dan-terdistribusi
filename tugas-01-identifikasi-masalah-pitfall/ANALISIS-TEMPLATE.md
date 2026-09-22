# Tugas 1 — Analisis Pitfall FoodGo

**Kelompok:** [nama kelompok]

| Nama | NIM | Kontribusi |
|---|---|---|
| Moch. Andy Yusuf Hendrawan E P | 103072400041 | Network is always reliable |
| Ighfir Maulana | 103072400029 | Latency is Zero |
| [Faiz Agit Zahiri] | [103072400123] | [Bandwidth is Infinite] |

## Pitfall 1: [Network is always reliable] — ditulis oleh [Moch. Andy Yusuf Hendrawan E P]

**Bukti di skenario:** Munculnya kode `#network is reliable, no need for retry` pada aplikasi Foodgo karena tim developer beransumsi kalau jaringan mereka itu dapat diandalkan dan juga sistem mengalami kegagalan dikarenakan pemanggilan antar modul tidak memiliki fungsi time-out.

**Kenapa ini keliru:** Komunikasi pada sistem terdistribusi pasti memerlukan *client* dan *server* pada jaringan komputer. Client mengirimkan berupa *Request* dan Server mengirimkan beupa Response. Baik maupun *Request* maupun Response harus melalu beberapa medium seperti router, modem, dll. Namun dalam proses pengiriman, tidak semuanya berjalan lancar karena ada beberapa faktor yang mempengaruhi meliputi masalah Hardware, Network Congestion, ataupun bug dalam software. Kondisi tersebut dinamakan packet loss. (sumber: [IR](https://www.ir.com/guides/what-is-network-packet-loss)).

Pada Aplikasi FoodGo, alasan kenapa asumsi `#network is reliable, no need for retry` bisa muncul karena testing dengan traffic rendah atau dilakukan  dengan tim developer sendiri. Oleh karena itu, saat traffic tiba tiba melonjak tinggi karena adanya event, sistem menjadi runtuh karena packet loss, koneksi terputus, dan request service yang gagal direspon.


**Dampak ke FoodGo:** Dengan sistem aplikasi FoodGo yang tidak memiliki fitur time-out atau retry, satu error skala kecil koneksi ke modul pembayaran bisa membuat sistem transaksi menjadi gagal. Hal tersebut bisa dilihat dari aplikasi yang hang karena threads menunggu respons modul pembayaran dengan tanpa batas. Lalu pada saat traffics tinggi, jumlah threads akan semakin bertambah hingga kapasitas server tidak mampu menahan karena menghabiskan connection pool dan memori, dan pada akhirnya crash total dan butuh restart manual.


**Solusi desain awal:** Mengimplementasikan fitur retry / 'coba lagi' dengan *exponential backoff* dan *jitter* saat request ke modul pembayaran gagal, lalu jumlah percobaanya akan dibatasi dengan *idempotency key* agar proses tidak bertambah banyak.

Cara kerja: Setiap kali Request gagal, sistem akan mencoba untuk mengirimkan ulang secara otomatis dengan jeda waktu yang bertambah di tiap percobaan. Selain itu, jeda waktu juga diberikan variasi agar request dari banyak user yang mencoba di waktu bersamaan tidak dapat ikut mencoba ulang di waktu yang sama sehingga tidak membanjiri server.

Contoh pada Apps: Kita bisa melihat pada aplikasi discord desktop dimana saat reconnect akan muncul pesan '1s.... 2s... 5s...' dan seterusnya.

**Trade-off:** Implementasi fitur retry akan menambah latency pada jeda tunggu sebelum request berhasil. Selain itu, jika fitur retry atau jitter tidak diatur secara tepat, request yang mencoba ulang malahan akan berisiko untuk menambah beban pada server pembayaran yang overload.

---

## Pitfall 2: Latency is Zero — ditulis oleh Ighfir Maulana

**Bukti di skenario:** Aplikasi FoodGo mengalami kegagalan sistem saat pesanan melonjak salah satunya yaitu aplikasi menjadi sangat lambat dan beberapa permintaan *timeout*.

**Kenapa ini keliru:** Karena dalam dunia jaringan, perangkat klien dan *server* berkomunikasi menggunakan jaringan komputer. Klien mengirimkan permintaan data, dan server mengirimkan *respons data*, yang mana pengirimannya dalam bentuk paket data kecil sehingga permintaan dan respons data melompat dari satu perangkat ke perangkat lain melalui medium pengirim paket dalam jaringan seperti *router*, modem, kabel serat optik, media transmisi nirkabel hingga sampai ke tujuan (sumber: [AWS](https://aws.amazon.com/id/what-is/latency/)). Sedangkan medium-medium tersebut dapat mengalami gangguan dari luar seperti hujan yang dapat mengganggu jaringan nirkabel karena air dapat menyerap dan memantulkan sinyal radio (sumber: [Vinotek](https://vinotek.id/article/koneksi-internet-sering-memburuk-saat-hujan-apa-penyebabnya)). Itu baru faktor/gangguan eksternal, belum lagi faktor/gangguan internal yang terjadi pada case aplikasi FoodGo ini. 

Seperti yang sudah kami jelaskan sebelumnya, dalam pengirimannya, data "melewati" server, yang mana dalam satu aplikasi seperti FoodGo memiliki beberapa *service*. Padahal pada suatu aplikasi, data akan berpindah berurutan dari *service* satu ke *service* yang lain. Dengan kata lain, kalau suatu *service* sedang bermasalah/terhambat, maka datanya juga tertunda/terhambat sehingga menyebabkan latensi. Terlebih lagi jika terjadi kesalahan dalam memanajemen aplikasi salah satunya salah dalam menentukan arsitekturnya.

Contoh: ketika *service* pesanan memanggil modul pembayaran dan ternyata terdapat lonjakan trafik, sehingga *sevice* pesanan menunggu respon tanpa batas waktu karena tidak dipasangi *timeout*. Karena tidak dipasangi *timeout*, *server* "terpaksa" menahan *resource* agar tidak digunakan pada proses/*service* lain sehingga menyebabkan latensi aplikasi semakin besar. Hal ini, kontradiksi dengan *Latency is Zero* karena memperlakukan komunikasi *service* satu dengan *service* yang lain seperti pemanggilan fungsi lokal yang instan.

**Dampak ke FoodGo:** Ketika *service* pembayaran melambat akibat lonjakan trafik, setiap *request* baru dari *service* pesanan menumpuk dan menunggu tanpa batas yang dapat menyebabkan *resource server* habis. Yang pada akhirnya menyebabkan "server backend kadang crash total dan perlu di-restart manual".

**Solusi desain awal:** Menetapkan *timeout* di setiap komunikasi/pemanggilan antar *service*. Serta membuat pesan bahwa suatu *sevice* sedang bermasalah dan tidak menerima *request* baru dengan langsung membuat *request* tersebut gagal/batal.

**Trade-off:** *Time out* yang terlalu ketat bisa membatalkan *request* yang sebenarnya masih bisa berhasil jika ditunggu sedikit lebih lama, sehingga membuat order gagal menjadi banyak, yang dapat menyebabkan pengguna menganggap bahwa aplikasi sedang *down* atau rusak.

---

## Pitfall 3: [Bandwidth is Infinite] — ditulis oleh [Faiz Agit Zahiri]

**Bukti Skenario:** Saat trafik naik, satu server yang menangani semua modul (pesanan, pembayaran, notifikasi kurir) kewalahan karena semuanya berjalan di satu proses monolitik yang sama.

**Kenapa ini keliru:** Karena dari bandwidth itu sendiri adalah shared resource, bukan dedicated atau bisa dimaksudkan dengan satu link yang dipakai bergantian / bersama oleh banyak proses, request, ataupun user. termasuk startup FoodGo itu sendiri akan selalu berubah - ubah pada bagian kapasitas yang tersedia tergantung siapa yang sedang memakai startup FoodGo secara bersamaan

**Dampak ke FoodGo:** Bandwidth yang cukup untuk beban rata - rata bisa jebol saat traffic burst (jam sibuk, flash sale, viral event)

**Solusi desain awal:** Menghitung estimasi payload per request dengan concurrent users dan juga frekuensi, lalu bandingkan dengan kapasitas link terlemah (biasanya last-mile client, bukan server)

**Trade-off:** Butuh waktu di fase perencanaan. kalau under-estimate, nantinya sistem tetap kena masalah. Kalau over-estimate, biaya infrastruktur jadi lebih mahal dari kebutuhan nyata.


---

## Kesimpulan Kelompok

[Ringkasan: jika FoodGo memperbaiki ketiga pitfall ini, apa arsitektur yang disarankan secara garis besar? Kaitkan dengan Tugas 2.]
