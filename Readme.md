# TP 1 : CI/CD

## Rappels
### Git
* Repository
* Clone
* Add
* Commit
* Push
* Pull 
* Branch
* Merge
* Fork
* Pull request
* [Git Documentation](https://git-scm.com/docs)

### Gitflow
![Gitflow diagram](https://stxnext.com/media/filer_public_thumbnails/filer_public/d4/41/d4414c91-483b-4904-9c1b-fc92c899678c/gitflow.png__1011x520_q85_crop_subsampling-2_upscale.png "Gitflow diagram")

## 1 : Git 
Toute la partie Git du tp doit être réalisée individuellement
### 1.0 : Création de compte Github
Aller sur [Github](https://github.com/) et se créer un compte si ce n'est pas déjà la cas.

### 1.1 : Forker un repo
Aller sur le projet Github [hello-world](https://github.com/cours-ece/simple-java-hello-world) et le forker pour réaliser une copie du repo dans votre espace personnelle Github.

Pour plus d'aide sur le fork voir la [documentation officielle](https://guides.github.com/activities/forking/)

### 1.2 : Cloner le fork
Cloner le repo forké sur votre ordinateur 
 
### 1.3 : Faire un commit 
Dans le repo forké ajouter votre nom au fichier i-was-here.txt au format première lettre du nom + nom de famille en minuscule.

> Ex: Arthur Mauvezin --> amauvezin

Committer et pusher les changements apportés au fichier

### 1.4 : Réaliser une pull request
Depuis la page de votre fork du projet Github, réaliser une pull request pour proposer vos changement au projet parent initial (le repo depuis lequel vous avez forké votre repo actuel)

Pour plus d'aide sur les pull requests voir la [documentation officielle](https://help.github.com/articles/about-pull-requests/)

### 1.5 : Naviguer dans les logs
Sur votre copie locale, afficher les logs des commits précédents.

Faites attention au fait que tout est loggué depuis le début du projet. Vous pouvez trouver les messages, les dates mais aussi les auteurs des commits passés.

### 1.? : WAAAAAAAAARPZOOOOOOOOOOONE
Question Bonus: A l'aide de l'outil utilisé dans la question précédente, trouver la couleur de mon chat ?!! 

> HELP : Si vous ne savez pas ou entrer la réponse, c'est que vous n'avez pas cherché assez loin ^^

### 1.6 : Lister les branches
Lister les branches du repo

Est-ce que toutes les branches vous paraissents normales ? 
Y a t-il une branche qui retient votre attention ? Si oui, noter le nom de cette branche dans le fichier answers et se placer dessus (checkout).

### 1.7 : Créer une branche
Créer une branche à partir de la branche trouvée ci-dessus. La branche à créer doit s'appeler "resolve/" suivi de la première lettre de votre prénom puis du nom de famille en minuscule.

> Ex Arthur Mauvezin --> resolve/amauvezin

### 1.8 : Apporter votre pière à l'édifice
Placez vous sur votre branche resolve/... (toujours checkout), puis répondre aux questions contenues dans le fichier enigma.
Après avoir répondu, commiter les changements sur la branche resolve.

### 1.9 : Merger la branche
Merger la branche "resolve/..." sur la branch master de votre repo, puis supprimer les branches "resolve/..." et la branche "étrange".

Vous devez normalement avoir un fichier answers et un fichier enigma dans la branch master.

### 1.10 : Pusher les changements
Pusher tous vos changements sur votre repo.

### 1.11 : Gitflow
Lister les branches présentes dans le repo. Au sens Gitflow, à quoi servent ces chacunes de ces branches ? Répondre dans le fichier answers.

## 2 : CI/CD
Dans cette deuxième partie du tp, vous devez utiliser le même repo que celui obtenu à la fin de la partie 1.

### 2.1 : Création du Jenkinsfile
Créer un fichier de pipeline as code appelé **Jenkinsfile** à la racine de votre projet.
Y coller le code suivant:
```groovy
// Configuration des images docker utilisées pour les différentes étapes CI/CD
pipeline {
  agent {
    kubernetes {
      label 'mypod'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
spec:
  containers:
  - name: maven
    image: maven:alpine
    command:
    - cat
    tty: true
  - name: docker
    image: docker
    command:
    - cat
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
    }
  }


// Configuration à modifier
  stages {

    stage('Build') {
      steps {
        container('maven') {
          sh 'mvn -B -DskipTests clean package'
        }
      }
    }

}

```

Le code ci-dessus permet à Jenkins d'exécuter les étapes définies dans entre les balises **stages** à chaque activation d'un trigger (cela peut être un commit, un cron, une pull request...)

### 2.2 : Connection à Jenkins
Se connecter à Jenkins disponible à l'adresse suivante : http://65.52.64.111:8080/

Les identifiants sont normalement créés pour tout le monde en respectant:
* login : première lettre du prénom + nom de famille en minuscule
* password : première lettre du prénom + nom de famille en minuscule

> Ex Arthure Mauvezin --> amauvezin/amauvezin

### 2.3 : Création d'un Multibranch Pipeline
Jenkins apporte un type d'objet appelé Multibranch Pipeline. Cet objet permet à Jenkins de


