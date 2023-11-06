## Prépration de l'environnement de travail

* Pour faire ce tutoriel il faut au minimum un pc avec 4 giga de ram voir plus.

* Pour dérouler tous les labs de cette formation il faudra au préalable provisionner les machines en suivant ce tutoriel.

* Ce tutoriel va provisionner une machine dans virtualbox en utilisant le langage infra as code vagrant.

* Ci-dessous la liste des machines avec leurs @ip  qui seront provisionnées:

```yaml
* Liste des serveurs et leurs @ip
docker: 192.168.10.1
```

## Installation des outils


* Télécharger et installer virtualbox: [ICI](https://www.virtualbox.org/wiki/Downloads)

* Télécharger et installer Vagrant: [ICI](https://www.vagrantup.com/downloads.html)

* Télécharger et installer git: [ICI](https://git-scm.com/downloads)

* Créer un répertoire de travail dans votre Bureau exemple: Labs_Ansible


## Création des répertoires

* Se positionner dans «Labs_Ansible» puis clique droit, ensuite cliquer sur «Git bash here»

* Cloner le projet formation-ansible à partir de gitlab

```yaml
$ git clone  https://github.com/papicool/docker-courses.git
```

Nouvelle url : https://github.com/papicool/docker-courses.git


## Provision des machines

* Se positionner dans formation-ansible/Lab1-preparation-envs

```yaml
$ cd formation-ansible/Lab01-preparation-envs
```

* Lancer la commande pour provisionner les machines

```yaml
$ vagrant up
```

## Actions sur le serveur ansible

* Connecter via ssh au serveur docker

```yaml
$ vagrant ssh docker
```


https://github.com/ACloudGuru-Resources/Course_Kubernetes_Deep_Dive_NP.git