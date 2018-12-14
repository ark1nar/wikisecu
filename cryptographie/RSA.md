# RSA

Il existe différents algorithmes asymétriques. L'un des plus connus est le RSA (de ses concepteurs Rivest, Shamir et Adleman). Cet algorithme est très largement utilisé, par exemple dans les navigateurs pour les sites sécurisés et pour chiffrer les emails. Il relève désormais du domaine public.

L'algorithme est remarquable par sa simplicité. Il est basé sur les nombres premiers.

Fonctionnement de l'algorithme : 
[RSA Fonctionnement](http://sebsauvage.net/comprendre/encryptage/crypto_rsa.html)

**Exploitation :**

Les outils nécessaires pour recréer une clé privé à partir d'une clé publique sont :

* [Factor DB](http://factordb.com/) : pour la factorisation du modulo

* Openssl : pour récupérer les infos de la clé privé

* [Get private key](tools/get_priv_key.c) : pour recréer la clé privée

* [Convertisseur de grands nombres](http://www.mobilefish.com/services/big_number/big_number.php) : pour passer de l'hexa au décimal.

__Etape 1 :__

On récupère les informations contenues dans la clé publique.

```BASH
openssl rsa -in pubkey.pem -pubin -text -modulus
```
ça nous donne l'exposant et le modulo

__Etape 2 :__

Il faut convertir le modulo qui est en hexa en décimal.

* Soit avec le site : [Convertisseur de grands nombres](http://www.mobilefish.com/services/big_number/big_number.php)

* Soit avec Python : 
 ```BASH
python -c " print int('modulo_en_hexa')"
```

__Etape 3 :__

Il faut obtenir p & q , les facteurs.

Pour cela nous devons factoriser notre modulo qui est en décimal.

Un site le fait très bien pour nous.

<http://factordb.com/index.php?query=mon_modulo_en_decimal>

Si factordb est capable de le factoriser il affiche les deux facteurs.

Si factordb n'est pas capable de le factoriser il faudra utiliser une autre attaque.

__Etape 4 :__

On va générer la clé privée avec le script gen key

Il faut d'abord le compiler :
```BASH
gcc -o get_priv_key get_priv_key.c -lssl -lcrypto 
```

Création de la clé privé : 
```BASH
./get_priv_key.bin p q exposant > ma_cle_priv.pem
```
__Etape 5 :__

Une fois qu'on a la clé privé on peut par exemple se connecter en ssh avec la commande :

```BASH
ssh isileaks@wikisecu.fr -i ma_cle_priv.pem
```
ou encore déchiffrer un fichier avec les commandes :

```BASH
openssl enc -in fichier_chiffre.txt -out textbinaire -d -a 
```

```BASH
openssl rsautl -decrypt -in textbinaire -out plaintext -inkey ma_cle_priv.pem
```

Source : <http://repo.mynooblife.org/.priv8/Mag/Hackin9/CD/tutoriels/rsa/rsa.html>
