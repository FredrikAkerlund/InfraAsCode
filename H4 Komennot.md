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

        vagrant@fmaster:~$ pwd
        /home/vagrant
        vagrant@fmaster:~$ hello
        Hello sunshine




### b) hello.py. Tee oma Python-skripti ja laita se kaikille käyttäjille.
Aloitan luomalla hellopython.py tiedoston `scripts` kansioon host koneella

Alla on kopio tiedostosta

        #!/usr/bin/python3
        print("Hello in python")

#!/usr/local/python3 sain komenolla `which python` vagrant master koneelta.

Committaan tässä vaiheessa muutokseni repoon ja pullaan ne master koneella.

        PS C:\Users\Fredr\GIt\InfraAsCode> git add .
        warning: in the working copy of 'Scripts/hello.sh', LF will be replaced by CRLF the next time Git touches it
        PS C:\Users\Fredr\GIt\InfraAsCode> git commit -
        error: pathspec '-' did not match any file(s) known to git
        PS C:\Users\Fredr\GIt\InfraAsCode> git commit
        [main c7e83c5] Add hellopython script and some changes to the H4.md file
        3 files changed, 89 insertions(+), 1 deletion(-)
        create mode 100644 Scripts/hellopython.py
        PS C:\Users\Fredr\GIt\InfraAsCode> git push
Siirryn master koneelle ja pullaan tiedostot:

        vagrant@fmaster:~/InfraAsCode$ git pull
        remote: Enumerating objects: 13, done.
        remote: Counting objects: 100% (13/13), done.
        remote: Compressing objects: 100% (4/4), done.
        remote: Total 9 (delta 3), reused 9 (delta 3), pack-reused 0
        Unpacking objects: 100% (9/9), 3.04 KiB | 1.52 MiB/s, done.
        From github.com:FredrikAkerlund/InfraAsCode
        0d5e0ab..a5de570  main       -> origin/main
        Updating 0d5e0ab..a5de570
        Fast-forward
        H4 Komennot.md         | 112 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++-
        Scripts/hello.sh       |   2 +-
        Scripts/hellopython.py |   2 ++
        3 files changed, 114 insertions(+), 2 deletions(-)
        create mode 100644 Scripts/hellopython.py

Kokeilen toimiiko python ohjelma virtuaalikoneella: 

        vagrant@fmaster:~/InfraAsCode/Scripts$ python3 hellopython.py
        Hello in python

Se toimii sitten teen saman asian minkä tein bash scriptillä:
Siirrän tiedoston `/usr/local/bin` hakemistoon ja muutan oikeudet siten että kaikilla on R ja X oikeudet

        vagrant@fmaster:~/InfraAsCode/Scripts$ sudo cp hellopython.py /usr/local/bin/hellopython
        vagrant@fmaster:~/InfraAsCode/Scripts$ ls -l /usr/local/bin/
        total 12
        -rwxr-xr-x 1 root root 290 Apr 18 19:54 greetme
        -rwxr-xr-x 1 root root  39 Apr 21 13:01 hello
        -rw-r--r-- 1 root root  45 Apr 21 13:12 hellopython
        vagrant@fmaster:~/InfraAsCode/Scripts$ sudo chmod ugo+rx /usr/local/bin/hellopython
Kokeilen toimiiko komento kotihakemistossa: 

        vagrant@fmaster:~/InfraAsCode$ hellopython
        -bash: /usr/local/bin/hellopython: /usr/local/python3: bad interpreter: No such file or directory

Eli minulla on väärä Shebang osoite. Python3 ei siis sijtaitse täällä:
Tarkastan missä se sijaitsee:

        vagrant@fmaster:~/InfraAsCode$ which python3
        /usr/bin/python3¨

Muokkaan python ohjelmistoa:

        #!/usr/bin/python3
        print("Hello in python")

Ja kokeilen uudestaan: 

        vagrant@fmaster:~$ hellopython
        Hello in python   

Homma rockkaa!!


### c) Automatisoi näiden skriptien asennus orjille Saltilla.

Seuraavaksi automatisoin ohjelmien asennuksen orjille.

Aloitan luomalla hakemistoon `/srv/salt/` hakemistoon uuden hakemiston mihin kerään kaikki scriptit. Nimeän sen `scripts`:

Kansioon lisään reposta pullatut tiedostot: `hello` sekä `hellopython`:
        
        vagrant@fmaster:~/InfraAsCode/Scripts$ ls
        hello.sh  hellopython.py    
        vagrant@fmaster:~/InfraAsCode/Scripts$ sudo cp hello.sh hellopython.py /srv/salt/scripts/

Poistan myös ohjelmista .py ja .sh päätteet:

        vagrant@fmaster:/srv/salt/scripts$ sudo mv hello.sh hello
        vagrant@fmaster:/srv/salt/scripts$ sudo mv hellopython.py hellopython
        vagrant@fmaster:/srv/salt/scripts$ ls
        greetme  hello  hellopython  init.sls
        vagrant@fmaster:/srv/salt/scripts$

Näen omalla koneella että hello ja hellopython on eri värillä kuin greetmee. Tämä johtuu että hello ja hellopython tiedostoilla on eri oikeudet: Käyttäjillä ei ole execute oikeuksia. Korjaan tämän

        vagrant@fmaster:/srv/salt/scripts$ sudo chmod ugo+x hello hellopython
        vagrant@fmaster:/srv/salt/scripts$ ls -l
        total 16
        -rwxr-xr-x 1 root root 338 Apr 18 20:22 greetme
        -rwxr-xr-x 1 root root  37 Apr 21 13:23 hello
        -rwxr-xr-x 1 root root  45 Apr 21 13:23 hellopython
        -rw-r--r-- 1 root root 104 Apr 18 20:19 init.sls

En ole varma onko tämä vaadittu. Mutta tein kuitenkin.

Seuraavaksi luon `init.sls` tiedoston: 

       




d) Asenna jokin yhden binäärin ohjelma Saltilla orjille.
e) Vapaaehtoinen: moniOSsaaja. kokeile Saltia jollain muulla käyttöjärjestelmällä kuin Linuxilla.
