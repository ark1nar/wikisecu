# Les failles Upload

La faille upload consiste à uploader un fichier au format inattendu dans un formulaire.

Un formulaire standard non sécurisé est développé comme ceci :

```HTML
<form method="POST" action="upload.php" enctype="multipart/form-data">
     Fichier : <input type="file" name="avatar">
     <input type="submit" name="envoyer" value="Envoyer le fichier">
</form>
```

Ici le fichier sera envoyé vers la page upload.php qui le traitera ou non.
```PHP

<?php
$target_path  =  "uploads/";
$target_path  =  $target_path  .  basename($_FILES['uploadedfile']['name']);
if (move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
echo "The file " . basename($_FILES['uploadedfile']['name']) . " has been uploaded";
} else {
echo "There was an error uploading the file, please try again!";
}
?>

```

Ici aucune extension n'est contrôlée.


## Exploitation : 

Le but est d'uploader un fichier php qui sera interprété par le serveur.


Le fichier que nous uploaderons : exploit.php
```PHP
<?php echo 'Best Hacker'; ?>
```

Dans notre exploitation le formulaire n'acceptera que les fichiers en .pdf et en .doc


__Première méthode : La double extension__

Le but est de rajouter une deuxième extension pour passer la validation.

Nous renommons notre fichier : exploit.php en exploit.php.pdf

Quand nous visualiserons notre fichier sur le serveur nous pourrons voir :

``` Best Hacker ```


__Deuxième méthode : Mime type__

MIME est un identifiant de format de données sur internet en deux parties. 

exemple : 

image/jpeg pour les photos en jpeg

text/csv pour les fichiers en csv

Il en existe une multitude que vous pourrez trouver ici : <http://www.freeformatter.com/mime-types-list.html>
Certains développeurs pensent que la validation du fichier peut se réaliser par une simple comparaison du type MIME. FAUX !

Il faut modifier les paramètre envoyé en POST au serveur :

Pour cela l’add-on firefox  "Tamper Data" est très pratique.
Nous allons altérer les données pendant l’upload de notre fichier .php ce qui nous donne dans la valeur du paramètre post :

```
Content-Disposition: form-data;
name="file";
filename="exploit.php"\r\n
Content-Type: application/x-php\r\n\r\n<?php\necho 'Best hacker'\n?>\n\r\n
```

Il suffit donc de modifier 
```
"Content-Type : application/x-php"
 par "Content-Type : application/msword"
```

Cela fera croire au serveur qu'il reçoit un fichier .doc au lieu du fichier .php.


__Troisième méthode : Le null byte__

Le null byte est un caractère qui signifie la fin de chaîne.
C'est une notion utilisé en C. Le PHP étant écrit en C il fait fasse au même problèmes.
Une fin de chaîne est signifié par le caractère \0

Pour l'exploiter nous utiliserons la technique suivante :

Renommer notre fichier exploit.php en exploit.php%00.pdf
```BASH
mv exploit.php exploit.php%00.pdf
```

source : <http://www.acunetix.com/websitesecurity/upload-forms-threat/>
