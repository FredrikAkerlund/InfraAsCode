## Hello salt 

Alusta: Lenovo ideapad 5 pro, R7 5000 sarja 16gt ram, 500gt SSD

OS: Windows 11

Aika: UTC+2 2030 29/03/23 

Sijainti: Kymenlaakso

Minulla on asenettu Debian 11 virtuaalikone johon on valmiiksi asenettu micro ja apache2 weppipalvelin.

[TLDR](Yhteenveto)

X) The basics of GIthub repositories

- GitHub on sivusto jossa kehittäjät ja muut voivat luoda keskitetyn versiohallinta tietokannan ja julkaista sen helposti muille nähtäväksi
- GitHubiin voi luoda "repositories" suomeksi säilytyspaikkoja omille luomuksille. Githubista on helppo jakaa muille sekä muut voivat tarvittaessa korjata ja auttaa alkuperäistä kehittäjää.
- Github voi luoda paljon erillaisia tiedostotyyppejä. Markdown, HTML sivustoja, Python koodia jne.
Lähde: https://terokarvinen.com/2023/create-a-web-page-using-github/

X) The basics of Salt Vagrant. How to master the slave master architecture

- Salt on voimakas ohjelma jolla voidaan hallita useita tietokoneita keskitetysti.
- Vagrant on ohjelma jolla voidaan luoda uusia virtuaalikoneita eri verkkoihin. Vagrantia voi käyttää jos haluaa jakaa kehitysympäristöjä jotka ovat identtisiä.
- Salt:ssa on ominaisuus jolla voidaan määritellä komennettaville koneille haluttu loppuasetelma ns. idempotensseja.
- Salt:n avulla määritettään komennettavat tietokoneet orjiksi (slave) jotka kuuntelevat heidän herraa (master) konetta
Lähde: https://terokarvinen.com/2023/salt-vagrant/

## Debian 11 asennus Vagrantilla.

Aloitan asentamalla vagrant  ohjelmiston:

        sudo apt-get install vagrant virtualbox
        
Asennukseen meni noin 3 min.

Seuraavaksi asennan virtualboxin. Saan kuitenkin seuraavan virheilmoituksen:

    fredrik@master:~$ sudo apt-get install virtualbox
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    Package virtualbox is not available, but is referred to by another package.
    This may mean that the package is missing, has been obsoleted, or
    is only available from another source

    E: Package 'virtualbox' has no installation candidate
    
Debianin virallisilla sivuilla löysin seuraavan tiedon: 

    Debian 10 "Buster" and Debian 11 "Bullseye"
    Packages for VirtualBox are not officially available in stable releases of Debian, due to lack of cooperation from upstream on security support for older releases.
    
Tutkin asiaa hieman tarkemmin ja näköjään virtualboxia ei ole saatavilla debian11 käyttöjärjestelmiin virallisen package managerin kautta. Jatkan tehtävää kuitenkin.
Lähde: https://wiki.debian.org/VirtualBox

Luon uuden hakemiston `saltdemo` ja luon sille tiedoston nimeltä Vagrantfile

          106  mkdir saltdemo
          107  ls
          108  cd saltdemo
          109  micro Vagrantfile
          
Vagrantfile tiedostoon kopioin hieman muutettuna Tero karvisen Vagrant tiedoston: 

        # -*- mode: ruby -*-
        # vi: set ft=ruby :
        # Copyright 2014-2023 Tero Karvinen http://TeroKarvinen.com

        $minion = <<MINION
        sudo apt-get update
        sudo apt-get -qy install salt-minion
        echo "master: 192.168.12.3">/etc/salt/minion
        sudo service salt-minion restart
        echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
        MINION

        $master = <<MASTER
        sudo apt-get update
        sudo apt-get -qy install salt-master
        echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
        MASTER

        Vagrant.configure("2") do |config|
          config.vm.box = "debian/bullseye64"


          config.vm.define "fmaster", primary: true do |tmaster|
            fmaster.vm.provision :shell, inline: $master
            fmaster.vm.network "private_network", ip: "192.168.12.3"
            fmaster.vm.hostname = "fmaster"
          end
        end

