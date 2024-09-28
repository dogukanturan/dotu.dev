---
title: "Portainer Nedir? | Docker ile Portainer Kurulumu"
date: 2020-06-28
categories: ["container", "devops", "portainer"]
tags: ["container", "devops", "portainer"]
draft: false
showtoc: false
tocopen: false
searchHidden: false
cover:
    relative: false
    hidden: false

---

# Portainer Nedir

Portainer, Docker veya Docker Swarm Cluster’ımızı yönetmemizi sağlayan bir management UI’dır. Docker’ı terminalden kullanmak sizlere işkence gibi geliyorsa, docker’ı bir arayüz ile yönetmek/kullanmak istiyorsanız portainer tamda bu anda yardımınıza koşan tatlımı tatlı bir yardımcı.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*I6U3NHuc0lLxoZK7PddRXA.png)

Kendi hayatımdan örnek vermem gerekirse; bir sunucuda birden fazla proje bulunabilmekte ve bazen development süreçleri ile ilgilenen kişiler sunucuda kendi gelişiştirdiği container ile düzenlemeler, incelemeler yapması gerekebiliyor. Yada image güncellemesinden sonra ortaya çıkan sorunları direk yerinde incelemek isteyebilir ve bu sunucuyu terminal üzerinden kullanmak ona bir işkence gibi geliyor. Peki ne yapılabilir? Bu gibi sorunlarla karşılaşan kişilerin aklından şöyle bir şey geçirmiş olması yüksek bir olasılık..

Ayrıca yüksek miktarda çalışmakta olan container’larımızı terminal üzerinden göstermek gerçekten pek hoş olmuyor. Şöyle bir dashboard’umuz olsa ordan çalışan/çalışmayan tüm container’ları görebilsek tek bir tıklama ile loglarını görebilsek yada container içerisine erişebilsek ne kadar da güzel olurdu.

Kısaca portainer yukarda bahsettiğim şekilde bize bir management ui ortamı vermekte ve biz bu ortam sayesinde işlemlerimizi yapabilmekteyiz. Şimdi lafı uzatmadan kurulum adımına geçelim.

# Kurulum

Portainer’ı harici olarak kurmanıza gerek yok, docker image olarak ayağa kaldırıp istediğimiz ortamdaki docker engine’e erişmesini sağlayacağız. Ben kendi localimdeki ubuntu makinemde çalışmakta olan docker’ımda kurulum işlemlerini yapacağım. Eğer siz windows yada linux ortamında docker kullanıyorsanız bunun için aşağıdaki adresten kurulum adımlarını tek tek yapabilirsiniz. Ben kendi hazırladığım docker-compose.yml ile anlatacağım hem daha anlaşılır bir şekilde neler yaptığımızı görecegiz ve ilerde birgün tekrar kurulum yapmak istersek de bu yml sayesinde tek komut ile ayağa kaldırabiliriz.

Kurulum: Portainer

# Docker Compose

Bulunduğum makinede “portainer” adında bir dizin oluşturuyorum ve bu portainer dizini içerisine docker-compose.yml dosyamı oluşturuyorum.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*JFB2AHOlhGZDYPYfqW4HOw.png)

Klasörümün içerisine giriyorum ve docker-compose.yml dosyamı düzenlemeye başlıyorum.

***Not:*** *Ben uzak sunucudaki dosyalarımı düzenlemek için visual studio code remote ssh eklentisini kullanıyorum.*
```
version: &#x27;3.8&#x27;
services:
  portainer:
    image: portainer/portainer-ce
    restart: always
    container_name: portainer
    ports:
      - &quot;8000:8000&quot;
      - &quot;9000:9000&quot;
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/datavolumes:
  portainer_data:
```

Evet docker-compose.yml dosyamız yukarıdaki gibi olmalı. Şimdi adım adım neler yaptığımıza bakalım.
- **Version:**  Docker daemon ile uyumlu olan son compose sürümünü kullanıyorum. Siz hangi versiyonu kullanmanız gerektiğini bilmiyorsanız bu adresten inceleyebilirsiniz. (docker 19.03.11 sürümünü kullanıyorum sizde son docker sürümünü kullanıyorsanız bunu son sürüm olan 3.8 olarak bırakabilirsiniz.)
- **Services:**  Bir servis oluşturacağımı söylüyorum docker’a
- **Portainer:**  Servisimizin adınızı belirliyoruz.
- **Images:** Ayağa kaldıracağımız image’i belirtiyoruz. Burdaki portainer/portainer hub.docker.com’dan miras almakta
- **Restart:**  Container herhangi bir sebepten durmak zorunda kalırsa tekrar restart etmesini söylüyoruz.
- **Container Name:**  Container’ımız çalışırkan hangi isim ile çalışması gerektiğini söylüyoruz. Boş bırakılırsa docker tarafından random bir isim verilecektir (pek de random değil :))
- **Ports:** Container’ımızın hangi portlarda çalışması gerektiğini belirliyoruz. Portainer default olarak 8000 ve 9000 portunu kullanmakta. Bu arada port tanımlamasında örnek olarak 9000:9000 olarak tanımlanmış, peki bu ne demek oluyor? Aslında diyoruz ki client tarafında 9000 portuna gelen istekleri içerde(container’da) 9000 portuna expose et. Yani 1234:9000 şeklinde bir tanımlama yaparsak, http://ipadresi:1234 olarak yaptığımız istek container içerisinde 9000&#x27;e expose edilicek. client:container olarak da düşünebiliriz.
- **Volumes:** İlk sırada tanımlanan volume, Docker daemon’ın dinlediği bir UNIX socket’ı dir. Portainer’ın docker’ımıza erişebilmesi için bu volume tanımlamasını yapmamız gerekmekte. İkinci volume tanımlamasında ise portainer datalarını docker’da portainer_data volume içerisinde saklamasını söylüyoruz.
- **Volumes.portainer_data:**  Direk kendi hostumuzdan bir path’i docker volume’e bind olarak vermediğimiz için portainer_data adında bir volume tanımlaması yapıyoruz ve bunu üst satırda kullanıyoruz.

