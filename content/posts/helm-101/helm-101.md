---
title: "Helm 101"
date: 2022-01-17
categories: ["DevOps", "Kubernetes"]
tags: ["kubernetes","helm","cluster"]
draft: false
description: "Helm Nedir? Uygulamalarımızı helm ile nasıl deploy edebiliriz?"
showtoc: false
tocopen: false
searchHidden: false
cover:
    image: "/posts/helm-101/images/helm.png"
    relative: false
    hidden: false

---

Helm, Kubernetes için geliştirilmiş bir paket yöneticisidir. Bunu Linux ortamındaki apt veya yum gibi düşünebilirsiniz, helm bizim için kubernetes ortamında çalışan uygulamalarımızın kaynaklarını (deployment, statefulset, service, ingress vb.) kolayca yönetebilmemizi ve karmaşıklıklardan kurtulmamızı sağlar.

Peki helm tüm bunları nasıl yapıyor? Helm, chart dediği yapıyı kullanarak uygulamamıza ait olan tüm kaynakları tek bir noktadan yönetmemizi sağlıyor, örneğin bir web uygulamamızın olduğunu ve bu uygulamanın elasticsearch, mysql gibi bağımlılıklarının olduğunu düşünelim, uygulamamız için kubernetes kaynaklarını oluşturduktan sonra bağımlılıkları için de aynı şekilde bu kaynakları hazırlamamız gerekiyor. Birden fazla proje olduğunda ve bağımlılıklar da aynı şekilde arttıkça kontrol etmesi zorlaşacaktır, bu durumu minimum efor ile yönetmek için helm bizim için güçlü bir opsiyon haline geliyor.


# 1. Helm Chart Nedir

 Chart, Helmin kullandığı paketleme biçimidir. Kubernetes ortamında ki uygulamalarımızı chart ile versiyonlayıp, paketleyebiliriz. Yeni bir helm chart oluşturduğumuzda varsayılan olarak gelen bazı klasörler ve dosyalar bulunmakta. Bu dosyaları incelemek gerekirse;

 ```
demo/
  Chart.yaml          # Chart hakkında bilgileri içeren bir yaml dosyası.
  LICENSE             # Chart lisansını içeren dosya (opsiyonel)
  README.md           # Chart kullanımı veya farklı bilgiler için readme dosyası. (opsiyonel)
  values.yaml         # Kubernetes kaynaklarımıza doğrudan erişmek için oluşturduğumuz değişkenlerin tutulduğu dosya.
  values.schema.json  # value.yaml dosyasına bir yapı empoze etmek için json şeması (opsiyonel)
  charts/             # Chartın içerisinde farklı chartları kullanabilmek için veya bağımlılıkları kullanabilmek için oluşturulan dizin. (kullanım opsiyonel)
  crds/               # Kubernetes Custom Resource Definitions
  templates/          # Kubernetes kaynaklarımızın bulunduğu dizin.
  templates/NOTES.txt # Kubernetes kaynaklarımız deploy olduktan sonra terminal üzerinden kullanım hakkında veya farklı bilgiler göstermek için kullanılabilir.(opsiyonel)
 ```

 Temel kavramları öğrendiğimize göre artık örnek bir demo yaparak ilerleyebiliriz. Öncelikle ortamımızı hazırlamamız gerekiyor, ortamımızı hazırladıktan sonra örnek bir uygulamayı helm ile deploy ederek tüm süreci anlatmaya çalışacağım.


# 2. Helm Kurulumu