Tavoitteena on vain luoda Master tietokone debian 11 käyttöjärjestelmällä.

Ajan komennon `vagrant up` mutta saan seuraavan virheilmoituksen:

      A Vagrant environment or target machine is required to run this
      command. Run `vagrant init` to create a new Vagrant environment. Or,
      get an ID of a target machine from `vagrant global-status` to run
      this command on. A final option is to change to a directory with a
      Vagrantfile and to try again.
      
Ajan `vagrant init` 

Totean että tämä luo vakioasetus `Vagrantfile` tiedoston siihen hakemistoon missä jo olen.

Koska loin jo aikasemmin `vagrantfile` tiedoston `saltdemo` hakemiston sisään. 

Ongelma taitaa johtua siitä että en ole asentanut virtualboxia.

Löysin ohjeet millä voin asentaa virtualboxin turvallisesti heidän virallisesta säilytyspaikalta.

Lähde: https://linuxiac.com/how-to-install-virtualbox-on-debian-11-bullseye/

Seuraan ohjeita mutta kiroitan lyhyen raportin.

1. Lataan virtualboxin virallisen PGP julkisen avaimen
2. Päivitän tietokantani
3. Asennan virtualboxin käyttämällä `sudo apt-get install virtualbox-7.0`

Kokeilen uudestaan `vagrant up` komentoa.

        fredrik@master:~/saltdemo$ vagrant up
        Bringing machine 'fmaster' up with 'libvirt' provider...
        ## Virhekoodi kertoo että Libvrt riippuvuus ei ole asenettu
        Error while connecting to Libvirt: Error making a connection to libvirt URI qemu:///system?no_verify=1&keyfile=/home/fredrik/.ssh/id_rsa:
        Call to virConnectOpen failed: Failed to connect socket to '/var/run/libvirt/libvirt-sock': No such file or directory

Sama ongelma. Ongelma johtuu että kaikki riippuvuudet ei ole asenettu. Libvrt ei ole asenettu

Lopetin ongelmanratkaisua 21.30 jatkan tehtävää myöhemmin.

Jatkan tehtävää 30/3/2023 kl 20:08

Tutkin debianin wiki sivustoa ja löysin ohjeet millä voin asentaa vagrantin oikeilla riippuvuuksilla.

Lähde: https://wiki.debian.org/Vagrant#Installation

Asennan vagrant-libvrt lisäosan sekä libvr-daemon-system

                sudo apt install vagrant-libvirt libvirt-daemon-system
                
Tarkistan että käyttäjäni on libvrt ryhmässä

                sudo usermod --append --groups libvirt $USER
                
Kirjaudun ulos ja takaisin sisään järjestelmään jonka jälkeen tarkistan että käyttäjäni on libvrt ryhmässä

                groups | grep -o libvirt
                libvrt
Ajan uudestaan `vagrant up` komennon.

Ensi asennukseen meni 5 min mutta sain seuraavan virheilmoituksen:

                Error while creating domain: Error saving the server: Call to virDomainDefineXML failed: invalid argument: could not get preferred machine for /usr/bin/qemu-system-x86_64 type=kvm

Tarkastan virtuaalikoneeni IP osoitteen

                fredrik@master:~/saltdemo$ hostname -I
                10.0.2.15 
                
virtuaalikoneeni on näköjään aivan eri verkossa. Kokeilen muuttaa Tero Karvisen `Vagrantfile` tiedostoa. 
                
                # -*- mode: ruby -*-
                # vi: set ft=ruby :
                # Copyright 2014-2023 Tero Karvinen http://TeroKarvinen.com

                $minion = <<MINION
                sudo apt-get update
                sudo apt-get -qy install salt-minion
                ##Tässä vaihdan IP osoitteen että se on samassa verkossa kun virtuaalikoneeni 10.0.2.15/24
                echo "master: 10.0.2.16">/etc/salt/minion
                sudo service salt-minion restart
                echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
                MINION

                $master = <<MASTER
                sudo apt-get update
                sudo apt-get -qy install salt-master
                echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
                MASTER

                Vagrant.configure("2") do |config|
                        config.vm.box = "debian/bullseye64"


                        config.vm.define "fmaster", primary: true do |fmaster|
                                fmaster.vm.provision :shell, inline: $master
                                ## Tässä vaihdan IP osoitteen myös
                                fmaster.vm.network "private_network", ip: "10.0.2.16"
                                fmaster.vm.hostname = "tmaster"
                        end
                end