Evet compose dosyamızı açıkladığımıza göre şimdi portainer’ı ayağa kaldırabiliriz. Terminalden docker-compose.yml dosyamızın olduğu konuma geliyoruz ve aşağıdaki komutu çalıştırıyoruz.
```
$ docker-compose up -d
```

Not: Sonda --d/--detach olarak belirttiğimiz komut şunu yapıyor. Compose’u ayağa kaldırırken lütfen bana yaptığın işleme ait logları gösterme. Bırak kardeşim terminali bana!
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*2IbEUwhE2KeFLZL_ZdVu_A.png)
- Karşımıza bu şekilde bir çıktı gelmesi gerekiyor, image’i hub.docker.com’dan pull etti ve container’ımızı belirttiğimiz ayarlar ile ayağa kaldırdı. Bakalım gerçekten de çalışan bir container’ımız varmı. Bunun için aşağıdaki komut ile bulunduğumuz dizindeki compose dosyasının çalıştırdığı container’ları listelemiş olduk.
```
$ docker-compose ps
```
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*mb-wO6cGR4A68QqkkQH-7w.png)
- Evet bir adet portainer ayakta. Şimdi bu container’a erişelim ve kullanıcımızı oluşturalım
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*G8iTFX3vGUjvkIDdvK19KA.png)
- Karşımıza 4 adet seçenek çıkmakta, uzaktaki bir docker sunucusuna bağlanmak isterseniz Remote’u kullanabilirsiniz yada Azure’a bağlanmak isterseniz Azure’u seçebilirsiniz. Ben kendi localimde çalışamakta olan docker’a erişmek istiyorum ve Local kısmından devam ediyorum. Aşağıda kırmızı olarak belirtilen alanda bizi bir uyarı karşılıyor. Yukarıda da belirttiğim docker.sock path’ini volume’e doğru bir şekilde verip vermediğimizi soruyor aslında.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*sBC01I1c1X3vSMGy8EyApw.png)
- Connect butonuna bastıktan sonra karşınıza bu şekilde bir ekran gelmekte. Burda portainer’a bağlı olan docker sunucumuzu görebilmekteyiz. Bu docker sunucumuza tıklayarak içerisine girebilirsiniz.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*o_BbzlZBBvvwteY6jcVJDg.png)
- Girdikten sonra çalışmakta container’ları, image’leri, volume’leri soldaki menüden görebiliriz.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*839N6tjSyBXPKhNPeOP0Qw.png)

## Container Ayağa Kaldırma

Şimdi dilerseniz basit bir nginx container ayağa kaldıralım ve bu container ile neler yapabiliyoruz bunlara bakalım. Soldaki menüde “App Templates” olarak belirtilen bölümde karşımıza image’ler çıkmakta burdan nginx image’ini seçiyoruz. (burda tüm image’ler listelenmemekte dilerseniz container kısmından manuel olarak image adını belirterek de devam edebilirsiniz)
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*Qs8TBzZ2gexcRXuu6qFTUQ.png)

Container’ıma “nginxdemo” adını verdim ve advanced options kısmından nginx’e erişebilmek için host port verdim. Yani 8080 olarak gelen istekleri container içerisinde 80 portuna expose edecek (8080:80). Tüm ayarlamaları yaptıktan sonra “Deploy the container” butonuna basarak container’ımızı ayağa kaldırıyoruz.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*30gby2HYKrdO4sWaXPLurg.png)

Aktif olarak çalışmakta olan iki adet container’ım bulunmakta ve bunlardan birtanesi portainer. Diğeri ise az önce ayağa kaldırdığımız nginx container’ı. Terminalimizden de bakalım gerçekten bir container ayağa kaldırdı mı :)
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*UgZGFM4LFeumKI72_3DhyQ.png)

