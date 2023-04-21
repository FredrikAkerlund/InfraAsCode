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

![image](https://user-images.githubusercontent.com/122887178/233614848-281b4e71-88ec-4d27-9c94-8cf79f932b8f.png)


Ja sitten ajetaan: 

        
        vagrant@fmaster:/srv/salt/scripts$ sudo salt 'f001' state.apply "scripts"
        f001:
        ----------
                ID: /usr/local/bin/greetme
            Function: file.managed
            Result: True
            Comment: File /usr/local/bin/greetme is in the correct state
            Started: 21:55:17.122359
            Duration: 28.486 ms
            Changes:
        ----------
                ID: /usr/local/bin/hello
            Function: file.managed
            Result: True
            Comment: File /usr/local/bin/hello updated
            Started: 21:55:17.151015
            Duration: 22.337 ms
            Changes:
                    ----------
                    diff:
                        New file
                    mode:
                        0755
        ----------
                ID: /usr/local/bin/hellopython
            Function: file.managed
            Result: True
            Comment: File /usr/local/bin/hellopython updated
            Started: 21:55:17.173431
            Duration: 20.762 ms
            Changes:
                    ----------
                    diff:
                        New file
                    mode:
                        0755

        Summary for f001
        ------------
        Succeeded: 3 (changed=2)
        Failed:    0
        ------------
        Total states run:     3
        Total run time:  71.585 ms
        vagrant@fmaster:/srv/salt/scripts$

Ja sitten kokeillaan:

        vagrant@fmaster:/srv/salt/scripts$ sudo salt '*' cmd.run "hello hellopython"
        f002:
            /bin/sh: 1: hello: not found
        f001:
             Hello sunshine

Bash komento toimi hyvin. Mites sitten python ohjelma:

        vagrant@fmaster:/srv/salt/scripts$ sudo salt '*' cmd.run "hellopython"
        f001:
            /bin/sh: 1: hellopython: not found
        f002:
            /bin/sh: 1: hellopython: not found

Ei näköjään löydy. Tarkastetaan `f001` `/usr/local/bin` sisältö:

        vagrant@fmaster:/srv/salt/scripts$ sudo salt '*' cmd.run "ls /usr/local/bin"
        f002:
        f001:
            greetme
            hello
            hellopython

On se siellä. Mitä ihmettä? Katsotaan myös `hellopython` tiedoston sisältö:

        vagrant@fmaster:/srv/salt/scripts$ sudo salt '*' cmd.run "cat /usr/local/bin/hellopython"
        f002:
            cat: /usr/local/bin/hellopython: No such file or directory
        f001:
            #!/usr/local/python3
            print("Hello in python")
        ERROR: Minions returned with non-zero exit code
        vagrant@fmaster:/srv/salt/scripts$ sudo salt '*' cmd.run "which python3"
        f001:
            /usr/bin/python3
        f002:
            /usr/bin/python3

Eli ongelma on siinä että #! notaatio on väärin. Korjaan tämän herrakoneella jotta virhe ei toistu:
Minulla on väärä python3 lokaatio. Virheellisesti kirjoitin `#!/usr/local/python3` python ohjelmaan. Sen pitäisi olla `#!/usr/bin/python3`. Korjaan tämän ja kokeilen uudestaan `state.apply`

        vagrant@fmaster:/srv/salt/scripts$ sudo salt 'f001' state.apply "scripts"
        f001:

            
                ID: /usr/local/bin/hellopython
            Function: file.managed
            Result: True
            Comment: File /usr/local/bin/hellopython updated
            Started: 22:03:05.795453
            Duration: 55.237 ms
            Changes:
                    ----------
                    diff:
                        ---
                        +++
                        @@ -1,2 +1,2 @@
                        -#!/usr/local/python3
                        -print("Hello in python")+#!/usr/bin/python3
                        +print("Hello in python")

        Summary for f001
        ------------
        Succeeded: 3 (changed=1)
        Failed:    0
        ------------
        Total states run:     3
        Total run time: 109.927 ms
Muutokset tapahtui. Kokeillaan `cmd.run`:

        vagrant@fmaster:/srv/salt/scripts$ sudo salt 'f001' cmd.run "hellopython"
        f001:
            Hello in python
Mahtavaa se toimi!!

### d) Asenna jokin yhden binäärin ohjelma Saltilla orjille.
Asennan micron kaikille orjille: 

Aloitan löytämällä binääri tiedoston microlle: 
Löydän tar kansion micron viralliselta github reposta:  https://github.com/zyedidia/micro/releases/tag/v2.0.11
lataan micro-2.0.11-linux64-static.tar.gz tiedoston local koneelle ja lisään sen omaan repoon: 

Seuraavaksi unzippaan sen ja lisään ohjelman `/srv/salt/scripts` hakemistoon
        
        vagrant@fmaster:~/InfraAsCode/micro$ ls
        micro-2.0.11-linux64-static.tar.gz
        vagrant@fmaster:~/InfraAsCode/micro$ tar -xf micro-2.0.11-linux64-static.tar.gz
        vagrant@fmaster:~/InfraAsCode/micro$ ls
        micro-2.0.11  micro-2.0.11-linux64-static.tar.gz
        vagrant@fmaster:~/InfraAsCode/micro/micro-2.0.11$ ls
        LICENSE  LICENSE-THIRD-PARTY  README.md  micro  micro.1  micro.desktop  micro.svg

Tiedosto nimeltä micro on binääri tiedosto. Sen lisään /srv/salt/scripts kansioon:

Sitten teen tarvittavat muutokset `init.sls` tiedostoon:

        



        



