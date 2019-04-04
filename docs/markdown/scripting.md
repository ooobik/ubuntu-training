#Scriptimine

Iga hea süsteemiadministraatori ülesanne on oma tööülesandeid automatiseerida. Rusikareeglina võib öelda, et sama asja
üle kahe korra käsitsi on juba ebavajalik ja pigem võib ülesande automatiseerida. Peale järgmise korra lihtsustamise on 
automatiseerimisel ka üks suurepärane lisaboonus - dokumentatsioon. Suuremad scriptid millega teenuseid üles seatakse 
annavad aastaid hiljem aimu, mis samme oli vaja läbida peale ametliku dokumentatsiooni.

![](https://imgs.xkcd.com/comics/is_it_worth_the_time.png)

<sub><sup>Randall Munroe - XKCD</sup></sub>

Scriptide kirjutamisel on üheks tähtsamaks osaks keele valik - scriptid on mõeldud automatiseerima samme, mida 
inimene sisestaks ükshaaval. Seepärast on tavaliselt linuxi scriptide puhul ka tarvis, et scriptid saaks välja kutsuda 
süsteemseid programme ja binaare. Scriptimiskeelega bash olema me juba tuttavad, kuid nimekiri nendest on pikk. Peale 
kõikide shellipõhiste keelte kuuluvad scriptimiskeelte alla ka nt. Powershell, python, perl. Täna vaatame kahte linuxi
populaarsemat keelt - bash ja python. 

Eile rääkisime ka iga scripti kõige esimesest reast - shebang. Shebang on rida scriptis, mis näitab kernelile millise
interpeteeatoriga script käivitada.

```
#!/bin/bash
#!/bin/python2
#!/bin/env python3
#!/bin/env perl
```
Kui tekib kahtlus millis programmi viidata shebangis, saab selle terminalis alati üle kontrollida käsuga `which`

##Bash

Bashis scriptide kirjutamine on küllaltki lihtne - .sh faili kävitamisel loetakse kõik käsud sisse samamoodi, nagu neid
käsitsi bashi shelli kirjutada.

Vaatame kiiret näidet, kus lisame kasutaja ja lisame kasutajale ka automaatselt sudo grupi

```bash
#!/bin/bash

adduser --disabled-password --gecos "" $1
usermod -a -G sudo $1
```

Nüüd jooksutades scripti kas käsuga `sh kasutaja_lisamine.sh` või andes talle käivitamisõiguse ja siis kasutades `./` käivitamisviisi
näeme, et adduser viriseb kasutajanime puudumise pärast. Selle põhjuseks on muutuja `$1`, mis võtab meie scriptile
esimese antud argumendi väärtuseks. Kui me jätame argumendi tühjaks, siis adduserile kasutajat mida lisada ei anta. 

**Muuda scripti nii, et käivitamisel kontrollitakse esimese argumendi olemasolu**

```bash
#!/bin/bash

USER=$1

PASSWORD="$(openssl rand -base64 12)"

if [ -z $USER ]; then
        echo "Argument username required"
        exit 1
fi

sudo useradd -p $(openssl passwd -6 $PASSWORD) --create-home --shell=/bin/bash $USER
sudo usermod -a -G sudo $USER

echo "Lisasin kasutaja $USER parooliga $PASSWORD"                                 
```
Scriptide teine oluline osa halduses on sama asja itereerimine mitmete sisendite vahel. **Kirjutame scripti, mis võtab kõik meie lisatud kasutajad, ja lisab nad uude gruppi.**

##Python

Kuigi python olemuslikult ei ole päris scriptimiskeelena mõeldud, on ta piisavalt lihtne, kerge ja võimas, et linuxis
täita scriptimiskeele rolli. Kuigi süsteemsete binaaride jooksutamine on pythonis keerulisem, pakub python täieliku turning-complete keele rolli 
scriptimises. Bashi puudusteks võrreldes pythoniga on raamatukogu (dictionary) andmetüübi puudus, lugemise lihtsus,
teksti manipulatsioonivõimekus, ja üleüldine loogika. 

Pythonis saame süsteemseid binaare kutsuda lisateegiga `subprocess`.

```
#!/bin/env python3

import subprocess

subprocess.call(["adduser", "-g", "1001", "-m", "juku" ])

```

Pythoni võimekus scriptimises on ka peidus pythoni lisapaketis `shutil`, mille dokumentatsiooni leiab [siit](https://docs.python.org/2/library/shutil.html)

## Cron

Cron(d) on linuxi daemon mis on mõeldud perioodiliste scriptide jooksutamiseks - peamiseks kasutusalaks on tavaliselt failide puhastamine,
logrotate, backupid ja monitooring. Croni dokumentatsioon räägib pikemalt croni täisvõimekusest, kuid hetkel vaatame crontabi süntaksi 
ja croni konfiguratsioonide kausta ehitust. 

[pilt](https://linuxconfig.org/images/crontab.png)

Peame `/etc/crontab` faili oma erilise süntaksiga on cronil ka perioodiliseks käivitamiseks kaustad - kui vajutame `TAB` klahvi
kaks korda pooliku käsuga näeme järgmist väljundit
```
[root@testing etc]# cd /etc/cron
cron.d/       cron.daily/      cron.hourly/  cron.monthly/ crontab       cron.weekly/  
```

Kui oleme laisad, või meil ei ole vaja konkreetselt kellaajalist käivitamist piisab ka intervallide kausta scripti lisamisest.
Croni scriptide puhul peab alati veenduma, et kasutaja kelle õigustes neid käivitatakse saab failidele ligi ning et tal oleks olemas käivitamisbitt.