Ajan uudestaan `Vagrant up` komennon ja saan seuraavan virheilmoituksen:

                Error while creating domain: Error saving the server: Call to virDomainDefineXML failed: invalid argument: could not get preferred machine for /usr/bin/qemu-system-x86_64 type=kvm

Tässä vaiheessa olin epätoivoinen ja käännyin Tekoälyn puoleen.

Vaihdoin Windows host koneen Virtualbox ohjelmasta paravirtualization interface settings KVM vaihtoehdoksi.

Käynnistän koneen ja kokeilen uudestaan. Sain saman virhekoodin.

                Error while creating domain: Error saving the server: Call to virDomainDefineXML failed: invalid argument: could not get preferred machine for /usr/bin/qemu-system-x86_64 type=kvm
                
Seuraavaksi kokeilen käyttää Teron alkuperäistä Vagrantfile tiedostoa:

                # -*- mode: ruby -*-
                # vi: set ft=ruby :
                # Copyright 2014-2023 Tero Karvinen http://TeroKarvinen.com

                $minion = <<MINION
                sudo apt-get update
                sudo apt-get -qy install salt-minion
                echo "master: 192.168.12.3">/etc/salt/minion
                sudo service salt-minion restart
                echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
                MINION

                $master = <<MASTER
                sudo apt-get update
                sudo apt-get -qy install salt-master
                echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
                MASTER

                Vagrant.configure("2") do |config|
                        config.vm.box = "debian/bullseye64"

                        config.vm.define "t001" do |t001|
                                t001.vm.provision :shell, inline: $minion
                                t001.vm.network "private_network", ip: "192.168.12.100"
                                t001.vm.hostname = "t001"
                        end

                        config.vm.define "t002" do |t002|
                                t002.vm.provision :shell, inline: $minion
                                t002.vm.network "private_network", ip: "192.168.12.102"
                                t002.vm.hostname = "t002"
                        end

                        config.vm.define "tmaster", primary: true do |tmaster|
                                tmaster.vm.provision :shell, inline: $master
                                tmaster.vm.network "private_network", ip: "192.168.12.3"
                                tmaster.vm.hostname = "tmaster"
                        end
                end
                
Saan huomattavasti enemmän virheilmoituksia mutta sama "päävirhe" kuitenkin;

                n error occurred while executing multiple actions in parallel.
                Any errors that occurred are shown below.

                An error occurred while executing the action on the 't001'
                machine. Please handle this error then try again:

                Error while creating domain: Error saving the server: Call to virDomainDefineXML failed: invalid argument: could not get preferred machine for /usr/bin/qemu-system-x86_64 type=kvm
                
Tässä vaiheessa alkaa taikurin hihat olemaan aika tyhjiä. Oma epäilyss että virtuaalikone ei pysty luomaan omia virtuaalikone. Inception tyylinen ratkaisu ei näköjään toimi.

Seuraava steppi on suorittaa koko tämä asennus suoraan host Windows koneelle.
Käytän lähteenä Tuomas Valkamo raporttia kyseisestä asennuksesta. Lähde: https://tuomasvalkamo.com/CMS-course/week-6/

Lataan Vagrantin virallisilta sivuilta uusimman Vagrant version Windowsille. https://developer.hashicorp.com/vagrant/downloads

Asensin suoraan C: levylle ohjelman. Asennuksen jälkeen taikuri päätti siirtyä lepäämään ja jatkamanaan orjien hallitsemista myöhemällä ajalla.'

Lopetin työskentelyn 30/3/2023 Kl 21.23
                
                
## Uudestisyntynyt taikuri jatkaa tehtävää 1/4/23 kl 19:00

