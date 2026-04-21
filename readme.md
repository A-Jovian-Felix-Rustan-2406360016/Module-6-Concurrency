Commit 1 Reflection Notes : 
Kode tersebut mencoba membangun sebuah server TCP dasar untuk mendengarkan koneksi jaringan yang masuk dari suatu browser dan membaca permintaan HTTP (HTTP request) yang dikirimkan oleh browser tersebut. Berikut penjelasan detailnya : 
- TopListener::bind : bagian ini inisialisasi dan mengikat server untuk mendengarkan pada alamat IP lokal (127.0.0.1)
- listener.incoming() : Sebuah iterator yang akan terus berjalan dan menunggu koneksi yang masuk dari klien. Setiap kali browser mengakses alamat tersebut, maka iterator ini akan menghasilkan sebuah TcpSteam yang nantinya akan ditangani oleh fungsi handle_connection(stream: TcpStream).
- handle_connection(stream: TcpStream) : Fungsi ini dipanggil untuk menangani setiap koneksi yang berhasil dibuat.
- BufReader::new(&mut stream) : Aliran data TCP tersebut diwrap menggunakan BufReader sehingga pembacaan data lebih efisien karena menggunakan buffer (memory sementara).
- Proses parsing : 
    1. .lines() : membaca data dari stream baris per baris
    2. .map(|result| result.unwrap()) : mengambil nilai String dari Result
    3. .take_while(|line| !line.is_empty()) : Bagian logika ini bertujuan untuk membaca HTTP request dan terus membaca baris dari stream sampai menemukan baris kosong karena protokol HTTP memisahkan bagian header dan body menggunakan sebuah baris kosong. 
    4. .collect() : kumpul semuua data yang telah dibaca ke dalam sebuah struktur data Vec<String>.

- println! : cetak isi dari HTTP request yang telah dibaca tersebut ke terminal sehingga kita dapat melihat metadata apa saja yang dikirimkan browser.

Commit 2 Reflection Notes : 
Pada tahap ini, kita mengubah kode yang tadinya hanya mendengarkan menjadi merespons. Sekarang fungsi handle_connection tidak hanya membaca, tetapi juga bisa respon. Berikut adalah penjelasan perubahannya :
- status_line = "HTTP/1.1 200 OK" : Mendefinisikan status line yang berarti berhasil ke browser.
- contents = fs::read_to_string("hello.html").unwrap() : Menggunakan modul file system untuk membuka file hello.html dan membaca semua teks tersebut ke variabel contents.
- length = contents.len() : Menghitung panjang dari content yang dibaca tadi
- let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}") : Merakit respons yang nantinya akan dikirimkan. status_line memakai respons berhasil tadi, \r\n untuk karakter khusus pindah baris, Content-Length sebagai header untuk ukuran data yang dikirimkan, \r\n\r\n untuk dua kali pindah baris sebagai tanda pemisah mutlak antara header dan body. Contents untuk isi dari file HTML 
- stream.write_all(response.as_bytes()).unwrap() : Terakhir, data yang sudah dirakit tadi dikirimkan dengan format kumpulan bytes dan dikirim melalui TCP stream menuju browser pengguna.

Jadi kesimpulannya, server akan menerima suatu request, kemudian mengambil file html yang diminta, lalu membungkus isi file tersebut dan akhirnya mengirimkan kembali ke browser dalam bentuk bytes melalui koneksi TCP.


![Commit 2 screen capture](/assets/images/commit2.png)




Commit 3 Reflection Notes : 
Pada tahap ini, saya mempelajari cara server memvalidasi request yang masuk dan melakukan refactoring struktur kode agar lebih optimal. 

- Pemisahan Respons : Sebelumnya server selalu mengirimkan file hello.html untuk semua request yang diminta client. Sekarang, kode tersebut menerapkan logika if else untuk memeriksa baris pertama request. Jika browser meminta path '/' (GET / HTTP/1.1), maka server akan merespons dengan status 200 OK dan menampilkan halaman hello.html. Lalu, jika browser meminta path lain, server akan langsung masuk ke blok else dimana akan mengirim status 404 NOT FOUND dan menampilkan 404.html. Pemisahan ini penting karena dalam aplikasi nyata, server harus membedakan berbagai routing sehingga dapat memberikan umpan balik yang tepat kepada pengguna.

- Refactoring : Awalnya, kode di dalam if dan else memiliki banyak duplikasi seperti pembacaan data, perhitungan length sampai proses pengiriman. Hal ini membuat kode menjadi panjang dan sulit dikelola jika terjadi perubahan di kemudian hari. Beberapa alasan detailnya : 
    1. Melanggar prinsip DRY : Dengan memisahkan bagian yang berbeda ke dalam blok if else, kita berhasil menghilangkan duplikasi logika.
    2. Maintenance : Jika nanti kita ingin mengubah cara server membaca file, kita hanya perlu mengubahnya di satu tempat, bukan di setiap blok percabangan. 
    3. Readability : Struktur kode yang jauh lebih bersih memudahkan kita untuk membaca dan mengerti tujuan kode tersebut. Blok if else sekarang hanya memiliki satu tanggung jawab.









![Commit 3 screen capture](/assets/images/commit3.png)






Commit 4 Reflection Notes : 
Pada tahap ini, saya mempelajari keterbatasan utama dari server yang sudah dibuat tadi karena hanya mengandalkan satu thread untuk berjalan (single thread). Dalam kode ini, kita diperlihatkan dengan fenomena blocking pada single thread melalui suatu simulasi menggunakan thread::sleep(Duration::from_secs(10)). Karena server bersifat single thread, maka ia hanya bisa mengerjakan satu tugas pada satu waktu. Hal ini disimulasikan ketika pengguna akses '/sleep', maka seluruh server akan blocked selama 10 detik. Selama masa tunggu tersebut, server tidak bisa menerima permintaan lain, bahkan untuk rute biasa seperti '/'.




Commit 5 Reflection Notes : 




Commit Bonus Reflection Notes : 