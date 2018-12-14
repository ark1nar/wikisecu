#Faille Web : LFI : Local File Inclusion

L’objet de l’attaque, comme son nom l’indique, est d’inclure un fichier local . 
L’intérêt est multiple :

* D’accéder au code source de fichiers privés stockés sur le serveur ciblé par l’attaque.

* D’exécuter un script disponible sur le serveur dans un contexte non conventionnel.

Cette faille est appelée ainsi car elle est en rapport avec le nom de la fonction PHP utilisée pour inclure un flux (include)

Explication en pratique :

##1.DETECTION

Si je tape cette url :

<http://localhost/lfi.php?page=../../../../../>

../ signifie : remonter d'un dossier,
 si on le multiplie un certain nombre de fois , nous remontons jusqu'à la racine du site qui est généralement /var/www/

Si le script plante, c’est que l’application est très probablement vulnérable ou que le paramètre est filtré.

Les erreurs pouvant être affichées par PHP pourraient être les suivantes :
//“failed to open stream: No such file or directory”//

Si cette erreur est affichée, la faille est avérée.


##2. ANGLE D'ATTAQUE

__La LFI Basique :__

Imaginons qu’il existe un fichier nommé “password.xml” dans un sous-dossier “admin”, en temps normal ce dossier est interdit aux utilisateurs.

Cependant grâce à la LFI il serait possible d’afficher son contenu en appelant la ressource comme il suit :

<http://localhost/lfi.php?page=admin/password.xml>

Le contenu de la page password s'affichera sous vos yeux.

__La LFI avec du Null Byte__

Il arrive parfois que les personnes qui développent les sites utilisent la fonction include comme ceci :
//include($page . “.html”);//

Ce qui à pour but d'ajouter une extension .html automatiquement à la fin du fichier inclus.

Dans n’importe quel langage, une chaîne de caractères se termine par un caractère de fin de chaîne (ou NULL). 

Ce caractère est représenté en hexadécimal, dans la table ASCII, par la valeur x00.

Les personnes qui développent en C sont familiarisées avec ce caractère.

Pour stipuler la fin de notre chaine de caractère nous allons rajouter à la fin de notre url %00

Cela nous donne: <http://localhost/lfi.php?page=admin/password.xml%00>



__Les Wrappers Php__

Pour récupérer le code source d'une page php, en utilisant la technique basique, le serveur vous affichera la page php mais exécuté par celui-ci. 

Pour récupérer son code source il faut utiliser une autre technique :

Le principe consiste à afficher le code source de la page en base 64 comme ceci :

<http://localhost/lfi.php?page=php://filter/read=convert.base64-encode/resource=lfi.php>

Cet exemple vous permettra d’afficher le contenu du script “lfi.php” encodé en base 64, soit 

```
PD9waHAgDQokcGFnZSA9IGFycmF5X2tleV9leGlzdHMoJ3BhZ2UnLCAkX0dFVCkgPyAkX0dFVFsncGFnZSddIDogbnVsbCA7DQppZiAoIWlzX251bGwoJHBhZ2UpKQ0Kew0KCWluY2x1ZGUoJHBhZ2UpOw0KfQ0KZWxzZQ0Kew0KCWVjaG8gIkF1Y3VuIHBhZ2Ug4CBpbmNsdXJlLi4uIjsNCn0NCj8+DQo=</code>
```
Pour récupérer le code source en clair, il suffit seulement d’effectuer le traitement inverse.

Si vous n’avez pas les outils pour le faire en local, sachez qu’il en existe de nombreux en ligne, comme par exemple : <http://www.base64decode.org>

__Les LFI avancées__

Certaines LFI ne se trouvent pas facilement mais une technique permet d'échapper à certains filtres :

L'encodage url permet comme son nom l'indique d'encoder une url pour éviter les filtres sur certains caractères.

Par exemple si le serveur filtre les / dans les paramètres pris par la variable page

<http://localhost/lfi.php?page=>

Si nous effectuons une LFI Basique nous nous retrouverons en face d'un "FORBIDDEN ACCESS"

Quoi de plus frustrant, mais si nous réessayons comme ceci :

<http://localhost/lfi.php?page=admin%2fpassword.xml>

Nous aurons accès ! Lorsque l'on encode une url le caractère / deviens %2f

Vous pouvez encoder des url entières grâce au site : <http://meyerweb.com/eric/tools/dencoder>

Exemple réaliste : <https://www.exploit-db.com/exploits/16250/>

source : <http://blog.clever-age.com/fr/2014/10/21/owasp-local-remote-file-inclusion-lfi-rfi/>