Linux ortamına helm kurabilmek için aşağıda ki adımları terminal ortamınızda uygulayabilirsiniz. Farklı bir işletim sistemine kurulum yapmak istiyorsanız helmin [dökümanından](https://helm.sh/docs/intro/install/) adımları izleyebilirsiniz.

>**Unutmadan:** Helmi kullanabilmek için kubernetes clusterımızın olması gerekiyor. Eğer herhangi bir kubernetes clusterınız yoksa ve hızlı bir şekilde kubernetes clusterı kurmak istiyorsanız [K3D ile K3S Kubernetes Cluster Kurulumu](https://kartaca.com/k3d-ile-k3s-kubernetes-cluster-kurulumu/) adlı yazımı okuyabilirsiniz.

```bash
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

Adımları yaptıktan sonra kurulumu doğrulamak için `helm version --client` komutunu çalıştırabilirsiniz.


# 3. Chart Oluşturma

Sisteminize helm kurulumunu başarılı bir şekilde yaptıysanız ve kubernetes clusterınız da hazırsa artık demolar yaparak helmin derinliklerine inebiliriz. Öncelikle helm chart oluşturmakla işe başlayalım. Sıfırdan helm chart oluşturabileceğimiz gibi hazır helm chartları da kullanabiliriz. Biz şimdi sıfırdan bir helm chart yaratmayı öğreneceğiz.

```bash
$ helm create demo
```

Komutu çalıştırdıktan sonra `ls` ile kontrol ederseniz, `demo` adında bir klasör oluştuğunu göreceksiniz. Klasörün içeriğine baktığımızda aşağıdakine benzeyen dosyaların oluşmuş olması gerekiyor.

```
demo
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

Helm tarafından oluşturulan bu chart aslında çoğu ihtiyacımızı karşılıyor. Temel kubernetes kaynakları bu chart içerisinde `templates` klasöründe bulunmakta. Şimdi `Chart.yaml` dosyamızın içeriğini inceleyelim.

```
apiVersion: v2
name: demo
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.16.0"
```

`Chart.yaml`dosyasında uygulamamızın adını, açıklamasını, versiyonunu vs değiştirebiliriz. Burada kullanılan versiyonlara birazdan`templates/deployment.yaml`dosyasını incelereken değineceğim.


# 4. Uygulamayi Deploy Etme

Uygulamamızı kubernetes clusterında dağıtmak için birden fazla yöntem bulunmakta. Aşağıdaki komutlar, oluşturulan uygulamayı kubernetes ortamında nasıl dağıtabileceğimizi göstermekte.

```
$ cd demo/
$ helm install demo .
```

Öncelikle chartın bulunduğu dizine gitmemiz ve ardından `helm install` komutu ile uygulamayı dağıtmamız gerekiyor. Yukarıdaki komut ile demo uygulamamızı kubernetes ortamında deploy edebiliriz.

>**NOT:** Eğer farklı bir namespace için dağıtmak isterseniz `--namespace` paramteresini kullanabilirsiniz.

Kurulumun ardından `kubectl get pods` ile podların durumunu görebiliriz.

```bash
$ kubectl get pods

NAME                    READY   STATUS    RESTARTS   AGE
demo-5c5559755c-vg9xx   1/1     Running   0          2m45s
```

## 4.1. Helm Package

Helm Chartlarımızı `package` komutu ile paketleyebiliriz. Paketlediğimiz chartları registrye gönderebilir veya bu şekilde de dağıtabiliriz. Demo uygulamamızı aşağıdaki komut ile paketleyebiliriz.

```
$ cd demo/
$ helm package .
```

paketleme işleminin ardından `ls` ile dizini kontrol ettiğimizde `*.tgz` ile biten bir arşiv dosyası oluşmalı.

```bash
$ ls
charts  Chart.yaml  demo-0.1.0.tgz  templates  values.yaml
```

Şimdi demo uygulamasını `demo-2` ile tekrar dağıtalım. Bunun için yukarıda kullandığımız `helm install` komutunu kullanacağız fakat bu sefer dizini belirttiğimiz `.` yerine `*.tgz` dosyamızı vereceğiz.

```bash
$ helm install demo-2 demo-0.1.0.tgz
```

Ardından `helm ls` komutu ile aktif chartlarımızı görebiliriz.

```bash
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
demo    default         1               2022-01-16 20:20:18.996794236 +0300 +03 deployed        demo-0.1.0      1.16.0
demo-2  default         1               2022-01-16 20:23:54.947237373 +0300 +03 deployed        demo-0.1.0      1.16.0
```


## 4.2. Chartı Kaldırmak

Dağıtılan chartı kaldırmak için `helm uninstall` kullanabiliriz. Bu komut, uygulamaya ait tüm kaynakları (deployment, service, ingress vb.) kubernetes clusterımızdan kaldırır. Yukarıda deploy ettiğimiz `demo-2` chartını kaldırmak için aşağıdaki komutu kullanmamız gerekiyor.

```bash
$ helm uninstall demo-2
```


# 5. Uygulamayı Güncellemek

Helm chart oluşturduğumuzda bizim için hazır bir chart şablonu oluşmuş oluyor ve varsayılan olarak nginx image ayağa kaldıracak şekilde geliyor. Biz bu imagei değiştirmek istiyoruz, bu yüzden `values.yaml` dosyasını güncellememiz gerekiyor. `values.yaml` dosyasını herhangi bir editör ile açtıktan sonra içerisinde bir çok değişkenin olduğunu görmüşsünüzdür. `values.yaml` içerisinde yapacağımız tüm değişiklikler`templates/`altındaki kubernetes kaynaklarını güncellemekte. Kısaca `templates/` altında birden fazla kubernetes kaynağı bulunuyor ve bu kaynakları tek bir yerden yönetmemiz için var diyebiliriz.

Şimdi `values.yaml` dosyasında imagei değiştirerek helmi deploy edebiliriz. Öncelikle `values.yaml` dosyasını herhangi bir editör ile açmamız gerekiyor, açtıktan sonra içerisinde `image.repository` yazan bölümü aşağıdaki gibi güncelleyebiliriz.

```yaml
image:
  repository: dturan/echo-server
  tag: "latest"
```

Peki yukarıdaki değişiklik ne anlama geliyor? `image.repository` üzerinde yaptığımız tüm değişiklikler aslında `templates/deployment.yaml` dosyasını güncellemiş oluyor. Bu arada `tag:` yazan kısma birazdan değineceğim. Şimdi `templates/deployment.yaml` dosyasını açalım ve ilgili yeri inceleyelim.

```
image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
```

Dosyanın içerisinde `image:` ile başlayan bölüm de yukarıdaki gibi birşey görmeniz gerekiyor. Peki bu tam olarak ne anlama geliyor? Adım adım inceleyelim.

- `{{ .Values.image.repository }}` = Bu kısım `values.yaml` dosyasının içerisinde `image` altındaki `repository` değerini al demek aslında.

- `{{ .Values.image.tag | default .Chart.AppVersion }}` = Bu kısım ise `values.yaml` dosyasının içerisinde `image.tag` değerini al demek oluyor, fakat yukarıda fark ettiyseniz bir değer ataması yapmadık. Peki değer ataması yapmadığımzda ne olacak? `| default .Chart.AppVersion` ile belirtilen kısım, eğer ilk koşul sağlanmazsa (boş olmasıda koşulun sağlanmaması için yeterli) diğer adıma geçiyor. Yani bu image için bir versiyon vermezsek `Chart.yaml` içerisinde `appVersion:` yazan kısmı okuyacaktır.

## 5.1. Environment Variable Tanımlamak

Uygulama için hazırladığım `text_message` adında bir environment bulunmakta. Bu environmentı tanımladığımız zaman ilgili servis ile bunu yayınlayabiliriz. Sıradaki amacımız bu environmentı helm ortamında tanımlayıp uygulamamıza dışardan erişmek.

Öncelikle `values.yaml` dosyamızı açarak aşağıdaki satırları ekleyelim.

```yaml
environments:
  name: text_message
  value: Hi from Helm Chart ^^
```

Peki bu tanımlamayı yapmak yeterli mi? Aslında hayır, biz sadece tanımlama yaptık. Şimdi bunu kubernetes deployments içerisine nasıl ekleyebiliriz onu inceleyelim. Öncelikle `templates/deployment.yaml` dosyamızı açalım ve `containers:` altına aşağıdaki satırları ekleyelim.

```yaml
env:
- name: {{ .Values.environments.name }}
  value: {{ .Values.environments.value }}
```

Tanımlamalarımızı yaptık. Artık uygulamayı deploy ederken `deployment.yaml` dosyamız, `values.yaml` dosyasından `env.name` ve `env.value` değişkenlerini okuyabilecek.

Peki birden fazla environment tanımlarsak ne yapmalıyız? Böyle bir durumda her env için farklı bir değişken tanımlayabilirsiniz veya daha doğru bir yol olan aşağıdaki durumu kullanabilirsiniz. Tekrar `values.yaml` dosyamızı açalım ve biraz önce eklediğimiz environmentları aşağıdakiler ile değiştirelim.

```yaml
environments:
  - name: text_message
    value: Hi from Helm Chart ^^
  - name: baska_bir_env
    value: baska bir deger
```

>**UYARI:** Bu tanımlamayı yaparken kubernetes ortamında nasıl kullanıldığına bakmanız gerekebilir. Çünkü böyle bir kullanımda değişkenleri istediğimiz gibi değiştiremeyiz.

Farklı değişken isimleri tanımlamamıza gerek duymadan bu şekilde de kullanabiliriz. Peki bunu `deployment.yaml` dosyasında nasıl tanımlayacağız? Bunun için de az önce `deployment.yaml` dosyamıza eklediğimiz environment kısmını aşağıdaki ile değiştirelim.

```yaml
env:
  {{- toYaml .Values.environments | nindent 12 }}
```
Bu tanımlama ile `.Values.environment` altında tanımlanan tüm environmentları alıp `deployments.yaml` dosyamıza tanımlayabiliriz. Aslında chart deploy edildiğinde `kubectl edit deployment demo` komutunu çalıştırırsanız aşağıdaki gibi tanımlandığını görebilirsiniz.

```yaml
spec:
  containers:
  - env:
    - name: text_message
      value: Hi from Helm Chart ^^
    - name: baska_bir_env
      value: baska bir deger
    image: dturan/echo-server:latest
```

## 5.2. Uygulamaya Erişmek

Değişkenlerimizi de tanımladığımıza göre artık chartı deploy edebiliriz. Öncelikle aşağıdaki komut ile uygulamamızı clusterımıza kuralım.

```bash
$ helm install demo .
```

> Kurulumun ardından `kubectl get pods` ile containerın hazır olup olmadığını kontrol edebiliriz.

Eğer container `1/1 Running` olarak görünüyorsa aşağıdaki komut ile ortamımızdan servise erişebiliriz.

```bash
$ kubectl port-forward demo-xxxxxxxxxx-xxxxx 8080:80
```

Ardından tarayıcımızdan `localhost:8080` adresine gittiğimizde aşağıdaki gibi bir json ile karşılaşmamız gerekiyor.

```json
{
  "CONTAINER": "demo-5c5559755c-vg9xx",
  "DATE": "Mon, 17 Jan 2022 00:00:00 GMT",
  "IP ADDRESS": "10.42.0.18",
  "TEXT": "Hi from Helm Chart ^^"
}
```

`TEXT` ile berirtilen alanda yukarıda tanımladığımız değişkeni başarılı bir şekilde gördük. Artık uygulamamız hazır :)

#### Happy Helming!
