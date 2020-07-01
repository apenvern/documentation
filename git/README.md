# Git commande line

## Cheatsheet

- [Atlassian](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)
- [Github](https://github.github.com/training-kit/downloads/github-git-cheat-sheet.pdf)

## Configuration de Git

Git have **3 levels** of configuration takes in count in this order:

- **Local** (repository) : /repository/.git/config
- **Global** (user directory) : /Users/name/.gitconfig
- **System** (git installation) : /usr/local/etc/gitconfig

```sh
# List all configurations
git config --list --show-origin

# Edit file config directly
git config --global --edit
```

Sample for **global** configuration

```sh

[user]
    name = name
    email = email
[core]
    autocrlf = input
    longpaths = true
    # Editor Tool
    editor = code --new-window --wait

# Proxy settings
[http]
    proxy = http://host:port
[https]
    proxy = http://host:port

# Diff Tool
[diff]
    tool = vscode-diff
[difftool]
    prompt = false
[difftool "vscode-diff"]
    cmd = code --wait --diff $LOCAL $REMOTE
# Merge Tool
[merge]
    tool = vscode-merge
[mergetool]
    keepBackup = false
[mergetool "vscode-merge"]
    cmd = code --wait $MERGED

```

## Git Bisect

Git bisect it's tool that help to find a bug.

```sh
# Start analyse with bisect
git bisect start
# Specify bad and good commits kown
git bisect bad <commit_number>
git bisect good <commit_number>
# Specify to git if the current commit is bad or good
git bisect <good|bad>
# Git detect by elimination the first bad commit after that we can see the bad commit
git show
# Leave Git bisect
git bisect reset
```

## Exemple de commande

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

# Commit history graph
git log --all --decorate --oneline --graph

```
