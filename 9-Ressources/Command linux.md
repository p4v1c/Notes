
##### Introduction à awk

awk est utilisé pour manipuler des données structurées en lignes et colonnes. Il peut effectuer des opérations de filtrage, de transformation, et de calcul sur des fichiers texte.

- Syntaxe de Base:

```sh
awk 'motif { action }' fichier
```

motif : Condition pour exécuter une action.
action : Instructions exécutées si le motif est vrai.

**Exemples d'Utilisation:**

- Afficher une colonne spécifique :

```bash
awk '{print $2}' fichier.txt
```

Calculer une somme :

```bash
awk '{somme += $2} END {print somme}' fichier.txt
```

Filtrer des lignes :

```bash
awk '$2 > 100' fichier.txt
```
Affiche les lignes où la deuxième colonne est supérieure à 100.

- Fonctions BEGIN et END

BEGIN : Exécute avant de lire les lignes.
END : Exécute après avoir lu toutes les lignes.

- Exemple avec BEGIN et END

```bash
awk 'BEGIN {print "Début"} {print $0} END {print "Fin"}' fichier.txt
```


##### Introduction à sed

sed est utilisé pour la manipulation de flux de texte. Il peut effectuer des opérations de recherche et remplacement, supprimer des lignes, insérer du texte, etc.
Syntaxe de Base

```bash
sed 'commande' fichier
````
commande : Instruction à appliquer sur chaque ligne.

**Exemples d'Utilisation**

- Remplacer du texte :

```bash
sed 's/ancien/nouveau/g' fichier.txt
```
Remplace toutes les occurrences de "ancien" par "nouveau".

- Supprimer des lignes :

```bash
sed '/motif/d' fichier.txt
```
Supprime les lignes contenant "motif".

- Insérer du texte avant une ligne :

```bash
sed '/motif/i Texte ajouté avant' fichier.txt
```
Ajoute "Texte ajouté avant" avant chaque ligne contenant "motif".

- Supprime les lignes qui contiennent uniquement produit
```sh
sed -e '/^Produit$/d' fichier.txt 
```

- Supprime les lignes vides.
```sh
sed -e '/^$/d' fichier.txt
```

Then you can also combien multiple sed commande : 
```sh
awk '{print $2}' ban.txt | sed -e '/^Produit$/d' -e '/^$/d'
```

##### Introduction à grep

- Rechercher un motif simple :
```bash
grep 'motif' fichier
```

- Rechercher en ignorant la casse :

```bash
grep -i 'motif' fichier.txt
```

- Afficher les numéros de ligne :

```bash
grep -n 'motif' fichier.txt
```

##### WC 

```sh
-l : Compte le nombre de lignes. 
-w : Compte le nombre de mots. 
-c : Compte le nombre de caractères. 
-m : Compte le nombre de caractères (identique à -c mais pour les caractères multioctets). 
-L : Affiche la longueur de la ligne la plus longue.
```

##### Sort 
```sh
-n : Trie numériquement. 
-r : Trie dans l ordre inverse. 
-k : Trie par clé (colonne) spécifiée. 
-u : Trie et supprime les doublons.
```

Example : 

- Trier par ordre alphabétique :
```bash
sort fichier.txt
```
- Trier numériquement la troisième colonne :
```bash
sort -k 3 -n fichier.txt
```
- Trier en ordre inverse :
```bash
sort -r fichier.txt
```

##### Uniq

La commande `uniq` supprime les doublons consécutifs dans un fichier trié. 

```sh
-c : Précède chaque ligne par le nombre de fois qu elle apparaît.
-d : Affiche uniquement les lignes dupliquées.
-u : Affiche uniquement les lignes uniques.
```

##### Head
La commande head affiche les premières lignes d'un fichier.

```sh
 -n : Spécifie le nombre de lignes à afficher.
```

##### tail
La commande head affiche les dernières lignes d'un fichier.

```sh
 -n : Spécifie le nombre de lignes à afficher.
```

##### Cut

La commande cut extrait des sections de chaque ligne d'un fichier.

```sh
-d : Spécifie le délimiteur (par défaut, la tabulation).
-f : Spécifie les champs (colonnes) à extraire.
```

- Extraire la deuxième colonne :

```bash
cut -d ' ' -f 2 fichier.txt
```
- Extraire les colonnes 2 à 4 :

```bash
cut -d ' ' -f 2-4 fichier.txt
```