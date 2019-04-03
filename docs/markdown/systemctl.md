#Systemd

Alates aastast 2015 on de facto standardiks Linuxi kernelis muutunud tükk tarkvara nimega systemd. Systemd ehk systemctl 
asendab linuxi igivana käivitamisscripte nimega `init ja init.d`. Init.d scriptide puhul oli tegu kollektsiooni scriptidega, 
mille ülesandeks oli linuxi kerneli esimese protssessi `init` käivitamine. Järkjärgult suurendas init oma keelte taset ja tuge,
jõudes lõpuks piisava keerukuseni, et lugeda ka ext4 failisüsteemi ja käivitada kernel.

Ja ega aastal 2015 elu lihtsamaks ei läinud - "vanad head" init.d scriptid asendati metsikult suure systemd ehk 
systemctliga (ctl protsessi lõpus tähistab kontrollerit). Systemd suurimaks veaks on see, et ta üritab kõike korraga teha - midagi 
mis läheb linux disainifilosoofiaga vastuollu.

Sellegipoolest ei ole tänane eesmärg systemd poolt või vastu vaielda, vaid aru saada kuidas systemd kontrollib protsesse ja teenuseid.
Systemctli teenustepuud saame vaatada käsuga `systemctl status`, mille väljundis näeme kõiki süsteemi poolt käivitatavaid protsesse.
Konkreetse teenuse enda infot saame vaadata sama käsuga, andes talle argumendiks teenuse nime. 

```
[ubuntu@ubuntu:~$] systemctl status ntpd
ntpd.service - Network Time Service
   Loaded: loaded (/usr/lib/systemd/system/ntpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2019-04-03 21:28:37 EEST; 6min ago
  Process: 458 ExecStart=/usr/bin/ntpd -g -u ntp:ntp (code=exited, status=0/SUCCESS)
 Main PID: 464 (ntpd)
    Tasks: 2 (limit: 4915)
   Memory: 3.7M
   CGroup: /system.slice/ntpd.service
           └─464 /usr/bin/ntpd -g -u ntp:ntp
```

Peale teenuse oleku jälgimise toimivad systemctli puhul ka käsud `start, stop, restart, enable, disable`. Arvatavasti on aru saada kõigest peale kahe - selleks vaatame teenusefaile.

## Systemd teenusefailid

Vaadates ükskõik mis teenuse staatust näeme rida stiilis `Loaded: loaded (/usr/lib/systemd/system/ntpd.service; enabled; vendor preset: disabled)` - väljundis näeme nii seda, kus teenusefail asub ja mis ta praegune olek on (loaded, enabled). Loaded näitab, et teenus on hetkel konkreetselt püsti kuid enabled staatus ütleb, et teenus käivitatakse automaatselt ka süsteemi käivitamisega.

Teenuse lubamisel näeme, et teenusefailist tehakse lihtsalt link konkreetsesse kausta

```
[ubuntu@ubuntu:~$]sudo systemctl enable mariadb
Created symlink /etc/systemd/system/multi-user.target.wants/mariadb.service → /usr/lib/systemd/system/mariadb.service.
[ubuntu@ubuntu:~$]sudo systemctl disable mariadb
Removed /etc/systemd/system/multi-user.target.wants/mariadb.service.
``` 

Viimaks vaatame ka teenusefaili ennast, juhul kui on kunagi soovi panna tööle enda ehitatud tarkvara kerneli käivitamisega.
Teenusefaili sisu koosneb tavaliselt kolmest osast - teenuse kirjeldus, teenuse andmed ja paigalduse info.

```
[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
```

Nginxi teenusefailist näeme, et tegelikult suurt midagi peale `/usr/bin/nginx -c /etc/nginx/nginx.conf` käsu nginxi jooksutamiseks pole vaja.
Tegelikuses on systemd.service failid mõeldud olema ilus alternatiiv "autostart" kaustale.
