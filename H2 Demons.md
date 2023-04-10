## Controlling all the demons

###  X) Controlling Daemons with Salt - Change SSH Serv port
Lähde: https://terokarvinen.com/2018/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=salt%20ssh

- Salt avulla voi kontrolloida erittäin ison määrään demoneja (palveluita Windows koneilla)
- Tämä voidaan saavuttaa hyödyntämällä `Package-file-service`
- Tämä voidaan tehdä luomalla Master koneelle halutun tilat ja kopioida tämä tila orja koneille.
- Arikellissa esitellään miten muutetaan sshd configuraatioita master koneella
- Tämä muutettu konfiguraatio voidaan kopioida sitten orja koneille.
- Tämä tehdään luomalla uusi SALT tila joka kopioidaan orja koneille


### A) OPEN SSH asennus sekä konfiguraation muuttaminen

7/4/2023 kl 16:30

Aloitan työskentelyn käynnistämällä virtuaaliympäristöni host koneellani (windows)

    PS C:\Users\Fredr\Saltdemo> vagrant up
    
Kokeilen että koneet ovat hengissä.

    PS C:\Users\Fredr\Saltdemo> vagrant ssh fmaster
    Linux fmaster 5.10.0-20-amd64 #1 SMP Debian 5.10.158-2 (2022-12-13) x86_64

    The programs included with the Debian GNU/Linux system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
    permitted by applicable law.
    Last login: Tue Apr  4 17:26:48 2023 from 10.0.2.2
    vagrant@fmaster:~$ sudo salt '*' cmd.run 'hostname -I'
    f002:
        10.0.2.15 192.168.12.102
    f001:
        10.0.2.15 192.168.12.100
        
Voin totea että herrani sekä orjat ovat pelikunnossa. Tarkastan vielä että ssh Demoni on käynnissä master koneella.

    vagrant@fmaster:/etc/ssh$ ps -aux |grep ssh
    root         331  0.0  1.0  13356  5324 ?        Ss   14:51   0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
    root       18345  0.0  1.8  14720  8752 ?        Ss   17:51   0:00 sshd: vagrant [priv]
    vagrant    18360  0.0  1.2  14720  6196 ?        S    17:51   0:00 sshd: vagrant@pts/0
    
Demoni on pyörimässä

Ennen kuin teen uuden Salt tilan orja koneille teen ensiksi tarvittavat muutokset master koneelle.

    vagrant@fmaster:~$ cd /etc/ssh/
    vagrant@fmaster:/etc/ssh$ sudoedit sshd_config
    
    
