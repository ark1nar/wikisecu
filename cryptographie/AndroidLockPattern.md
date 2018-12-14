**Lock Pattern Android / Schémas de verrouillage Android**

Le schémas de verrouillages Android fonctionne de la manière suivante :

Neuf pastilles sont disposées sous la forme d'une grille.

Au niveau logiciel, elles sont numérotées de 1 à 9

```
-------------------
| 1 |  | 2 |  | 3 |
-------------------
| 4 |  | 5 |  | 6 |
-------------------
| 7 |  | 8 |  | 9 |
-------------------
```

Android va stocker notre mot de passe de façon chiffré dans le fichier **/android/data/system/gesture.key**

Dans notre exemple notre code de verrouillage sera 1234.

Avant de chiffré notre code, Android va décrémenter le pattern(schémas) de 1.

1234 deviendra 0123

Celui-ci sera encodé en hexa et ensuite chiffré en sha1.

Ce qui nous donne :

```SHA1("\x00\x01\x02\x03")```


**Exploitation :**

En face du faible nombre de possibilités il est possible de se faire un tableau de correspondance entre le pattern et le hash sha1

__Première méthode : Générer une rainbow table__

Il faut développer un script faisant la correspondance entre le pattern et le sha1 du pattern.

En attendant de proposer un script voici la Rainbow table complète : [Gesture keys](tools/gesture.txt.gz)

md5sum : d5a9204e9ac2aee5e1ddb883580c4e4a

Attention à ne pas oublier d’incrémenter tous les chiffres de 1 pour retrouver le code d'origine.


__Deuxième méthode : Bruteforce__

Le bruteforce dans ce cas est une solution envisageable du fait du faible nombre de combinaisons


Sur github un utilitaire à été développé pour cela : [Crack Android lock pattern](https://github.com/sch3m4/androidpatternlock)

Son utilisation :

```BASH
 python aplc.py gesture.key 
```

```

################################
# Android Pattern Lock Cracker #
#             v0.1             #
# ---------------------------- #
#  Written by Chema Garcia     #
#     http://safetybits.net    #
#     chema@safetybits.net     #
#          @sch3m4             #
################################

[i] Taken from: http://forensics.spreitzenbarth.de/2012/02/28/cracking-the-pattern-lock-on-android/

[:D] The pattern has been FOUND!!! => 0123

[+] Gesture:

 -----  -----  -----
 | 1 |  | 2 |  | 3 |  
 -----  -----  -----
 -----  -----  -----
 | 4 |  |   |  |   |  
 -----  -----  -----
 -----  -----  -----
 |   |  |   |  |   |  
 -----  -----  -----

```

Dans la première partie il vous retourne la clé si il l'a trouvée. (clé à incrémenter de 1 pour avoir la bonne valeur)

Dans la seconde partie il vous explique comment le dessiner au cas où vous n'ayez pas lu la première partie.
