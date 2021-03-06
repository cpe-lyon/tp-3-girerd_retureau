# Compte-Rendu TP3 - Gestion de paquets
 
&nbsp;

 ## Auteurs et date
 *Thomas GIRERD*
 
 *Guillaume RETUREAU*
 
*Date : 28.02.20*


***

&nbsp;

### Exercice 1. Commandes de base

**Donnez les commandes répondant aux questions suivantes :**

**1. Quels sont les 5 derniers paquets installés sur votre machine ?**

```bash
grep " install " /var/log/dpkg.log | tail -5
```

*Résultats :*

```
2020-02-28 14:41:46 install libbinutils:amd64 <none> 2.30-21ubuntu1~18.04.2
2020-02-28 14:41:46 install binutils-x86-64-linux-gnu:amd64 <none> 2.30-21ubuntu1~18.04.2
2020-02-28 14:41:46 install binutils:amd64 <none> 2.30-21ubuntu1~18.04.2
2020-02-28 14:41:46 install java-common:all <none> 0.68ubuntu1~18.04.1
2020-02-28 14:41:47 install oracle-java11-installer-local:amd64 <none> 11.0.6-2~linuxuprising0
```

&nbsp;

**2. Utiliser dpkg et apt pour compter le nombre de paquets installés (ne pas hésiter à consulter le manuel !).
Comment explique-t-on la (petite) différence de comptage ?**

```bash
dpkg --list | wc -l
```

*Résultats :*

```
525
```

***

```bash
apt list --installed | wc -l
```

*Résultats :*

```
521
```

*`wc` compte le nombre de lignes, or `dpkg` affiche ses résultats dans un tableau et il y a quelques lignes avant le début de l'affichage des paquets, d'où le résultat supérieur à celui d'`apt`.*

&nbsp;

**3. Combien de paquets sont disponibles en téléchargement ?**

```bash
sudo apt-cache pkgnames | wc -l
```

*Résultats :*

```bash
65462
```

&nbsp;

**4. Créer un alias “maj” qui met à jour le système**

*Dans le fichier .bash_aliases, on va écrire la ligne suivante :*

```bash
alias maj='apt update & upgrade'
```

*Puis on tape la commande :*

```
source .bashrc
```

*Cela permet de recharger le fichier .bashrc et ainsi actualiser les alias.*

&nbsp;

**5. A quoi sert le paquet fortunes ? Installez-le.**

*Le paquet fortunes permet d'afficher de petits messages, des citations, des proverbes dans le terminal.*

&nbsp;

**6. Quels paquets proposent de jouer au sudoku ?**

* *xenial*
* *bionic*
* *disco*
* *eoan*
* *focal*

&nbsp;

**7. Lister les derniers paquets installés explicitement avec la commande apt install**

```bash
apt list --manual-installed
```


***

&nbsp;

### Exercice 2

**1. A partir de quel paquet est installée la commande ls ?**

*GNU coreutils*

&nbsp;

**2. Comment obtenir cette information en une seule
commande, pour n’importe quel programme (indice : la réponse est dans le poly de cours 2, dans la liste des
commandes utiles) ?**

```bash
dpkg -S [commandName]
```

*Attention, il faut indiquer le chemin de la commande, exemple :*

```bash
dpkg -S /bin/ls
```

*Qui nous donne :*

```bash
coreutils: /bin/ls
```
&nbsp;

**3. Utilisez la réponse à cette commande pour écrire un script appelé origine-commande (sans l’extension .sh) prenant en argument le nom d’une commande, et indiquant quel paquet l’a installée.**
```bash
touch origine-commande
```

*Dans origine-commande:*

```bash
dpkg -S [commandName] | cut -d':' -f 1
```


***

&nbsp;

### Exercice 3

**Ecrire une commande qui affiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package spécifié dans cette commande.**

```bash
(dpkg -l npm | grep "^i" > /dev/null ) && echo "INSTALLÉ" || echo "NON INSTALLÉ"
```


***

&nbsp;

### Exercice 4

