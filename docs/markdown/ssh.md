#Kaughaldus

Kõige populaarsem viis linuxi masinaid kaughallata on SSH - tegu on protokolliga, mis lubab üle võrgu 
avada terminalisessioone masinas. SSH puhul on autentimiseks mitmeid võimalusi, kuid kaks mis kasutust leiavad on parooliga logimine ja avaliku-salajane võtmepaari 
autentimine. Võtmega autentimine tuleb eraldi seadistada tavaliselt, aga pärast seadistamist on tegu palju võimekama ja
turvalisema autentimisviisiga, kui parooliautentimine. 

## SSH server - sshd

Tavakasutaja versioonid linuxist ei tule alati pärast paigaldamist ssh serveriga - eeldatakse, et ligipääs toimub kasutajal 
läbi monitori ja klaviatuuri. Sshd teenust täna seadistama ei hakka, kuid konfiguratsiooni piilume sisse küll. Paigaldame enda ubuntu virtuaalmasinatele paketi nimega `ssh` ja vaatame faili `/etc/ssh/sshd_config`

Tähtsamad lõigud mida konfiguratsioonist vaadata on:

```
Port 22
PermitRootLogin yes #Võiks olla no
PubkeyAuthentication yes
PasswordAuthentication yes #Samamoodi võiks olla no

# Example of overriding settings on a per-user basis
Match Address 172.17.36.0/23
    PasswordAuthentication yes
```

Järeldada tuleks, et ssh server ei ole vaikesätetega turvaline - parooliga logimine on lubatud, ka root kasutajal
lubatakse sisselogimisi. Pärast oma serveri masina ülespanekut, on tava ära muuta sshd konfiguratsioon, 
sätestada oma võtmed, keelata parooliga logimine.

## SSH harjutus

Kuigi sshd ei ole vaikimisi paigaldatud, on olemas ikkagi käsud ssh ühenduste avamiseks. Ssh ühenduse saab avada käsuga `ssh <kasutaja>@<masina ip>`.
Peale terminalisesioonide avamise suudab ssh ka nt. suunata porte ja graafilisi liideseid (Graafika suunamine on kohutavalt aeglane, soovitan RDP protokolli kasutada kui on vaja graafikat).

Ssh poolne harjutus jääb täna küllaltki lihtsaks - vaja on aru saada kuidas ühenduste loomine käib.

```
1. Kõigepealt avame ühenduse eelseadistatud masinasse - ip, kasutaja ja parool tulevad juhendajalt
    ssh <kasutaja>@<ip aadress>
2. Teises terminaliaknas genereerime endale avaliku-salajase võtmepaari
    ssh-keygen , väärtused jätame samaks
3. Lisame remote masinas endale kasutaja, seekord oleks hea genereerida kasutaja ilma paroolita!
    adduser <uuskasutaja>
4. Viimaks sätestame kasutajale võtmega autentimise. Selleks peame kopeerima enda masina avaliku võtme uue kasutaja alla,
spetsiaalsesse faili nimega authorized_keys. Vahetame uue kasutaja alla ja loome vastavad failid

    su - uuskasutaja
    mkdir ~/.ssh
    chmod 700 ~/.ssh
    touch ~/.ssh/authorized_keys
    chmod 700 ~/.ssh/authorized_keys

Nüüd avame authorized-keys faili tekstiredaktoriga ja kopeerima enda masina (seal kus genereerisime võtme) avaliku võtme sinna.
    (enda masinas) cat ~/.ssh/id_rsa.pub
    
```
