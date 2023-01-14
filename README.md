#### Possuo uma impressora Ender 3 V2, recentemente instalei o [OctoPrint](https://octoprint.org/) para facilitar a comunicação com a mesma em meu Raspberry Pi 2 B+. Como ele não possui Wifi integrada comprei um dongle Wifi para realizar a conexão, após diversas pesquisas para encontrar o driver correto decidi criar esse repositorio para facilitar proximas instalações.

------------------

# Realtek USB Wireless Adapter Drivers

### Realtek USB Wireless Adapter Drivers [rtl8188fu] [0bda:f179]

### My kernel is 5.10.103-v7+ but according to the original author of the repository it also works on the following 4.15.x ~ 5.9.x (ARM devices)
------------------
#### My dongle looks like the following
![Alt text](/realtek-usb-wireless-adapter.jpg?raw=true "Realtek USB Wireless Adapter")

------------------
## How to install

#### Connect to your Raspberry Pi - (ssh)
```
ssh USER@IP
```

#### Check your kernel
```bash
uname -r
# The return should look like the following
# 5.10.103-v7+
```
#### Upgrade your Raspberry Pi
```bash
sudo apt-get update

sudo apt-get install -f
```

#### Install tools
```bash
sudo apt-get install -y build-essential git dkms raspberrypi-kernel-headers
```
#### Link folders
```bash
sudo ln -s /lib/modules/$(uname -r)/build/arch/arm /lib/modules/$(uname -r)/build/arch/armv7l
```

#### Clone and build driver
```bash
git clone -b ARM-driver-Kernel-4.15.x-5.9.x https://github.com/leopinnheiro/Realtek-USB-Wireless-Adapter-Drivers.git

cd Realtek-USB-Wireless-Adapter-Drivers

sudo dkms add ./rtl8188fu

sudo dkms build rtl8188fu/1.0
# It may take several minutes

sudo dkms install rtl8188fu/1.0

sudo cp ./rtl8188fu/firmware/rtl8188fufw.bin /lib/firmware/rtlwifi/
```
------------------
## Configure your connection
```bash
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```
#### Add or change
```
network={
    ssid="You SSID Name"
    psk="Your WiFI Password"
    key_mgmt=WPA-PSK
}
```
```
CTRL + X and YES for save file
```
```bash
sudo reboot
```

------------------
## Disable power management and solve plugging/replugging issues
```bash
sudo mkdir -p /etc/modprobe.d/

sudo touch /etc/modprobe.d/rtl8188fu.conf

echo "options rtl8188fu rtw_power_mgnt=0 rtw_enusbss=0" | sudo tee /etc/modprobe.d/rtl8188fu.conf
```
------------------
## Disable MAC address spoofing
```bash
sudo mkdir -p /etc/NetworkManager/conf.d/

sudo touch /etc/NetworkManager/conf.d/disable-random-mac.conf

echo -e "[device]\nwifi.scan-rand-mac-address=no" | sudo tee /etc/NetworkManager/conf.d/disable-random-mac.conf
```
------------------
## How to uninstall
```bash
sudo dkms remove rtl8188fu/1.0 --all

sudo rm -f /lib/firmware/rtlwifi/rtl8188fufw.bin

sudo rm -f /etc/modprobe.d/rtl8188fu.conf
```