## H3 GIT gud

Tehtävän tarkoituksen on tutustua `GIT` ohjelmaan ja oppia hyödyntämään versiohallinnan tuomat edut.
Tehtävässä tulen toimimaan Host windows koneella sekä virtuaalikoneella jossa on Debian 11 OS

### A) Online

Tee uusi varasto Githubiin ja luo siihen joitain tiedostoja (README.md sekä LICENSE)

Aloitan tehtävän luomalla uuden Repositoryn

<img width="651" alt="image" src="https://user-images.githubusercontent.com/122887178/231420027-b9b3833e-7cbf-4962-aab6-c5cb55bcb4ec.png">

Repoa luodessa varmistan että nimi on julkaisukelpoinen, varasto on julkinen (Muuten ei git toimi hyvin), varastossa on `README.md` sekä GNU3.0 lisenssi.

Varaston nimi: summershine

### B)
Seuraavaksi työskentelen virtuaalikoneella ja luon edellytykset että voin käyttää GITtiä SSH yhteydellä

Aloitan poistamalla julkiset avaimet GIThub tililtä niin että voin aloittaa nollista.

<img width="770" alt="image" src="https://user-images.githubusercontent.com/122887178/231423985-206bc6da-6c4e-4c8d-af78-1c0ac34918e0.png">

Lisään virtuaalikoneen julkisen avaimen GIThub tililleni.

    12:59:37 fredrik@hiekkis:~/.ssh$ cat id_rsa.pub 
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDdEJFji/FyMpH8YAtotXjIxhjSz7QkWQ1icDHu1n0S5Bh2MA435V+nzInPwxpAHENKxmv+vQokQlc8BDGlCaQBPd1BzcmDc+h4bvrsIWGmMhjAcVVsmAEIb/nIvRDvOevzjJ8AL+aAa4BAIBpWcBICCnirYVvp2AHZWJwOFXk8ff43+GshEfVwN1xSdqptF9R8LjOzlrRVHwtm4thFmQ5YJlm8LgfHTVBtCAiP0OBcY7hrBMJjErTLkm81Br9HjZQmUb0FxU0LIgONpgV4XAAmS887SG4s95VORjTNo5hr2x8unqi8n1QQvIgmRqiJ92fWWG19z8mMmqzad1V1FkcRVVdQW5YZx/FkVBG5S+XlPKYuKbFXZsRN19bqumvYqOB0TM3qCzb7LTSePrGeRnmJSUJDLnTaZIFiCyapwBgKtOKxuLoIFASIflkWGCSmZXEKLhvu9VdvdQ7+C9m684hJF0TdDeAwozTYpYR4r42NNj2wbPxlJyiiipmcstXiQxU= fredrik@hiekkis
    
Kopioin manuaalisesti avaimen GIThubiin.'

<img width="754" alt="image" src="https://user-images.githubusercontent.com/122887178/231425017-ec74eef1-1578-4d71-9593-6bdc2bb2873f.png">

Kopioin GIthubista SSH osoitteen

<img width="767" alt="image" src="https://user-images.githubusercontent.com/122887178/231425273-600849e7-192e-4cda-b245-1c881f034b67.png">

Ja cloonaan varaston virtuaalikoneelle:

    3:07:38 fredrik@hiekkis:~$ git clone git@github.com:FredrikAkerlund/summershine.git
    Cloning into 'summershine'...
    remote: Enumerating objects: 4, done.
    remote: Counting objects: 100% (4/4), done.
    remote: Compressing objects: 100% (4/4), done.
    remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
    Receiving objects: 100% (4/4), 12.54 KiB | 12.54 MiB/s, done.
    13:07:46 fredrik@hiekkis:~$ ls
    Desktop    Downloads        koodit  Pictures  public_html  summershine  Videos
    Documents  Eternalsunshine  Music   Public    publicwsgi   Templates
Sieltähän se löyty. Nyt on `summershine` varasto kloonattu virtuaalikoneelle.

Teen ensimmäiset muutokset varastoon virtuaalikoneen kautta. Lisään `testfile.md` tiedoston.

    13:09:40 fredrik@hiekkis:~/summershine$ ls
    LICENSE  README.md  testfile.md

