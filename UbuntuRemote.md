 # CLOUD UBUNTU REMOTE DESKTOP İLE KULLANMA

> Hetzner ve Contabo üzerinden VPS Ubuntu sunucular oluşturarak uzak bağlantı ile kontrol edeceğiz
 #
<details>
  <summary> <h1> HETZNER </summary> </h1>

> Kayıt olurken referans kodu ile '20 Euro' deneme bakiyenizi alabilirsiniz.

> [Ref Linki](https://hetzner.cloud/?ref=VUlUHlXsLbAs)

> Kayıt olurken KYC istiyorlar aksi takdirde hesabı devre dışı bırakıyorlar kullanabilmeniz için resmi belge göndermeniz gerekli.

> Kayıt olduktan sonra eğer bakiye yüklenmemişse support kısmından bilet oluşturarak talepte bulunun yüklüyorlar. 
    
> Hetznerde sunucuyu oluşturduktan sonra kayıtlı olduğumuz mail adresine IP ve PW bilgileri gönderiliyor.

![image](https://github.com/user-attachments/assets/035f2c59-5dd3-4a74-a1b9-ca3fb7ad1e0e)

> Hetzner İlk SSH bağlantısından sonra şifreyi değiştirmeyi gerektiriyor ilk bağlantıdan sonra yeni şifremizi oluşturacağız.
</details>

<details>

 #

  <summary> <h1> CONTABO </summary> </h1>
    
> CONTABO da sunucuyu oluştururken SSH için gerekli PW oluşturmak gerekiyor.

> Daha sonra bu belirlediğiniz bağlantı bilgilerini kaydedin Ve profildeki Your Services kısmına sunucunuzun oluşturulmasını bekleyin

> 
</details>
 
 #

> SSH ile bağlanmak için IP ve Password bilgisini aldıktan sonra Terminale ihtiyacımız olacak

> Terminal için [Git Bash](https://git-scm.com/downloads) ve [MobaXTerm](https://mobaxterm.mobatek.net/) kullandım.

> Kurulumu tamamladıktan sonra terminalden ssh bağlantımızı aşağıdaki gibi gerçekleştirelim. Hetzner için key oluşturak yes diyelim

```console
 ssh root@Sunucu IP adresi
```
> Sonrasında ilk bağlantı şifremizi gireceğiz. Şifreyi yazarken karakter göstermez doğru yazmaya dikkat edelim



```console
# BAŞARISIZ BAĞLANTIDAN SONRA WARNING MAN IN MIDDLE HATASI ALIRSANIZ AŞAĞIDAKİ KOD İLE O BAĞLANMAK İSTENİLEN SUNUCUYA KEYGEN SIFIRLAYIN
 ssh-keygen -R SUNUCUIP
```

> HETZNER için-Sunucumuza bağlantıyı başarıyla bağlandıktan sonra
```console
# sistemi sırasıyla aşağıdakileri yazarak güncelleyelim
 sudo apt update
 sudo apt upgrade
```
> CONTABO için - Contabo da ubuntunun son sürümü 24.04 olmadığından
```console
# önce versiyonu yükseltelim gelen seçeneklere y diyelim
sudo do-release-upgrade
# sistemi sırasıyla aşağıdakileri yazarak güncelleyelim
sudo apt update
sudo apt upgrade
```
> Kulanıcı oluşturup izinleri vermek ve remote aktifleştirmek için önce klasör olusturalım
```console
mkdir remote
```
> Klasöre geçmek için
```console
cd remote
```
> Dosya oluşturalım
```console
nano remote.sh
```
> Gerekli paketler için aşağıdakileri açılan dosyaya yapıştıralım , Sırasıyla Ctrl + X , Ctrl + Y , Enter basarak kaydedelim
```console
#!/bin/bash

# Remote olarak kontrol edebileceğimiz kullanıcı bilgilerini aşağıya yazalım özel karakter olmasın.
USER="KULLANICI ADI"
PASSWORD="ŞİFRE"

# Paket listesini günceller
apt update && apt upgrade -y

# GNOME Masaüstünü yükler
sudo apt install -y ubuntu-desktop

# Uzak masaüstü sunucusunu (xrdp) kurar
sudo apt install -y xrdp

# USER kullanıcısını şifreyle ekler
sudo useradd -m -s /bin/bash $USER
echo "$USER:$PASSWORD" | sudo chpasswd

# USER kullanıcısını yönetim hakları için sudo grubuna ekler
sudo usermod -aG sudo $USER

# xrdp'yi GNOME masaüstünü kullanacak şekilde yapılandırır
echo "gnome-session" > ~/.xsession

# xrdp hizmetini yeniden başlatır
sudo systemctl restart xrdp

# Başlangıçta xrdp'yi etkinleştirir
sudo systemctl enable xrdp

echo "Kurulum tamamlandi."

```
> Dosya erişimini düzenleyelim.
```console
chmod +x remote.sh
```
> Dosyamızı çalıştıralım
```console
./remote.sh
```
Sistemimiz uzaktan bağlantılar için hazır. Belirlediğimiz kullanıcı ile giriş yapacağız

```console
# Eğer seasman gibi bağlantı sorunu yaşarsanız uzaktan bağlantı ekranı gelmez ise aşağıdakileri deneyin.
# IPv6 etkin mi görelim
sysctl -a | grep disable_ipv6
# Devre dışı bırakalım
sysctl -w net.ipv6.conf.lo.disable_ipv6=0
```
