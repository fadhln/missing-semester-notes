# Scripting dalam shell
*Shell scripting* adalah proses automasi perintah-perintah dalam shell menggunakan *flow control* dan sintaks tertentu.
## Variabel bash
Bash mengenal variabel yang dapat digunakan untuk menyimpan nilai tertentu. Variabel tersebut dapat digunakan dalam scripting. Sebagai contoh, untuk menentukan suatu variabel dalam bash, digunakan perintah `foo=bar`. Untuk mengakses nilai dari variabel tersebut, gunakan perintah `$foo`. Perlu diperhatikan bahwa shell sangat memperhatikan spasi untuk melakukan *parsing*, sehingga perintah `foo = bar` tidak dapat dilakukan.
```bash
username:~$ foo=bar
username:~$ echo $foo
bar
```
## Delimiter
String pada bash dapat didefinisikan dalam simbol `'` dan `"` (*delimiter*). Kedua simbol tersebut memiliki fungsi yang berbeda. String yang berada di antara simbol `'` adalah string **sebenarnya** dan nilai-nilai yang terdapat pada string tersebut tidak akan dievaluasi, namun nilai-nilai dalam string yang berada di antara simbol `"` akan dievaluasi.
```bash
username:~$ echo 'hello $foo'
hello $foo
username:~$ echo "hello $foo"
hello bar
```
## Kendali alur
Seperti bahasa pemrograman pada umumnya, bash mendukung kendali alur program seperti `if`, `case`, `while`, dan `for`. Serupa dengan itu, bash juga memiliki fungsi yang dapat menerima argumen.
```bash
mcd(){
    mkdir -p "$1"
    cd "$1"
}
```
Dalam contoh di atas, `$1` merupakan argumen pertama dari fungsi/script. Berbeda dengan bahasa pemrograman lain, bash menggunakan berbagai variabel spesial untuk argumen, kode error, dan variabel relevan lain. Beberapa contoh dari variabel tersebut antara lain:
- `$0` - Nama script
- `$1` sampai `$9` - Argumen script, `$1` adalah argumen pertama dan seterusnya
- `$@` - Semua argumen
- `$#` - Jumlah argumen
- `$?` - Kode *return* dari perintah sebelumnya
- `$$` - *Process Identification Number* (PID) dari script yang sedang berjalan
- `!!` - Keseluruhan dari perintah sebelumnya, termasuk argumen. Biasanya dipakai jika ingin memanggil perintah dengan `sudo !!`.
- `$_` - Argumen terakhir dari perintah sebelumnya.

Daftar yang lebih lengkap mengenai informasi di atas dapat diakses di [link ini](https://www.tldp.org/LDP/abs/html/special-chars.html).
## Kode *return*
Seringkali perintah me*return* ouput dengan menggunakan `STDOUT`, dan error menggunakan `STDERR`, dan sebuah kode return untuk menandakan error. Kode error atau *exit status* adalah cara yang digunakan oleh script/perintah untuk menandakan bagaimana proses berjalan. Nilai 0 menandakan bahwa proses berjalan normal; selain itu berarti terdapat error.
## Perintah kondisional dan operasi boolean

Kode *exit* yang dibahas pada bagian sebelumnya dapat digunakan menjadi syarat kondisional untuk menjalankan perintah. Operator kondisional dalam bash antara lain `&&` (operator AND) dan `||` (operator OR). Perintah juga dapat dipisahkan dalam satu baris yang sama menggunakan semikolon `;`. Perintah `true` selalu memiliki nilai kode return 0, sedang perintah `false` selalu memiliki nilai kode return 1.
```bash
username:~$ false || echo "Oops, fail"
Oops, fail
username:~$ true || echo "Will not be printed"

username:~$ false && echo "Things went well"
Things went well
username:~$ true && echo "Will not be printed aswell"

username:~$ false ; echo "Will always run"
Will always run
```
## Substitusi perintah dan proses
### Substistusi perintah
Ketika dituliskan `$(CMD)` maka perintah tersebut akan mengeksukisi `CMD` dan mengambil outputnya untuk disubstitusikan ke posisi perintah tersebut. Contohnya, jika dituliskan `for file in $(ls)`, maka shell akan mengeksekusi `ls` dan kemudian mengiterasi di atas nilai tersebut.
### Substitusi proses
Perintah `<(CMD)` akan mengeksekusi `CMD` dan menempatkan outputnya pada sebuah file sementara dan mensubstitusikan `<()` dengan nama file tersebut. Langkah ini berguna saat sebuah perintah meminta sebuah input berbentuk file, bukan `STDIN`. Contohnya, `diff <(ls foo) <(ls bar)` akan menunjukkan perbedaan (perintah `diff`) antara file dalam direktori `foo` dan `bar`.

Contoh dari penggunaan substitusi perintah dan proses dapat diperhatikan dari file script berikut:
```bash
#!/bin/bash

echo "Starting program at $(date)" # Date will be substituted

echo "Running program $0 with $# arguments with pid $$"

for file in $@; do
    grep foobar $file > /dev/null 2> /dev/null
    # When pattern is not found, grep has exit status 1
    # We redirect STDOUT and STDERR to a null register since we do not care about them
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```
