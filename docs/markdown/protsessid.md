#Protsessid ja deemonid

Protsesside tundmine linuxis on küllaltki hädavajalik - mida teha, kui soovid teenuse peatada hoolduseks? 
Kogemata sisestasid vale käsu ja tahad seda lõpetada? Avasid pika protsessi terminalis, aga soovid terminaliakent sulgeda ilma protsessi lõpetamata?

Igal lõimele (protsessile) antakse käivitamisel unikaalne number (process-id, PID), millega teda kontrollida saab. Ka protsessidest saab mõelda kui puust -
iga lõim võib luua endale alamlõimesi (child process). Lisaks jooksevad veel lõimed kindla kasutaja õigustes. 
Lõimepuu kõige tipus asub linuxi kernel ise - /sbin/init, PID=1, lõime alustajaks on kasutaja `root`. Kõik ülejäänud lõimed
on init'i alamlõimed ning süsteemi väljalülitamisel saadetakse PID 1 protsessile signaal SIGTERM(15).
Nii propageerib lõim enda alamlõimedele signaali edasi, kuni kõik lapslõimed on suletud ja init ise saab enda töö lõpetada. 

## Lõimevahelised signaalid

Protsesside kontrollimiseks kasutab linux signaalide süsteemi - osasi püüab protsess ise, osasi kernel. [Loe rohkem](https://www.tutorialspoint.com/unix/unix-signals-traps.htm)

Vaatame kõige tavalisemaid signaale

```
SIGINT	2	Kasutaja pool saadetud lõpetamise signaal (Ctrl + C)
SIGQUIT	3	Kasutaja poolt väljumise signaal, sessiooni lõpetamine (Ctrl + D)
SIGKILL	9	Kohene lõpetamine, protsess kustutatakse ilma, et ta saaks lõpetamistoiminguid teha
SIGTERM	15	Pehme lõpetamine, lubab protsessil veel viimased toimingud teha.
SIGCNT  18      Protsessi tegevuse jätkamine
SIGSTOP 19      Protsessi pausile panek - PID jääb alles, kuid CPU aega ei jagata. (Ctrl + Z)
```

Eelpoolnimetatud signaalidega saab teha kõige tähtsamat - lõimesi peatada. STOP ja CNT signaale kasutatakse kui
pole just vajadust lõpetada programmi tööd, vaid see hetkeks pausile panna. Tavaliselt seda kasutatakse kui soovitakse 
lahtine protsess terminalist lahti haakida. Ctrl + Z pausib, `bg` viib protsessi taustale, `disown` haagib protsessi lahti.

### Signaalide saatmine

Protsesside leidmiseks on mitmeid viise, levinuim on kasutada käsku `ps`, `ps -u` enda kasutaja protsesside jaoks, 
`ps -aux` kõikide protsessida jälgimiseks.
Teine võimalus on kasutada midagi Windowsi task manageri sarnast - `top` ja täiustatud versioon `htop`. Htop lubab samas aknas
sorteerida ja ka vajadused saata signaale protsessidele.

Kui PID on käes, saame protsessile ka signaale saata käsitsi - `kill -<signaal> <PID>`. Protsessi nimepõhiseks tapmiseks kasutatakse
ka käsku `killall`

## Deemonid/daemonid

Linuxi nimeruumis elavad mitmed rituaalse nimega elukad, ühtedeks nendest on deemonid. Daemoni mõte on tegelikult olla taustprotsess -
enamasti on tegu teenusega mis käivitatakse masina bootiga, ning mille tegevusse kasutaja ei sekku. Daemonite alla kuuluvad
näiteks `ǹscd, syslogd, sshd, dhcpcd, journald, systemd`. Daemoneid võib ka mingis mõttes nimetada teenusteks, kuigi tegelikult on
daemoni puhul tegu serveripoolse osaga tarkvarast.