**Lister les programmes livrés avec coreutils. A quoi sert la commande ’[’ et comment afficher ce qu’elle retourne ?**

```bash
dpkg-query -L coreutils
```

*La commande sert à vérifier le type de fichiers et à comparer des valeurs.*

*On peut afficher ce qu'elle retourne avec la commande suivante :*

```bash
[ [test] && echo "True" || echo "False"
```

***

&nbsp;

### Exercice 5 : aptitude

**Installez le paquet emacs à l’aide de la version graphique d’aptitude**
On installe aptitude
`sudo apt install aptitude`
&nbsp;

*On appuie sur "Control+T" puis avec les flèches directionnelles, on choisit "search"*

![aptitude 1](https://github.com/cpe-lyon/tp-3-girerd_retureau/blob/master/aptitude1.png)

*On appuie sur "Entrer" et on choisit le package "emacs25-dbg"
puis "Control+T" -> package -> install*

![aptitude 2](https://github.com/cpe-lyon/tp-3-girerd_retureau/blob/master/aptitude2.png)

*Enfin, on appuie sur "Control+T" -> actions -> install/remove packages 2 fois*

***

&nbsp;

### Exercice 6 : Installation d’un paquet par PPA

**1. Installer la version Oracle de Java (avec l’ajout des PPA)**

```bash
sudo add-apt-repository ppa:linuxuprising/java
sudo apt update
sudo apt install oracle-java11-installer
```

&nbsp;

**2. Vérifiez qu’un nouveau fichier a été créé dans /etc/apt/sources.list.d. Que contient-il ?**

*Dans le dossier `/etc/apt/sources.list.d`, on a le fichier :*
`linuxuprising-ubuntu-java-bionic.list`

*Qui contient :*

```
deb http://ppa.launchpad.net/linuxuprising/java/ubuntu bionic main
# deb-src http://ppa.launchpad.net/linuxuprising/java/ubuntu bionic main
```

***

&nbsp;

### Exercice 7. Création de dépôt personnalisé

**Dans cet exercice, vous allez créer vos propres paquets et dépôts, ce qui vous permettra de gérer les programmes que vous écrivez comme s’ils provenaient de dépôts officiels.**

##### Création d’un paquet Debian avec dpkg-deb

**1. Dans le dossier scripts créé lors du TP 2, créez un sous-dossier origine-commande où vous créerez un sous-dossier DEBIAN, ainsi que l’arborescence usr/local/bin où vous placerez le script écrit à l’exercice**

*On exécute la commande :*
```
cd ~/scripts
mkdir -p origine-commande/DEBIAN/usr/local/bin
```

&nbsp;

**2. Dans le dossier DEBIAN, créez un fichier control avec les champs suivants :**

**Package: origine-commande #nom du paquet**

**Version: 0.1 #numéro de version**

**Maintainer: Foo Bar #votre nom**

**Architecture: all #les architectures cibles de notre paquet (i386, amd64...)**

**Description: Cherche l'origine d'une commande**

**Section: utils #notre programme est un utilitaire**

**Priority: optional #ce n'est pas un paquet indispendable**


*On exécute la commande :*

`nano origine-commande/DEBIAN/control`

*Et on écrit dans ce dernier :*

```
Package: origine-commande
Version: 0.1
Maintainer: Foo Bar
Architecture: all
Description: Cherche l'origine d'une commande
Section: utils
Priority: optional
```

&nbsp;

**3. Revenez dans le dossier parent de origine-commande (normalement, c’est votre $HOME) et tapez la commande suivante pour construire le paquet :**

```bash
dpkg-deb --build origine-commande
```

**Félicitations ! Vous avez créé votre propre paquet !**

***

&nbsp;

#### Création du dépôt personnel avec reprepro

**1. Dans votre dossier personnel, commencez par créer un dossier repo-cpe. Ce sera la racine de votre dépôt**

*On exécute les commandes :*

```
mkdir repo-cpe
cd repo-cpe
```

&nbsp;

**2. Ajoutez-y deux sous-dossiers : conf (qui contiendra la configuration du dépôt) et packages (qui contiendra nos paquets)**

```
mkdir conf
mkdir packages
```

&nbsp;

**3. Dans conf, créez le fichier distributions suivant :**

**Origin: Un nom, une URL, ou tout texte expliquant la provenance du dépôt**

**Label: Nom du dépôt**

**// Suite: stable**

**Codename: cosmic #le nom de la distribution cible**

**Architectures: i386 amd64 #(architectures cibles)**

**Components: universe #(correspond à notre cas)**

**Description: Une description du dépôt**



*On exécute la commande :*

`nano conf/distributions`

*On remplit le fichier avec :*

```
Origin: TP CPE
Label: repo-cpe
Codename: cosmic
Architectures: i386 amd64
Components: universe
Description: Depot exemple de CPE
```

&nbsp;

**4. Dans le dossier repo-cpe, générez l’arborescence du dépôt avec la commande ``reprepro -b . export``**

```
reprepro -b . export
```

*Résultats :*

```
Command 'reprepro' not found, but can be installed with:

apt install reprepro
```

*On l'installe et on l'exécute à nouveau.*

&nbsp;

**5. Copiez le paquet ``origine-commande.deb`` créé précédemment dans le dossier packages du dépôt, puis, à la racine du dépôt, exécutez la commande `reprepro -b . includedeb cosmic origine-commande.deb` afin que votre paquet soit inscrit dans le dépôt.**


```bash
cd ~
cp scripts/origine-commande.deb repo-cpe/packages/
reprepro -b . includedeb cosmic packages/origine-commande.deb
```
*Note : on a ajouté packages au path du .deb car il n'est pas dans la racine du repo.*

&nbsp;

**6. Il faut à présent indiquer à apt qu’il existe un nouveau dépôt dans lequel il peut trouver des logiciels. Pour cela, créez (avec sudo) dans le dossier ``/etc/apt/sources.list.d`` le fichier repo-cpe.list contenant :**

```bash
deb file:/home/VOTRE_NOM/repo-cpe cosmic multiverse
```

**(cette ligne reprend la configuration du dépôt, elle est à adapter au besoin)**

*Commande exécutée :*

```bash
deb file:/home/herysia/repo-cpe cosmic multiverse
```

&nbsp;

**7. Lancez la commande sudo apt update.**

```
herysia@server:~$ sudo apt update
[sudo] password for herysia: 
Get:1 file:/home/herysia/repo-cpe cosmic InRelease
Ign:1 file:/home/herysia/repo-cpe cosmic InRelease
Get:2 file:/home/herysia/repo-cpe cosmic Release [1,672 B]
Get:2 file:/home/herysia/repo-cpe cosmic Release [1,672 B]
Get:3 file:/home/herysia/repo-cpe cosmic Release.gpg                                                 
Ign:3 file:/home/herysia/repo-cpe cosmic Release.gpg                                                 
Hit:4 http://ppa.launchpad.net/linuxuprising/java/ubuntu bionic InRelease                            
Hit:5 http://security.ubuntu.com/ubuntu bionic-security InRelease                                    
Hit:6 http://us.archive.ubuntu.com/ubuntu bionic InRelease
Hit:7 http://us.archive.ubuntu.com/ubuntu bionic-updates InRelease
Hit:8 http://us.archive.ubuntu.com/ubuntu bionic-backports InRelease
Reading package lists... Done                      
E: The repository 'file:/home/herysia/repo-cpe cosmic Release' is not signed.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```

*Note : on remarque qu'il y a une erreur lors de la mise à jour de cosmic Release (depuis repo-cpe) car il n'est pas signé. On la corrigera avec GPG.*

**Félicitations ! Votre dépôt est désormais pris en compte ! ... Enfin, pas tout à fait... Si vous regardez la sortie d’``apt update``, il est précidé que le dépôt ne peut être pris en compte car il n’est pas signé. La signature permet de vérifier qu’un paquet provient bien du bon dépôt. On doit donc signer notre
dépôt.**

&nbsp;

***

&nbsp;

#### Signature du dépôt avec GPG

**GPG est la version GNU du protocole PGP (Pretty Good Privacy), qui permet d’échanger des données de manière sécurisée. Ce système repose sur la notion de clés de chiffrement asymétriques (une clé publique et
une clé privée)**

**1. Commencez par créer une nouvelle paire de clés avec la commande**

```bash
gpg --gen-key
```

*Cette commande n'a pas abouti après plus d'une heure à deux reprises*

**Attention ! N’oubliez pas votre passphrase !!!**

&nbsp;

**2. Ajoutez à la configuration du dépôt (fichier distributions la ligne suivante :
SignWith: yes**

*Fait.*

&nbsp;

**3. Ajoutez la clé à votre dépôt :**

```bash
reprepro --ask-passphrase -b . export
```

**Attention ! Cette méthode n’est pas sécurisée et obsolète ; dans un contexte professionnel, on utiliserait plutôt un gpg-agent.**

&nbsp;

**4. Ajoutez votre clé publique à votre dépôt avec la commande**

```bash
gpg --export -a "auteur" > public.key
```

&nbsp;

**5. Enfin, ajoutez cette clé à la liste des clés fiables connues de apt :**

```bash
sudo apt-key add public.key
```

**Félicitations ! La configuration est (enfin) terminée ! Vérifiez que vous pouvez installer votre paquet comme n’importe quel autre paquet.**

&nbsp;

***

&nbsp;

### Exercice 8 :  Installation d’un logiciel à partir du code source

**Lorsqu’un logiciel n’est disponible ni dans les dépôts officiels, ni dans un PPA, ou encore parce qu’on souhaite n’installer qu’une partie de ses fonctionnalités, on peut se tourner vers la compilation du code
source.
Malheureusement, cette installation ”à la main” fait qu’on ne propose pas des bénéfices de la gestion de paquets apportée par ``dpkg`` ou ``apt``. Heureusement, il est possible de transformer un logiciel installé ”à la
main” en un paquet, et de le gérer ensuite avec apt ; c’est ce que permet par exemple ``checkinstall``.**

**1. Commencez par cloner le dépôt git suivant :**

```
git clone https://github.com/jubalh/nudoku
```

**Ceci permet de récupérer en local le code source du logiciel nudoku.**

&nbsp;

**2. Rendez vous dans le dossier ``nudoku`` qui vient d’être créé et lancez la commande ``autoreconf -i ``(ainsi que spécifié dans le fichier README.md). A vous d’installer les éventuels paquets manquants (un peu d’aide : pour résoudre le problème de la macro AM_GNU_GETTEXT manquante, installez le paquet gettext). Relancez la commande `autoreconf -i` après chaque paquet installé jusqu’à ce qu’elle se termine sans erreur.**

*Fait.*

&nbsp;

**3. Exécutez le script configure**

**4. Normalement, à cette étape on exécute la commande `` make install``, qui procède à la compilation proprement dite et à l’installation (copie des fichiers compilés dans leur dossier de destination). Mais dans notre cas, on va demander à ``checkinstall`` de s’en charger et de créer un paquet au format .deb :**

```bash
sudo checkinstall
```

**Le logiciel est à présent installé (exécutez ``nudoku`` pour vous en assurer) ; on peut vérifier par exemple avec aptitude qu’il provient bien du paquet qu’on a créé avec ``checkinstall``.**
