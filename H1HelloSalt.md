## Hello salt 

Alusta: Lenovo ideapad 5 pro, R7 5000 sarja 16gt ram, 500gt SSD

OS: Windows 11

Aika: UTC+2 2030 29/03/23 

Sijainti: Kymenlaakso

Minulla on asenettu Debian 11 virtuaalikone johon on valmiiksi asenettu micro ja apache2 weppipalvelin.


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




