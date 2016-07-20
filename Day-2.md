# Gün 2
Genelde bir ağın dışarıda firewallı olur bu ağın birden fazla ayağı olur. DMZ ve Internal Network olarak iki ayak vardır. Bir site yayını yapmak için dışarıdan insan girmesi için uygulamalar DMZ'ye konulur. Bu ayak dışarıya açıktır ve internal network'ün etkilenmemesi için bu kısma konulur. Kullanıcılar her zaman firewall arkasındadır ve firewalldan girer çıkarlar.

Her şeyin patchi vardır ama insanların patchi yoktur. Son zamanlarda zayifetler sosyal mühendislik ve hedef bazlı olarak artmıştır. Frameworkler arttıkca shell, sql injection gibi açıklar azalıcaktır. Başka türlü(sosyal mühendislik vb.) zafiyetler olmaya devam edicektir.

Virüs programları imza bazlı ve davranış bazlı çalışmaktadır. Örneğin reverse tcp davranışsal bazlı olarak yakalanır, imza bazlı yakalanmaz. (SEP güzel bir davranış bazlı antivürüs)

## Bir sosyal ağ senaryosu
- Bir zararlı yazılımı sosyal mühendislik ile bir şirketdeki işciye indirttir.
- Bu yazılım ile işci blgisayarındaki windows hash şifrelerini al ve ağdaki diğer bilgisayarlara login olmaya çalış.(pass the hash) (Şirketler genelde her yeni işciye aynı işletim sistemin yüklenmesi snucu, aynı local admin şifresine sahiptir. bunun hashi ile diğerler makinalara login olmak denenebilir)
- Ağda ele geçirdiğin bilgisayarlar üzerinden sunuculardan veri çek ve kendine gönder(reverse tcp ile)

## Network 
```
nc -lvp 444            # 444 portunda dinlemeye başla, verbose output ver
nc xxx yyy             # xxx sunucusuna yyy portundan bağlan
tcpdump -w analiz.pcap # analiz.pcap'e tcp iletişimi kaydet
tcpdump -tttnn         # güzel formatta kaydet/kaydedileni oku
tshark                 # wireshark'ın ilkel versiyonu
wireshark -r test.pcap # test.pcap'i wireshark ile incele

nbtscan 192.168.202.1/24            # subnetdeki tüm bağlı kullanıcıları gorup ve mac adreslerini alabilirsin.
macchanger -m "mac:adr:esi" eth0    # mac adresi eth0 için değiştir.
macoff                              # mac flooding yaparak ağ switchini doldurarak, kullanıcıların ağ erişimini zorlamak.
pig.py                              # dhcp starvation yapmak için ip alma istediği göndererek ip flood yapar. 
```

## Hacking tool ve Terimleri
- emkei.cz - anonymous mail ama ipleri blacklisted (mail gönderirken spame düşmemek için yaşlı bir domain kullanmak bile gerekebilir.)
- setoolkit - mrrobotda bile çıkan sosyal mühendislik toolkiti :) (örneğin sitecloner ile bir siteyi clonelayıp credential harvester yapılabilir)
- fierce - dns bruteforce aracı. subdomain bulmamıza yardımcı olabilir.
- crunch - bruteforce şifre vb. oluşturmak için için kullanabilirsin.
- ipchicken.com - whatismyip alternatifi daha temiz.

## Tunneling 
Tünelleme kısaca protokol üzerine protokol bindirmektir. Firmalarda politika ve güvenlik gereği kullanıcıların dışarıya erişimi sınırlıdır.(örneğin sakarya ünide de limitasyon altındayız) Bazı networkler örneğin sadece HTTP protokole izin verirler. Buna çözüm olarak bir kaç yöntemimiz vardır.

### Limitli ağ dışına çıkmak için bir kaç yöntem
- ssh 443 portuna alırsak sadece 80 ve 443 portu açık olan networklerde sunucularımıza ssh atabiliriz.
- shuttle port tunneling, sunucuda sadece ssh varken, client tarafından vpn bağlantısı yapmak için. vpn sunucuya gerek kalmaksızın.
- sslh hem ssl hem ssh protokolü aynı port üzerinden sunmak için.
- iodine dns tunneling, sunucuda hiç bir port açık değil iken, udp dns sorguları ile tünelleme yaparak dış internete erişim sağlamak. network interface oluşturup size bir ip atar.

sshuttle, iodine engellemek dpi(deep packet inspection) ile mümkündür. imza bazlı korumalar trafik anomalitesinden yakalayabilir.
