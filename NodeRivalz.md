# Sunucumuzu Rivalz Node kuracağız
<details>
<summary><h1>sunucumuz normal bir şekilde oluşturduysak</summary></h1>
chrome kuralım
  
```console
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

```
# Eğer hata alırsak
```console
apt install wget
```
İndirdiğimiz dosyayı kuralım

```console
sudo apt install ./google-chrome-stable_current_amd64.deb
```
 >Rivalz nodemizi sitesinden indirip çalıştırabiliriz

<details>
  <summary> <h1> İndirilen dosya Run edilmez veya gözükmez ise </summary> </h1>
  
  > Dosya açalım

  ```console
nano VPSFix.sh
```
  > Aşağıdakileri içine yapıştıralım ve ```Ctrl + x``` ve ```CTRL + y``` bastıktan sonra ```ENTER``` basıp çıkalım
  ```console
#!/bin/bash

# Version 1.1

# Hizmet dosyası yolunu tanımlar
SERVICE_FILE="/etc/systemd/system/default-interface-config.service"

# Komut dosyasının root ayrıcalıklarıyla çalıştırılıp çalıştırılmadığını kontrol eder
if [ "$EUID" -ne 0 ]; then
  echo "Lutfen root olarak çaliştirin."
  exit 1
fi

# Zaten yüklü değilse ethtool'u yükler
if ! command -v ethtool &> /dev/null; then
  echo "ethtool bulunamadi, yukleniyor..."
  apt-get update
  apt-get install -y ethtool
fi

# Varsayılan ağ arayüzünü alır
DEFAULT_INTERFACE=$(ip route show default | awk '/default/ {print $5}')

# systemd hizmet dosyasını oluşturur
cat <<EOL > $SERVICE_FILE
[Unit]
Description=Configure default network interface
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ethtool -s $DEFAULT_INTERFACE speed 1000 duplex full autoneg off
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOL

# Yeni hizmeti tanımak için systemd'yi yeniden yükler
systemctl daemon-reload

# Hizmetin önyüklemede başlatılmasını sağlar
systemctl enable default-interface-config.service

# Starts the service immediately
systemctl start default-interface-config.service

```

Kullanıcı izinlerini oluşturalım

```console
chmod +x VPSFix.sh
```

Ardından dosyamızı çalıştıralım

```console
./VPSFix.sh
```
  </details>
</details>

<details>
  <summary><h1>
    sıfırdan node kurmak için
  </summary>
  </h1>
    
> Kulanıcı oluşturup izinleri vermek ve remote aktifleştirmek için önce klasör olusturalım

```console
mkdir ubuntu-rivalz
```

> Klasöre geçmek için

```console
cd ubuntu-rivalz
```
> Script dosyası oluşturalım

```console
nano setup_rivalz.sh
```

Gerekli paketler için aşağıdakileri açılan dosyaya yapıştıralım , Sırasıyla ```Ctrl + X``` , ```Ctrl + Y``` , ```Enter``` basarak kaydedelim

```console
#!/bin/bash

# Remote olarak kontrol edebileceğimiz kullanıcı bilgilerini aşağıya yazalım özel karakter olmasın.
USER="KULLANICI ADI"
PASSWORD="ŞİFRE"

# Paket yöneticisini günceller
apt update && apt upgrade -y

# GNOME desktop kurar
sudo apt install -y ubuntu-desktop

# remote için xrdp kurar
sudo apt install -y xrdp

# Kullanıcımızı ekler
sudo useradd -m -s /bin/bash $USER
echo "$USER:$PASSWORD" | sudo chpasswd

# Kullanıcının yönetim hakları için sudo grubuna ekler
sudo usermod -aG sudo $USER

# xrdp'yi GNOME masaüstünü kullanacak şekilde yapılandıralım
echo "gnome-session" > ~/.xsession

# xrdp hizmetini yeniden başlatır
sudo systemctl restart xrdp

# Sistem başlangıcına xrdp yi ekler
sudo systemctl enable xrdp

# wget paket listesini kurar
sudo apt install -y wget

# Rivalz client indirir
wget https://api.rivalz.ai/fragmentz/clients/rClient-latest.AppImage -O rClient-latest.AppImage

# Dosyaya izinleri verir
chmod +x rClient-latest.AppImage

# Belgeler dizinini oluşturur
sudo -u $USER mkdir -p /home/$USER/Documents

# clienti documentse taşır
sudo mv rClient-latest.AppImage /home/$USER/Documents/rClient-latest.AppImage

# client'i kullancak kullanıcıyı değiştirir
sudo chown $USER:$USER /home/$USER/Documents/rClient-latest.AppImage

```

> Dosyayı kullanıcı izinlerini verelim.

```console
chmod +x setup_rivalz.sh
```

> Dosyamızı çalıştıralım.

```console
./setup_rivalz.sh
```

Clientin gözükmesini ve run edilmemesi hatası için

```console
nano VPSFix.sh
```
.

```console
#!/bin/bash

# Version 1.1

# Hizmet dosyası yolunu tanımlar
SERVICE_FILE="/etc/systemd/system/default-interface-config.service"

# Komut dosyasının root çalıştırılıp çalıştırılmadığını kontrol eder
if [ "$EUID" -ne 0 ]; then
  echo "Lutfen root olarak çaliştirin."
  exit 1
fi

# Yüklü değilse ethtool'u yükler
if ! command -v ethtool &> /dev/null; then
  echo "ethtool bulunamadi, yukleniyor..."
  apt-get update
  apt-get install -y ethtool
fi

# Varsayılan ağ arayüzünü alır
DEFAULT_INTERFACE=$(ip route show default | awk '/default/ {print $5}')

# systemd hizmet dosyasını oluşturur
cat <<EOL > $SERVICE_FILE
[Unit]
Description=Configure default network interface
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ethtool -s $DEFAULT_INTERFACE speed 1000 duplex full autoneg off
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOL

# Systemd'yi yeniden yükler, yeni servisin gözükmesi için
systemctl daemon-reload

# Servisin sistem başlangıcında başlatılmasını sağlar
systemctl enable default-interface-config.service

# Servisi başlatır
systemctl start default-interface-config.service

```
Sırasıyla ```Ctrl + X``` , ```Ctrl + Y``` , ```Enter``` basarak kaydedelim.

> Dosyayı kullanılabilir hale getirelim.

```console
chmod +x VPSFix.sh
```

> Dosyayı çalıştıralım.

```console
./VPSFix.sh
```
</details>
