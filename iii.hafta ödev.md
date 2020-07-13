What is task process and thread?

A **thread** is the smallest unit of execution that lies within the **process**. A **process** can have multiple **threads** running. An execution of **thread** results in a **task**. ... A **task** is simply a set of instructions loaded into the memory. **Threads** can themselves split themselves into two or more simultaneously running **tasks**.

How do threads differ from processes?

A **process** is a program under execution i.e an active program. A **thread** is a lightweight **process** that can be managed independently by a scheduler. **Processes** require more time for context switching as they are more heavy. **Threads** require less time for context switching as they are lighter than **processes**.

What are the advantages and disadvantages of threads?

Also, the execution of the process via **threads** is time-consuming. But processes cannot be run without **threads**. **Threads** are not reusable and it requires more hardware than software due to application changes from the base. **Threads** cannot be made work without process as they do not have their own address space.

What is task process and thread?

A **thread** is the smallest unit of execution that lies within the **process**. A **process** can have multiple **threads** running. An execution of **thread** results in a **task**. ... A **task** is simply a set of instructions loaded into the memory. **Threads** can themselves split themselves into two or more simultaneously running **tasks**.



How do I write an extension in Visual Studio?

**Creating Visual Studio Extensions Using Visual Studio 2019—A Complete Guide**

1. Prerequisites.

2. Create Visual Studio project template. ...

3. Add filter tags in vstemplate. ...

4. Create VSIX project. ...

5. Creating a custom project from menu. ...

6. Run the extension. ...

7. Install the extension. ...

8. Publish the extension to Visual Studio Marketplace.

   

How do I download Visual Studio extension codes?

**Visual Studio Code OFFLINE Extension Downloader**

1. Find extension you want to download at Visual Studio Code Marketplace.
2. Copy the URL of that extension from your browser's address bar and paste it into the textbox below.
3. Click Download. Download. Wait a few seconds for the download to start (once you click the Download button) Tweet.

How do you add extensions to VS codes?

You can browse and **install extensions** from within **VS Code**. Bring up the **Extensions** view by clicking on the **Extensions** icon in the Activity Bar on the side of **VS Code** or the View: **Extensions** command (Ctrl+Shift+X). This will show you a list of the most popular **VS Code extensions** on the **VS Code** Marketplace.



# SQLite Nedir ve Nasıl Kullanılır?

SQLite kullanımı ve kurulumu oldukça basit olan bir veritabanı kütüphanesidir. Özelliklerinin tam listesini sitesinde bulabilirsiniz ancak ben bazılarını buraya aktarıyorum.

- SQLite’ın çalışması için herhangi bir sunucuya ihtiyacı olmadığı için, kurulum ve ya konfigürasyon adımları yoktur.
- Her veritabanı için sadece bir dosya vardır. Bu da veritabanının yedeklenmesini ve kopyalanmasını kolaylaştırır.
- Platform bağımsızdır.
- SQLite kompakttır. Tüm kütüphanenin boyutu 225kb’dır. Bazı özellikler çıkartılarak, bu boyut 170kb’a kadar indirilebilir. Bu sayede embedded ve ya symbian gibi platformlar için uygundur.

Özelliklerin tam listesini [burada](http://www.sqlite.org/different.html) bulabilirsiniz.

SQLite’ın eşdeğeri olarak Oracle’ı ya da MySQL’i düşünmemelisiniz. Sitesinde de çok değinildiği gibi SQLite fopen’ın yerini almayı amaçlayan bir kütüphanedir. SQLite’ın size uygun olup olmadığını daha rahat anlamak için ne zaman kullanmalı sayfasına [buradan](http://www.sqlite.org/whentouse.html) göz atabilirsiniz.

SQLite ve C++, Basit bir örnek.

Öncelikle işlem yapabilmemiz için bir veritabanımızın olması gerekiyor. Bunun için http://www.sqlite.org/sqlite-3_6_16.zip adresinden sqlite3’ü indiriniz. Konsoldan sqlite3.exe’yi aşağıdaki şekilde başlatınız.

sqlite3 test.db

Argüman olarak “test.db” verdiğimiz için, herhangi bir tablo yarattığınızda, bu tablolar test.db dosyasına kayıt edilecektir.

Şimdi test veritabanımızda bir tablo oluşturuyoruz. Aşağıdaki SQL sorgusunu açtığınız sqlite3 programında yazınız.

CREATE TABLE users( id INTEGER PRIMARY KEY,
name VARCHAR(25),
lastname VARCHAR(25),
email VARCHAR(100)
);

Böylelikle, veritabanımızda kullanıcıları tutabileceğimiz bir tablo oluşturmuş olduk. id Primary Key olarak tanımlandığı için otomatik olarak arttırılacaktır (autoincrement), bu SQLite’ın bir özelliği, çünkü normalde primary key olmasının haricinde autoincrement olarak da tanımlamadan o kolon autoincrement olmaz. Name ve lastname 25 karakter ve email ise 100 karakter olarak oluşturulmuştur. Programı kapatmadan önce birkaç veri girerek tabloyu dolduralım.

INSERT INTO users VALUES (NULL, ‘Ercan’, ‘Gerçek’, ‘mail@mail.com’);
INSERT INTO users VALUES (NULL, ‘İsim’, ‘Soyisim’, ‘mail@mail.com’);

Bu girdiğimiz iki veriyi listelemek için, bir SELECT sorgusu atmamız yeterli olacaktır.

SELECT * FROM users;

dediğiniz taktirde kullanıcılar listelenecektir.
( ‘;’ koymayı unutmayın )

Eğer kolon isimlerini de görmek istiyorsanız

.explain on

yazarak bu seçeneği açabilirsiniz. Bundan sonraki SELECT sorgularında kolon isimleride listelenecektir.

Diğer komutlarıda görmek için ‘.help’ yazmanız yeterlidir.

Eğer bir tablonun içeriği bir dosyaya yazdırmak isterseniz, aşağıdaki komutları girin.

.output deneme.txt
SELECT * FROM users;

ve çıkışı eski haline getirmek için tekrar

.output stdout

demeniz gerekiyor. Aksi taktirde tüm sorguların cevapları deneme.txt dosyasına yazdırılacaktır.

Artık bir veritabanımız olduğuna göre, bunu açmak için bir C++ programı yazabiliriz.
Önce SQLite’ın kaynak kodunu [buradan](http://www.sqlite.org/sqlite-amalgamation-3_6_16.zip) indiriniz. (Güncel versiyonu için sitesine bakınız.)

main.cpp
==================================================
\#include <stdio.h>
\#include “sqlite3.h”

int main(int argc, char **argv)
{
sqlite3 *db;
// veritabanına bağlantı açıyoruz.
int ret = sqlite3_open(“test.db”, &db);
sqlite3_stmt *stmt;
int rc = sqlite3_prepare(db, “SELECT * FROM users”, -1, &stmt, 0);
while(sqlite3_step(stmt) == SQLITE_ROW) {
printf( “id:%s, name:%s, lname:%s, email:%s \n”,
sqlite3_column_text(stmt, 0),
sqlite3_column_text(stmt, 1),
sqlite3_column_text(stmt, 2),
sqlite3_column_text(stmt, 3));
};
sqlite3_finalize(stmt);
sqlite3_close(db);

return 0;
}
==================================================

Eğer id gibi değerleri integer olarak almak istiyorsanız,

int id = sqlite3_column_int(stmt, 0);

demeniz yeterli.

SQLite projesinin C/C++, PHP, Perl, Python, Tcl gibi diller için hazır API’si bulunmakta, fakat C# için bulunmamaktadır.







