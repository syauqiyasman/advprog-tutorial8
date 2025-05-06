# Reflection

1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?
> Unary: Klien mengirim satu permintaan, server membalas dengan satu respons. \
> Server Streaming: Klien mengirim satu permintaan, server mengirim banyak respons secara bertahap. \
> Bi-directional Streaming: Klien dan server saling bertukar pesan secara bebas dan bersamaan.

2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?
> Authentication: Gunakan TLS/mTLS untuk memverifikasi identitas klien dan server. \
> Authorization: Batasi akses berdasarkan peran pengguna (role-based access control). \
> Data Encryption: Gunakan TLS untuk mengenkripsi data dalam perjalanan.

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?
> 1. Manajemen Sinkronisasi: Mengelola aliran data masuk dan keluar secara bersamaan bisa rumit, terutama untuk menjaga urutan pesan dan race conditions.
> 2. Manajemen Koneksi: Menangani putusnya koneksi atau klien yang tidak responsif perlu strategi retry atau timeout.
> 3. Penggunaan Resource: Streaming terus-menerus bisa menyebabkan kebocoran memori jika pesan tidak dikonsumsi/dibersihkan dengan benar.
> 4. Concurrency & Async: Mengelola banyak klien secara asinkron memerlukan desain yang aman terhadap thread dan efisien.
> 5. Keamanan & Validasi: Harus ada validasi setiap pesan untuk mencegah spam, penyalahgunaan, atau data berbahaya dari klien.

4. What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?
> Kelebihan:
> 1. Mudah dikombinasikan dengan tokio::mpsc::Receiver, cocok untuk model produser-konsumer.
> 2. Mendukung async dengan performa baik karena berbasis Tokio.
> 3. Ideal untuk mengirim data secara bertahap ke klien dalam layanan gRPC streaming.

> Kekurangan:
> 1. Tidak otomatis menangani error dalam aliran, harus ditangani manual.
> 2. Jika penerima lambat, antrian bisa membesar dan menyebabkan penggunaan memori tinggi.
> 3. Kurang cocok jika logika streaming sangat kompleks atau memerlukan kontrol aliran lanjutan.

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?
> Untuk menjaga kode gRPC di Rust tetap modular dan mudah dikembangkan, sebaiknya pisahkan tanggung jawab logika program ke dalam bagian-bagian terstruktur, seperti pemrosesan permintaan, konfigurasi layanan, dan utilitas umum. Gunakan abstraksi melalui trait agar mudah diuji dan diubah tanpa mengganggu keseluruhan sistem. Manfaatkan middleware untuk menangani fitur seperti autentikasi atau logging secara terpusat.

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?
> - Validasi Data: Periksa keabsahan data pembayaran (nomor kartu, saldo, dll).
> - Autentikasi dan Otorisasi: Pastikan permintaan berasal dari pengguna yang sah.
> - Manajemen Error: Tangani kegagalan jaringan, pembayaran ditolak, atau timeout.
> - Pencatatan dan Audit: Simpan log transaksi untuk keperluan audit atau pelacakan.
> - Keamanan Data: Enkripsi data sensitif dan hindari pencetakan data rahasia ke log.

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?
> Efisiensi dan Performa: gRPC menggunakan Protocol Buffers yang lebih ringan dan cepat dibandingkan JSON atau XML, mengurangi overhead komunikasi dan meningkatkan kinerja.
> Interoperabilitas: gRPC mendukung berbagai bahasa pemrograman, memungkinkan integrasi antar sistem yang dibangun dengan teknologi atau platform berbeda (misalnya, Python, Java, Go, dll).
> Sederhana dan Terstruktur: Dengan menggunakan definisi API di file .proto, gRPC mempermudah konsistensi dalam komunikasi antar layanan, namun membutuhkan tool tambahan untuk generasi kode di berbagai platform.
> Keterbatasan pada Protokol HTTP/2: Meskipun mendukung komunikasi yang efisien, gRPC bergantung pada HTTP/2, yang mungkin tidak didukung oleh beberapa platform atau infrastruktur yang lebih lama.

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?
> Kelebihan: \
> HTTP/2 memungkinkan pengiriman banyak permintaan dalam satu koneksi secara bersamaan, mengurangi latensi dan overhead dibandingkan HTTP/1.1 yang membatasi satu permintaan per koneksi. \
> Kekurangan: \
> HTTP/2 lebih kompleks untuk diimplementasikan dan memerlukan pengaturan tambahan, sedangkan HTTP/1.1 lebih sederhana dan didukung secara luas di berbagai platform dan alat.

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?
> Model request-response pada REST API mengharuskan klien untuk mengirim permintaan dan menunggu respons dari server, yang berarti komunikasi bersifat satu arah dan synchronous. Ini bisa mengurangi responsivitas dalam aplikasi yang memerlukan pembaruan real-time, karena klien harus terus-menerus melakukan polling untuk mendapatkan data terbaru. \
> Sementara itu, bidirectional streaming pada gRPC memungkinkan komunikasi dua arah secara real-time, di mana klien dan server dapat mengirim dan menerima data secara bersamaan tanpa menunggu respons satu sama lain. Ini meningkatkan responsivitas dan sangat cocok untuk aplikasi seperti chat atau streaming data yang memerlukan pembaruan terus-menerus dan komunikasi yang lebih interaktif.

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?
> Pendekatan berbasis skema pada gRPC menggunakan Protocol Buffers memberikan struktur yang ketat pada data, memastikan konsistensi dan validasi tipe data yang lebih baik. Ini memudahkan komunikasi antar layanan dan meningkatkan performa karena data lebih ringkas dan cepat diproses. Namun, pendekatan ini lebih rigid dan memerlukan pembaruan skema saat terjadi perubahan pada API. \
> Di sisi lain, JSON dalam REST API bersifat lebih fleksibel dan schema-less, memungkinkan perubahan payload tanpa perlu mengubah struktur yang ada. Ini membuat pengembangan lebih cepat dan mudah, tetapi dapat menyebabkan masalah seperti inkonsistensi data dan kesalahan validasi, karena tidak ada kontrol skema yang ketat.