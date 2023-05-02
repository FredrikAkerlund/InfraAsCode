### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Huomaa, että osa artikkeleista on luonteeltaan raportteja tai muistiinpanoja, eli vähemmän valmiita kuin testatut ja siivotut ohjeet. Keskity kohtiin, joita voisit itse kokeilla tai hyödyntää)Karvinen 2018: Control Windows with Salt. Itse valitsemasi opiskelijan raportti Saltin käytöstä Windowsilla. Löytyy esimerkiksi Googlella "salt windows karvinen"

- Artikelissa: `URL TÄHÄN` Tero Karvinen kertoo ja näyttää miten saltin avulla voidaan komentaa Windows orjia.
- Salt voidaan ajaa paikallisesti `salt-call --local` komenolla jolla voidaan testata Salt toimintoja.
- Tämän harjoituksen tehtävät on totetutettu tällä tavoin rajoittuneen internetin saatavuuden takia (Lentokoneessa)
- Koska Windowsissa ei ole järkevää paketin hallintaa Salttia käyttäessä joutuu olemaan hieman luova.
- Windowsissa on kyllä vajavaisia paketinhallinta ohjelmia (wget, chocolatey)
- Saltti hyödyntää windowsin powershell komentoja. Sen avulla herra voi käskyttää orjalle tiettyjä toimintoja.
- 

### a) Asenna Salt Windowsille
Aloitan lataamalla Salt ohjelmiston. Lataan `Salt-Minion-3006.0-Py3-AMD64-Setup` tiedoston ja ajan sen `run as adminstrator` paikallisella koneella.


Aloitan tämän tehtävän työston lentokoneessa joten apuja ei ole hirveästi saatavilla. 

Siirryn windows powershellillä kansioon mihin asensin `salt` ohjelmiston ja kokeilen toimiiko `salt-call --local test.ping`. Tällä tavoin saan ainakin selville asentuiko Salt paikalliselle koneelle.

        PS C:\Program Files\Salt Project\Salt> pwd

        Path
        ----
        C:\Program Files\Salt Project\Salt


        PS C:\Program Files\Salt Project\Salt> ./salt-call --local test.ping
        local:
            True

Voin ainakin totea että salt toimii paikallisesti.

Salt asentui ja pystyn antamaan yksinkertaisia komentoja paikallisesti.

        PS C:\Program Files\Salt Project\Salt> ./salt-call --local cmd.run 'whoami'
        local:
           laptop-rn8g6q1t\fredr


### b) Ei voi kalastaa. Käytä Windowsilla Salttia paikallisesti ilman verkkoa (Ruma-X, powershell as admin, salt-call --local state.single ...)

Aikaisemmassa tehtävässä kokeilin että toimiiko salt paikallisesti ja toimi se.

Minulla on ladattu micron binääri tiedosto ja haluan asentaa sen hyödyntämällä `salt-call --local` komentoa.

ALoitan poistamalla binääri tiedoston mikä minulla oli jo tallenettu C:\\windows\system32 kansioon.

Seuraavaksi yritän selvittää minne luon `init.sls` tiedoston. Eli mikä on windowsilla vastaava kuin `srv/salt` linuxilla.

Tässä vaiheessa hyödynnän toisten opiskelijoiden raportteja: Tuomas Valkamon raportti miten salt käytetään windowsilla.

Teen kansioon `C:\Program Files\Salt Project\Salt` uuden kansion missä on sisällä `init.sls` tiedosto

Törmään kuitenkin nopeasti ongelmiin. Koska kansio on järjestelmä valvojan alaisuudessa en pysty kätevästi luomaan tiedostoja sinne.

Päätän luoda omaan Git repoon kansion joka on syncattu paikallisen koneen kanssa: `C:\Users\Fredr\GIt\InfraAsCode\saltforwindows>`

Kansioon luon `init.sls` tiedoston: 

        whoami:
          cmd.run:
            - 'whoami'
