**NOT: Lütfen sadece burada ki yazılara bağlı kalmayın. Elinizden geldiğince Docker, Docker-Compose, Containerler ile ilgili araştırma yapın.**

Kaynak: [Docker](https://docs.docker.com/) [Wikipedia](https://en.wikipedia.org/wiki/Docker_(software))


# Wordpress Container Açıklamaları

Aşağıda wordpress için oluşturulan Dockerfile dosyası ve bu dosyada kullanılan diğer dosyaların içerikleri açıklanmıştır. Bu dosyalar, wordpress'nin Docker imajının oluşturulması için kullanılmaktadır.

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
FROM debian:buster # Debian Buster imajı kullanılacak

RUN apt-get update && apt-get -y install php7.3 php-mysqli php-fpm wget sendmail # Sistemde yer alan paketlerin güncellenmesi ve php7.3, php-mysqli, php-fpm, wget ve sendmail paketlerinin yüklenmesi

EXPOSE 9000 # 9000 portu dışarıya açılıyor

COPY ./conf/www.conf /etc/php/7.3/fpm/pool.d # host üzerinde bulunan ./conf/www.conf dosyası, container içindeki /etc/php/7.3/fpm/pool.d dizinine kopyalanıyor

COPY ./tools /var/www/ # host üzerinde bulunan ./tools dizini, container içindeki /var/www/ dizinine kopyalanıyor

RUN chmod +x /var/www/wordpress_start.sh # /var/www/wordpress_start.sh dosyasının çalıştırılabilir yapılması

ENTRYPOINT ["/var/www/wordpress_start.sh"] # /var/www/wordpress_start.sh dosyasının çalıştırılması

CMD ["/usr/sbin/php-fpm7.3", "--nodaemonize"] # php-fpm7.3 servisinin başlatılması. --nodaemonize parametresi, php-fpm7.3 servisinin arka planda çalıştırılmasını sağlar
```

</details>

<details>
<summary>wordpress_start.sh İçeriği</summary>

```bash
#!/bin/bash # bash kabuğu kullanılacak

	sed -i "s/listen = \/run\/php\/php7.3-fpm.sock/listen = 9000/" "/etc/php/7.3/fpm/pool.d/www.conf"; # /etc/php/7.3/fpm/pool.d/www.conf dosyasındaki listen = /run/php/php7.3-fpm.sock satırı listen = 9000 olarak değiştiriliyor. Bunun sebebi PHP-FPM'nin Unix soketleri yerine TCP soketleri kullanarak çalışmasını sağlayacaktır. Bu değişiklik, PHP-FPM'yi nginx veya Apache HTTP Server gibi başka bir web sunucusuyla birlikte kullanırken faydalı olabilir.

	chown -R www-data:www-data /var/www/*; # /var/www dizini ve altındaki tüm dosyaların ve dizinlerin sahibi www-data kullanıcısı ve grubu yapılıyor

	chown -R 755 /var/www/*; # /var/www dizini ve altındaki tüm dosyaların ve dizinlerin okuma, yazma ve çalıştırma izinleri www-data kullanıcısı ve grubu için ayarlanıyor

	mkdir -p /run/php/; # /run/php dizini oluşturuluyor. -p parametresi, /run dizininin var olması durumunda dizinin oluşturulmamasını sağlar.

	touch /run/php/php7.3-fpm.pid; # /run/php/php7.3-fpm.pid dosyası oluşturuluyor

if [ ! -f /var/www/html/wp-config.php ]; then # /var/www/html dizini içerisinde wp-config.php dosyası yoksa

	echo "Wordpress: setting up..." # Ekran çıktısı

	mkdir -p /var/www/html # /var/www/html dizini oluşturuluyor. -p parametresi, /var/www dizininin var olması durumunda dizinin oluşturulmamasını sağlar.

	wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar; # wp-cli.phar dosyası indiriliyor. "wp-cli.phar" WordPress kullanıcıları tarafından kullanılan bir komut satırı aracıdır. WordPress yöneticileri, bir sunucuda veya web sitesinde otomatik işlemler gerçekleştirmek için kullanabilirler. Bu araç, WordPress sitelerindeki dosya ve veritabanı işlemlerini, eklenti ve tema yönetimini, site ayarlarını yönetmeyi, kullanıcı hesaplarını yönetmeyi vb. birçok işlemi kolaylaştırır. WP-CLI, PHP tabanlı bir araçtır ve kullanımı için öncelikle PHP ve bir terminal/komut satırı arayüzü gerektirir. "wp-cli.phar" dosyası, WP-CLI'nin çalıştırılabilir bir dosyasıdır ve kurulum için kullanılır.

	chmod +x wp-cli.phar; # wp-cli.phar dosyasının çalıştırılabilir yapılması

	mv wp-cli.phar /usr/local/bin/wp; # wp-cli.phar dosyasının /usr/local/bin dizinine taşınması

	cd /var/www/html; # /var/www/html dizinine geçiş yapılıyor

	wp core download --allow-root; #  wp core download, WordPress'in belirtilen sürümünü indirmek için kullanılır. --allow-root bayrağı, WP-CLI'nin kök kullanıcı olarak çalıştırılmasına izin verir. Yani, bu komut ile belirtilen sürümdeki WordPress dosyaları indirilir ve sisteme kurulmak üzere hazırlanır. Sürüm belirtilmezse, en son WordPress sürümü indirilir.

	mv /var/www/wp-config.php /var/www/html/ # /var/www dizini içerisinde bulunan wp-config.php dosyası /var/www/html dizinine taşınıyor

	echo "Wordpress: creating users..." # Ekran çıktısı

	wp core install --allow-root --url=${WP_URL} --title=${WP_TITLE} --admin_user=${WP_ADMIN_LOGIN} --admin_password=${WP_ADMIN_PASSWORD} --admin_email=${WP_ADMIN_EMAIL} # Bu komut WordPress kurulumunu gerçekleştirir. Aşağıdaki parametrelerle birlikte kullanılır:

	# --allow-root: Bu parametre, root kullanıcısı olarak WordPress komutlarını çalıştırmaya izin verir.

	# --url: WordPress sitesinin URL'sini belirtir.

	# --title: WordPress sitesinin başlığını belirtir.

	# --admin_user: Yönetici hesabının kullanıcı adını belirtir.

	# --admin_password: Yönetici hesabının parolasını belirtir.

	# --admin_email: Yönetici hesabının e-posta adresini belirtir.

	# Örneğin, --url parametresi http://example.com şeklinde, --admin_user parametresi admin şeklinde ve --admin_password parametresi mypassword şeklinde belirtilebilir. Bu komut, WordPress'i belirtilen URL'de kuracak ve belirtilen yönetici kullanıcı adı ve parolasıyla bir yönetici hesabı oluşturacaktır.

	wp user create --allow-root ${WP_USER_LOGIN} ${WP_USER_EMAIL} --user_pass=${WP_USER_PASSWORD}; # Bu komut, WordPress sitenizde yeni bir kullanıcı hesabı oluşturur. Komut, WP-CLI aracılığıyla çalıştırılır ve aşağıdaki parametreleri alır:

	# --allow-root: WP-CLI'nin root kullanıcısı altında çalışmasına izin verir.

	# ${WP_USER_LOGIN}: Yeni kullanıcının giriş yapacağı kullanıcı adını belirtir.

	# ${WP_USER_EMAIL}: Yeni kullanıcının e-posta adresini belirtir.

	# --user_pass=${WP_USER_PASSWORD}: Yeni kullanıcının şifresini belirtir.

	# Bu komut çalıştırıldığında, belirtilen kullanıcı adı, e-posta adresi ve şifreyle bir kullanıcı hesabı oluşturulur.

	echo "Wordpress: set up!" # Ekran çıktısı
fi

exec "$@" # exec "$@" satırı, bir shell scriptin son satırıdır ve scriptin çalıştırılmasını sağlar. $@ değişkeni, scripte verilen tüm argümanları içerir. exec komutu, mevcut shell işlemini sonlandırarak verilen komutu çalıştırır. Yani exec "$@", shell scriptin sonlandırılmasına ve scripte verilen argümanların kullanılarak bir komutun çalıştırılmasına yol açar. Bu sayede, script sonlandırılmadan önce son komutun çıkışı (exit status) scriptin çıkışı olarak kullanılır. 
```

</details>

<details>
<summary>wp-config.php İçeriği</summary>

**Aşağıda, wp-config.php dosyasının yorum satırlarından arındırılmış hali vardır!** <br><br> WordPress'in temel yapılandırma dosyasıdır ve WordPress kurulumunu yapılandırmak için kullanılır. Bu dosya, WordPress'in veritabanı ayarlarını, tablo ön eki, kimlik doğrulama anahtarları, karakter seti vb. gibi çeşitli ayarlarını içerir.

Ayrıca, geliştirme ortamlarında hata ayıklama modunu etkinleştirmek ve güvenlik anahtarlarını tanımlamak için kullanılır. Bu dosyada yapılan değişiklikler, WordPress sitenizde yapılandırma ve güvenlik açıklarını önlemek için önemlidir.

```php
<?php
define( 'DB_NAME', 'wordpress' ); # WordPress veritabanının adı
define( 'DB_USER', 'test' ); # WordPress veritabanı kullanıcısı
define( 'DB_PASSWORD', '12345' ); # WordPress veritabanı kullanıcısının parolası
define( 'DB_HOST', 'mariadb' ); # WordPress veritabanının barındığı sunucu
define( 'DB_CHARSET', 'utf8' ); # WordPress veritabanının karakter seti
define( 'DB_COLLATE', '' ); # WordPress veritabanının karakter sıralaması

define('AUTH_KEY',         'IC&cVT&-%Gs8W}2xjnvj>`O+e:D!9@,QzZ,L`F[+t;jT[,Ke#^B)Gv^>[Bg{3u<5'); # WordPress kimlik doğrulama anahtarı
define('SECURE_AUTH_KEY',  'y)QI~k-PuOQO$-xpOJu&] ST;bu_y>GOPuLTH^AhzsX6^bl{m+k%7GBz9`uAX7G:'); # WordPress güvenli kimlik doğrulama anahtarı
define('LOGGED_IN_KEY',    'ome62RPT371rC^Dft.rWq  *QPr])nv--gs@l3Vg>|6C,L=+UC$xIrsLsV|C|~hT'); # WordPress oturum açma anahtarı
define('NONCE_KEY',        'ZM p<3k[.PdZFH$!)_nc`2;`=en[b(+XE!70|-3-t<@8)fhuZhwf[q4BXITgIl}J'); # WordPress nonce anahtarı
define('AUTH_SALT',        ':)op+:mW,sQP6)bxH-Lq%VLfy~6#-z*5JDBD2,+EkxH`c3z~lg/3864nRO|14s12'); # ...
define('SECURE_AUTH_SALT', 'Jho5)/aUQI{v]4D?u(7d#Ms j-a=:Ky{V4;DjqrrHD^.JGUwWzOXKRDAlM5LjmT}'); # ...
define('LOGGED_IN_SALT',   ' 5FT>@G4@j`s/R]:`3|$qFE1lx|QcWG@fL`na<I+b+m|8;pXunPWD,E%yT94eKp_'); # ...
define('NONCE_SALT',       '/5wP4{[*.8N=m*7!f- jBO-i);!^G/^`Suw>=RR*)p~}Q4q KeE/^s|ybxmOwqjg'); # ...

$table_prefix = 'wp_'; # WordPress tablo ön eki. Örneğin, wp_posts tablosu, wp_ ön ekiyle wp_wp_posts şeklinde oluşturulur.

define( 'WP_DEBUG', false ); # Hata ayıklama modunu etkinleştirir. Geliştirme ortamlarında true, canlı ortamlarda false olmalıdır.

if ( ! defined( 'ABSPATH' ) ) { # WordPress dosyalarının bulunduğu dizin tanımlı değilse
	define( 'ABSPATH', '/var/www/html/wordpress' ); # WordPress dosyalarının bulunduğu dizini tanımlar
}

require_once ABSPATH . 'wp-settings.php'; # require_once ABSPATH . 'wp-settings.php'; satırı, WordPress'in varsayılan ayarlarını yüklemek için kullanılır. ABSPATH sabiti, WordPress'in kurulum dizinini belirtir ve burada wp-settings.php dosyasının konumunu belirlemek için kullanılır. wp-settings.php, WordPress'in ana ayarlarını yükler, gerekli dosyaları dahil eder ve WordPress çalıştırmadan önce önemli işlevleri yürütür. Bu dosyanın yokluğunda, WordPress çalıştırılamaz ve hata mesajları alınabilir.
```
</details>