![image](https://user-images.githubusercontent.com/122887178/230621854-950ea613-cf42-408c-8b8f-71c663a44aaf.png)

Muutan seuraavat tiedot

    #Port 22 --> Port 22 8888
    
Kokeilen tätä kirjautumalla ulos virtuaalikoneelta ja ottaa SSH yhteyden portti 8888 kautta master koneelle.

Kun palasin windows terminaaliin tajusin etten tiedä fmaster koneen ip osoitteen. Palaan koneelle ja katson mikä sen ip osoite oli.

    vagrant@fmaster:~$ ip addr
    3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:bc:34:16 brd ff:ff:ff:ff:ff:ff
    altname enp0s8
    inet 192.168.12.3/24 brd 192.168.12.255 scope global eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:febc:3416/64 scope link
       valid_lft forever preferred_lft forever
       
Komenolla `ip addr` saan näkyviin koneen ip osoitteen.

Kokeilen uudestaan:

    PS C:\WINDOWS\system32> ssh -p 8888 fmaster@192.168.12.3
    ssh: connect to host 192.168.12.3 port 8888: Permission denied
    
Eipä tietenkään. Enhän minä ole antanut `fmaster` koneen julkista avainta Host koneelle.

Teen tämän. luon uuden Avain parin `fmaster` koneella.

    vagrant@fmaster:~/.ssh$ ssh-keygen
Kopioin juuri luomani `id_rsa.pub` tiedoston host windows koneelleni.

    C:\Users\Fredr\.ssh
    authorized_keys
    
Kokeilen uudestaan toivoen ettei tarvitse käynnistää windows uudelleen.
    
    PS C:\WINDOWS\system32> ssh vagrant@192.168.12.3
    ssh: connect to host 192.168.12.3 port 22: Permission denied

Sama ongelma. Nyt epäilen että fmaster koneella on SSH demoni jotenki konfiguroitu väärin.

`sshd_config` tiedostossa tein todennäköisesti väärin muutoksen

     #Port 22 --> Port 22 8888
     Pitäisi olla
     Port 22
     Port 8888
     
Käynnistän ssh demonin uudestaan virtuaalikoneella ja kokeilen uudestaa.

    vagrant@fmaster:~$ sudo systemctl restart ssh
    vagrant@fmaster:~$ sudo systemctl status ssh
    ● ssh.service - OpenBSD Secure Shell server
         Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
         Active: active (running) since Tue 2023-04-04 18:48:18 UTC; 29s ago
           Docs: man:sshd(8)
                 man:sshd_config(5)
        Process: 20869 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
       Main PID: 20870 (sshd)
          Tasks: 1 (limit: 525)
         Memory: 1.1M
            CPU: 12ms
         CGroup: /system.slice/ssh.service
                 └─20870 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups

    Apr 04 18:48:18 fmaster systemd[1]: Starting OpenBSD Secure Shell server...
    Apr 04 18:48:18 fmaster sshd[20870]: Server listening on 0.0.0.0 port 8888.
    Apr 04 18:48:18 fmaster sshd[20870]: Server listening on :: port 8888.
    Apr 04 18:48:18 fmaster systemd[1]: Started OpenBSD Secure Shell server.
    Apr 04 18:48:18 fmaster sshd[20870]: Server listening on 0.0.0.0 port 22.
    Apr 04 18:48:18 fmaster sshd[20870]: Server listening on :: port 22.
    
Samalla totean että virtuaalikoneen päivämäärä on väärä???

    vagrant@fmaster:~$ date
    Tue Apr  4 18:49:37 UTC 2023
    
Työskentelen tehtävää 7/4/2023. Outoa.

Kirjaudun ulos virtuaalikoneelta ja kokeilen uudestaan.
   
    PS C:\Users\Fredr\Saltdemo> ssh vagrant@192.168.12.3
    ssh: connect to host 192.168.12.3 port 22: Permission denied
    
Onpa ärsittävää windowsin kanssa puljailla.

Kokeilen ottaa ssh yhteyden `vagrant` komennon avulla ja määrittämällä portti 8888
    
    PS C:\Users\Fredr\Saltdemo> vagrant ssh -p 8888 fmaster
    The machine with the name '8888' was not found configured for
    this Vagrant environment.
    
Ai että windows on raivostuttava.

Kokeilen ssh virtuaalikoneen localhostille portilla 8888

    vagrant@fmaster:~$ ssh -p 8888 localhost
    vagrant@localhost: Permission denied (publickey).
    vagrant@fmaster:~$ ssh -p 8889 localhost
    ssh: connect to host localhost port 8889: Connection refused
    
Tämä ainakin kertoo minulle että virtuaalikone kuuntelee porttia 8888 mutta ei anna minun kirjautua koska julkinen avain ei ole jaettu localhostin ja käyttäjän välillä

En kuitenkaan pääse ottamaan SSH yhteyden windows koneeltani muutein kuin käyttämällä `vagrant ssh fmaster` komentoa.

Ongelmat jotka törmään liitty windows host koneeseeni ja sen avain sekoiluun. 
Voin tässä vaiheessa kuitenkin totea että portti 8888 kuuntelee ssh yhteyksiä joten jatkan tehtävää.

kl 18:00

### B) Automatisointi ja testaus

Aloitan luomalla uuden tilan salt kansiooni.

    vagrant@fmaster:/srv/salt/ssh$ ls
    init.sls
    vagrant@fmaster:/srv/salt/ssh$ sudoedit init.sls
![image](https://user-images.githubusercontent.com/122887178/230631666-eb572047-b6bf-4f72-a3b5-3ab1af994793.png)

Kokeilen ajaa tilan orjilleni.

    vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' state.apply ssh
    f001:
        Data failed to compile:
    ----------
        Rendering SLS 'base:ssh' failed: mapping values are not allowed in this context
        
Okej lähdetään tarkastamaan ongelmaa. 

Ongelma voi johtua tiedostoje nimeämisestä. Olen nimennyt kansion `ssh` alle init.sls tiedoston. Muutan tämän ssh.sls nimeksi

    vagrant@fmaster:/srv/salt$ tree
    .
    ├── hello
    │   └── init.sls
    ├── ssh
    │   └── init.sls
    └── top.sls
    
    vagrant@fmaster:/srv/salt/ssh$ sudo mv init.sls ssh.sls
    vagrant@fmaster:/srv/salt/ssh$ ls
    ssh.sls
    vagrant@fmaster:/srv/salt/ssh$ cd ..
    vagrant@fmaster:/srv/salt$ tree
    .
    ├── hello
    │   └── init.sls
    ├── ssh
    │   └── ssh.sls
    └── top.sls

    2 directories, 3 files
    
Kokeilen uudestaan.

    vagrant@fmaster:/srv/salt$ sudo salt 'f001' state.apply ssh
    f001:
        Data failed to compile:
    ----------
        No matching sls found for 'ssh' in env 'base'
        
Sama ongelma. Kokeilen muuttaa ssh.sls tiedostoani joksikin yksinkertaisemmaksi.

![image](https://user-images.githubusercontent.com/122887178/230632780-0665cca0-c6c8-4594-9445-e5c9f0000d31.png)

Sama ongelma

Muutin taas hieman .sls tiedostoa.

![image](https://user-images.githubusercontent.com/122887178/230633162-8dde2e4a-af1e-44fd-a395-7a80b016446e.png)

Sama ongelma.
Kopioin aiemman luodun `hello world` tilan `/srv/salt/ssh` kansioon.
![image](https://user-images.githubusercontent.com/122887178/230633580-58ff47f4-4a4c-4093-9910-1b5244f5abfe.png)


    vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' state.apply ssh
    f001:
    ----------
              ID: hello
        Function: cmd.run
            Name: echo "Hello World"
          Result: True
         Comment: Command "echo "Hello World"" run
         Started: 19:31:44.455046
        Duration: 5.53 ms
         Changes:
                  ----------
                  pid:
                      2049
                  retcode:
                      0
                  stderr:
                  stdout:
                      Hello World

    Summary for f001
    ------------
    Succeeded: 1 (changed=1)
    Failed:    0
    ------------
    Total states run:     1
    Total run time:   5.530 ms

homma toimii. Eli siis kansiossa pitää olla init.sls tiedoston nimi.

Saan seuraavan vikakoodin:

    vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' state.apply ssh
    f001:
        Data failed to compile:
    ----------
        ID 'ssh' in SLS 'ssh' contains a short declaration (cmd.run) with a trailing colon. When not passing any arguments to a state, the colon must be omitted.
    ----------
        State 'ssh' in SLS 'ssh' is not formed as a list
        
Korjasin init.sls tiedoston että YAML syntaxi on oikein:


![image](https://user-images.githubusercontent.com/122887178/230634046-bcf0f6f6-c220-4510-ae75-2dce23fef6d8.png)

Lähdetäänpä tekemään `init.sls` tiedostoa siten että se toimii ja kopioi master koneen `sshd_config` tiedoston tehdyt muutokset myös orjille.

![image](https://user-images.githubusercontent.com/122887178/230634504-3b0fe6ee-22e5-4ad2-8c84-72372c8002dc.png)

lähde: https://terokarvinen.com/2018/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=salt%20ssh

Kokeilen `sudo salt '*' state.apply ssh`

    vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' state.apply ssh
    f001:
        Data failed to compile:
    ----------
        State 'sshd' in SLS 'ssh' is not formed as a list
       
Luen Tero karvisen artikkelia ja tajua että sshd_config tiedostoa ei löydy `/srv/salt/ssh/` kansiosta.

Kopioin sen sinne:

        vagrant@fmaster:/srv/salt/ssh$ sudo cp /etc/ssh/sshd_config /srv/salt/ssh/
        vagrant@fmaster:/srv/salt/ssh$ ls
        init.sls  sshd_config
        
Teen pieniä muutoksia `init.sls` tiedostoon.

![image](https://user-images.githubusercontent.com/122887178/230635554-aa4e51e0-43b3-4d2a-8a88-5dc80c89a1e9.png)

ja kokeilen ajaan `state.apply ssh` uudestaan.

        vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' state.apply ssh
        f001:
        ----------
                  ID: openssh-server
            Function: pkg.installed
              Result: True
             Comment: All specified packages are already installed
             Started: 19:45:33.876290
            Duration: 20.296 ms
             Changes:
        ----------
                  ID: /etc/ssh/sshd_config
            Function: file.manage
              Result: False
             Comment: State 'file.manage' was not found in SLS 'ssh'
                      Reason: 'file.manage' is not available.
             Changes:
        ----------
                  ID: sshd
            Function: service.running
              Result: False
             Comment: One or more requisite failed: ssh./etc/ssh/sshd_config
             Started: 19:45:34.293963
            Duration: 0.004 ms
             Changes:

        Summary for f001
        ------------
        Succeeded: 1
        Failed:    2
        ------------
        Total states run:     3
        Total run time:  20.300 ms
        ERROR: Minions returned with non-zero exit code
        vagrant@fmaster:/srv/salt/ssh$
        
Noniin sain uuden virhekoodin. Hyvä tässä oli se että ainakin `Open-ssh` ohjelmisto asentui!!

Virhekoodista ilmenee että `file.manage` ei ole käytettävissä. Ei sinäänsä ihme koska se pitäisi olla `file.managed`.
Korjaan syntaxi virheen ja kokeilen uudestaan:

        
          ID: /etc/ssh/sshd_config
    Function: file.managed
      Result: False
     Comment: Source file salt://sshd_config not found in saltenv 'base'
     Started: 19:49:21.880219
    Duration: 15.311 ms
     Changes:

Noniin nyt sain taas eri virheen.
    
![image](https://user-images.githubusercontent.com/122887178/230636499-212cd605-57db-427e-a27f-a56d63ab362a.png)

Teen muutoksia `init.sls` tiedostoon. (huom kommentoin muut tilat pois mitä tällä hetkellä en korjaa)
![image](https://user-images.githubusercontent.com/122887178/230636576-862483f7-2835-49f3-b794-71824a60935d.png)

Ja taas tajuan missä virhe on. `salt://` vastaa herran tiedostopolkua `/srv/salt/` mutta minullahan on `sshd_config` tiedosto polussa `/srv/salt/ssh/`
Korjaan tämän.

![image](https://user-images.githubusercontent.com/122887178/230637909-6d8ac18f-31a0-420e-821b-ff910eff5f79.png)

        vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' state.apply ssh
        f001:
        ----------
                  ID: /etc/ssh/sshd_config
            Function: file.managed
              Result: True
             Comment: File /etc/ssh/sshd_config updated
             Started: 20:03:12.632146
            Duration: 61.444 ms
             Changes:
                      ----------
                      diff:
                          ---
                          +++
                          @@ -13,7 +13,7 @@
                           Include /etc/ssh/sshd_config.d/*.conf

                           Port 22
                          -Port 443
                          +Port 8888
                           #AddressFamily any
                           #ListenAddress 0.0.0.0
                           #ListenAddress ::

        Summary for f001
        ------------
        Succeeded: 1 (changed=1)
        Failed:    0
        ------------
        Total states run:     1
        Total run time:  61.444 ms
        
Homma toimii!! Aivan mahtava.

Vielä varmistan että orjien `ssh` demonit tarkastavat muutokset tiedostossa `/etc/ssh/sshd_config` ja tarvittaessa käynnistävät ssh demonin uudestaan.

![image](https://user-images.githubusercontent.com/122887178/230638449-51baf90c-06b8-4d02-a413-296469e6d521.png)

        -----
          ID: sshd
    Function: service.running
      Result: True
     Comment: The service sshd is already running
     Started: 20:07:33.556394
    Duration: 28.294 ms
     Changes:

    Summary for f001
    ------------
    Succeeded: 3
    Failed:    0
    ------------
    Total states run:     3
    Total run time:  85.024 ms
    vagrant@fmaster:/srv/salt/ssh$
    
Kokeilen seuraavaksi työni tulokset: 

      
        vagrant@fmaster:/srv/salt/ssh$ ssh -p 8888 vagrant@192.168.12.100
        ssh: connect to host 192.168.12.100 port 8888: Connection refused
        
        ## Portti 8888 ei vastaa
        
        vagrant@fmaster:/srv/salt/ssh$ ssh -p 22 vagrant@192.168.12.100
        The authenticity of host '192.168.12.100 (192.168.12.100)' can't be established.
        
        ## Portti 22 vastaa mutta ei salli pääsyä fmasterilta mikä on outoa
        
        ECDSA key fingerprint is SHA256:NK6SjqExRGeEX/XZQV0iUZWzVKcr1oukeb9jlOqoecg.
        Are you sure you want to continue connecting (yes/no/[fingerprint])? y
        Please type 'yes', 'no' or the fingerprint: yes
        Warning: Permanently added '192.168.12.100' (ECDSA) to the list of known hosts.
        vagrant@192.168.12.100: Permission denied (publickey).

Annan f001 orjalle komennon `sudo systemctl restart ssh`

        vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' cmd.run 'sudo systemctl restart ssh'
        f001:
        vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' cmd.run 'sudo systemctl status ssh'
        f001:
            * ssh.service - OpenBSD Secure Shell server
                 Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
                 Active: active (running) since Tue 2023-04-04 20:11:37 UTC; 13s ago
                   Docs: man:sshd(8)
                         man:sshd_config(5)
                Process: 2278 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
               Main PID: 2279 (sshd)
                  Tasks: 1 (limit: 525)
                 Memory: 1.1M
                    CPU: 13ms
                 CGroup: /system.slice/ssh.service
                         `-2279 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups

            Apr 04 20:11:37 f001 systemd[1]: Starting OpenBSD Secure Shell server...
            Apr 04 20:11:37 f001 sshd[2279]: Server listening on 0.0.0.0 port 8888.
            Apr 04 20:11:37 f001 sshd[2279]: Server listening on :: port 8888.
            Apr 04 20:11:37 f001 sshd[2279]: Server listening on 0.0.0.0 port 22.
            Apr 04 20:11:37 f001 sshd[2279]: Server listening on :: port 22.
            Apr 04 20:11:37 f001 systemd[1]: Started OpenBSD Secure Shell server.

Käynnistyksen jälkeen `f001` kuuntelee porttia 8888. Testaan vielä.

        vagrant@fmaster:/srv/salt/ssh$ ssh -p 8888 f001@192.168.12.100
        f001@192.168.12.100: Permission denied (publickey).
        
#### Tätä jään ihmettelemään. Miksi pystyn komentamaan sekä ottamaan `vagrant ssh` komenolla host tietokoneeltani yhteyden orjiin mutta en `fmaster` koneella?

Kokeilen antaa salt komennon orjalle että se lähettä SSH julkisen avaimen master koneelle.

        vagrant@fmaster:/srv/salt/ssh$ sudo salt 'f001' cmd.run 'ssh-keygen'
        f001:
            Generating public/private rsa key pair.
            Enter file in which to save the key (/root/.ssh/id_rsa):
Yritän kopioida master koneen julkisen avaimen orjalleni.

Lopetin yrittämisen tässä vaiheessa.


### D) Apachen koskettelu Saltilla.

Teen uuden tilan master koneellani.

        vagrant@fmaster:/srv/salt$ sudo mkdir apache

        apache  hello  ssh  top.sls
        vagrant@fmaster:/srv/salt$ cd apache/
        vagrant@fmaster:/srv/salt/apache$ sudoedit init.sls
Teen tilan jotta koneeni asentaa apache2 weppipalvelimen.

![image](https://user-images.githubusercontent.com/122887178/230644580-d9780ce2-3674-43db-8386-6e28f4ce24d1.png)

Kokeilen että tila toimii:
        
        vagrant@fmaster:/srv/salt/apache$ sudo salt 'f002' cmd.run 'sudo apt-get update'
        f002:
            Get:1 https://security.debian.org/debian-security bullseye-security InRelease [48.4 kB]
            Hit:2 https://deb.debian.org/debian bullseye InRelease
            Get:3 https://deb.debian.org/debian bullseye-updates InRelease [44.1 kB]
            Get:4 https://deb.debian.org/debian bullseye-backports InRelease [49.0 kB]
            Reading package lists...
            E: Release file for https://security.debian.org/debian-security/dists/bullseye-security/InRelease is not valid yet (invalid for another 1d 13h 5min 19s). Updates for this repository will not be applied.
            E: Release file for https://deb.debian.org/debian/dists/bullseye-updates/InRelease is not valid yet (invalid for another 2d 17h 15min 18s). Updates for this repository will not be applied.
            E: Release file for https://deb.debian.org/debian/dists/bullseye-backports/InRelease is not valid yet (invalid for another 2d 17h 15min 18s). Updates for this repository will not be applied.
            
Tämä virhe johtuu siitä että järjestelmän kello on väärässä. Kuten aikasemmin totesin tämä selittää ongelmat repositorien käytössä.

Lopetan tältä päivältä 07/04/2023 kl 1943. Hauskoja ongelmia oli tänään ratkottavana. Jatkan vapaaehtoisen tehtävän tekemistä myöhemällä ajalla. Joten jos luet tämän ennen kun se on valmis niin älä ihmettele.

### Kellon ajan korjaaminen

Jatkan työskentelyä 10/4/23 kl 935

Aloitan käynistämällä virtuaaliympäristön `vagrant up`

Otan yhteyden `fmaster` koneeseen ja tarkistan järjestelmien kellonajat.

        vagrant@fmaster:~$ sudo salt '*' cmd.run 'date'
        f001:
            Tue Apr  4 21:10:37 UTC 2023
        f002:
            Tue Apr  4 21:08:00 UTC 2023
        vagrant@fmaster:~$
        
Nämä ovat taas väärässä. Yritän etsiä netistä ratkaisua tähän.

LÄhde: https://wiki.debian.org/DateTime

Koska en pysty käyttämään repositoreja syötän ensimmäiseksi manuaalisesti kellon jotta voin asentaa `ntp` ohjelman.

        vagrant@fmaster:~$ date --set 2023-10-4
        date: cannot set date: Operation not permitted
        Wed Oct  4 00:00:00 UTC 2023
        vagrant@fmaster:~$ sudo date --set 2023-10-4
        Wed Oct  4 00:00:00 UTC 2023
        vagrant@fmaster:~$ sudo date --set 09:43:35
        Wed Oct  4 09:43:35 UTC 2023
        
Tämä kuitenkin aiheutti ongelmia `apt-get` käytössä:

        vagrant@fmaster:~$ sudo apt-get update
        Err:1 https://deb.debian.org/debian bullseye InRelease
          Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.246.132 443]
        Err:2 https://security.debian.org/debian-security bullseye-security InRelease
          Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.130.132 443]
        Err:3 https://deb.debian.org/debian bullseye-updates InRelease
          Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.246.132 443]
        Err:4 https://deb.debian.org/debian bullseye-backports InRelease
          Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.246.132 443]
        Reading package lists... Done
        W: Failed to fetch https://deb.debian.org/debian/dists/bullseye/InRelease  Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.246.132 443]
        W: Failed to fetch https://security.debian.org/debian-security/dists/bullseye-security/InRelease  Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.130.132 443]
        W: Failed to fetch https://deb.debian.org/debian/dists/bullseye-updates/InRelease  Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.246.132 443]
        W: Failed to fetch https://deb.debian.org/debian/dists/bullseye-backports/InRelease  Certificate verification failed: The certificate is NOT trusted. The revocation or OCSP data are old and have been superseded. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 151.101.246.132 443]
        W: Some index files failed to download. They have been ignored, or old ones used instead.
        vagrant@fmaster:~$
        
Minulla ei ole `ntp` demonia olemassa enkä pysty asentamaan sitä apt-get koska aika ei ole oikein.
Kokeilen manuaalisesti laittaa kellon uudestaan ja tämän jälkeen asentamaan `ntp` demonin.

        vagrant@fmaster:~$ sudo date -s "2023-04-10 09:55:30"
        Mon Apr 10 09:55:30 EEST 2023
        
        Nyt toimi apt-get
        vagrant@fmaster:~$ sudo apt-get update
        vagrant@fmaster:~$ sudo apt-get install ntp
        
       vagrant@fmaster:~$ sudo systemctl status ntp
        ● ntp.service - Network Time Service
             Loaded: loaded (/lib/systemd/system/ntp.service; enabled; vendor preset: enabled)
             Active: active (running) since Mon 2023-04-10 09:55:53 EEST; 7s ago
               Docs: man:ntpd(8)
            Process: 30201 ExecStart=/usr/lib/ntp/ntp-systemd-wrapper (code=exited, status=0/SUCCESS)
           Main PID: 30207 (ntpd)
              Tasks: 2 (limit: 525)
             Memory: 1.4M
                CPU: 40ms
             CGroup: /system.slice/ntp.service
                     └─30207 /usr/sbin/ntpd -p /var/run/ntpd.pid -g -u 108:114
                     
Nyt näyttäisi `ntp` toimivan.

Seuraavaksi korjaan orjien kellot.

        vagrant@fmaster:~$ sudo salt '*' cmd.run 'sudo date -s "2023-04-10 10:00:00"'
        f001:
            Mon Apr 10 10:00:00 UTC 2023
        f002:
            Mon Apr 10 10:00:00 UTC 2023
            
Kokeilen päivittää repot:

        vagrant@fmaster:~$ sudo salt '*' cmd.run 'sudo apt-get update'
        Ei tullut vikakoodeja.
        
Ja viimeiseksi asennan `ntp` demonin ja tarkistan demonien statuksen:

        vagrant@fmaster:~$ sudo salt '*' cmd.run 'sudo apt-get install -y ntp'
        
        vagrant@fmaster:~$ sudo salt '*' cmd.run 'sudo systemctl status ntp'
        Molemmat orjat näyttää state: active
        
Noniin nyt homma näyttäisi toimivan.

### D) Apachen koskettelua SALT:lla

Seuraavan tilan tavoite on asentaa orjille apache, Muuttaa vakio oletus sivu ja tehdä apache konfiguraatiot siten että käyttäjän ei tarvitse sudo oikeuksia muuttaakseen apachen sivuja.

Aloitan tekemällä muutokset master koneelle:

        vagrant@fmaster:~$ sudo apt-get install apache2
        vagrant@fmaster:~$ sudo systemctl status apache2
        ● apache2.service - The Apache HTTP Server
             Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
             Active: active (running) since Mon 2023-04-10 10:11:40 EEST; 32s ago
               Docs: https://httpd.apache.org/docs/2.4/
           Main PID: 31514 (apache2)
              Tasks: 55 (limit: 525)
             Memory: 7.1M
                CPU: 75ms
             CGroup: /system.slice/apache2.service
                     ├─31514 /usr/sbin/apache2 -k start
                     ├─31516 /usr/sbin/apache2 -k start
                     └─31517 /usr/sbin/apache2 -k start

        Apr 10 10:11:40 fmaster systemd[1]: Starting The Apache HTTP Server...
        Apr 10 10:11:40 fmaster apachectl[31513]: AH00558: apache2: Could not reliably determine the server's fully qualified d>
        Apr 10 10:11:40 fmaster systemd[1]: Started The Apache HTTP Server.

APache on asenettu ja vakio sivu löyty. Seuraavaksi teen muutokset apache conf tiedostoon.

<img width="279" alt="image" src="https://user-images.githubusercontent.com/122887178/230851538-a706b5e2-dfd3-4176-9b68-e3f66d3f05cc.png">

          
Tämän pitäisi muuttaa kotisivuni sijainnin /home/vagrant/public.sites kansioon.

TÄmän pitäisi toimia kaikilla orjilla koska käyttäjät on aina vagrant.
En tiedä miten tekisin siten että conf tiedosto viittaisi käyttäjään. ~ merkki käyttäjän tilalla ei toiminut.

Otan nämä sivut käyttöön.

        vagrant@fmaster:~$ pwd
        /home/vagrant
        vagrant@fmaster:~$ mkdir public.sites
        vagrant@fmaster:~$ ls
        public.sites
        vagrant@fmaster:~$ micro index.html
        vagrant@fmaster:~$ ls
        index.html  public.sites

        vagrant@fmaster:~$ sudo a2ensite frontpage.conf
        Enabling site frontpage.
        To activate the new configuration, you need to run:
          systemctl reload apache2
        vagrant@fmaster:~$ sudo systemctl restart apache2

Tein uuden kansion käyttäjälle. Loin sinne testisivun. Otin sen confin käyttöön ` sudo a2enmod` komenolla
 
        vagrant@fmaster:~$ curl localhost
        <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
        <html><head>
        <title>403 Forbidden</title>
        </head><body>
        <h1>Forbidden</h1>
        <p>You don't have permission to access this resource.</p>
        <hr>
        <address>Apache/2.4.56 (Debian) Server at localhost Port 80</address>
        </body></html>

Tein kirjoitusvirheen kansion nimeämisessä korjaan sen: `public.sites` --> `public_sites`

        vagrant@fmaster:/etc/apache2/sites-available$ curl localhost
        Apache user testsite
        
Noin nyt apache näyttää käyttäjän hakemistossa olevan `index.html` sivun.

#### Automatisointi





        


     
        


    
        
        














  
    

    
    










    

