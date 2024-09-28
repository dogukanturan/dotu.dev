---
title: "STMCTF’19 FINAL | Fugitive2 Writeup"
date: 2019-11-01
categories: ["CTF", "Cyber Security"]
tags: ["ctf", "cybersecurity"]
draft: false
showtoc: false
tocopen: false
searchHidden: false
cover:
    relative: false
    hidden: false

---

Merhaba, 31.10.2019 tarihinde STM’nin beşinci kez düzenlemiş olduğu CTF(capture the flag) yarışmasının MISC kategorisinde yer alan fugitive2 sorusunun çözümünü sizlerle paylaşacağım.

# Çözüm

Sorunun üzerinde takım arkadaşım tayfur ile bir süre düşündük ve soruda bize “Geçen sene tanıdığımız” ipucundan yola çıkarak geçen yıl çözmüş olduğumuz “KAÇAK” sorusuna odaklandık. Geçen yıl soruda geçen kişinin instagram hesabına ulaştık ve 24 Eylül’de bir paylaşım yaptığını gördük.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*-DO4XTeC-8ji9ijUROE4HQ.png)

Resmin açıklamasındaki rusça yazı ve “ctf19” hemen dikkatimizi çekti, yazıyı çevirdiğimizde “Ben bir rus sosyal ağındayım” yazdığını gördük. Bundan sonra aklımıza ilk gelen sosyal medya vk oldu ve vk da aynı hesap adı ile arama yaptığımızda “Ege Deniz” kişisinin vk hesabına ulaşmış olduk. Bu hesabında 3 adet fotoğraf paylaştığını gördük ve fotoğrafları tek tek inceledik ama bir sonuç bulamadık. Daha sonra fotoğraflardan birisinin ismi dikkatimi çekti(mar.jpg) aklımıza ilk gelen marmaris oldu.
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*kb9pauDI77-eirGpXo83Lg.png)

Haritayı açıp marmarise baktık ve etrafındaki adalara göz attığımızda Keci adası olduğunu görüp flag’in bu olduğunu düşünerek sisteme STMCTF{Keci_Adasi} olarak girdik ve mission complete :)
![Medium-Image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*V_h5hdcWdu2H5Jj7WPESJg.png)

### FLAG: STMCTF{Keci_Adasi}
