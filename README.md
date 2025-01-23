# iRedMail + Mail Proxmox Gateway Entegrasyonu

![image](https://github.com/user-attachments/assets/fd2ca850-a018-4438-bc29-3567bd67f4d2)


Bu döküman, e-posta trafiğini filtrelemek ve güvenli bir şekilde yönetmek için kullanılan Proxmox Mail Gateway (PMG) ile kullanıcıların e-posta gönderip almasını sağlayan iRedMail arasında entegrasyon kurma sürecini adım adım açıklar. Bu rehber sayesinde, iki sistem arasında güvenli ve verimli bir e-posta altyapısı oluşturabilir, spam ve virüs korumasını güçlendirebilirsiniz.

## Gereksinimler

- 2 Adet Linux sunucu ( Debian 12, Ubuntu 22/24)


Eğer satın almayı yapmış olduğunuz VDS sağlayıcısında Debian 12 bulunmuyor ise aşağıda iletmiş olduğum döküman üzerindeki adımları takip ederek upgrade işlemleri sağlayabilirsiniz.

[Upgrade Debian 11 to 12](https://phoenixnap.com/kb/upgrade-debian-11-to-12)

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

![image](https://github.com/user-attachments/assets/623c18e6-9d1f-49c8-9c3c-e8c2ef95a862)

Tracking Center sayfası üzerinden tarafınıza gelen mailleri görüntüleyebilirsiniz.


**Not:** Port olarak 25 kullanmanız önerilir. Eğer 25 portunu kullanacak iseniz bazı VDS sağlayıcıları 25 portunuzun gelen ve giden trafiğini engelleyebilmektedir. Kendilerine bildiride bulunmanız durumunda işlem sağlayabilirler.

Mail Proxmox üzerinde yapılacak ek bir ayar bulunmuyor. Temel olarak bunlar yeterlidir.


## iRedMail Kurulumu

Aşağığdaki sayfa üzerinden sunucunuzun sürümünü kontrol ediniz. Eğer sunucu sürümünüz düşük ise yükselterek işlem sağlayınız.


[iRedMail Release](https://www.iredmail.org/download.html)

Aşağıdaki komutları sırayla çalıştırınız:

```
 sudo apt update && apt upgrade -y
 sudo apt-get install -y gzip dialog
 wget https://github.com/iredmail/iRedMail/archive/refs/tags/1.7.1.tar.gz
 tar zxf  1.7.1.tar.gz iRedMail-1.7.1/
 cd iRedMail-1.7.1/
 bash iRedMail.sh
```

**Not:** Linke bir süre sonra erişilmeyebilir. Yukarıda bıraktığım iredmail sitesi üzerinden kontrol edebilirsiniz.


![image](https://github.com/user-attachments/assets/ea76e914-d65c-4946-90a7-9a2496b0037b)


![image](https://github.com/user-attachments/assets/fd293878-d79b-46dc-b9eb-9de3d2627d32)

Tüm posta kutularının depolanacağı konumu belirtin. Varsayılan /var/vmail/'dir.


![image](https://github.com/user-attachments/assets/c5c1c01a-f135-4044-b8ae-6f30d742f20a)

Posta hesaplarını OpenLDAP'ta saklamayı seçerseniz, iRedMail yükleyicisi LDAP sonekini ayarlamanızı isteyecektir.

Posta hesaplarını MySQL/MariaDB/PostgreSQL'de depolamayı seçerseniz iRedMail yükleyicisi sizin için rastgele, güçlü bir şifre oluşturacaktır. Bunu iRedMail.tips dosyasında bulabilirsiniz.

![image](https://github.com/user-attachments/assets/7d8f377f-019c-4ded-b006-67f1995fe1ee)

![image](https://github.com/user-attachments/assets/fdc31b92-42c9-499d-b566-17367e0f7c5d)

İlk posta alan adınızı ekleyin

![image](https://github.com/user-attachments/assets/0ad06fdf-370e-4ff5-bd0e-b8a8addf19d5)

İlk posta etki alanınızın yönetici hesabının şifresi.

**Not:** Bu hesap bir yönetici hesabı ve bir posta kullanıcısıdır. Bu, web postasına ve yönetici paneline (iRedAdmin) bu hesapla giriş yapabileceğiniz anlamına gelir, giriş kullanıcı adı tam e-posta adresidir.

## Son Ayarlamalar

iRedMail sunucumuzun gateway sunucumuzu dinlemesi için postfix üzerinden IP adresini yazıyoruz.

```
nano /etc/postfix/main.cf
mynetworks = 127.0.0.0/8, [::1]/128, 1.1.1.1 # proxmox mail gateway ip adresinizi yazınız.
```

### Webmail

Webmail için ise hosts dosyanızın üzerinde aşağıdaki gibi ayarlamalar yapmanız gerekmekte:

```
sudo hostnamectl set-hostname mail.domain.com
nano /etc/hosts
1.1.1.1  mail.domain.com
reboot
```

Ek olarak DNS bölgenize de bu şekilde bir kayıt girmeniz gerekmektedir.

### Webmail SSL

Webmail için SSL sertifikası kurmamız gerekmekte. Aşağıdaki komutları çalıştıralım:


```
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d mail.domain.com
```

Eğer Nginx üzerinde ilgili domain için bir conf dosyası yok ise hata alabilirsiniz. Almış olacağınız hata çok önemli bir hata değil. Sadece sertifikalarımızın dosya yolu bize gereklidir.

iRedMail SSL sertifika template dosyasını  `sudo nano /etc/nginx/templates/ssl.tmpl` barındırır.

```
#ssl_certificate /etc/ssl/certs/iRedMail.crt;
#ssl_certificate_key /etc/ssl/private/iRedMail.key;
```
Yorum satırı yaparak bunları devre dışı bırakacağız ve oluşturmuş olduğumuz sertifikaları ekleyeceğiz.

```
ssl_certificate /etc/letsencrypt/live/domain.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/domain.com/privkey.pem;
```

Conf dosyalarımızda hata olup olmadığını kontrol ediyoruz. bir sorun yok ise yeniden başlatıyoruz.

```
sudo nginx -t
sudo systemctl restart nginx
```

## PMG Web Arayüz SSL

Buradaki süreç biraz sancılı olabilmekte. O yüzden geçmiş SSL dosyasının yedeğini almanızı öneririm.

Aşağıdaki komutları sırayla yazalım:

```
certbot certonly --standalone --key-type rsa -d mail.domain.com
cat /etc/letsencrypt/live/mail.domain.com/fullchain.pem /etc/letsencrypt/live/mail.domain.com/privkey-rsa.pem > /etc/pmg/pmg-api.pem
644 /etc/pmg/pmg-api.pem
chmod 600 /etc/pmg/pmg-api.key
```

İşlem tamamlandıktan sonra servisimizi restart edeceğiz.

```
systemctl restart pmgproxy
```


Mail arayüzünüzü açarak kontrol edebilirsiniz.


## iRedMail Admin

Eğer iRedMail admin hesabınızı unutursanız aşağıdaki dosyayı kontrol ediniz. Sürümünüz değişiklik gösterebilir.

```
/root/iRedMail-1.7.1/iRedMail.tips 
```

https://mail.domain.com/iredadmin/ adresi üzerinden ise admin paneline ulaşıp domain oluşturma, user oluşturma gibi işlemleri sağlayabilirsiniz.









## Kaynakça
- [Proxmox Mail Gateway Documentation](https://pve.proxmox.com/wiki/Install_Proxmox_VE_on_Debian_12_Bookworm)
- [iRedMail Official Website](https://docs.iredmail.org/install.iredmail.on.debian.ubuntu.html)
- [Web İnterface SSL](https://forum.proxmox.com/threads/how-to-have-mail-gateway-url-dashboard-with-ssl.108153/)
