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
Fungsi dari file di atas adalah mencari string `foobar` dari tiap-tiap file yang dimasukkan ke dalam argumen. Baris `for file in $@; do` berguna untuk melakukan iterasi pada semua file yang menjadi argumen dan memasukkannya pada variabel `file`. Selanjutnya, dipanggil perintah `grep foobar $file > /dev/null 2> /dev/null`, baris ini berfungsi untuk mencari string `foobar` pada file yang selanjutnya diredirect ke direktori `/dev/null`. Jika string tidak ditemukan, maka perintah `grep` di atas akan memberikan status *exit* 1. Untuk mengatasi hal ini, status *exit* tersebut diredirect menggunakan variabel `$?` dan dibandingkan dengan 0. Perbandingan pada shell dapat dibaca lebih dalam di halaman manual `test` dengan memanggil `man test`.
## Shell globbing
Argumen yang diberikan saat mengeksekusi script terkadang mirip dan cenderung repetitif. Untuk mengatasi hal tersebut, bash mendukung suatu teknik yang dapat meng*extend* suatu ekspresi nama file. Teknik ini bernama *shell globbing*.
- ***Wildcard*** - Contohnya, jika ingin menghapus beberapa file yang namanya mirip dalam satu direktori dapat digunakan simbol `*` dan `?`. Misal, terdapat file `foo1`, `foo2`, `foo10`, dan `bar`, perintah `rm foo?` akan menghapus `foo1` dan `foo2` sedangkan `rm foo*` akan menghapus semua kecuali `bar`.
- **Kurung kurawal** `{}` - Saat ingin memproses file yang memiliki *substring* mirip, contohnya `convert image.{png,jpg}` adalah sama dengan `convert image.png image.jpg`.

## Menggunakan script Python
Script untuk bash tidak selalu ditulis dalam bash. Misalnya, berikut contoh script Python sederhana yang mengoutputkan argumennya dengan membalik urutannya:
```bash
#!/usr/local/bin/python
import sys
for arg in reversed(sys.arg[1:]):
    print(arg)
```
Shell dapat mengeksekusi script di atas dengan *interpreter* python karena dituliskan baris [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) di header script. Beberapa perbedaan dalam fungsi shell yang perlu diperhatikan antara lain:
- Fungsi harus dalam bahasa yang sama dengan shell, sedangkan script dapat dituliskan dalam bahasa lain. Itulah mengapa *shebang* sangat penting dalam penulisan script.
- Fungsi dieksekusi dalam *environment* shell yang sedang berjalan, sedangkan script dieksekusi dalam proses tersendiri. Sehingga fungsi dapat mengubah variabel *environment* seperti direktori dll., sedangkan script tidak bisa secara langsung. Script dapat mengakses variabel *environment* yang diekspor menggunakan `export`.

# Alat-alat dalam Shell
## Mencari cara penggunaan Perintah
Terkadang kita lupa cara menggunakan beberapa perintah yang digunakan dalam shell. Untuk mengetahui guna suatu perintah, gunakan flag `-h` atau `--h`. Beberapa perintah juga memiliki file manual yang menyediakan semacam dokumentasi terkait dengan program/perintah tersebut. Untuk mengakses manual di shell, gunakan perintah `man`.
## Mencari file
Salah satu hal yang cukup repetitif dalam alur kerja menggunakan shell adalah pencarian file. Semua sistem UNIX memiliki program bernama `find` untuk mencari file. Beberapa contoh penggunaan `find`:
```bash
# Find all directories named src
find . -name src -type d
# Find all python files that have a folder named test in their path
find . -path '**/test/**/*.py' -type f
# Find all files modified in the last day
find . -mtime -1
# Find all zip files with size in range 500k to 10M
find . -size +500k -size -10M -name '*.tar.gz'
```
## Mencari listing kode
Terkadang kita membutuhkan cara untuk mencari sesuatu dalam suatu file. Hal ini biasa dilakukan untuk mencari suatu string atau suatu pola dalam berbagai file. Shell memiliki perintah `grep` untuk melakukan itu. Perintah `grep` memiliki banyak flag yang dapat digunakan untuk mencari di dalam file. Beberapa alternatif dari `grep` telah dikembangkan untuk menunjang optimasi, salah satunya adalah ripgrep (`rg`). Beberapa contoh pencarian dalam file menggunakan `rg`:
```bash
# Find all python files where I used the requests library
rg -t py 'import requests'
# Find all files (including hidden files) without a shebang line
rg -u --files-without-match "^#!"
# Find all matches of foo and print the following 5 lines
rg foo -A 5
# Print statistics of matches (# of matched lines and files )
rg --stats PATTERN
```
## Mencari perintah shell
Sejauh ini kita telah dapat mencari file dan isinya. Terkadang kita perlu mencari perintah-perintah yang pernah kita tuliskan di shell. Untuk memenuhi itu, shell memiliki perintah `history` yang akan mencetak semua perintah yang pernah kita tuliskan. Keseluruhan perintah yang pernah kita tuliskan di suatu *device* tentu saja merupakan daftar yang sangat panjang dan cenderung *redundant*. Untuk itu, kita perlu melakukan *piping* terhadap perintah ini. Contoh implementasi dari perintah ini adalah `history | grep find`. Perintah itu akan mencari semua perintah `find` yang pernah kita tuliskan.