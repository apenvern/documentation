# Git commande line

Quelques commande git en vrac

```sh
#Etat du repo
git status

# commit all
git commit -a -m 'commentaire' 

# Créer une nouvelle branche et swith dessus
git checkout -b ma-branche 

# Merger la branche sur le master
git checkout master
git merge ma-branche

# Supprimer une branche
git branch -d ma-branche

# Mettre à jour une branch depuis le master
# Attention non testé avec des modification sur la branche..
git rebase master

# Regrouper deux commit ensemble <<<
git rebase -i HEAD~10
# squash pour relier 2 commint ensemble qui se suivent !!!

# Stash <<<
git stash
git stash pop

# Stash certain fichier
git add <les fichiers que l on ne veut pas stasher>
git stash --keep-index

# Revert sur le dernier commit 
git reset --soft HEAD~

# Path
# créer un path sur le dernier commit local
git format-patch HEAD~1

# Ajout d'un fichier .gitkeep dans un repertoire vide
find . -type d -empty -not -path "./.git/*" -exec touch {}/.gitkeep \;

# Surpprimer les fichier untrack 
git clean -f -d

```