Tässä vaiheessa kokeilin monta vaihtoehtoa käyttää windowsilla vagranttia mutta onnistumatta. En tallentanut muistiinpanojani joten valitettavasti tieto on menetetty.

Aloitan nollista ja asennan uunituoreen virtuaalikoneen Virtualboxilla. Luon Debian 11 koneen. Lähde: https://github.com/FredrikAkerlund/saitti/blob/main/h1l%C3%A4ksy.md

Toistan vaiheet mitä aikaisemmin tein:

- Asennan vagrant ohjelman `sudo apt install vagrant-libvirt libvirt-daemon-system`
- Muutan käyttöoikeudet: `sudo usermod --append --groups libvirt $USER`
- Luon kansion saltdemo
- annan komennon `vagrant init debian/bullseye64`
- Annan komennon `vagrant up` ja saan jälleen kerran samat ongelmat mitä alkuperäisessä tilassa oli: 

                Error while creating domain: Error saving the server: Call to virDomainDefineXML failed: invalid argument: could not get preferred machine for /usr/bin/qemu-system-x86_64 type=kvm
                
                
Tässä vaiheessa en oikeasti tiedä mitä tehdä.


Siirryn takaisin Windowsin äärelle.

Poistan kaikki Vagrant asennukset. Ja asennan sen uudestaan toivoen että tämä muuttaisi jotain! Välissä käyn ulkoiluttamassa koiraa toivoen että raikas ulkoilma parantaisi ajatuksen juoksua. 

Luon jälleen kerran uuden kansion: 

C:\Users\Fredr\Saltdemo

Avaan kansion powershellissä ja annan komennon `vagrant init debian/bullseye64`

