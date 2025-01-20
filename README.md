# İRedMail + Mail Proxmox Gateway


Bu döküman, Proxmox Mail Gateway (PMG) ile iRedMail arasında bir entegrasyon kurmayı hedefleyen teknik bir rehberdir. PMG, e-posta trafiğini filtreleyip güvenli bir şekilde yönetirken; iRedMail, kullanıcıların e-posta gönderimi ve alımı için kullanılan ana mail sunucusudur.

## Gereksinimler

- 2 Adet Linux sunucu ( Debian 12, Ubuntu 22/24)


Eğer satın almayı yapmış olduğunuz VDS sağlayıcısında Debian 12 bulunmuyor ise aşağıda iletmiş olduğum döküman üzerindeki adımları takip ederek upgrade işlemleri sağlayabilirsiniz.

https://phoenixnap.com/kb/upgrade-debian-11-to-12



## Mail Proxmox Gateway Kurulum


İlk öncelikle proxmoxu repolarımıza ekliyoruz


```
nano /etc/apt/sources.list
deb http://download.proxmox.com/debian/pmg bookworm pmg-no-subscription
```

Aşağıdaki komutları sırayla çalıştıralım:

```
apt update && apt upgrade -y
wget https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg
apt install proxmox-mailgateway
apt update && apt upgrade -y
```

Bu komutları yazmanız yeterlidir. Gerekli kurulumlar sağlandıktan sonra arayüze tarayıcı üzerinden `https://server_ip:8006` adresi ile erişebilirsiniz.

![proxmox1](https://github.com/user-attachments/assets/86521b6a-75b7-48b8-94d8-46780fc1bd91)

Giriş sağladıktan sonra Sol tarafta Mail proxy sayfasına erişiniz.

![image](https://github.com/user-attachments/assets/8fa90eae-5ea9-41af-b9be-2b416451bd4f)

Sayfa üzerinde bulunan default relay sekmesine iRedMail sunucunuzun IP adresinizi yazınız.

![image](https://github.com/user-attachments/assets/7b80f1a6-c864-4650-a869-b6e202df39a5)




