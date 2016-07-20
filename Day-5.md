# Gün 5
Siberkamp Gün #5.

## DNS Tunneling
Iodine engellemenin yollarından biri aynı domaine yapılan isteklerin kısa dönemli sıklıklarına bakmak olabilir. Iodine veri kaçakçılığı yaparken DNS caching'e yakalanmamak için random gen subdomainlere istek yaparak çalışır. Diğer bir yöntem olarak Iodine encodingini çözüp, 'belki' bunu yakalayak bir iodine script yapılabilir.

Örneğin bunu yapmanın benzer bir boyutu, aynı ns'a bağlı domainlerin bir listesini çıkarıp, göndereceğiniz bir mesajı parçalara ayırıp, bu domainlerin subdomainleri mesajın parçaları olacak şekilde dns query yaparakda yapabilirsiniz. Iodine bunu packet seviyesinde yaptığı için genel programların işleyişi engellemeden pivoting yapabiliyoruz.

Bir başka dns tunneling uygulaması, java ile yazılmıştır. Bu uygulama packet bazlı tam bir çevirme yapmak yerine, ön yüze bir http proxy kurup, arka tarafta bunları dns querylerine çevirerek dış dünya ile bağlantı kurmaktadır. Burada sistem bazlı bir paket alışverişi yapılmadığı için, user yetkileri ile dışarısı ile bağlantı kurulabilir.

## Network Pivoting
FW arkasında ve DMZ içinde olan sunucular, ele geçerdiğiniz bir makine ile aynı networkde ise. Client aracılığı ile networkdeki diğer makinelere bağlanabilirsiniz. Örneğin BGA bu şekilde sosyal mühendislik yaparak bir banka işcisinin makinesini ele geçirdi. daha sonra bu makinede local exploit ile admin oldu ve workgroupdaki dc'a geçerek domain admin oldu.

### Meterpreter
Bir makinaya meterpreter ile bağlandıktan sonra 2 tarz basic persistance örneği var. Bunlardan biri kendisini registry'e ekleyip, kendini dosya sistemine yazarak her açılışta veya login/logoff çalışması. Diğeri ise kendini apache tarzı bir isimle windows service olarak eklemesi.

Windows Service'in yakalanma ihtimali biraz daha fazla.

meterpreter ile bağlantıktan sonra arp_scanner -r subnet tarzında tarama yaparak ağdaki diğer makineleri tarayıp, bu makinelerde de zayifet arayabiliriz.

`portfwd -l port -r victim_local.ip -p port2` kendi makinenizde belli bir portdan victim networkdeki bir makinenin port'una proxy yapar.
`route add kendi_makinenizde_yeni_bir_subnet gateway session` kendi makinenizde bir subnet oluşturup, buna bağlanınca başka bir networke geç. --route 172.16.239.0 255.255.255.0 3

### DDOS Time
TCP Syn ->, <- SYN <- ACK, ACK -> şeklinde çalışır 3-way handshake denir.

Syn flood açığında, sunucuya syn gönderip, sunucudan syn/ack aldıktan sonra ack göndermediğiniz zaman, sunucu 60-120 saniye timeout beklerken state tablosu dolduğu için daha fazla tcp connection kabul edemez. 8M işleyebilen sistemlerde var, 1500 tane isteyebilende var. Genelde windos state tablosu daha büyük.
`hping3 --rand-source -S -p 80 192.168.99.101 --flood` random sourcelar ile sadece tcp syn istek yap, 80 portuna verilen ipye ve floodla

#### Syn Cookie
Sunucu ile clientler arasına bir makine kurarak, istek yapan clientin gerçek olduğunu anlamak için tcp handshake tamamlanana kadar tcp sunucuya syn paket göndermez. Tcp clientin isteği tamamlandıktan sonra, istek sunucuya replicate edilir. Bu şekilde sunucunun state tablosu doldurulmamış olur.

#### DNS Flood
`dig @ns3.nic.fre www.ripe.net +dnssec` fransız dns sunucusu üzerinden www.rip.net kayıtlarını iste dns ile ilgili ne var ne yok ver

Recursive çalışan dns sunucuları başkalarının iplerini src olarak yazdığınız udp paketlere cevap verdiği için. Birden fazla ve güçlü dns sunuculara istek gönderek, kendi trafiğinizi katlayabilirsiniz. DNS sunucular engellense bile ağ trafiği dolabileceği için bu atak çok tehlikelidir. (DNS Amplification Attack)

`mz eth0 -B 172.16.52.148 -A xx.com -t dns "dp=53, q=www.google.com" -c 10` eth0 portundan -B'den sonra verilen dns sunucusunu kullanarak, -A'dan sonra fake src verilir ve -c kaç tane paket gönderileceğini söyler. -c 0 yapılırsa sonsuz istek gönderilir.

#### Payload
IIS payload göndererek headera yazdığımız bir Accept Byte range olayı ile BSOD verebiliyoruz. Bu açık 2015de bulunmuş. Bu tarz 0 dayler ve payloadlar sektörde var.

#### Wifi Jammer.py
Wifi Deauth attack ağda müsait olduğu için bu script kendiniz dışındakileri düşürmek için işe yarıyor. Bir wifi ağına bağlanmadığınız halde bunu yapabiliyorsunuz. Monitor moddaki paketleri yakaladığınız zaman, mac adresleri unencrypted olduğu için bu adresleri alıp, access point src ile, spoofed udp deauth paketleri gönderebiliyorsunuz. Collectorlar ile kapalı alanlarda bunun önüne geçilebiliyor. Collectorlar daha güçlü paketler göndererek bunların önüne geçiyor. Eğer kendi cihazından farklı bir access point alıp, jamming işlemini bu cihaz ile yaparsan, kendi bilgisayarından internete daha iyi bağlanırsın.

#### Easycreds
Telefon ve Laptop cihazların ağ aramasını kullanarak daha önce bağlandıkları ağlar ile aynı özelliklerde access point oluşturur. SSLStrip vb. uygulamalar ile Mitm attack yapar. Örneğin bankalara gidip banka ağına deauth paket atılıp, easycreds vb. ile mitm yapılabilir

# Kaynak ve Toollar
- CTFTime -> Site ctfleri ve bitince pcapleri veriyor. Bu pcapleri ve çözüm yöntemlerini ders edinmek için kullanabilirsiniz. 
- Canyoupwnme -> Hakkı'nın. Network Forensics Puzzle Contest gerçek dünyadan senaryolar veriyor. Gerçek yakınlı hikayeler veriyor. Mesela kablosuz ağ sorunları yaşayan arkadaşına yardımcı olmak için bir senaryo. 
- Hanubis ve Virustotal -> kullanarak pcap inceleyebilirsin. Hanubis ve Virustotal kullanarak pcap inceleyebilirsin. 
- pcapr -> pcap database. 
- pentest bookmarks -> pentest ile ilgili programlar 
- ceh sertifika -> ethical hacker
- oscp -> sertifika eğitimi