Ja sama ongelma kuin aikasemmin: 

                                PS C:\Users\Fredr\Saltdemo> vagrant init debian/bullseye64
                A `Vagrantfile` has been placed in this directory. You are now
                ready to `vagrant up` your first virtual environment! Please read
                the comments in the Vagrantfile as well as documentation on
                `vagrantup.com` for more information on using Vagrant.
                Traceback (most recent call last):
                        16: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/bin/vagrant:251:in `<main>'
                        15: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/environment.rb:799:in `unload'
                        14: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/environment.rb:543:in `hook'
                        13: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/action/runner.rb:46:in `run'
                        12: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/environment.rb:217:in `block in action_runner'
                        11: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/environment.rb:748:in `machine_index'
                        10: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/environment.rb:748:in `new'         9: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/machine_index.rb:54:in `initialize'
                         8: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/machine_index.rb:350:in `with_index_lock'
                         7: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/machine_index.rb:350:in `open'
                         6: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/machine_index.rb:352:in `block in with_index_lock'
                         5: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/machine_index.rb:55:in `block in initialize'
                         4: from C:/HashiCorp/Vagrant/embedded/gems/2.3.4/gems/vagrant-2.3.4/lib/vagrant/machine_index.rb:322:in `unlocked_reload'
                         3: from C:/HashiCorp/Vagrant/embedded/mingw64/lib/ruby/2.7.0/json/common.rb:335:in `load'
                         2: from C:/HashiCorp/Vagrant/embedded/mingw64/lib/ruby/2.7.0/json/common.rb:156:in `parse'
                         1: from C:/HashiCorp/Vagrant/embedded/mingw64/lib/ruby/2.7.0/json/common.rb:156:in `new'
                C:/HashiCorp/Vagrant/embedded/mingw64/lib/ruby/2.7.0/json/common.rb:156:in `initialize': negative string size (or size too big) (ArgumentError)




Käännyn tekoälyn puoleen ja sain vinkin että päivitä Ruby ohjelmisto. Eihän minulla sitä ole asenettu joten asennan sen.

https://www.ruby-lang.org/en/downloads/

Asennan sen ja tajuan miksi windows on niin huono näihin hommiin!!!

Käynnistän koneen jälleen kerran ja kokeilen nollasta taas.

Tuhoan kaikki aikasemmat tiedostot sekä annan `vagrant destroy` komennon.

Ajan komennon `vagrant init debian/bullseye64` ja saan taas saman virhekoodin kun aikasemmin.

Jälleen kerran käännyn epätoivossa tekoälyn puoleen.

Liitän suoraan koko virheilmoituksen ja saan vastaukseksi seuraavan: 

                It appears that there is an issue with loading a JSON file in Vagrant, resulting in an "ArgumentError" with the message "negative string size (or size too big)". This error can occur when the JSON file being loaded is malformed or corrupted.

                Here are some possible solutions:

                Check the JSON file: Make sure that the JSON file being loaded is valid and well-formed. You can use an online JSON validator to check the file's syntax.
                ##TÄMÄN SUORITAN
                Delete the Vagrant machine index: The error message suggests that there might be an issue with the Vagrant machine index. You can try deleting the machine index and running Vagrant again. The machine index is typically located in the following directory: %USERPROFILE%\.vagrant.d\data\machine-index

                Update Vagrant: Make sure you are using the latest version of Vagrant. You can update Vagrant by running the following command: vagrant plugin install vagrant.

                Reinstall Vagrant: If none of the above solutions work, you can try reinstalling Vagrant.
                
Poistan väliaikaisia tiedostoja ja kokeilen `Vagrant up` komentoa.

NYT SE TOIMI!!!

![image](https://user-images.githubusercontent.com/122887178/229309108-df73dd14-9fb1-42fa-a348-421d2e50c51b.png)

Sain luotua virtuaalikoneen.
                
Otan etäyhteyden koneeseen ja totean että se toimii!!

                PS C:\Users\Fredr\Saltdemo> vagrant ssh
                Linux bullseye 5.10.0-20-amd64 #1 SMP Debian 5.10.158-2 (2022-12-13) x86_64

                The programs included with the Debian GNU/Linux system are free software;
                the exact distribution terms for each program are described in the
                individual files in /usr/share/doc/*/copyright.

                Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
                permitted by applicable law.
                vagrant@bullseye:~$ whoami
                vagrant
                
Ja lopuksi tuhoan suurella vaivalla tehdyn virtuaalikoneen :(

        PS C:\Users\Fredr\Saltdemo> vagrant destroy
            default: Are you sure you want to destroy the 'default' VM? [y/N] y
        ==> default: Forcing shutdown of VM...
        ==> default: Destroying VM and associated drives...
Pieni kyynel pääsi valumaan.

B&C) Asenna kolmen koneen verkko ja katso hengittääkö orjat

Muokkaan `Vagrantfile` tiedostoa ja kopioin Tero Karvisen tiedoston. Huom. tein pieniä muutoksia siihen

                                # -*- mode: ruby -*-
                                # vi: set ft=ruby :
                                # Copyright 2014-2023 Tero Karvinen http://TeroKarvinen.com

                                $minion = <<MINION
                                sudo apt-get update
                                sudo apt-get -qy install salt-minion
                                echo "master: 192.168.12.3">/etc/salt/minion
                                sudo service salt-minion restart
                                echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
                                MINION

                                $master = <<MASTER
                                sudo apt-get update
                                sudo apt-get -qy install salt-master
                                echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
                                MASTER

                                Vagrant.configure("2") do |config|
                                        config.vm.box = "debian/bullseye64"

                                        config.vm.define "f001" do |f001|
                                                f001.vm.provision :shell, inline: $minion
                                                f001.vm.network "private_network", ip: "192.168.12.100"
                                                f001.vm.hostname = "f001"
                                        end

                                        config.vm.define "f002" do |f002|
                                                f002.vm.provision :shell, inline: $minion
                                                f002.vm.network "private_network", ip: "192.168.12.102"
                                                f002.vm.hostname = "f002"
                                        end

                                        config.vm.define "fmaster", primary: true do |fmaster|
                                                fmaster.vm.provision :shell, inline: $master
                                                fmaster.vm.network "private_network", ip: "192.168.12.3"
                                                fmaster.vm.hostname = "fmaster"
                                        end
                                end
                                
Nyt alko homma niin sanotusti rockaamaan. Näen Virtualbox ohjelmasta että tietokoneita luodaan:

![image](https://user-images.githubusercontent.com/122887178/229309568-33c90703-ddcd-4957-a327-11c365c73748.png)

Asennuksessa meni noin 5min.

Seuraavaksi otan etäyhteyden master koneeseen, hyväksyn orjien julkiset avaimet ja kokeilen vastaako orjat pingaugseen.

                PS C:\Users\Fredr\Saltdemo> vagrant ssh fmaster
                Linux fmaster 5.10.0-20-amd64 #1 SMP Debian 5.10.158-2 (2022-12-13) x86_64

                The programs included with the Debian GNU/Linux system are free software;
                the exact distribution terms for each program are described in the
                individual files in /usr/share/doc/*/copyright.

                Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
                permitted by applicable law.
                Last login: Sat Apr  1 19:06:11 2023 from 10.0.2.2
                vagrant@fmaster:~$ sudo salt-key -A
                The following keys are going to be accepted:
                Unaccepted Keys:
                f001
                f002
                Proceed? [n/Y] y
                Key for minion f001 accepted.
                Key for minion f002 accepted.
                vagrant@fmaster:~$ sudo salt '*' test.ping
                f002:
                    True
                f001:
                    True
                    
Voin totea että loin 3 konetta ja kaikki toimii. Lopetan tältä päivältä. Kello 22:10

D) Esimerkkejä ja orjien komentelua

Aloitan työskentelyn 2/4/23 kl 0930

Käynnistän virtuaalikoneet komennolla `vagrant up`

Seuraavaksi otan yhteyden master koneeseen.

                PS C:\Users\Fredr\Saltdemo> vagrant ssh fmaster
                Linux fmaster 5.10.0-20-amd64 #1 SMP Debian 5.10.158-2 (2022-12-13) x86_64

                The programs included with the Debian GNU/Linux system are free software;
                the exact distribution terms for each program are described in the
                individual files in /usr/share/doc/*/copyright.

                Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
                permitted by applicable law.
                Last login: Sat Apr  1 19:07:49 2023 from 10.0.2.2
                vagrant@fmaster:~$


Kokeilen saada hieman tietoa koneista:

                vagrant@fmaster:~$ sudo salt '*' cmd.run 'hostname -I'
                f002:
                    10.0.2.15 192.168.12.102
                f001:
                    Minion did not return. [Not connected]
                ERROR: Minions returned with non-zero exit code
                
`f001` orjani ei näköjään ole hengissä. Tarkastetaan pingaamalla se

                vagrant@fmaster:~$ sudo salt 'f001' test.ping
                f001:
                    Minion did not return. [Not connected]
                    
Sammutan orjat komennolla `vagrant halt` ja käynnistän ne uudestaan `Vagrant up`
Otan etäyhteiden master koneeseen

Ja kokeilen pingaa kaikkia orjia taas:

		vagrant@fmaster:~$ sudo salt '*' cmd.run 'hostname -I'
		f002:
		    10.0.2.15 192.168.12.102
		f001:
		    Minion did not return. [Not connected]

Tuhoan f001 ja luon sen uudestaan:

		vagrant destroy f001
		exit
		PS C:\Users\Fredr\Saltdemo> vagrant destroy f001
		    f001: Are you sure you want to destroy the 'f001' VM? [y/N] y
		==> f001: Forcing shutdown of VM...
		==> f001: Destroying VM and associated drives...
		PS C:\Users\Fredr\Saltdemo> vagrant up
		

		
		vagrant@fmaster:~$ sudo salt '*' cmd.run 'hostname -I'
		f002:
		    10.0.2.15 192.168.12.102
		f001:
		    Minion did not return. [Not connected]
		    
	    	
Minulla on näköjään f001 avaimet tuplana:

		vagrant@fmaster:~$ sudo salt-key f002
		Accepted Keys:
		f001
		f002
		Denied Keys:
		f001
		Unaccepted Keys:
		Rejected Keys:
                
		
Poistan f001 avaimet ja hyväksyn ne uudestaan: 

		vagrant@fmaster:~$ sudo salt-key -d f001
		The following keys are going to be deleted:
		Accepted Keys:
		f001
		Denied Keys:
		f001
		Proceed? [N/y] y
		Key for minion f001 deleted.
		Key for minion f001 deleted.
		vagrant@fmaster:~$ sudo salt-key f002
		Accepted Keys:
		f002
		Denied Keys:
		Unaccepted Keys:
		Rejected Keys:
		
Samalla poistin f002 avaimet. Luon avaimet uudestaan `vagrant up` komenolla ja hyväksyn ne molemmat.
Ennen kun teen tämän tarkastan että minulla ei ole avaimia hyväksytty

		vagrant@fmaster:~$ sudo salt-key
		Accepted Keys:
		Denied Keys:
		Unaccepted Keys:
		Rejected Keys:
Annan host koneelta ` vagrant up` komennon ja hyväksyn uudet avaimet:

Tämä ei kuitenkaan luo uusia avaimia.
Selvitän miten saan orjat lähettämään julkiset avaimet master koneelle

Totean että helpoin tapa on tuhoa kaikki koneet ja luoda uudet.
		
		PS C:\Users\Fredr\Saltdemo> vagrant destroy
		    fmaster: Are you sure you want to destroy the 'fmaster' VM? [y/N] y
		==> fmaster: Forcing shutdown of VM...
		==> fmaster: Destroying VM and associated drives...
		    f002: Are you sure you want to destroy the 'f002' VM? [y/N] y
		==> f002: Forcing shutdown of VM...
		==> f002: Destroying VM and associated drives...
		    f001: Are you sure you want to destroy the 'f001' VM? [y/N] y
		==> f001: Forcing shutdown of VM...
		==> f001: Destroying VM and associated drives...
		
		PS C:\Users\Fredr\Saltdemo> vagrant up
		
Odotan noin 10 min ja keitän kahvit itselleni.

Kirjaudun `fmaster`koneelle ja hyväksyn uudet avaimet

		vagrant@fmaster:~$ sudo salt-key -A
		The following keys are going to be accepted:
		Unaccepted Keys:
		f001
		f002
		Proceed? [n/Y] y
		Key for minion f001 accepted.
		Key for minion f002 accepted.
		vagrant@fmaster:~$
		
Noniin tähän jäi siis kysymys. Miten generoin uudet julkiset avaime orjilta herralle?

		vagrant@fmaster:~$ sudo salt '*' grains.item osfinger ipv4
		f001:
		    ----------
		    ipv4:
			- 10.0.2.15
			- 127.0.0.1
			- 192.168.12.100
		    osfinger:
			Debian-11
		f002:
		    ----------
		    ipv4:
			- 10.0.2.15
			- 127.0.0.1
			- 192.168.12.102
		    osfinger:
			Debian-11
			
Seuraavaksi kokeilen idempotenttia ja luon tiedoston nimeltä `testi` jokaiselle orjalle:

		vagrant@fmaster:~$ sudo salt '*' state.single file.managed '/tmp/testi'
		f002:
		----------
			  ID: /tmp/testi
		    Function: file.managed
		      Result: True
		     Comment: Empty file
		     Started: 07:10:35.458743
		    Duration: 4.931 ms
		     Changes:
			      ----------
			      new:
				  file /tmp/testi created

		Summary for f002
		------------
		Succeeded: 1 (changed=1)
		Failed:    0
		------------
		Total states run:     1
		Total run time:   4.931 ms
		f001:
		----------
			  ID: /tmp/testi
		    Function: file.managed
		      Result: True
		     Comment: Empty file
		     Started: 07:10:35.463000
		    Duration: 6.997 ms
		     Changes:
			      ----------
			      new:
				  file /tmp/testi created

		Summary for f001
		------------
		Succeeded: 1 (changed=1)
		Failed:    0
		------------
		Total states run:     1
		Total run time:   6.997 ms
		
Asennan apache kaikille orjille käyttäen idempotenttia:

		vagrant@fmaster:~$ sudo salt --state-output=terse '*' state.single pkg.installed apache2
		f001:
		  Name: apache2 - Function: pkg.installed - Result: Changed Started: - 07:13:24.224072 Duration: 7470.232 ms

		Summary for f001
		------------
		Succeeded: 1 (changed=1)
		Failed:    0
		------------
		Total states run:     1
		Total run time:   7.470 s
		f002:
		  Name: apache2 - Function: pkg.installed - Result: Changed Started: - 07:13:24.236551 Duration: 7878.969 ms

		Summary for f002
		------------
		Succeeded: 1 (changed=1)
		Failed:    0
		------------
		Total states run:     1
		Total run time:   7.879 s
		vagrant@fmaster:~$
		
Käynnistän demonin ja curlaan orjien nettisivut jonka jälkeen sammutan demonit:

		vagrant@fmaster:~$ sudo apt -y install curl
		vagrant@fmaster:~$ sudo salt '*' state.single service.running apache2
		
		vagrant@fmaster:~$ curl -s 192.168.12.102 | grep title
		    <title>Apache2 Debian Default Page: It works</title>
		vagrant@fmaster:~$ curl -s 192.168.12.100 | grep title
		    <title>Apache2 Debian Default Page: It works</title>
	    	vagrant@fmaster:~$ sudo salt '*' state.single service.dead apache2
		
Luon orjille käyttäjän nimeltä `fredrikte01` ja asennan käyttäjälle bashin käyttöön:

		vagrant@fmaster:~$ sudo salt '*' state.single user.present fredrikte01 shell="/bin/bash"
		
Tarkastan että tuliko käyttäjät luotua:

		vagrant@fmaster:~$ sudo salt '*' cmd.run 'cat /etc/passwd |grep fredrikte01'
		f001:
		    fredrikte01:x:1001:1001::/home/fredrikte01:/bin/bash
		f002:
		    fredrikte01:x:1001:1001::/home/fredrikte01:/bin/bash
		    
e) Oma infra koodina

Luon uuden kansion jonne luon `init.sls` tiedoston.

		vagrant@fmaster:/srv/salt/hello$ ls                                                                                     
		init.sls  
		
		$ cat /srv/salt/hello/init.sls
		/tmp/infra-as-code:
		  file.managed

		$ sudo salt '*' state.apply hello
		
Luon toisen tiedoston nimeltä `top.sls` ja kopioin tero karvisen kirjoituksesta seuraavat tiedot:

		$ sudo salt '*' state.apply hello^C
		$ sudoedit /srv/salt/top.sls
		$ cat /srv/salt/top.sls
		base:
		  '*':
		    - hello
		    
Kokeilen ajaa uutta komentoani. Tajuan nopeasti että olen tehnyt virheitä.

Kansioon `/srv/salt` luon tiedoston nimeltä top.sls

		vagrant@fmaster:/srv/salt$ cat top.sls
		base:
		  '*':
		    - hello
Kansioon `/srv/salt/hello` luon tiedoston `init.sls`

		vagrant@fmaster:/srv/salt/hello$ cat init.sls
		hello:
		  cmd.run:
		    - name: echo "Hello World"
		    
Nyt on syntaxi kohdillaan. Kokeilen ajaa komentoa:

		vagrant@fmaster:/srv/salt/hello$ sudo salt '*' state.apply
		f001:
		----------
			  ID: hello
		    Function: cmd.run
			Name: echo "Hello World"
		      Result: True
		     Comment: Command "echo "Hello World"" run
		     Started: 08:11:03.477734
		    Duration: 6.532 ms
		     Changes:
			      ----------
			      pid:
				  4509
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
		Total run time:   6.532 ms
		f002:
		----------
			  ID: hello
		    Function: cmd.run
			Name: echo "Hello World"
		      Result: True
		     Comment: Command "echo "Hello World"" run
		     Started: 08:11:03.540675
		    Duration: 7.388 ms
		     Changes:
			      ----------
			      pid:
				  4506
			      retcode:
				  0
			      stderr:
			      stdout:
				  Hello World

		Summary for f002
		------------
		Succeeded: 1 (changed=1)
		Failed:    0
		------------
		Total states run:     1
		Total run time:   7.388 ms
		
### Yhteenveto {#Yhteenveto}:


		
		
		
		