Seuraavaksti teen pull add commit ja push komennot.

    13:10:47 fredrik@hiekkis:~/summershine$ git pull .
    hint: Pulling without specifying how to reconcile divergent branches is
    hint: discouraged. You can squelch this message by running one of the following
    hint: commands sometime before your next pull:
    hint: 
    hint:   git config pull.rebase false  # merge (the default strategy)
    hint:   git config pull.rebase true   # rebase
    hint:   git config pull.ff only       # fast-forward only
    hint: 
    hint: You can replace "git config" with "git config --global" to set a default
    hint: preference for all repositories. You can also pass --rebase, --no-rebase,
    hint: or --ff-only on the command line to override the configured default per
    hint: invocation.
    From .
     * branch            HEAD       -> FETCH_HEAD
    Already up to date
    
    13:11:31 fredrik@hiekkis:~/summershine$ git commit
    [main 88bf1e6] Add testfile.md to summershine repo
     Committer: Fredrik <fredrik@debian-BULLSEYE-live-builder-AMD64>
    Your name and email address were configured automatically based
    on your username and hostname. Please check that they are accurate.
    You can suppress this message by setting them explicitly. Run the
    following command and follow the instructions in your editor to edit
    your configuration file:

        git config --global --edit

    After doing this, you may fix the identity used for this commit with:

        git commit --amend --reset-author

     1 file changed, 1 insertion(+)
     create mode 100644 testfile.md
     
     13:14:34 fredrik@hiekkis:~/summershine$ git push
    Enumerating objects: 4, done.
    Counting objects: 100% (4/4), done.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 391 bytes | 130.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    To github.com:FredrikAkerlund/summershine.git
       c7d15ce..88bf1e6  main -> main
       
 Tarkastetaan GITHUB nettiselaimessa.
 
 <img width="729" alt="image" src="https://user-images.githubusercontent.com/122887178/231428643-397c4c57-275d-4c33-a392-f774c28d0d15.png">


Päivittyhän se. Mahtava homma

### C) DOH!

Teen tyhmiä muutoksia ja korjaan ne ennen pushia.

Muokkaan lisenssi tiedostoa siten ettei se ei ole enään validi.

<img width="975" alt="image" src="https://user-images.githubusercontent.com/122887178/231430080-4f4cf4a4-e954-485b-ad86-9d1fe5935835.png">


    13:23:32 fredrik@hiekkis:~/summershine$ git add .
    13:23:36 fredrik@hiekkis:~/summershine$ git status
    On branch main
    Your branch is up to date with 'origin/main'.

    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
      modified:   LICENSE

Korjaan muutokset:

    13:23:54 fredrik@hiekkis:~/summershine$ git reset --hard
    HEAD is now at 88bf1e6 Add testfile.md to summershine repo

Ja lisenssitiedosto on takaisin alkuperäisessa muodossa:

<img width="933" alt="image" src="https://user-images.githubusercontent.com/122887178/231430895-9f2d6d13-f766-41f9-8ed3-aa3705c897d6.png">


### D) TUKKI lokien tarkastelua

Aloitan muuttamalla sähköpostini GITssa jotta se näkyy järkevänä:

    2032  git config --global user.name "Fredrik Å"
     2033  git config --global user.email "Fredrik@haaga.com"
     2034  git log
     
     13:32:25 fredrik@hiekkis:~/summershine$ git log
    commit a6f718330e28a4da06bd892f5630db3201ac5d1e (HEAD -> main, origin/main, origin/HEAD)
    Author: Fredrik Å <Fredrik@haaga.com>
    Date:   Wed Apr 12 13:31:02 2023 +0300

        Add text to testfile

    commit 88bf1e6c3eb9ff08173ce308d77e573f496d6628
    Author: Fredrik <fredrik@debian-BULLSEYE-live-builder-AMD64>
    Date:   Wed Apr 12 13:11:37 2023 +0300

        Add testfile.md to summershine repo

    commit c7d15cee87401c94b0beabfaaf7dc31cf912a056
    Author: FredrikAkerlund <122887178+FredrikAkerlund@users.noreply.github.com>
    Date:   Wed Apr 12 12:44:30 2023 +0300

        Initial commit
Lokissa näkyy että uusi nimi sekä käyttäjä tulee lokiin vasta näkyviin muutoksien jälkeen. Ekat commitit näkyvät vanhalla default nimellä ja spostilla

### F) Salted räck

Aloitan tekemällä uuden `Salt` tilan. Jotta pääsen vagrant `fmaster` koneelle käytän Host koneeni Windows Powershelliä:

Minun ajatukseni on `Commit && push` Fmaster herrakoneeltani Salt tilat luomaani repoon. Reposta Pullaan sen windows host koneelle jossa teen muutoksia.

Aloitan siirtämällä fmaster julkisen avaimen Github tililleni:

        vagrant@fmaster:~$ cd .ssh
        vagrant@fmaster:~/.ssh$ cat id_rsa.pub
        ssh-rsa XXXXXXX
        vagrant@fmaster:~/.ssh$
        
Kopioin avaime työpöydälle ja liitän sen github tililleni niin kuin A) osiossa.

