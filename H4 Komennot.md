## H4 Komennot

Tämän harjoituksen tarkoituksena on luoda omia bash komentoja sekä python komentoja ja jakaa ne orja koneille.

Tässä tehtävässä aion hyödyntää `git` komentoja. Koodin työstöön aion käyttää Windows host konetta `visual basic studio` ohjelmistoa josta committaan ne repoon mistä siirrän ne virtuaalikoneilleni. Hyödynnän ohjelman sisään rakenettua command prompita. Työskentelen myös paljon offline tilassa koska olen ulkomailla ja yhteydet ovat vaihtelevat.

### a) hello.sh. Tee oma shell script (bash, sh...) ja laita se kaikille käyttäjille. Yksinkertainen skripti voi tehdä mitä vain, mutta sen tulee tulostaa "shine". Tee skripti alusta, vaikka olisit joskus aiemmin tehnyt skriptejä. Testaa lopputulos (kuten aina).
Aloitan tehtävän luomalla uuden kansion repoon `Scripts`  jonne kerään kaikki tiedostoni.

Ensimmäisenä teen uuden bash ohjelman:

        #!/usr/bin/bash
        echo "Hello sunshine"

Committaan tämän ja siirryn virtuaalikoneelle

        PS C:\Users\Fredr\GIt\InfraAsCode> git pull
        remote: Enumerating objects: 5, done.
        remote: Counting objects: 100% (5/5), done.
        remote: Compressing objects: 100% (3/3), done.
        remote: Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
        Unpacking objects: 100% (3/3), 831 bytes | 59.00 KiB/s, done.
        From https://github.com/FredrikAkerlund/InfraAsCode
        8363af8..6e7ab62  main       -> origin/main
        Updating 8363af8..6e7ab62
        Fast-forward
        H3 Git gud.md | 2 ++ 1 file changed, 2 insertions(+)PS C:\Users\Fredr\GIt\InfraAsCode> git add .warning: in the working copy of 'Scripts/hello.sh', LF will be replaced by CRLF the next time Git touches itPS C:\Users\Fredr\GIt\InfraAsCode> ^CPS C:\Users\Fredr\GIt\InfraAsCode> git commit
        [main 0d5e0ab] add h4 assignment and folder with custom scripts
        2 files changed, 17 insertions(+)
        create mode 100644 H4 Komennot.md
        create mode 100644 Scripts/hello.sh
        PS C:\Users\Fredr\GIt\InfraAsCode> git push
        Enumerating objects: 6, done.
        Counting objects: 100% (6/6), done.
        Delta compression using up to 16 threads
        Compressing objects: 100% (3/3), done.
        Writing objects: 100% (5/5), 1.05 KiB | 1.05 MiB/s, done.
        Total 5 (delta 1), reused 0 (delta 0), pack-reused 0
        remote: Resolving deltas: 100% (1/1), completed with 1 local object.
        To https://github.com/FredrikAkerlund/InfraAsCode.git
          6e7ab62..0d5e0ab  main -> main

Cloonaan `H4 komennot` repon herrakoneen kotihakemistoon:

        vagrant@fmaster:~$ ls
        koodit  public_sites  summershine
        vagrant@fmaster:~$ git clone git@github.com:FredrikAkerlund/InfraAsCode.git

Kloonauksen meni noin 10 sec. SIirryn `Scripts` kansioon ja kokeilen ohjelmaa:

        vagrant@fmaster:~/InfraAsCode/Scripts$ bash hello.sh
        Hello sunshine

Voin totea että ohjelma toimii tässä kansiossa. Seuraavaksi siirrän tiedoston `/usr/local/bin` hakemistoon ja laitan sen kaikille käyttäjille käytettäväksi:

        vagrant@fmaster:~/InfraAsCode/Scripts$ sudo cp hello.sh /usr/local/bin/hello
        vagrant@fmaster:~/InfraAsCode/Scripts$ ls /usr/local/bin/
        greetme  hello

Testaan toimiiko komento kaikissa hakemistoissa

        vagrant@fmaster:~$ hello
        -bash: /usr/local/bin/hello: Permission denied

Eli käyttäjällä `vagrant` ei ole käyttöoikeuksia tiedostoon. Tarkastan oikeudet ja muutan tämän:

        vagrant@fmaster:/usr/local/bin$ ls -l
        total 8
        -rwxr-xr-x 1 root root 290 Apr 18 19:54 greetme
        -rw-r--r-- 1 root root  38 Apr 18 20:44 hello

Tämä rivi tarkoittaa seuraavaa: 
- eka `-` tarkoittaa että kyseessä on tiedosto. 
- Seuraavat kolme merkkiä tarkoittavat tiedoston omistajan oikeudet :Read and write
- Seuraavat kolme tarkoittavat ryhmän oikeuksia : Read - -
- VIimeiset kolme tarkoittavat muiden käyttäjien oikeuksia : Read - -
- Yhteenvetona: Root käyttäjällä on read write oikeudet, ryhmällä on read oikeudet, muilla on read oikeudet.

Jotta komennot toimivat kaikilla minun pitää lisätä execute oikeudet kaikille.

        vagrant@fmaster:/usr/local/bin$ sudo chmod ugo+x hello
        vagrant@fmaster:/usr/local/bin$ ls -l
        total 8
        -rwxr-xr-x 1 root root 290 Apr 18 19:54 greetme
        -rwxr-xr-x 1 root root  38 Apr 18 20:44 hello

Tiedoston väri muuttui myös. Kopioidessa värit eivät näy.

Kokeilen uudestaan komentoa kotihakemistossa:





b) hello.py. Tee oma Python-skripti ja laita se kaikille käyttäjille.
Aloitan luomalla .py tiedoston `scripts` kansioon:


c) Automatisoi näiden skriptien asennus orjille Saltilla.
d) Asenna jokin yhden binäärin ohjelma Saltilla orjille.
e) Vapaaehtoinen: moniOSsaaja. kokeile Saltia jollain muulla käyttöjärjestelmällä kuin Linuxilla.
