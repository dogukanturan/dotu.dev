---
title: "Virtualization ve Hypervisor Nedir?"
date: 2020-02-07
categories: ["DevOps", "Virtualization"]
tags: ["virtualization","hyperv","hypervisor"]
draft: false
description: ""
showtoc: false
tocopen: false
searchHidden: false

---

Nedir bu virtualization?

Sanallaştırma, gerçek donanımdan soyutlanmış bir layer(katman)’da bir bilgisayar sisteminin sanal bir kopyasını çalıştırma işlemidir. Kısaca, bir bilgisayar sisteminde aynı anda birden fazla işletim sisteminin çalıştırılması anlamına gelir.
Sanallaştırmayı kullanmamızın birçok nedeni vardır. Bu nedenlerden en yaygın olanı ise, bilgisayar değiştirmek veya uygulamalarımızı farklı platformlarda test etmek ve ve vee linux ile windows işletim sistemlerinin ikisine’de ihtiyacın doğması. Kendimden örnek vermek gerekirse; kişisel bilgisayarıma linux(herhangi birisi) kurulumu yapınca tam istediğim performansta çalışmaması durumunda sanal olarak linux kullanmam gösterilebilir.

# Hypervisor

Şimdi gelelim hypervisor’un ne olduğuna…
En kısa tabiri ile sanal ortamı oluşturan, çalıştıran yazılım/donanım’a denir. Örneğin; benim bir linux dağıtımına ihtiyacım var ve sistemime vmware kurup bu vmware üzerinde sanal bir linux sistem kurmam gerekiyor. CPU, RAM, HDD vs. Birçok ayarı bu vmware üzerinden kolayca konfigüre edebiliyorum.
Aynı senaryoda vmware değilde hyper-v de kullanabilirdim işte tam da bu hypervisor’un devreye girdiği an. Benim için bu işlemleri yapan vmware, hyper-v veya farklı bir sanallaştırma yazılımı olabilirdi. Sonuçta hepsinin yapmış olduğu iş benim sistemimde sanal bir ortam kurmak olacaktı.

## Hypervisor Type 1 (Bare-Metal Hypervisor)

Type1 Hypervisor, doğrudan ana makinenin fiziksel donanımı üzerinde çalışır ve buna bare-metal hypervisor denir, öncesinde bir işletim sistemi yüklemesi gerekmez. Type1 hypervisor’ler sistemin fiziksel donanımlarına doğrudan eriştiğinden dolayı çok daha performanslıdır. Product ve Test süreçlerinde sık sık kullanılan bir sanallaştırma teknolojisidir. Type 1 Hypervisor yazılımlarından birkaçına örnek vermek gerekirse;

- VMware ESX/ESXi
- Microsoft Hyper-V (Windows 10)
- Oracle VM

## Hypervisor Type 2 (Hosted Hypervisor)

Type 2 Hypervisor, doğrudan ana makinenin üzerinde değilde bir işletim sistemi üzerinde layer(katman) olarak çalışır. Type1 Hypervisor kadar performanslı bir şekilde çalışmamaktadır. Çünkü host sistemin fiziksel kaynaklarından tam olarak faydalanamamaktadır. Kullanıcılar tarafından sık sık kullanılırlar ve basit test ortamları vs oluşturmak için sık sık başvurulur. Çoğumuzun bir işletim sistemi bulunmakta ve ihtiyaç doğduğunda bu işletim sistemi üzerine kurduğumuz sanal işletim sistemleri aslında Type2 Hypervisor’dır. Type2 Hypervisor yazılımlarından birkaçına örnek vermek gerekirse;

- VMware Server
- VMware Workstation/Fusion/Player
- Oracle VM Virtualbox
- Microsoft Virtual PC
- Red Hat Enterprise Virtualization

![4.png](/posts/virtualization-ve-hypervisor/images/1.png)

Yukarıdaki şema ile Type 1 ve Type2 Hypervisor’ların nasıl çalıştıkları daha anlaşılır bir şekilde gösterilmekte. Örneğin yukarıdaki resimlerde Type2 Hypervisor’u ele alırsak; benim bir bilgisayarım var ve doğal olarak bunu bilgisayar yapan fiziksel donanımları var cpu, ram, hdd gibi, bunun üzerinde ise bir işletim sistemimiz yer alsın örneğin windows. Şimdi ben bir sanal ubuntu kullanmak istiyorum ve bu windows makinamın üzerinde bir hypervisor yazılımı kuruyorum bunada örnek vermek gerekirse virtualbox, virtualbox aracılığı ile ubuntu yu sistemime kurmuş oluyorum daha sonrasında ise birde CentOS kurmak istiyorum ve virtualbox’u tekrar kullanarak CentOS’u kuruyorum ve windows makinemde sanal Ubuntu ve CentOS kurmuş oluyorum.

Kısaca anlatmak gerekirse Sanallaştırma nedir? Hypervisor nedir? Türleri neler? Bunlara cevap aradık. Bir sonraki yazımda Container teknolojisinin ne olduğunu, nerelerde kullanıldığını, artılarını ve eksilerini ele alacağım.
