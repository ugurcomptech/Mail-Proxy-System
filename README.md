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

![image](https://github.com/user-attachments/assets/7b80f1a6-c864-4650-a869-b6e202df39a5)

Sayfa üzerinde bulunan default relay sekmesine iRedMail sunucunuzun IP adresinizi yazınız.

**Not:** Port olarak 25 kullanmanız önerilir. Eğer 25 portunu kullanacak iseniz bazı VDS sağlayıcıları 25 portunuzun gelen ve giden trafiğini engelleyebilmektedir. Kendilerine bildiride bulunmanız durumunda işlem sağlayabilirler.

Mail Proxmox üzerinde yapılacak ek bir ayar bulunmuyor. Temel olarak bunlar yeterlidir.


## iRedMail Kurulumu

Aşağığdaki sayfa üzerinden sunucunuzun sürümünü kontrol ediniz. Eğer sunucu sürümünüz düşük ise yükselterek işlem sağlayınız.

https://www.iredmail.org/download.html

Aşağıdaki komutları sırayla çalıştırınız:

```
 sudo apt update && apt upgrade -y
 sudo apt-get install -y gzip dialog
 wget https://github.com/iredmail/iRedMail/archive/refs/tags/1.7.1.tar.gz
 tar zxf  1.7.1.tar.gz iRedMail-1.7.1/
 cd iRedMail-1.7.1/
 bash iRedMail.sh
```

**Not:**inke bir süre sonra erişilmeyebilir. Yukarıda bıraktığım iredmail sitesi üzerinden kontrol edebilirsiniz.


![image](https://github.com/user-attachments/assets/ea76e914-d65c-4946-90a7-9a2496b0037b)


![image](https://github.com/user-attachments/assets/fd293878-d79b-46dc-b9eb-9de3d2627d32)

Tüm posta kutularının depolanacağı konumu belirtin. Varsayılan /var/vmail/'dir.


![image](https://github.com/user-attachments/assets/c5c1c01a-f135-4044-b8ae-6f30d742f20a)

Posta hesaplarını OpenLDAP'ta saklamayı seçerseniz, iRedMail yükleyicisi LDAP sonekini ayarlamanızı isteyecektir.

![image](https://github.com/user-attachments/assets/7d8f377f-019c-4ded-b006-67f1995fe1ee)

![image](https://github.com/user-attachments/assets/fdc31b92-42c9-499d-b566-17367e0f7c5d)

İlk posta alan adınızı ekleyin

![image](https://github.com/user-attachments/assets/0ad06fdf-370e-4ff5-bd0e-b8a8addf19d5)

İlk posta etki alanınızın yönetici hesabının şifresi.

Not:** Bu hesap bir yönetici hesabı ve bir posta kullanıcısıdır. Bu, web postasına ve yönetici paneline (iRedAdmin) bu hesapla giriş yapabileceğiniz anlamına gelir, giriş kullanıcı adı tam e-posta adresidir.


