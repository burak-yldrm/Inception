**NOT: Lütfen sadece burada ki yazılara bağlı kalmayın. Elinizden geldiğince Docker, Docker-Compose, Containerler ile ilgili araştırma yapın.**

Kaynak: [Docker](https://docs.docker.com/) [Wikipedia](https://en.wikipedia.org/wiki/Docker_(software))

# Docker-Compose Nedir?

Docker-Compose, birden fazla container'ı tek bir komut ile yönetmemizi sağlayan bir araçtır. Docker-Compose ile birden fazla container'ı tek bir komut ile ayağa kaldırabilir, durdurabilir, silip tekrar oluşturabiliriz. Ayrıca Docker-Compose ile container'lar arasında bağlantı kurabiliriz.

# Docker-Compose Nasıl Kurulur?

Docker-Compose, Docker Desktop ile birlikte gelmektedir. Eğer Docker Desktop kurulu ise Docker-Compose kurulu demektir. Docker-Compose kurulumunu kontrol etmek için aşağıdaki komutu çalıştırabilirsiniz.

```bash
docker-compose --version
```

# Docker-Compose Nasıl Kullanılır?

Docker-Compose kullanımı için öncelikle bir docker-compose.yml dosyası oluşturulmalıdır. Bu dosya içerisinde container'ların nasıl oluşturulacağı, container'lar arasında nasıl bağlantı kurulacağı gibi bilgiler bulunur. Docker-Compose ile ilgili tüm komutlar docker-compose.yml dosyasının bulunduğu dizinde çalıştırılmalıdır.

<details>
<summary>Docker-Compose ile ilgili bazı temel komutlar</summary>

```bash
docker-compose up # docker-compose.yml dosyasındaki tüm container'ları ayağa kaldırır.

docker-compose up -d # docker-compose.yml dosyasındaki tüm container'ları arka planda ayağa kaldırır.

docker-compose up <container_adı> # docker-compose.yml dosyasındaki <container_adı> container'ını ayağa kaldırır.

docker-compose up -d <container_adı> # docker-compose.yml dosyasındaki <container_adı> container'ını arka planda ayağa kaldırır.

docker-compose down # docker-compose.yml dosyasındaki tüm container'ları durdurur.

docker-compose down <container_adı> # docker-compose.yml dosyasındaki <container_adı> container'ını durdurur.

docker-compose ps # docker-compose.yml dosyasındaki container'ların durumlarını gösterir.

docker-compose logs # docker-compose.yml dosyasındaki container'ların loglarını gösterir.

docker-compose logs <container_adı> # docker-compose.yml dosyasındaki <container_adı> container'ının loglarını gösterir.

docker-compose exec <container_adı> <komut> # docker-compose.yml dosyasındaki <container_adı> container'ında verilen komutu çalıştırır.

docker-compose events # docker-compose.yml dosyasındaki container'ların event'larını gösterir.

docker-compose top # docker-compose.yml dosyasındaki container'ların process'lerini gösterir.

docker-compose pause # docker-compose.yml dosyasındaki container'ları duraklatır.

docker-compose unpause # docker-compose.yml dosyasındaki container'ları duraklatmayı kaldırır.

docker-compose build # docker-compose.yml dosyasındaki container'ları build eder.

docker-compose config # yapılandırma dosyasını doğrular ve herhangi bir hata olup olmadığını bildirir. Eğer bir hata varsa, hata mesajı belirtilir ve nerede oluştuğu belirtilir. Bu sayede yapılandırma dosyasını kontrol etmek ve olası hataları önlemek için kullanılabilir.

docker-compose images # docker-compose.yml dosyasındaki container'ların image'larını gösterir.

docker-compose kill # docker-compose.yml dosyasındaki container'ları durdurur.

docker-compose rm # docker-compose.yml dosyasındaki container'ları siler.

docker-compose start # docker-compose.yml dosyasındaki container'ları başlatır.

docker-compose stop # docker-compose.yml dosyasındaki container'ları durdurur.

docker-compose restart # docker-compose.yml dosyasındaki container'ları yeniden başlatır.

docker-compose run # docker-compose.yml dosyasındaki container'ları ayağa kaldırır.

docker-compose scale # docker-compose.yml dosyasındaki container'ları ölçeklendirir.

docker-compose pull # docker-compose.yml dosyasındaki container'ların image'larını çeker.

docker-compose push # docker-compose.yml dosyasındaki container'ların image'larını push eder.

docker-compose create # docker-compose.yml dosyasındaki container'ları oluşturur.
```
</details>
<br>

# .yml Dosyası Nedir?

.yml dosyası, YAML (Yet Another Markup Language) formatında yazılmış dosyalardır. YAML, insanların okuyup yazması kolay olan bir veri serileştirme formatıdır. YAML, XML ve JSON formatlarına benzer. YAML, XML ve JSON formatlarına göre daha kolay okunabilir ve yazılabilir. YAML formatında yazılmış dosyaların uzantısı .yml veya .yaml'dır. Docker-Compose ile ilgili tüm yapılandırma dosyaları .yml uzantılıdır.

# Docker-compose.yml İçerisinde Komutlar Ne Anlama Gelmektedir?

<details>
<summary>VERSION</summary>
docker-compose.yml içerisinde yer alan 'version' anahtar kelimesi, docker-compose.yml dosyasının hangi versiyon ile yazıldığını belirtir. Docker-Compose ile ilgili yapılandırma dosyaları her sürümde değişiklik gösterebilir. Bu yüzden docker-compose.yml dosyasının hangi versiyon ile yazıldığını belirtmek önemlidir. Docker-Compose ile ilgili yapılandırma dosyaları için kullanılabilecek versiyonlar aşağıdaki gibidir.

```yml
version: "3.8"	# 19.03.0+
version: "3.7"	# 18.06.0+
version: "3.6"	# 18.02.0+
version: "3.5"	# 17.12.0+
version: "3.4"	# 17.09.0+
version: "3.3"	# 17.06.0+
version: "3.2"	# 17.04.0+
version: "3.1"	# 1.13.1+
version: "3.0"	# 1.13.0+
version: "2.4"	# 17.12.0+
version: "2.3"	# 17.06.0+
version: "2.2"	# 1.13.0+
version: "2.1"	# 1.12.0+
version: "2.0"	# 1.10.0+
```
</details>

<details>
<summary>SERVICES</summary>
'services' özelliği, Docker Compose dosyasında tanımlanan her bir öğenin, birbirleriyle etkileşim kurmasını sağlayan bir servis olarak çalışmasını sağlar.<br>
Bir Docker Compose dosyası, birden fazla bağımsız öğeyi tek bir projede birleştirebilir. Örneğin, bir web uygulaması için, dosya içinde birden fazla hizmet tanımlayabiliriz, biri web sunucusu, diğeri veritabanı sunucusu ve diğerleri olabilir. Bu hizmetlerin her biri, Docker Compose tarafından birbirleriyle koordine edilerek uygulamanın çalışmasını sağlar.<br>
Her bir servis, özel ayarlarla tanımlanır ve ardından Docker Compose, belirtilen ayarları kullanarak her bir servisin Docker konteynerini oluşturur. Bu sayede, tüm projeyi tek bir komutla çalıştırabilir, her bir servisi ayrı ayrı başlatmakla uğraşmamız gerekmez.<br>
Servisler, Docker Compose dosyasında "services" anahtar kelimesiyle tanımlanır ve her bir servis, adı, konteynerin ayarları, ağ bağlantıları vb. gibi ayrıntılı bilgiler içeren bir liste şeklinde belirtilir.<br><br>
Örnek bir servis tanımı aşağıdaki gibidir:

```yml
services:
  web:
	build: . # Dockerfile dosyasının bulunduğu dizin
	ports: # container'ın portlarını host'a yönlendirir
	  - "5000:5000" # container'ın 5000 portunu host'un 5000 portuna yönlendirir
	volumes: # container'ın dizinlerini host'un dizinlerine bağlar
	  - .:/code # container'ın /code dizinini host'un . dizinine bağlar
	  - logvolume01:/var/log # container'ın /var/log dizinini host'un logvolume01 dizinine bağlar
	links: # container'ın redis servisine bağlanmasını sağlar
	  - redis # redis servisini bağlar
  redis: # redis servisi
	image: redis # redis image'ını kullanarak redis servisini oluşturur
  restart: # restart servisi
	Container'ı yeniden başlatmaya yarayan servistir. Temelde dört farklı yeniden başlatma politikası vardır.
	Bu projede kullanılan "unless-stopped" container her hangi bir şekilde elle müdahale edilmeden durduğunda her zaman yeniden başlatır.
```

Burada "web" ve "redis" adında iki servis tanımlanmıştır. "web" servisi, Dockerfile dosyası içerisindeki ayarlar kullanılarak oluşturulurken, "redis" servisi, Docker Hub üzerindeki "redis" image'ı kullanılarak oluşturulur.
</details>

<details>
<summary>NETWORKS</summary>
'networks' bölümü, container'ların birbirleriyle veya dış dünya ile nasıl iletişim kuracaklarını tanımlayan bir bölümdür. Bu bölümde, farklı network modları (bridge, host, overlay vb.) ve her bir container için ayrı ayrı tanımlanabilecek özel network'ler bulunur.<br><br>
Docker Compose'da tanımlanmış bir network, aynı proje içindeki container'lar tarafından paylaşılabilir. Bu sayede, container'lar arasındaki iletişim daha kolay bir şekilde sağlanabilir ve network'ün özellikleri (DNS, IP adresleri, port mapping vb.) tek bir yerden yönetilebilir.<br><br>Örnek bir network tanımı aşağıdaki gibidir:

```yml
version: "3.9" # docker-compose.yml dosyasının versiyonu
services: # servisler
  web: # web servisi
    image: nginx:latest # nginx image'ını kullanarak nginx servisini oluşturur
    networks: # network'ler
      - my-network
  db: # db servisi
    image: postgres:latest # postgres image'ını kullanarak postgres servisini oluşturur
    networks: # network'ler
      - my-network
networks: # network'ler
  my-network: # my-network adında bir network oluşturur
    driver: bridge # bridge modunda bir network oluşturur. 'bridge' sürücüsü, Docker'ın yerel ağıdır. Bu, Docker konteynerleri arasında iletişim kurmalarını sağlayan varsayılan bir ağdır.
```

</details>

<details>
<summary>VOLUMES</summary>
'volumes', Docker container'larının kullanabileceği disk alanını sağlamak için kullanılan bir mekanizmadır. Docker container'ları çalıştırılırken, disk alanı ihtiyaçları host işletim sisteminden karşılanır. Ancak bu, verilerin container'lar arasında veya host makine ile container'lar arasında taşınması, yönetilmesi ve korunması zorluğu yaratabilir.<br><br>
Docker volumes, container'ların kullanabileceği özel bir disk alanı sağlayarak, verilerin container'lar arasında veya host ile container'lar arasında daha kolay taşınmasını, yönetilmesini ve korunmasını sağlar. Bu disk alanları, container'lar arasında paylaşılabileceği gibi, container'a özgü olacak şekilde de ayarlanabilir.<br><br>
Örneğin, bir web uygulaması container'ının konfigürasyon dosyaları, log dosyaları veya veritabanı dosyaları Docker volume'leri ile yönetilebilir. Bu sayede, bu verilerin container'dan bağımsız olarak saklanabilmesi, yönetilebilmesi ve korunabilmesi sağlanır.<br><br>Örnek bir volume tanımı aşağıdaki gibidir:

```yml
version: "3.9" # docker-compose.yml dosyasının versiyonu

services: # servisler
  web: # web servisi
    image: nginx:latest # nginx image'ını kullanarak nginx servisini oluşturur
    volumes: # volume'ler
      - app-code:/usr/share/nginx/html # container'ın /usr/share/nginx/html dizinini app-code volume'üne bağlar
      - app-data:/data # container'ın /data dizinini app-data volume'üne bağlar

volumes: # volume'ler
  app-code: # app-code adında bir volume oluşturur
  app-data: # app-data adında bir volume oluşturur
``` 

**NOT:** **'volumes'** anahtar kelimesi, Docker konteynerlerindeki dosya ve dizinlerin, Docker ana makinesi üzerindeki nereye monte edileceğini belirlemek için kullanılır. Volumes tanımlandığında, Docker Compose tarafından belirtilmeyen bir konumda, örn. **'/var/lib/docker/volumes'** altında saklanır.

Yukarıdaki örnekte, **'app-code'** ve **'app-data'** adlı iki farklı volume tanımlanmıştır. Bu iki volume'un Docker ana makinesindeki tam yolu, varsayılan olarak **'/var/lib/docker/volumes/<proje_adı>_app-code'** ve **'/var/lib/docker/volumes/<proje_adı>_app-data'** şeklindedir. Burada **'<proje_adı>'**, Docker Compose projesinin adıdır. Örneğin, Docker Compose projesi adı "myproject" ise, **'app-code'** volumes'unun tam yolu **'/var/lib/docker/volumes/myproject_app-code'** olacaktır.
</details>
<br>

**NOT: Bunlar tamamı ile yüzeyde kalmış örnekler ve açıklamalardır. Docker Compose hakkında daha detaylı bilgi almak için [Docker Compose Resmi Dokümantasyonu](https://docs.docker.com/compose/) incelenebilir.**
