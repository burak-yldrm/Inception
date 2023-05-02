**NOT: Lütfen sadece burada ki yazılara bağlı kalmayın. Elinizden geldiğince Docker, Docker-Compose, Containerler ile ilgili araştırma yapın.**

Kaynak: [Docker](https://docs.docker.com/) [Wikipedia](https://en.wikipedia.org/wiki/Docker_(software))

# Mariadb Container Açıklamaları

Aşağıda Mariadb için oluşturulan Dockerfile dosyası ve bu dosyada kullanılan diğer dosyaların içerikleri açıklanmıştır. Bu dosyalar, Mariadb'nin Docker imajının oluşturulması için kullanılmaktadır.

<details>
<summary>.dockerignore Nedir?</summary>

**'.dockerignore'** dosyası, Docker imajlarının oluşturulması sırasında yaratılacak olan imaj dosyaları için hangi dosyaların görmezden gelinmesi gerektiğini belirlemek için kullanılan bir dosyadır. Bu dosya, **'.gitignore'** dosyasına benzer bir şekilde çalışır.

Dockerfile dosyası, Docker imajının içeriği için kaynak kodu, bağımlılıkları, konfigürasyon dosyalarını ve diğer gereksinimleri içerir. Dockerfile dosyasının **'COPY**' veya **'ADD**' komutları, Docker imajının içine belirli dosyaları kopyalar. Bu dosyaların hepsi imajın boyutunu artırır ve bazı dosyalar gereksiz olabilir. **'.dockerignore**' dosyası, Dockerfile tarafından oluşturulacak dosyaların bir listesini belirler ve bu dosyaların Docker imajı oluşturulurken yoksayılmasını sağlar.

Örneğin, **'.git**' dizinini ve geçici dosyaları imajda görmezden gelmek isteyebilirsiniz. Bu dosyaların görmezden gelinmesi, imajın boyutunu azaltabilir ve gereksiz dosyaların imajda yer almasını önleyebilir.

Bir **'.dockerignore**' dosyası oluşturduktan sonra, Dockerfile dosyanızda **'COPY**' veya **'ADD**' komutlarıyla belirtilen dosyaların, bu dosyada belirtilen dosyaların dışında kalan dosyaların imajın oluşturulmasında kullanılmayacağından emin olun.

</details>

<details>
<summary>Dockerfile İçeriği</summary>

```dockerfile
FROM debian:buster # Kurulacak işletim sistemi

RUN apt-get update && apt-get install -y mariadb-server # Sistemde yer alan paketlerin güncellenmesi ve mariadb-server paketinin kurulması..

EXPOSE 3306 # Mariadb'nin kullanacağı port numarası

COPY ./conf/50-server.cnf /etc/mysql/mariadb.conf.d/ # Mariadb'nin kullanacağı konfigürasyon dosyasının kopyalanması

COPY ./tools /var/www/ # Mariadb'nin kullanacağı veritabanı ve kullanıcıların oluşturulması için kullanılacak script dosyalarının kopyalanması.

RUN service mysql start && mysql < /var/www/initial_db.sql && rm -f /var/www/initial_db.sql; # Mariadb servisinin başlatılması ve veritabanı ve kullanıcıların oluşturulması için kullanılacak script dosyasının çalıştırılması. veritabanı için 'initial_db.sql' dosyası mysql içerisinde çalıştırırıldıktan sonra silinir.

CMD ["mysqld"] # Bu komut ise, Docker konteyneri başlatıldığında çalışacak varsayılan komutu belirler. Burada, 'mysqld' servisi varsayılan olarak çalışacak şekilde ayarlanmıştır. Bu, konteyner başlatıldığında MySQL servisinin otomatik olarak başlayacağı anlamına gelir.
```

</details>

<details>
<summary>50-server.cnf İçeriği</summary>

Dosyanın yorum satırlarından ayıklanmış hali şu şekildedi:
```config
[server] # Sunucu ayarları
[mysqld] # MySQL sunucusu için ayarlar
user                    = mysql # MySQL servisinin çalışacağı kullanıcı
pid-file                = /run/mysqld/mysqld.pid # MySQL servisinin çalıştığı PID dosyasının yolu
socket                  = /run/mysqld/mysqld.sock # MySQL servisinin çalıştığı soket dosyasının yolu
port                    = 3306 # MySQL servisinin çalışacağı port numarası
basedir                 = /usr # MySQL servisinin çalışacağı dizin
datadir                 = /var/lib/mysql # MySQL servisinin kullanacağı veritabanı dizini
tmpdir                  = /tmp # MySQL servisinin kullanacağı geçici dizin
lc-messages-dir         = /usr/share/mysql # MySQL servisinin kullanacağı dil dosyalarının dizini
query_cache_size        = 16M # MySQL servisinin kullanacağı sorgu önbelleği boyutu
log_error               = /var/log/mysql/error.log # MySQL servisinin kullanacağı hata log dosyasının yolu
expire_logs_days        = 10 # MySQL servisinin kullanacağı log dosyalarının saklanacağı gün sayısı
character-set-server    = utf8mb4 # MySQL servisinin kullanacağı karakter seti
collation-server        = utf8mb4_general_ci # MySQL servisinin kullanacağı karşılaştırma seti
[mariadb] # Mariadb için ayarlar
[mariadb-10.3] # Mariadb 10.3 için ayarlar
```

Bu dosya, Mariadb'nin kullanacağı konfigürasyon dosyasıdır. Bu dosya, Mariadb'nin kullanacağı veritabanı dizini, geçici dizini, karakter seti, karşılaştırma seti, log dosyası gibi ayarları içerir. Bazı ayarlar varsayılan olarak gelmektedir. Bu ayarlar, Mariadb'nin çalışması için yeterlidir. Ancak, bu ayarlar, Mariadb'nin performansını artırmak için değiştirilebilir.

</details>

<details>
<summary>initial_db.sql İçeriği</summary>

```sql
CREATE DATABASE IF NOT EXISTS wordpress; --Eğer 'wordpress' adında bir veritabanı yoksa, bu komut ile oluşturulur.

CREATE USER IF NOT EXISTS 'test'@'%' IDENTIFIED BY '12345'; --Eğer 'test' adında bir kullanıcı yoksa, bu komut ile oluşturulur.

GRANT ALL PRIVILEGES ON wordpress.* TO 'test'@'%'; --'test' kullanıcısına, 'wordpress' veritabanı üzerindeki tüm yetkiler verilir.

FLUSH PRIVILEGES; --Yetkilerin güncellenmesi için bu komut çalıştırılır.

ALTER USER 'root'@'localhost' IDENTIFIED BY 'root12345'; --'root' kullanıcısının şifresi değiştirilir.
```

</details>