# Shell
## Apa itu Shell?
*Shell* dalam komputer adalah salah satu jenis program asli sistem operasi yang menyediakan **komunikasi langsung** antara pengguna dan sistem operasi. Pada catatan ini, akan dibahas penggunaan *Bourne Again SHell* atau biasa disebut **bash**.

## Dasar-dasar Shell
Saat pertama kali anda membuka terminal, maka akan terdapat *prompt* yang terlihat seperti ini:
```console
username:~$
```
Ini adalah tampilan utama dari shell. Informasi di atas memberitahukan username anda melalui `username` dan direktori yang sedang diakses, yaitu `~` atau home. Tanda `$` memberitahukan bahwa anda bukan user *root* (akan dibahas lebih lanjut nanti).

### Command dasar Shell
Pada *prompt* ini, anda dapat menuliskan perintah atau *command*. Salah satu contoh penggunaan command adalah sebagai berikut:
```console
username:~$ date
Jum 29 Mei 2020 03:46:13  WIB
username:~$
```
Contoh di atas adalah pemanggilan command `date` yang menampilkan tanggal dan waktu. Shell kemudian dapat meminta command lain untuk di eksekusi. Kita juga dapat mengeksekusi command dengan menambahkan **argumen** seperti contoh berikut:
```console
username:~$ echo hello
hello
username:~$
```
Pada kasus ini, kita memberikan perintah `echo` dengan argumen `hello`. Program `echo` hanya melakukan print sesuai dengan argumen yang diberikan. Shell dapat melakukan *parsing* terhadap argumen yang diberikan dengan mengidentifikasi *whitespace*.

### Penggunaan argumen
Jika anda ingin memberikan argumen dengan spasi atau karakter spesial lain, anda dapat menggunakan karakter `'` atau `"`, atau dengan meng*escape* karakter tertentu dengan `\`.
```console
username:~$ echo "hello world"
hello world
username:~$ echo hello\ world
hello world
```
Kita dapat mengetahui program apa yang sedang dieksekusi saat suatu perintah dipanggil dengan memanggil perintah `which`.

## Menavigasi file dalam Shell
### Apa itu Path?
**Path** dalam shell adalah sebuah *list* yang terbagi-bagi dengan karakter `/` pada Linux dan MacOS, atau `\` pada Windows. Pada Linux dan MacOS, path `/` adalah *root* atau *akar* dari sistem file, dimana semua direktori dan semua file berada di bawahnya. Sebuah path yang diawali dengan `/` adalah **absolute path**, artinya path yang diberikan dapat ditarik ke *root*. Selain dari ketentuan di atas adalah **relative path**. *Relative path* adalah path yang relatif terhadap direktori yang sedang diakses. Untuk mengetahui direktori apa yang sedang diakses dalam shell dapat dipanggil perintah `pwd`.
```console
username:~$ pwd
/home/username
username:~$
```
### Perintah cd dan ls
Perintah `cd` pada shell digunakan untuk mengubah pengaksesan direktori. Perintah `cd` mengambil argumen sebagai path yang akan diakses. Dalam path, simbol `.` merupakan direktori yang sedang diakses sedang simbol `..` merupakan direktori *parent*.
```console
username:~$ cd /home
username:/home$ pwd
/home
username:/home$ cd ..
username:/$ pwd
/
username:/$ cd ~
username:~$ ../../bin/echo hello
hello
```
Dapat diperhatikan bahwa shell selalu memberikan informasi mengenai direktori yang sedang diakses. Pada umunya, saat kita menjalankan suatu program, program tersebut akan berjalan pada direktori yang sedang diakses kecuali disebutkan secara eksplisit. 

Selanjutnya perintah `ls` digunakan untuk melihat apa saja yang terdapat pada direktori tersebut.
```console
username:~$ cd /
username:/$ ls
bin    
dev   
lib    
...
```
Kebanyakan dari perintah-perintah dalam shell menerima **flag** dan *options* (*flag* dengan suatu nilai) yang dimulai dengan karakter `-` untuk mengubah perilakunya. Biasanya, memanggil perintah dengan flag `-h` atau `--help` dapat memberikan informasi mengenai flag-flag yang didukung. Contohnya, perintah `ls --help` memberikan informasi:
```console
...
-k, --kibibytes            default to 1024-byte blocks for disk usage; used only with -s and per directory totals
-l                         use a long listing format
-L, --dereference          when showing file information for a symbolic link, show information for the file the link
...
```
```console
username:~$ ls -l /home
drwxr-xr-x 1 username 4096 Jun 29 2020 username
username:~$
```
Perintah `ls -l` di atas memberikan informasi yang lebih mendalam mengenai direktori saat ini. Karakter pertama `d` menginformasikan bahwa `username` adalah sebuah direktori. Kemudian dilanjutkan dengan tiga karakter `rwx` yang mengindikasikan *permission* milik pemilik file (`username`), grup (`users`), dan orang lain terhadap item tersebut. Dan seterusnya (*to be updated later*).

## Perintah-perintah dasar Shell
Perintah-perintah lain dalam shell yang cukup berguna antara lain `mv` untuk memindahkan atau *rename* file, `cp` untuk meng-*copy*, `mkdir` untuk membuat direktori, `man` untuk membuka manual, serta `xdg-open` (pada Linux, untuk MacOS menggunakan `open`) untuk membuka file dengan program yang sesuai.

## Mengoneksikan program
Dalam shell, program memiliki dua *alur* (*stream*) utama yang terasosiasi dengan perintah: input stream dan output stream. Contohnya, saat suatu program mencoba untuk mengambil input, program tersebut membaca dari input stream, pun berlaku sebaliknya. Biasanya, input dan output program berada di terminal. Namun kita dapat mengarahkan ulang (*redirect*) stream tersebut.

### Melakukan redirect stream
Contoh sederhana dari redirect adalah `< file` dan `> file`. Perintah tersebut digunakan untuk mengatur arah tujuan stream input dan output dari sebuah program pada file:
```console
username:~$ echo hello > hello.txt
username:~$ cat hello.txt
hello
```
Perintah `cat` pada contoh di atas adalah untuk menampilkan isi dari sebuah file `.txt`.

Anda juga dapat menggunakan simbol `>>` untuk melakukan *append* file. Simbol ini akan dibahas lebih lanjut pada penggunaan *pipes*. Simbol `|` digunakan untuk mengaitkan berbagai program sehingga output suatu program merupakan output dari program yang lain.
```console
username:~$ ls -l / | tail -n1
drwxr-xr-x 1 root root 4096 Jun 29 2020 var
```
Perintah `tail` digunakan untuk membaca `n` baris dari suatu input.

## Alat yang serbaguna
Pada sistem UNIX seperti Linux, terdapat satu user yang "spesial", yakni *root*. User root dapat mengakses, membaca, mengubah, dan menghapus semua file dalam sistem. Anda biasanya tidak langsung menjadi root, karena sistem dapat rusak dengan mudah. Namun, anda dapat mengakses hak-hak user root dengan menggunakan perintah `sudo`. Dengan akses root, anda diberi akses untuk mengubah file-file dalam **sysfs**. `sysfs` menyimpan berbagai parameter kernel sebagai file, sehingga anda dapat mengkonfigurasi kernel tanpa menggunakan alat khusus.