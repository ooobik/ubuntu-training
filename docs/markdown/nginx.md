#Teenuse ülesseadmine

Kiirelt käime läbi ka teenuse ülesseadmise - mõnikord on vaja seda testimise eesmärgil, mõnikord ka päris püsivat teenust muuta. Valime lihtsaks näiteks
nginx veebiteenuse.

Nginx on apache2 uuem sugulane - väiksem, kiirem ja lihtsam kasutada oskab teha kõike, mida apache 2 oskab. Nginxi
saab kasutada nii puhverserverina, reverse-proxy reziimis, kui ka staatiliste veebisaitide serveerimiseks. 


## Paigaldus

Nagu ubuntul ikka, saame oma tarkvara laadida `apt` käsuga, niisiis paigaldame paketi ´nginx´. Käsu väljundit näeme ka,
et nginxi paigaldamise käigus tehakse teenusele automaatseks kävitamiseks symlink - `systemctl enable nginx` käsk ei ole otseselt vajalik.

Nginxi  