Seuraavaksi cloonaan Repon Fmaster koneelle:

        vagrant@fmaster:~$ git clone git@github.com:FredrikAkerlund/summershine.git
        Cloning into 'summershine'...
        The authenticity of host 'github.com (140.82.121.3)' can't be established.
        ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
        Are you sure you want to continue connecting (yes/no/[fingerprint])? y
        Please type 'yes', 'no' or the fingerprint: yes
        Warning: Permanently added 'github.com,140.82.121.3' (ECDSA) to the list of known hosts.
        remote: Enumerating objects: 10, done.
        remote: Counting objects: 100% (10/10), done.
        remote: Compressing objects: 100% (8/8), done.
        remote: Total 10 (delta 2), reused 6 (delta 1), pack-reused 0
        Receiving objects: 100% (10/10), 13.10 KiB | 13.10 MiB/s, done.
        Resolving deltas: 100% (2/2), done.
        
        
        vagrant@fmaster:~$ ls
        public_sites  summershine
        vagrant@fmaster:~$ cd summershine/
        vagrant@fmaster:~/summershine$ ls
        LICENSE  README.md  testfile.md
Noin nyt on repot käytettävissä.

Seuraavaksi lataan kaikki salt tilani repoon:

        vagrant@fmaster:~/summershine$ sudo cp -r /srv/salt/ /home/vagrant/summershine/
        vagrant@fmaster:~/summershine$ ls
        LICENSE  README.md  salt  testfile.md
        
Teen Pull, add sekä commit, push komennot:

        vagrant@fmaster:~/summershine$ git add .
        vagrant@fmaster:~/summershine$ git commit .
        Author identity unknown

        *** Please tell me who you are.

        Run

          git config --global user.email "you@example.com"
          git config --global user.name "Your Name"

        to set your account's default identity.
        Omit --global to set the identity only in this repository.

        fatal: empty ident name (for <vagrant@fmaster>) not allowed
        vagrant@fmaster:~/summershine$
        
Saan seuraavan virheen. Muutan tietojani git samalailla miten tein kohdassa D):

Samalla teen commitin:

        vagrant@fmaster:~/summershine$ git config --global user.name "Fmaster"
        vagrant@fmaster:~/summershine$ git config --global user.email "Fmaster@haaga.com"
        vagrant@fmaster:~/summershine$ git commit .
        [main 8a924df] Upload Salt states to repo for test purposes
         7 files changed, 169 insertions(+)
         create mode 100644 salt/apache/frontpage.conf
         create mode 100644 salt/apache/index.html
         create mode 100644 salt/apache/init.sls
         create mode 100644 salt/hello/init.sls
         create mode 100644 salt/ssh/init.sls
         create mode 100644 salt/ssh/sshd_config
         create mode 100644 salt/top.sls
         
Viimeiseksi Pushaan tiedostot repositoryyn:

        vagrant@fmaster:~/summershine$ git push
        Enumerating objects: 14, done.
        Counting objects: 100% (14/14), done.
        Delta compression using up to 2 threads
        Compressing objects: 100% (9/9), done.
        Writing objects: 100% (13/13), 2.78 KiB | 2.78 MiB/s, done.
        Total 13 (delta 0), reused 0 (delta 0), pack-reused 0
        To github.com:FredrikAkerlund/summershine.git
           a6f7183..8a924df  main -> main
        
Tarkastan että hakemisto löytyy githubista:

