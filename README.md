**NOT: Lütfen sadece burada ki yazılara bağlı kalmayın. Elinizden geldiğince Docker, Docker-Compose, Containerler ile ilgili araştırma yapın.**

Kaynak: [Docker](https://docs.docker.com/) [Wikipedia](https://en.wikipedia.org/wiki/Docker_(software))

Kurulum: [YouTube](https://www.youtube.com/watch?v=BjbhxtUjkhg&feature=youtu.be)

**DEĞİŞMESİ GEREKEN YERLER:**
- .env --> srcs/
- wp-config.php --> srcs/requirements/wordpress/tools
- ngnix_start.sh --> srcs/requirements/nginx/tools
- default --> srcs/requirements/nginx/conf
- initial_db.sql --> srcs/requirements/mariadb/tools

**NOT:** Tarayıcınız üzerinden domain adresine erişmek için bu yazının sonunda ki **Projeyi Nasıl Çalıştırabilirim?** kısmında yer alan YouTube linkini inceleyebilirsiniz.

# Docker Nedir?

![Docker Logo](https://github.com/burak-yldrm/Inception/blob/main/img/1920px-Docker_(container_engine)_logo.svg.png)

Docker, uygulamaları birleştirmek, test etmek ve dağıtmak için açık kaynaklı bir platformdur. Docker, yazılım geliştirme sürecini hızlandırmak için uygulamaları hızlı bir şekilde paketlemenizi, test etmenizi ve dağıtmanızı sağlar.

Docker içerisinde çalışan uygulamaları container olarak adlandırıyoruz.
Bir container içerisinde çalışan uygulama diğer container içerisinde çalışan uygulamalardan etkilenmez. Containerlar aslında birer sanal makina gibi çalışır fakat sanal makinadan farklı olarak containerlar birbirinden izole edilmiş bir şekilde çalışır ve sanal makinadan daha hafiftir.

<details>
<summary>Docker'ın çalışma mantığı şu şekildedir</summary>

- **Docker Engine:** Docker'ın çalışması için gerekli olan tüm bileşenleri içerir. Docker Engine, Docker Daemon ve Docker Client'tan oluşur.
  
- **Docker Daemon:** Docker Engine'in arka planda çalışan kısmıdır. Docker Daemon, Docker Client tarafından gönderilen komutları alır ve bu komutları uygular. Docker Daemon, Docker Host üzerinde çalışır.

- **Docker Client:** Docker Engine'e komut göndermek için kullanılır. Docker Client, Docker Host üzerinde çalışır.

- **Docker Host:** Docker'ın kurulu olduğu makinedir. Docker Host üzerinde Docker Daemon çalışır.

- **Docker Registry:** Docker imajlarının depolandığı yerdir. Docker Registry üzerinde Docker Hub, Docker Cloud ve diğer Docker Registry'leri bulunur.

- **Docker Image:** Docker imajları Dockerfile ile oluşturulur. Dockerfile içerisinde imajın nasıl oluşturulacağı belirtilir. Dockerfile içerisinde belirtilen komutlar Docker Daemon tarafından uygulanır ve Docker Image oluşturulur.

- **Docker Container:** Docker Image'lerinden oluşturulan çalışan uygulamalardır. Docker Container'lar Docker Daemon tarafından oluşturulur ve yönetilir.

- **Docker Compose:** Docker Compose, birden fazla container'ın tek bir komut ile oluşturulmasını ve yönetilmesini sağlar.

- **Docker Swarm:** Docker Swarm, birden fazla Docker Host'un tek bir Docker Host gibi yönetilmesini sağlar.

</details>
<br>

# Dockerfile içerisinde kullanılan komutlar:

- **FROM:** Dockerfile içerisinde kullanılan ilk komuttur. Dockerfile içerisinde kullanılan tüm komutlar bu komuttan sonra kullanılmalıdır. FROM komutu ile Dockerfile içerisinde kullanılacak olan Docker Image belirtilir. Eğer belirtilen Docker Image Docker Host üzerinde bulunmuyorsa Docker Host üzerinde Docker Registry'den bu imaj çekilir.

	**FROM komutuna örnek:**

	- ```dockerfile
		FROM debian:buster
		```

	- Bu komut ile Dockerfile içerisinde kullanılacak olan Debian Buster imajı belirtilmiştir. Eğer Docker Host üzerinde bu imaj bulunmuyorsa Docker Registry'den bu imaj çekilir.


- **RUN:** Dockerfile içerisinde kullanılan RUN komutu ile Docker Image içerisinde çalıştırılacak komutlar belirtilir. RUN komutu ile birden fazla komut çalıştırılabilir. RUN komutu ile çalıştırılan komutların çıktıları Docker Image'e yazılır.

	**RUN komutuna örnek:**

	- ```dockerfile
		RUN apt-get update && apt-get install -y nginx
		```

	- Bu komut ile Docker Image içerisinde bulunan paketler güncellenir ve nginx paketi kurulur.

	- **RUN komutu mümükün olduğunca tek satırda yazılmalıdır. Böylece Docker Image'in boyutu küçük tutulabilir.**

- **EXPOSE:** Dockerfile içerisinde kullanılan EXPOSE komutu ile Docker Image içerisinde çalışacak olan uygulamanın hangi portu dinleyeceği belirtilir.

	**EXPOSE komutuna örnek:**

	- ```dockerfile
		EXPOSE 80
		```

	- Bu komut ile Docker Image içerisinde çalışacak olan uygulama 80 numaralı portu dinleyecektir.

	- **EXPOSE komutu sadece belirtilen portun dinlenebileceğini belirtir. Bu nedenle Docker Image çalıştırılırken belirtilen portun açık olması gerekir.**

- **COPY:** Dockerfile içerisinde kullanılan COPY host üzerinde bulunan dosyaları Docker Image içerisine kopyalamak için kullanılır.

	**COPY komutuna örnek:**

	- ```dockerfile
		COPY index.html /var/www/html
		```

	- Bu komut ile Docker Image içerisine host üzerinde bulunan index.html dosyası image üzerinde ki /var/www/html dizinine kopyalanır.

- **ENTRYPOINT:** Bu komut containerin başlatılması sırasında çalıştırılacak bir uygulamanın veya komutun tanımlanmasına olanak tanır. Bu komut Dockerfile içerisinde sadece bir kez kullanılabilir eğer birden fazla tanım yapıldıysa en son komut referans alınır.

	**ENTRYPOINT komutuna örnek:**

	- ```dockerfile
		ENTRYPOINT ["var/www/bash.sh"]
		```

	- Bu komut ile container başlatıldığında image içerisinde bulunan /var/www/bash.sh dosyası çalıştırılır. ENTRYPOINT'in farklı kullanım şekilleri vardır. Bunlar:

		- ```dockerfile
			ENTRYPOINT ["executable", "param1", "param2"]
			```

			- Bu kullanım şeklinde container başlatıldığında executable dosyası param1 ve param2 parametreleri ile çalıştırılır.

		- ```dockerfile
			ENTRYPOINT command param1 param2
			```

			- Bu kullanım şeklinde container başlatıldığında command komutu çalıştırılır ve param1 ve param2 parametreleri ile çalıştırılır.

		- ```dockerfile
			ENTRYPOINT ["executable", "param1", "param2"]
			CMD ["param1", "param2"]
			```

			- Bu kullanım şeklinde container başlatıldığında executable dosyası param1 ve param2 parametreleri ile çalıştırılır. Eğer container başlatılırken parametre belirtilmezse CMD komutu ile belirtilen parametreler ile çalıştırılır.

		- ```dockerfile
			ENTRYPOINT command param1 param2
			CMD param1 param2
			```

			- Bu kullanım şeklinde container başlatıldığında command komutu param1 ve param2 parametreleri ile çalıştırılır. Eğer container başlatılırken parametre belirtilmezse CMD komutu ile belirtilen parametreler ile çalıştırılır. <br><br>

- **CMD:** CMD komutu, Docker imajı çalıştırıldığında otomatik olarak başlatılacak olan komutu tanımlar. Eğer kullanıcı Docker imajını çalıştırdığında, komut belirtilmemişse CMD komutu ile belirlenen varsayılan komut çalıştırılır. Eğer kullanıcı Docker imajını çalıştırırken bir komut belirtirse, o komut varsayılan komut yerine çalıştırılır. CMD komutu, Dockerfile içinde sadece bir kere kullanılabilir ve Dockerfile içinde birden fazla CMD tanımlandığında, yalnızca en son tanım geçerli olur.

	**CMD komutuna örnek:**

	- ```dockerfile
		CMD ["nginx", "-g", "daemon off;"]
		```

	- Bu komut ile nginx servisine -g parametresi ile daemon off; parametresi gönderilir. Bu parametre ile nginx servisi arka planda çalıştırılır. Eğer bu parametre gönderilmezse nginx servisi arka planda çalıştırılmaz ve container başlatıldıktan sonra nginx servisi çalıştırılmış olur.

- **WORKDIR:** WORKDIR komutu, Dockerfile içinde bulunan diğer komutların çalıştırılacağı dizini belirler. WORKDIR komutu, Dockerfile içinde birden fazla kullanılabilir. WORKDIR komutu, Dockerfile içinde kullanılmadığı takdirde, varsayılan olarak / dizini belirlenir.

	**WORKDIR komutuna örnek:**

	- ```dockerfile
		WORKDIR /var/www/html
		```

	- Bu komut ile Dockerfile içinde bulunan diğer komutlar /var/www/html dizini üzerinde çalıştırılır.


Temel olarak Docker, Container ve Dockerfile hakkında ki bilgiler bu kadar. Proje içerisinde yapılan diğer detayları klasörlerin içerisinde yer alan README.md dosyalarından inceleyebilirsiniz.

# Projeyi Nasıl Çalıştırabilirim?

[YouTube](https://www.youtube.com/watch?v=BjbhxtUjkhg&feature=youtu.be)
