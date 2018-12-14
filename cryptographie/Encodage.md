# Encodage

Voici une liste des encodages courants et les manières de les décoder.

Le premier texte encodé dans tous les cas sera : ceci est un test


__Base64 :__

Caractéristiques :

- Se finit souvent par ==

- Se compose de lettres A-Z/a-z/0-9

- Taille variable en fonction du message


```Y2VjaSBlc3QgdW4gdGVzdA==```

Pour le décoder il existe de multiples fonctions dans tous les langages.

Le site <https://www.base64decode.org/> le fait très bien aussi.

__Morse :__

Caractéristiques :

- Il a un type d'encodage très particulier qui lui permet d'être facilement reconnu

- Taille variable en fonction du message


<code> 
-.-. . -.-. .. / . ... - / ..- -. / - . ... -
</code>

Pour le decoder le site : [[http://www.dcode.fr/code-morse]]

__UUencode :__

Caractéristiques :

* Il a un type d'encodage très particulier qui lui permet d'être facilement reconnu

* Taille variable en fonction du message

```BASH
 -0T5#24535%5.5$535``` `  
```

Pour le décoder le site : <http://www.dcode.fr/uuencode-uudecode>

Sinon il existe un outil uudecode du paquet sharutils sous Linux.

__ASCII - Décimal__

Caractéristiques :

- Suite de chiffres décimal

- Taille variable en fonction du message

```99 101 99 105 32 101 115 116 32 117 110 32 116 101 115 116```

Pour le décoder le site : <http://www.dcode.fr/code-ascii>

