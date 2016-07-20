# Gün 4
- Framework ve CMS versiyonu izle
- Crawling yap ve sitedeki urlleri takip et. Login varsa giriş yapıp daha fazla dizine erişim sağla.
- Dirbuster ile ara.

## Teknik Açıklar, Mantıksal Açıklar, Bilinen Açıklar
Arama yöntemleri; Manuel, Otomatize ve Hibrid yöntem 

Mutlaka şüpheci bakın. ID ise rakam alıyor diye düşünmeyin.
Test ettiğiniz kurum büyük olabilir. Kurumu gözünüzde büyütmeyin. Motive olun. (Garanti Bankası yıllardır Pentest desteği alıyor. Burada açık yok diye bakarsanız, bulamazsınız.)

Güvenlik bakış açısı:
Önlem -> Denetim -> Hack
İlk önce uygulama geliştirme ve yayın sırasında önlemlerinizi alıp, daha sonra bu sistemi birden fazla kez denetimden geçirmeniz gerekir.

Denetim ve Hack'i ayıran tek şey birinin iyi niyetli olmasıdır. İkiside saldırıcı gözünden bakar. Denetimde zaman kısıtlıdır, Hack konusunda zaman sıkıntısı yoktur. Hackerların sayısı bilinmez ve zaman kısıtlarının olmadığı varsayılır(infinite resources, bitch). Bu yüzden iyi bir denetim yapılması lazım.

## Penetration Life Cycle
- Bilgi topla -> Bilgi toplamayı ne kadar başarılı yaparsanız, o kadar iyi bir saldırı yaparsanız. Atak vektörünüzü arttırır.
- Ağ haritasını çıkar -> Hangi modül var, hangi uygulamalar çalışıyor, hangi cihazlar var vs.
- Zayıflık tarama süreci -> Genelde otamitize araçlar yapılır. Mesela nessus ile bir subneti tarayıp, açık portlardaki servisleri öğrendikten sonra localde benzer bir sistem kurup sızma testi yapabilir veya online açıklar ararsınız.
- Exploit, Erişim, Hak Yükselt -> Topladığın bilgiler ile açıklar ara.
- Raporlama, Post - Exploitation -> Sunucudan veri çek, zarar ver, loglarını sil 

Mantıksal açık bulmak için uygulamayı iyi tanımak lazım. (Örneğin: -50 + 50 fiyatlarında, 2 gömlek alıp bedava 2 gömlek almak. source, dest apilerde src kendiniz iken başkasını girmek vs. Mobil uygulamada gelen sms yerine yanlış bir değer gönderin, dönen response'u yakala success:true|false yerine true yapın, giriş yapın.)

### Bilinen açıklar
- Sriptin kendisinde, modüllerinde açık olabilir.	
- DC üzerinde Local Admin oluşturarak persistance oluşturabilirsiniz windows sunucularda. Domain admin olursanız durumdurum çakılabilir.
- Bug bounty mail listesini takip ederek farklı bakış açıkları elde edebilirsiniz.

## Teknik Hatalar
- GET, POST'a göre biraz güvensiz sayılır. Çünkü Apache loglarında kalır, tarayıcı geçmişinde kalır. (Apache loglarına kod ekelenerek execute, etme denenebilir.)
- CONNECT methodu açıksa, sunucu PROXY olarak kullanılabilir.
- OPTIONS izinli olan tüm istekleri listeler. PUT vs. gibi methodlar ile sunucuya bir şeyler upload etmeyi deneyebiliriz.

### Senaryo
- IP bloklarını çıkar
- Sub ve diğer domainlere bak
- Whois'den domain kilitlenmişse önceki bilgilerden yararlanırız.
- archive.org bakarız, alt dizinleri bulabiliriz
- cname, dns sonucuları	
- google dorks (intitle: index of vs.)
	
## Tools
`nmap` random x ip üret, x portunu şu script ile dene falan diyebilirsin. -t5 hızlandırır.
```
wfuzz # web testing için güzel bir programdır. lfi, xss gibi açıklar bulabilir. -c = color, -z type,parameters,encoders
# Örnek: wfuzz -c -z file,worldist.txt,md5 -d "pass=x" sss.com
```
`crunch` kullanmayı öğren. password generation için iyi
`nitto` website yorumlama için güzel bir tool
`burb` interceptor ve proxy görevi görüyo. gezdiğiniz yerlerdeki geçmiş htmlleri incelemek, fuzzing yaparak brute force deneme vb. gibi şeyler yapabiliyor. Geçmiş analizinde oldukca güçlü bir tool. Örneğin yine geçmişte gezdiğiniz sitelerin verisini kullanarak bu site üzerinde ne kadar dinamik url var, ne kadar statik url ve ne kadar parametre var tarzında veriler veriyor.
`cewl` belli bir siteyi crawl ederek, site içindeki kelimelerden wordlist oluşturur.

### Senaryo
- phpmyadmin şifresini bildiğin sunucuya gir
- outfile ile shell at ve komut çalışılabilir duruma gel. (bu durumda C:\xampp\htdocs idi)
- shell ile payload gönder veya user oluşturup rdesktop ile bağlan

### Senaryo
- login yapılınca guest session değişmiyorsa,
- xss açığı olan bir yerde kullanıcı sayfanıza girdiği zaman siteye bir xss request atarak sessionlarını çalıp, adam login olana kadar bekleyebilirsin

## Diğerleri...
- lfi ile loglara apache veya başka loglara php kod düşmesini sebep olup, dosyayı lfi ile okutarak php kod çalıştırabilirsiniz.
- ....// ./ silindiği zaman ../ oluyor lfi için işe yarayabilir.

OVH
ocunetix ocuforum

xss denenicek eyler:
double encode
html encode

Bruce Schiener: "Engelleyemezsin, sadece farkında olup, karşılık verirsin."
CRT -> Cyber Response Team
Yaptıkları tek şey network farkındalığı.
Siber güvenlik ikiye ayrılır Savunma ve Saldırı, saldıraya verilen önemin artması ve türkiyedeki insanların savunma konularında daha bilinçli olmaları lazım.
netflow, 360 derece alan hakimiyeti, big data, ipfix
dpi, honeypot
cert.org'u inceleyip bu konular hakkında bilgi edin. bu konular siber savunma konusunda uç noktalar.
network telescope araştır.
tap yap ve incele. aslında bir ay içinde bilgisayarına ne kadar çok trafik geldiğini görüceksin. 
secops -> dev ops'un security versiyonu 
