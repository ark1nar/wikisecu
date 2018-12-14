# El Gamal - Logarithme Discret

## 1.Presentation

El Gamal est un algorithme à clef publique présent à la base de la norme U.S. de signature électronique.

Il fut inventé par Taher ElGamal en 1984. Sa sécurité repose, comme le protocole de Diffie et Hellman, sur la difficulté de calculer le logarithme discret.

Exemple :

Le destinataire, Bob, possède deux clés :

* une clé secrète (privé) : un entier c

* une clé publique, qui consiste en un nombre premier p
```d = a^c mod b```

où :

* a est la racine primitive de b

* p est un nombre premier

* c est un nombre entier choisis aléatoirement

pour que a soit la racine primitive de b il faut que :

PGCD (a,b)=1

Exemple :

PGCD (20,30) donne 1,2,5,10 donc ce n'est pas une racine primitive

PGCD (2,5) donne 1 , donc nous avons une racine primitive 

__Chiffrement :__

Soit un message M , avec M < b . 
On détermine un nombre aléatoire k qui n’est connu que de celui qui chiffre et différent à chaque message.

On calcule alors :
```
C1 = a^k mod p 
C2 = M*d^k mod p
```
On obtient alors le message chiffré
```
E = ( C1 , C2)
```

Le message chiffré est alors deux fois plus long que le message original.

__Déchiffrement :__

A la réception, on calcule :
```
R1 = (C1)^c mod b 
   = a^ck mod b
   = d^k mod b
```

Le destinataire possède la clé privée ( c ).

Ayant d^k , on divise C2 par cette valeur : 
```
DK(E) = C2 / (R1) 
      = M*d^k mod p/P^k mod p 
      = M
```


**2. Exploitation**

Pour faire la comparaison , le probleme du logarithme discret dans le chiffrement Elgamal est ce qu'est la factorisation au RSA.

Le problème du logarithme discret consiste à retrouver un entier c tel que :
```d = a^c mod b```

Le plus simple étant d'utiliser Sagemath : [Télécharger ici](http://www.sagemath.org/download-linux.html)

Prenons la clé publique de Bob (a,d,b) :
a=2
d=3
b=5 

```PYTHON
Sur sage :
sage: a=2
sage: b=5
sage: d=3
sage: e=mod(a,b)
sage: e
2
sage: discrete_log(d,e)
3     <= Ici nous avons la clé privé
```
Si vous voulez le détail d'une fonction dans sagemath , rajoutez un ? exemple :

```
sage: mod?
```

La fonction mod fait : 

e = a mod b

La fonction discrete_log est la fonction mathématique qui va retrouver la clé privé.


**3.Comment se protéger**

Lorsque vous créez vos clés privés avec le chiffrement elgamal choisissez une clé de minimum 2048 bits.

```
gpg --gen-key

Sélectionnez le type de clef désiré :
   (1) RSA et RSA (par défaut)
   (2) DSA et Elgamal
   (3) DSA (signature seule)
   (4) RSA (signature seule)
Quel est votre choix ? 2
les clefs DSA peuvent faire une taille comprise entre 1024 et 3072 bits.
Quelle taille de clef désirez-vous ? (2048) 2048
La taille demandée est 2048 bits
```

source : [bibmath](http://www.bibmath.net/crypto/index.php?action=affiche&quoi=moderne/elgamal)
	
[zenk-security](https://repo.zenk-security.com/Cryptographie%20.%20Algorithmes%20.%20Steganographie/Le%20chiffrement%20par%20cle%20publique.pdf)
