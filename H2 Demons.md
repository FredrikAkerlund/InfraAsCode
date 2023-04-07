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

### B) Automatisointi

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

    












  
    

    
    










    