Sitten yritän ajaa sen paikallisesti:

                PS C:\Program Files\Salt Project\Salt> ./salt-call --file-root=C:\Users\Fredr\GIt\InfraAsCode\saltforwindows --local state.apply 'koodit'
                local:
                Data failed to compile:
                ----------
                Too many functions declared in state 'cmd' in SLS 'koodit'
                PS C:\Program Files\Salt Project\Salt>
Tämä virhe ainakin kertoo että salt ainakin löytää init.sls tiedoston. Epäilen että `inits.sls` tiedostossani on jotain vikaa.

Teen pieniä muutoksia `init.sls` tiedostoon:

                whoami:
                  cmd.run:
                    - name: "whoami"

Lopputulos: 

                PS C:\Program Files\Salt Project\Salt> ./salt-call --file-root=C:\Users\Fredr\GIt\InfraAsCode\saltforwindows --local state.apply 'koodit'
                local:
                ----------
                        ID: whoami
                Function: cmd.run
                Result: True
                Comment: Command "whoami" run
                Started: 13:55:59.366813
                Duration: 47.094 ms
                Changes:
                        ----------
                        pid:
                                16284
                        retcode:
                                0
                        stderr:
                        stdout:
                                laptop-rn8g6q1t\fredr

                Summary for local
                ------------
                Succeeded: 1 (changed=1)
                Failed:    0
                ------------
                Total states run:     1
                Total run time:  47.094 ms
Tämä toimi ongelmitta.

Huomasin tässä harjoitteessa että salt toimii winkkarilla samalla tavalla kuin linuxilla mutta pitää vain muistaa käyttää oikeita tiedostopolkuja ja osaa käyttää windowsille ominaisia komentoja.



### c) Hei ikkuna! Tee hei maailma Windowsin Saltille. Voit vaikkapa tehdä tyhjän tiedoston johonkin väliaikaistiedostojen kansioon. Käytä idempotentteja komentoja, esim file.managed.

Tavoitteena on luoda texti tiedosto kaikkien käyttäjien työpäydälle. Eli tarkoituksena on luoda `Winkkaritrutussa.txt` tiedosto käyttäjien työpäydälle.

Aloitan muokkaamalla äsken luomaani `koodit` kansiota.

Lisään kansioon `Winkkaritrutussa.txt` tiedoston:

                PS C:\Users\Fredr\GIt\InfraAsCode\saltforwindows\koodit> ls


                Directory: C:\Users\Fredr\GIt\InfraAsCode\saltforwindows\koodit


                Mode                 LastWriteTime         Length Name
                ----                 -------------         ------ ----
                -a----         4/30/2023   1:55 PM             41 init.sls
                -a----         4/30/2023   2:04 PM             61 winkkaritrutussa.txt

Seuraavaksi muokkaan `init.sls` tiedoston siten että se luo idempotenssin ja kopioi tiedoston käyttäjien työpöydälle:

        C:\Users\Public\Desktop\Winkkaritrutussa.txt
          file.managed:
            - source: "C:\Users\Fredr\GIt\InfraAsCode\saltforwindows\koodit\Winkkaritrutussa.txt"

Kokeilen ajaa tilan koneelle. Katsotaan miten käy.

                C:\Users\Public\Desktop\Winkkaritrutussa.txt
                file.managed:    <======================
                - source: "C:\Users\Fredr\GIt\InfraAsCode\saltforwindows\koodit\Winkkaritrutussa.txt"
                ---
                local:
                Data failed to compile:
                ----------
                Rendering SLS 'base:koodit' failed: mapping values are not allowed here; line 2

                ---
                C:\Users\Public\Desktop\Winkkaritrutussa.txt
                file.managed:    <======================
                - source: "C:\Users\Fredr\GIt\InfraAsCode\saltforwindows\koodit\Winkkaritrutussa.txt"
                ---
                PS C:\Program Files\Salt Project\Salt>
