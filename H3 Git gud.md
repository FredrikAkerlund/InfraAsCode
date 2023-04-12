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



