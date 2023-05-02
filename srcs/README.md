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

Docker-Compose ile ilgili tüm komutlar aşağıdaki gibidir:

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