Virhe voi johtua siitä koska kaikkien käyttäjien työpöydälle luominen vaati admin oikeuksia. Kokeilen muuttaa tilaa siten että se vain kopioi tiedoston käyttäjän työpöydälle:

        #Vanha polku
        C:\Users\Public\Desktop\Winkkaritrutussa.txt
        #Uusi polku
        c:\Users\fredr\desktop\Winkkaritrutussa.txt
                file.managed:
                - source: "C:\Users\Fredr\GIt\InfraAsCode\saltforwindows\koodit\Winkkaritrutussa.txt"

Sain kuitenkin saman virheilmoituksen.

Kokeilen lisätä tiedoston `tmp` kansioon:
Sama virhe koodi.

Teen hieman kansio tutkiskelua ja totean että tätä kansiota ei ole edes olemassa. Eikä myöskään Yllä mainittuja kansioita:

Teen vielä lisää muokkauksia init.sls tiedostoon:

                C:\Users\Fredr\OneDrive\Desktop\Winkkaritrutussa.txt:
                file.managed
                ## Tämä rivi ei tunnu toimivan   - source: "C:\Users\Fredr\GIt\InfraAsCode\saltforwindows\koodit\Winkkaritrutussa.txt"
Tässä init.sls tiedostossa yritän luoda tyhjän teksti tiedoston minun käyttäjän "onedrive\desktop" kansioon. Raivostuttava tämä onedrive. Pitääpi joku päivä poistaa se.

Kokeillaan:

                PS C:\Program Files\Salt Project\Salt> ./salt-call --file-root=C:\Users\Fredr\GIt\InfraAsCode\saltforwindows --local state.apply 'koodit'
                [WARNING ] State for file: C:\Users\Fredr\OneDrive\Desktop\Winkkaritrutussa.txt - Neither 'source' nor 'contents' nor 'contents_pillar' nor 'contents_grains' was defined, yet 'replace' was set to 'True'. As there is no source to replace the file with, 'replace' has been set to 'False' to avoid reading the file unnecessarily.
                local:
                ----------
                        ID: C:\Users\Fredr\OneDrive\Desktop\Winkkaritrutussa.txt
                Function: file.managed
                Result: True
                Comment: Empty file
                Started: 14:30:59.189047
                Duration: 22.642 ms
                Changes:
                        ----------
                        new:
                                file C:\Users\Fredr\OneDrive\Desktop\Winkkaritrutussa.txt created

                Summary for local
                ------------
                Succeeded: 1 (changed=1)
                Failed:    0
                ------------
                Total states run:     1
                Total run time:  22.642 ms

Tämähän näyttää toimivan. Ensimmäiseksi varmistan idempotenssin ajamalla tilan uudestaan:

                 ID: C:\Users\Fredr\OneDrive\Desktop\Winkkaritrutussa.txt
                Function: file.managed
                Result: True
                Comment: File C:\Users\Fredr\OneDrive\Desktop\Winkkaritrutussa.txt exists with proper permissions. No changes made.
                Started: 14:32:09.316542
                Duration: 23.246 ms
                Changes:

                Summary for local
                ------------
                Succeeded: 1
                Failed:    0
                ------------
                Total states run:     1
                Total run time:  23.246 ms
TOimii. Siiten katson että löytyykö tyhjä tiedosto työpöydältä:

                   Directory: C:\Users\Fredr\OneDrive\Desktop


                Mode                 LastWriteTime         Length Name
                ----                 -------------         ------ ----
                da---l         4/25/2023   6:49 PM                Asennusmediat
                -a----         4/30/2023   2:30 PM              0 Winkkaritrutussa.txt

Löytyhän se sieltä. 

Pienen ajatustyön jälkeen totean etten tiedä mikä on windowsilla linuxin `srv://` vastaava polku. Joten tämä vaihe jäi tähän. 
Ratkaisu: `srv://` = hakemisto missä salt palvelu on asenettu. Minun tapauksessa. `C:\Program Files\Salt Project\Salt\koodit` ja tämä aiheuttaa ongelmia koska kansio on järjestelmävalvojan hallinoima enkä pysty muokkaamaan tiedostojani.

