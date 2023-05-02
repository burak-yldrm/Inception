**NOT: Lütfen sadece burada ki yazılara bağlı kalmayın. Elinizden geldiğince Docker, Docker-Compose, Containerler ile ilgili araştırma yapın.**

Kaynak: [Docker](https://docs.docker.com/) [Wikipedia](https://en.wikipedia.org/wiki/Docker_(software))

# nginx Container Açıklamaları

Aşağıda nginx için oluşturulan Dockerfile dosyası ve bu dosyada kullanılan diğer dosyaların içerikleri açıklanmıştır. Bu dosyalar, nginx'nin Docker imajının oluşturulması için kullanılmaktadır.

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

RUN apt-get update && apt-get install -y nginx openssl # Sistemde yer alan paketlerin güncellenmesi ve nginx ile openssl paketlerinin kurulması

EXPOSE 443 # Container'ın 443 portunu dinlemesi için gerekli komut

COPY ./conf/default/ /etc/nginx/sites-enabled/default # host makinede bulunan default dosyasının container içindeki default dosyasına kopyalanması

COPY ./tools/nginx_start.sh /var/www # host makinede bulunan nginx_start.sh dosyasının container içindeki /var/www dizinine kopyalanması

RUN chmod +x /var/www/nginx_start.sh # nginx_start.sh dosyasının çalıştırılabilir hale getirilmesi

ENTRYPOINT ["var/www/nginx_start.sh"] # nginx_start.sh dosyasının container çalıştırıldığında çalıştırılması

CMD ["nginx", "-g", "daemon off;"] # nginx -g daemon off; komutunun container çalıştırıldığında çalıştırılması. Bu komut nginx'in arka planda çalışmasını sağlar.
```

</details>

<details>
<summary>nginx_start.sh İçeriği</summary>

```bash
#!/bin/bash //Bu dosyanın bash ile çalıştırılması için gerekli komut

if [ ! -f /etc/ssl/certs/nginx.crt ]; then // /etc/ssl/certs/nginx.crt dosyasının olup olmadığının kontrolü

echo "Nginx: setting up ssl ..."; // Ekran çıktısı

openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout /etc/ssl/private/nginx.key -out /etc/ssl/certs/nginx.crt -subj "/C=TR/ST=KOCAELI/L=GEBZE/O=42Kocaeli/CN=buyildir.42.fr"; // Burada openssl ile sertifika oluşturuluyor. Bu sertifika ile nginx ssl bağlantısı kuruluyor.

# req: sertifika isteği oluşturur.
# -x509: x509 sertifikası oluşturur.
# -nodes: sertifika için şifre kullanılmayacağını belirtir.
# -days: sertifikanın geçerlilik süresini belirtir.
# -newkey: yeni bir anahtar oluşturur.
# rsa:4096: 4096 bitlik bir rsa anahtarı oluşturur.
# -keyout: oluşturulan anahtarın kaydedileceği dosya.
# -out: oluşturulan sertifikanın kaydedileceği dosya.
# -subj: sertifikanın bilgilerini belirtir.



echo "Nginx: ssl is set up!"; // Ekran çıktısı

fi

exec "$@" // exec "$@" satırı, bir shell scriptin son satırıdır ve scriptin çalıştırılmasını sağlar. $@ değişkeni, scripte verilen tüm argümanları içerir. exec komutu, mevcut shell işlemini sonlandırarak verilen komutu çalıştırır. Yani exec "$@", shell scriptin sonlandırılmasına ve scripte verilen argümanların kullanılarak bir komutun çalıştırılmasına yol açar. Bu sayede, script sonlandırılmadan önce son komutun çıkışı (exit status) scriptin çıkışı olarak kullanılır. 
```

</details>

<details>
<summary>default İçeriği</summary>

```bash
server {
    listen 443 ssl; # IPv4 üzerinden 443 portunu dinlemesi için gerekli komut
    listen [::]:443 ssl; # IPv6 üzerinden 443 portunu dinlemesi için gerekli komut

    server_name buyildir.42.fr; # Server ismi

    ssl_certificate /etc/ssl/certs/nginx.crt; # ssl sertifikasının bulunduğu dizin

    ssl_certificate_key /etc/ssl/private/nginx.key; # ssl anahtarının bulunduğu dizin

    ssl_protocols TLSv1.3; # ssl protokolü https://datatracker.ietf.org/doc/rfc8446/

    index index.php; # Bu konfigürasyon ayarı, varsayılan olarak bir dizindeki bir dosya istendiğinde, sunucunun hangi dosya adının kullanılacağını belirler. "index" belirtimi, web sunucusunun bir dizindeki varsayılan dosya adını belirlemesine yardımcı olan bir direktiftir. Örneğin, eğer bir kullanıcı "http://www.example.com/" adresine erişirse, sunucu otomatik olarak "http://www.example.com/index.php" dosyasını sunar.

    root /var/www/html; # Nginx sunucusunda istek yapılan bir URL'nin dosya sistemi üzerindeki nerede bulunacağını belirler. /var/www/html belirtilen dosya yolu, Nginx sunucusunun kök dizinidir ve bu yolu belirten istekler, sunucunun bu dizinindeki dosyaları veya klasörleri servis edecektir. Örneğin, bir istemci /about-us URL'sine girdiğinde, Nginx sunucusu, /var/www/html/about-us yolundaki dosyaları veya klasörleri arayacaktır. index index.php direktifi, dizin içinde bulunan dosyaların sırasını belirler ve Nginx'in dizinde index.php dosyasını bulup servis etmesini sağlar.

    location / { 
        try_files $uri $uri/ /index.php$is_args$args;
    } # Bu Nginx konfigürasyon bloğu, istemci tarafından gelen tüm istekleri ele alır ve ardından aşağıdaki işlemleri gerçekleştirir:

# try_files komutu, bir dizi dosya yolu deneyerek var olan ilk dosyayı sunucuya istemek için kullanılır. Bu blokta, try_files $uri $uri/ /index.php$is_args$args; ifadesi, Nginx'in önce istemcinin isteği olan $uri dosya yolunu aramasını söyler. Eğer varsa, sunucu bu dosyayı istemciye sunar. Eğer yoksa, $uri/ ifadesi dosya yolunu bir klasör olarak kabul eder ve içindeki dosyaları aramaya devam eder. Eğer bu adımda da bir sonuç alınamazsa, /index.php$is_args$args ifadesi devreye girer ve Nginx, index.php dosyasını çalıştırır.

# $is_args$args ifadesi, istemciden gelen tüm argümanları (? karakterinden sonra gelen tüm veriler) birleştirir ve index.php dosyasına aktarır. Bu sayede PHP kodu, istemciden gelen tüm verilere erişebilir.

# Sonuç olarak, bu Nginx konfigürasyon bloğu, istemcilerin herhangi bir dosya yolunu veya dosya yolu klasörünü isteyebileceği bir web sunucusu için temel bir yapılandırmadır. İstemcilerin istekleri, öncelikle dosya yolunda aranır ve ardından index.php dosyasına aktarılır. Bu, PHP kodu tarafından işlenebilir ve sonuç, istemciye geri döndürülür.

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
# Bu blok, nginx sunucusunun PHP dosyalarının işlenmesi için yapılandırılmasını sağlar.

#location ~ \.php$ : Bu, eşleşen istek URI'nin sonunda ".php" uzantısı olan tüm PHP dosyalarını hedefleyen bir regular expression'dır.

# try_files $uri =404;: Bu, bir istek URI'sinin varlığını kontrol eder ve eğer varsa, dosyayı servis etmeye çalışır. Eğer dosya bulunamazsa, 404 hata sayfasını gösterir.

# fastcgi_split_path_info ^(.+\.php)(/.+)$; : Bu, istek URI'sinden PHP dosyasının tam adını ve yolunu çıkarmak için kullanılan bir regular expression'dır.

# fastcgi_pass wordpress:9000; : Bu, PHP uygulamasının bulunduğu adres ve portu belirtir. Bu örnekte, PHP uygulaması Docker ağındaki "wordpress" adlı bir serviste çalışıyor ve 9000 portunu kullanıyor.

# fastcgi_index index.php;: Bu, PHP dosyası belirtilmezse, varsayılan olarak "index.php" dosyasını servis etmeye çalışır.

# include fastcgi_params; : Bu, FastCGI parametrelerinin bulunduğu dosyanın dahil edilmesini sağlar.

# fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; : Bu, PHP uygulamasının dosya yolunu belirler.

# fastcgi_param PATH_INFO $fastcgi_path_info;: Bu, PHP uygulaması tarafından işlenen yolları belirler.
```