![image](https://user-images.githubusercontent.com/122887178/232697305-12ef733b-6e04-4d14-a580-993ebc7cca1e.png)


Step 1 suoritettu.

Seuraavaksi päivitän host koneeni repon ja alan työstämään Salt tiloja siellä:
Tähän käytän ohjelmistoa `git bash`. Lähde: https://gitforwindows.org

![image](https://user-images.githubusercontent.com/122887178/232697697-95f58758-2830-4417-9107-f5f8556688ab.png)

Olin jo aikasemmin cloonannut kotitehtävien palautus repon. Lisään uuden `summershine` repon kansioon:

![image](https://user-images.githubusercontent.com/122887178/232698014-4d080246-1eab-4491-a73d-c03acf96c5ef.png)

Avaan hakemiston `vbscode` ohjelmistolla:

![image](https://user-images.githubusercontent.com/122887178/232698650-7172e9e1-3924-47b2-99e9-ba357e61bba4.png)

Teen ohjelmassa uuden salt tilan. Yksinkertaisuuden vuoksi teen yksinkertaisen `Print (hello world again)`

![image](https://user-images.githubusercontent.com/122887178/232700202-92b5747b-10e8-4520-aca7-43e1bc80cbdb.png)

Seuraavaksi teen `git add. && git commit . && git push` suoraan Visual basic studion terminaalissa.

![image](https://user-images.githubusercontent.com/122887178/232700889-18438bbb-b9fe-4834-8055-ace7782ad952.png)

Seuraavaksi menen takaisin Vagrant `fmaster` koneelle ja Pullaan uusimman version reposta:

        vagrant@fmaster:~/summershine/salt/hello$ git pull
        hint: Pulling without specifying how to reconcile divergent branches is
        hint: discouraged. You can squelch this message by running one of the following
        hint: commands sometime before your next pull:
        hint:
        hint:   git config pull.rebase false  # merge (the default strategy)
        hint:   git config pull.rebase true   # rebase
        hint:   git config pull.ff only       # fast-forward only
        hint:
        hint: You can replace "git config" with "git config --global" to set a default
        hint: preference for all repositories. You can also pass --rebase, --no-rebase,
        hint: or --ff-only on the command line to override the configured default per
        hint: invocation.
        Updating 8a924df..1f24387
        fatal: cannot create directory at 'salt/hello2': Permission denied
Virhe viittaa siihen että salt kansiolla on väärät oikeudet
    
        vagrant@fmaster:~/summershine$ ls -l
        total 48
        -rw-r--r-- 1 vagrant vagrant 35149 Apr 18 09:52 LICENSE
        -rw-r--r-- 1 vagrant vagrant   105 Apr 18 09:52 README.md
        drwxr-xr-x 5 root    root     4096 Apr 18 09:55 salt
        -rw-r--r-- 1 vagrant vagrant    80 Apr 18 09:52 testfile.md
        
Näinhän se on. Pelkästään rootilla on oikeus tehdä muutoksia kansioon. Korjaan tämän

        vagrant@fmaster:~/summershine$ sudo chmod ugo+rwx /home/vagrant/summershine/salt/
        
        vagrant@fmaster:~/summershine/salt$ git pull
        hint: Pulling without specifying how to reconcile divergent branches is
        hint: discouraged. You can squelch this message by running one of the following
        hint: commands sometime before your next pull:
        hint:
        hint:   git config pull.rebase false  # merge (the default strategy)
        hint:   git config pull.rebase true   # rebase
        hint:   git config pull.ff only       # fast-forward only
        hint:
        hint: You can replace "git config" with "git config --global" to set a default
        hint: preference for all repositories. You can also pass --rebase, --no-rebase,
        hint: or --ff-only on the command line to override the configured default per
        hint: invocation.
        Updating 8a924df..1f24387
        Fast-forward
         salt/hello2/init.sls | 3 +++
         1 file changed, 3 insertions(+)
         create mode 100644 salt/hello2/init.sls
        
Lisään tiedoston `/srv/salt/` kansioon ja ajan `state.apply`

        
        vagrant@fmaster:~/summershine/salt$ sudo cp -r hello2 /srv/salt/
        vagrant@fmaster:~/summershine/salt$ ls /srv/salt/
        apache/  hello/   hello2/  ssh/     top.sls
        vagrant@fmaster:~/summershine/salt$ ls /srv/salt/hello2/
        init.sls
        vagrant@fmaster:~/summershine/salt$ sudo salt '*' state.apply hello2
        f001:
        ----------
                  ID: hello
            Function: cmd.run
                Name: echo "Hello World again"
              Result: True
             Comment: Command "echo "Hello World again"" run
             Started: 18:40:35.302430
            Duration: 8.673 ms
             Changes:
                      ----------
                      pid:
                          1096
                      retcode:
                          0
                      stderr:
                      stdout:
                          Hello World again

        Summary for f001
        ------------
        Succeeded: 1 (changed=1)
        Failed:    0
        ------------
        Total states run:     1
        Total run time:   8.673 ms
        f002:
        ----------
                  ID: hello
            Function: cmd.run
                Name: echo "Hello World again"
              Result: True
             Comment: Command "echo "Hello World again"" run
             Started: 10:04:16.150046
            Duration: 7.291 ms
             Changes:
                      ----------
                      pid:
                          12353
                      retcode:
                          0
                      stderr:
                      stdout:
                          Hello World again

        Summary for f002
        ------------
        Succeeded: 1 (changed=1)
        Failed:    0
        ------------
        Total states run:     1
        Total run time:   7.291 ms
        
Ja tämähän. Toimi. Tällä tavoin sain siirrettyä herra koneelta Salt tilat github repositoryyn. Josta sain ne pullattua windows host koneelle. Jossa muokkasin sitä Visual Basic Studio editorilla. Ohjelmasta suoraan pushashin muokatun tiedoston repoon. Herra kone pystyi pullaamaan tiedoston ja käyttämään sitä käskeäkseen orjiaan.


