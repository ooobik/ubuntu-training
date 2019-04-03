#Teenuse ülesseadmine

Kiirelt käime läbi ka teenuse ülesseadmise - mõnikord on vaja seda testimise eesmärgil, mõnikord ka päris püsivat teenust muuta. Valime lihtsaks näiteks
nginx veebiteenuse.

Nginx on apache2 uuem sugulane - väiksem, kiirem ja lihtsam kasutada oskab teha kõike, mida apache 2 oskab. Nginxi
saab kasutada nii puhverserverina, reverse-proxy reziimis, kui ka staatiliste veebisaitide serveerimiseks. 


## Paigaldus

Nagu ubuntul ikka, saame oma tarkvara laadida `apt` käsuga, niisiis paigaldame paketi ´nginx´. Käsu väljundit näeme ka,
et nginxi paigaldamise käigus tehakse teenusele automaatseks kävitamiseks symlink - `systemctl enable nginx` käsk ei ole otseselt vajalik.

Nginxi peamine konfiguratsioonifail asub kaustas `/etc/nginx/nginx.conf`, kuid nginxi konfiguratsioon on kirjutatud modulaarselt.
Peamises konfifailis näeme kirjet `include /etc/nginx/sites-enabled/*` ja `include /etc/nginx/conf.d/*.conf`. Kui kasutame veebiserverit
mitme erineva veebilehe hoidmiseks, ei ole kasulik panna kõikide saitide konfid ühte faili. Selleks võtab nginx lisaconfi
kahest eelmainitud kaustast.

Teeme uue faili `sites-enabled` kausta, kus seadistame enda lihtsakoelise veebiteenuse, selleks teeme koopia vaikimisi kaasa tulevast näidisest. Enne kui koopiat muudame peame ka `default` saidi välja võtma.
Selleks eemaldame symlingi, mis sites-enabled kaustas asub. Kuna symlinkide kustutamine on ohtlik - võime ka kustutada faili enda,
siis kasutame lingi lahtihaakimiseks käsku `unlink <link>`

Nüüd seadistame ära oma lehe - default saidist on jäänud veebi juurikaks kaust `/var/www/html`, selleks määrame nüüd oma esimese kasutaja kodukaustas oleva kausta `www`

Kuna määrasime veebijuurikaks kodukaustas oleva kausta, siis me peame sellel ka õigused korrastama.

```
1. Loome enda kodukausta uue kausta www
2. www kausta sisse loome faili index.html, kuhu kirjutame tervituse
3. Nüüd muudame www kausta grupi ja seal sees olevate failide kausta grupiks sama grupi mille õiguste all nginx jookseb.
```

Kui sammud edukalt tehtud, saame virtuaalmasina brauseris avada lehe http://localhost ning näeme enda kirjutatud tervitust
