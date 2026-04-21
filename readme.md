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




![Commit 2 screen capture](/assets/images/commit2.png)


Commit 3 Reflection Notes : 









![Commit 3 screen capture](/assets/images/commit3.png)






Commit 4 Reflection Notes : 





Commit 5 Reflection Notes : 




Commit Bonus Reflection Notes : 