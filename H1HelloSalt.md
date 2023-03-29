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
        Error while connecting to Libvirt: Error making a connection to libvirt URI qemu:///system?no_verify=1&keyfile=/home/fredrik/.ssh/id_rsa:
        Call to virConnectOpen failed: Failed to connect socket to '/var/run/libvirt/libvirt-sock': No such file or directory

Sama ongelma. En tiedä mikä ongelma on.

Lopetin ongelmanratkaisua 21.30 jatkan tehtävää myöhemmin.





