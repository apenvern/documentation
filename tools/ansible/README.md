# Tool Box Unix <!-- omit in toc -->

## Commandes utiles

```bash
# Lancer un playbook
ansible-playbook mon-playbook.yml 

# Spécifier l'inventaire 
ansible-playbook -i /path/to/inventory mon-playbook.yml 

# Filtrer la serveur ou un groupe de serveur ou exectuer le playbook
ansible-playbook -i /path/to/inventory -l server mon-playbook.yml 
ansible-playbook -i /path/to/inventory -l group mon-playbook.yml

# Redémarrer à une tache définie
ansible-playbook -i ../common/virtualbox installation.yml --start-at-task="Installation de Keycloak"

# Executer que les taches avec le tag correspondant
ansible-playbook -i ../common/virtualbox installation.yml --tags idp

# Lister l'execution des taches du playbook
ansible-playbook ./installation.yml --list-task

# Afficher un graph des inventaires
ansible-inventory -i ../common/ --graph

```
