# Gün 1
Ne kadar çok bilgi o kadar çok işimize yarar. Örneğin hepsi burada geldi ilk önce site hangi ipleri kullanıyor tarzında konlar hakkında bilgi edinmemiz lazım. 

## Pasif ve Aktif Keşif
- Ping atarak sitenin bize gösterdiği ip'yi bulabiliriz. whois kullanarak site hakkında genel bilgileri alabiliriz(subnet ipleri, mail sunucu ipleri vs., registar). Örneğin bir siteye whois atıp kişinin mail adresini alarak, aynı mail adresine kayıtlı diğer domainleri bularak bu sitelerde zafiyet arayabiliriz. Aynı zamanda bir sitenin subnetini bularak bu subnetteki sunucularda açık portları ve kullanılan programları görebiliriz.
- Online leakleri kullanarak insanların başka sitelerdeki çalınan üyeliklerini kullanarak, hedef sitemizde bu üyelikleri deneyebiliriz.
- Subdomain ve Firma işcilerinin mail adreslerini bulmak için arama motorlarını kullanabiliriz. Buna yardımcı olan bir program kali lnuxda bulunan theharvesterdır. (theharvester hepsiburada.com -l 500). Bu programa alternatif programlarda vardır. Aynı zamanda site:linkedin.com "hepsiburada" gibi manuel aramalar ilede kendimiz bir arama yapabiliriz.

#### Side Story
Bir firma için elle üretilen bir mailing liste atılan pishing maili ile elde edilen mail şifresi sonrasında, firmanın işcileri için kullandığı bir platform keşifedilmiştir. Daha sonra bu platformdan işciye gelen bir mail, diğer işcilere aktarılarak sisteme tekrar giriş yapılmaları istenmiştir. İşcilerin platform şifresi elde edildikten sonra sitede bir sql injection bulunmuş ve 6 milyon kullanıcının verisine erişilmiştir.

### Subdomain
Yöntemler değişeceği gibi subdomain ve mail adresi elde etmek blackbox testler için önemli bir olaydır. Waldo subdomain keşfetmek için kullanabileceğimiz programlardan biridir. Bruteforce veya Wordlist kullanılabilir.

```
dig mx siteadi.com # mailserverlarını bulmak için
dig ns|cname|a
```

### Diğer yöntemler
- Waybackmachine, google cache tarzı interneti arşivleyen siteler kullanarak, sitenin önceki hallerine erişerek bilgiye ulaşmaya çalışabiliriz.
- exploit-db kullanılarak google hacking databaseden yararlanarak site üzerinde açıklar bulunabilir.
- Opensource sitelerin kodlarına bakılarak unutulan config verilerinden yaralanılabilir.
- wappalyzer eklentisi kullanılarak site hakkında bilgi edinilebilir.
- robots.txt, humans.txt dosyaları incelenebilir.
- Bing üzerinden ip: xxx.xxx.xxx.xxx şeklinde arama yaparak aynı ip üzerinde barınan siteleri bulabiliriz. Bu sitelerde bulduğumuz açıklar bize yardımcı olabilir.
- Reverse DNS yaparak subnet ile eşleştirilmiş bütün domainleri bulabiliriz.
- Shodan kullanarak interneti açık portlar ve exploitler için arayabiliriz. Aynı zamanda pasif keşif kısmında spesifik bir site için bize yardımcı olabilir. Örneğin mongodb portu açık olan sitelere bakılarak şifresiz mongodbg sunucularına giriş yapılabilir.
- Sunucuların verdiği file/directory not found tarzı hatalardan yararlanarak internete açık olan klasör ve dosyaları bulabiliriz. Bruteforce/Wordlist veya Hatalardan yararlanarak.
- builtwith.com kullanarak siteler hakkında bilgi edinebiliriz. Örneğin: SPF koruması olanların ip adresi bulunabilir.
- fierce scriptini kullanarak fierce -dns siteadi.com yaparak siteye ait subdomainleri bruteleyebilirsiniz.