Evet iki adet container’ımız terminaldede gözükmekte. Şimdi tarayıcımızdan nginx’e erişmeyi deneyelim. Ben bunun için http://dockerip:8080 adresini giriyorum ve nginx bizi karşılıyor.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*h_7w-74eziEshtKng7YtrA.png)

## Log Kayıtlarını İzleme

Çalışan nginx container’ımızın bulunduğu satırda “Quick Actions” bölümü bulunmakta ve burda ilk sıradaki Logs kısmına giriyoruz. Nginx’e istekler yolladığınızda bu istekler logs ekranında karşınıza canlı olarak düşmesi gerekmekte(Auto-refresh logs açık olmalı)
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*u02Z1dJMPRCk4wUpYwA1ZA.png)

## Container’a Bağlanma

Nginx container’ımıza terminal üzerinden bağlanmak için öncelikle bağlanacağımız uygulamanın container-id’sini öğrenmemiz gerekicekti ve daha sonrasında aşağıdaki komutu terminalimizden girmemiz gerekicekti. Biraz can sıkıcı değil mi :(
```
$ docker exec -it [container-id] /bin/bash
```

Şimdi de portainer üzerinden nginx image’imize bağlanalım bunun için yine nginx container’ının bulunduğu satırdaki “Quick Actions” kısmında bulunan “Exec Console” butonuna basıyoruz. Karşımıza birkaç seçenek çıkmakta bağlanacağımız bash kabuğunu burdan seçebiliriz biz direk connect diyerek sonraki adıma geçiyoruz.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*oiNZfMRENMd_Mhz_6pgjnw.png)

Evet web üzerinden container’ımızın shell’ine eriştik. Şimdi container’ımızı güncelleyelim ve değişiklik yapabilmek için vim’i yükleyelim.
```
$ apt update -y &amp;&amp; apt install vim -y
```

Komutu girdikten sonra vim ile index.html dosyasını açalım bunun için aşağıdaki komutu terminale giriyoruz.
```
$ vim /usr/share/nginx/html/index.html
```
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*aZ2Eb0JtcSmUf6Grdged9w.png)

Evet nginx index.html dosyamıza eriştik burda dilediğiniz gibi değişiklikler yapın ve kaydettikten sonra web üzerinden tekrar nginx’i açın.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*MU8xxEVIk41Jr16Vm3Teeg.png)

## Kullanıcı ve Takım Oluşturma

Yazının başındada belirtmiştim bizim a ve b projelerimiz olsun ve bu projelere sadece back-end takımı erişebilsin. Bunun için portainerda sol menüden “Users/Teams” kısmına giriyoruz ve bizden bir takım adı girmemizi istiyor. Ben “back-end” adında bir takım oluşturuyorum. Takım oluşturduktan sonra tekrar “users” kısmına geliyorum ve bu sefer bir kullanıcı oluşturuyorum.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*LeelqH-SPP5HkCDj-4N6tQ.png)

Bob adında bir kullanıcı oluşturdum ve “Add to team(s)” kısmından back-end takımına ekledim. Şimdi local docker sunucumuza bu back-end takımının erişmesini sağlayalım bunun için tekrar sol menüden “Endpoints” kısmına giriş yapıyoruz ve karşımıza çıkan local sunucunun sağındaki “Manage access” bölümüne giriyoruz.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*ya58VabsaJc0lDxqGKkfxQ.png)

back-end takımını bu docker sunucusuna ekledim ve Create access diyerek işlemimi sonlandırdım. Bu adımdaan sonra Bob kullanıcısı ile giriş yaptığınızda tüm container’ları gördüğünüzü fark etmişsinizdir. Tüm containerları görmesini istemiyorum sadece onu ilgilendiren nginx container’ını görmesini istiyorum bu yüzden containers bölümünden nginx container’ıma giriyorum ve “Owership” bölümünden Administrator olan bölümü Restricted olarak değiştiriyorum ve back-end takımımı bu container’a ekliyorum.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*0ObaTOJJPcN5UQG3rGxEOQ.png)

Şimdi admin account’umdan çıkış yapıyorum ve oluşturduğum Bob kullanıcı ile giriş yapıyorum. Karşıma local docker sunucum çıkıyor ve içerisine girerek container’ları inceliyorum.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*DUSYTy4f7mryZjOpqSfjlA.png)

Gördüğünüz üzere sadece yetki verdiğimiz container’lar karşımıza çıktı. Bu sayede yanlışlıkla oluşacak kazaların önüne geçmiş olduk :)

Bundan sonraki işlemleri birazda siz kurcalayarak kavrayabilirsiniz. Elimden geldiği kadar sizlere portainer’ın ne olduğu, nasıl kullanıldığını anlatmaya çalıştım. Umarım faydalı olabilmişimdir. Bu arada corona sürecinde geçen boş zamanlarımı bir docker eğitim serisi hazırlamakla geçiriyorum. En yakın zamanda docker serisinide medium hesabımdan paylaşacağım. Kendinize iyi bakın :)
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/0*5PT-8Utv-DXUHO6e.jpg)