Myös jäi mietityttämään miten voin suorittaa windowsilla `run as adminstrator` salt komentoja. Olisin halunnut lisätä tiedostoja kaikkien käyttäjien työpöydälle mutta salt antoi virhekoodin siinä vaiheessa. Vaikka ajoin powershelliä järjestelmänvalvojana.


### d) Installed. Asenna Windowsille ohjelma Saltilla. (Voit käyttää eri vaihtoehtoja: kopioida binäärin suoraan sopivaan kansioon, pkg.installed ja choco, pkg.installed ja salt winrepo).

Viimeisen tehtävän aikana olin internet yhteyksien äärellä joten rakennan Herra-orja arkitehtuurin hyödyntämällä windows virtuaalikonetta.

Aloitan asentamalla Windows virtuaalikoneen. OVA tiedoston sain: https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/. Lähde: https://terokarvinen.com/2023/palvelinten-hallinta-2023-kevat/#h5-vaihtoehdot

Tiedosto on melko suuri 22Gt. Puran tiedoston ja asennan sen Virtual boxilla.

Käytän aiemissa tehtävissä luotua `fmaster` debian virtuaalikonetta herra koneena.

Oracle VirtualBoxissa valitsin valikosta `file` "import appliance" ja valitsin .ova tiedoston. 

Import vaihe kesti noin 20min.

Kun tämä vaihe on valmis minulla on virtuaali windows-kone.

