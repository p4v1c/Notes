Cours Rapide sur Vim pour la Programmation
Mode Normal (Navigation et Commandes) :

    h, j, k, l : Déplacement du curseur.
    w : Déplacement d'un mot vers la droite.
    b : Déplacement d'un mot vers la gauche.
    e : Déplacement à la fin du mot.
    0 : Déplacement au début de la ligne.
    $ : Déplacement à la fin de la ligne.
    gg : Aller au début du fichier.
    G : Aller à la fin du fichier.
    :x : Quitter et enregistrer les modifications.

Mode Insertion (Écriture de Code) :

    i : Mode insertion avant le curseur.
    I : Mode insertion au début de la ligne.
    a : Mode insertion après le curseur.
    A : Mode insertion à la fin de la ligne.
    o : Ajouter une nouvelle ligne en dessous et entrer en mode insertion.
    O : Ajouter une nouvelle ligne au-dessus et entrer en mode insertion.

Commandes Utiles :

    :w : Enregistrer le fichier.
    :q : Quitter le fichier.
    :wq : Enregistrer et quitter.
    :x ou :wq : Enregistrer et quitter.
    :q! : Quitter sans enregistrer les modifications.
    u : Annuler la dernière modification.
    Ctrl + r : Rétablir la modification annulée.

Copier/Coller (Mode Visuel) :

    v : Mode visuel pour la sélection.
    V : Mode visuel ligne par ligne.
    y : Copier le texte sélectionné.
    d : Couper le texte sélectionné.
    p : Coller le texte copié/coupé après le curseur.
    P : Coller le texte copié/coupé avant le curseur.

Modifier Plusieurs Lignes Simultanément :

    Ctrl + v : Entrer en mode visuel bloc.
    Sélectionner le texte à modifier.
    Shift + i : Entrer en mode insertion, ajouter/modifier le texte.
    Esc : Sortir du mode insertion.

Autres Raccourcis Utiles :

    :%s/recherche/remplacement/g : Remplacer toutes les occurrences de "recherche"     par "remplacement" dans tout le fichier.
    Ctrl + w puis h/j/k/l : Naviguer entre les fenêtres divisées.