![image](https://user-images.githubusercontent.com/122887178/235628328-70398b10-f9fd-4c38-8b54-04a1c00402a1.png)

Lataan virtuaalikoneelle salt minion ohjelman. Dokumentaatiosta ja muiden opiskelijoiden raportista luin että salt ohjelma pitää olla samaa versiota mitä herran koneen salt versio on. 

Tarkastan `fmaster` koneen salt version:

                vagrant@fmaster:~$ salt --version
                salt 3002.6

Windows virtuaalikoneelle lataan kyseisen version saltista. Lähde: (https://archive.repo.saltproject.io/windows/Salt-Minion-3002.6-1-Py3-AMD64.msi)



![image](https://user-images.githubusercontent.com/122887178/235643479-fe4144fb-e22a-4da8-8096-2f57733c1fae.png)

Seuraavassa vaiheessa tarkistan herra koneen ip osoitteen: 

                3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
                    link/ether 08:00:27:bc:34:16 brd ff:ff:ff:ff:ff:ff
                    altname enp0s8
                    inet 192.168.12.3/24 brd 192.168.12.255 scope global eth1
                       valid_lft forever preferred_lft forever
                    inet6 fe80::a00:27ff:febc:3416/64 scope link
                       valid_lft forever preferred_lft forever
![image](https://user-images.githubusercontent.com/122887178/235632901-31b7d55f-f106-46c9-ace5-45d40590ae81.png)

Ja lisään sen salt ohjelman konfiguraatioon.

Tarkastan myös windows virtuaalikoneen verkkoasetukset: 

![image](https://user-images.githubusercontent.com/122887178/235640145-2d6a9223-49e9-4edd-9d3f-db67321e86c3.png)

Käynnistän koneen uudestaan ja tarkastan IP osoitteen:

![image](https://user-images.githubusercontent.com/122887178/235641052-9f403bd9-7a47-4e0c-9a3d-ae433209c2ef.png)

Nyt virtuaalikoneeni on samassa verkossa kuin herra koneeni.
        

Asennuksen jälkeen tarkastan että orja on kutsunut herraansa:

             vagrant@fmaster:~$ sudo salt-key
                Accepted Keys:
                f001
                f002
                Denied Keys:
                Unaccepted Keys:
                windowslave
                Rejected Keys:   
                
Onhan se. Hyväksyn avaimet:

                vagrant@fmaster:~$ sudo salt-key -A
                The following keys are going to be accepted:
                Unaccepted Keys:
                windowslave
                Proceed? [n/Y] y
                Key for minion windowslave accepted.
                
Testaan että orja windowsini toimii!


                vagrant@fmaster:~/InfraAsCode$ sudo salt 'windowslave' test.ping
                windowslave:
                    True
Huomiona että windows kone on huomattavasti hitaampi kun Linux kone.

Seuraavaksi luon uuden idempotenssitilan windowskoneelle: 

Alla on kuva `init.sls` tiedostosta:

![image](https://user-images.githubusercontent.com/122887178/235645189-a6cd37c1-88b5-42d9-aef6-a52d373b1337.png)

Kokeilen ajaa tilaa orjalleni:

                vagrant@fmaster:/srv/salt/saltforwindows$ sudo salt 'windowslave' state.apply 'saltforwindows'
                windowslave:
                ----------
                          ID: C:\Users\public\desktop\winkkaritrutussa.txt
                    Function: file.managed
                      Result: True
                     Comment: File C:\Users\public\desktop\winkkaritrutussa.txt updated
                     Started: 03:40:54.422727
                    Duration: 192.906 ms
                     Changes:
                              ----------
                              diff:
                                  New file

                Summary for windowslave
                ------------
                Succeeded: 1 (changed=1)
                Failed:    0
                ------------
                Total states run:     1
                Total run time: 192.906 ms

        

Tarkastan orjani työpöydältä ilmestyikö tiedosto sinne:

![image](https://user-images.githubusercontent.com/122887178/235646142-48add6b3-31f0-4cb2-ba98-29b841dad92a.png)


Ja viimeisenä tarkastan että idempotenssi toimii:

                vagrant@fmaster:/srv/salt/saltforwindows$ sudo salt 'windowslave' state.apply 'saltforwindows'
                windowslave:
                ----------
                          ID: C:\Users\public\desktop\winkkaritrutussa.txt
                    Function: file.managed
                      Result: True
                     Comment: File C:\Users\public\desktop\winkkaritrutussa.txt is in the correct state
                     Started: 03:45:35.620219
                    Duration: 132.779 ms
                     Changes:

                Summary for windowslave
                ------------
                Succeeded: 1
                Failed:    0
                ------------
                Total states run:     1
                Total run time: 132.779 ms
                
Viimeisenä temppuna asennan micron windows koneelle `system32` kansioon:

Siirrän micron binääritiedoston `saltforwindows` tilaan.

Ja muokkaan init.sls tiedostoa:

![image](https://user-images.githubusercontent.com/122887178/235647269-bbc9b370-f1c1-4e27-9ee7-254b3381f2f5.png)

Ja kokeilen ajaa tilan orjalleni(tässä kesti jo hieman pidempään):

          windowslave:
                ----------
                          ID: C:\Users\public\desktop\winkkaritrutussa.txt
                    Function: file.managed
                      Result: True
                     Comment: File C:\Users\public\desktop\winkkaritrutussa.txt is in the correct state
                     Started: 03:52:40.212584
                    Duration: 115.171 ms
                     Changes:
                ----------
                          ID: C:\Windows\system32\micro.exe
                    Function: file.managed
                      Result: True
                     Comment: File C:\Windows\system32\micro.exe updated
                     Started: 03:52:40.327755
                    Duration: 1416.785 ms
                     Changes:
                              ----------
                              diff:
                                  New file

                Summary for windowslave
                ------------
                Succeeded: 2 (changed=1)
                Failed:    0
                ------------
                Total states run:     2
                Total run time:   1.532 s      
                
Se näyttää toimivan. Katson vielä virtuaalikoneen työpöydältä löytyykl Micro asenettuna:

![image](https://user-images.githubusercontent.com/122887178/235647725-4642307b-65aa-46c3-a04c-6279cf9308e6.png)

Siellähän se on!!

### Yhteenveto:

Tein tehtävän hieman eri tavalla mitä piti internetin saatavuuden takia. Kuitenkin suurempia ongelmia ei esiintynyt.


