# TP 1 : CI/CD

## Rappels
### Git
* Repository
* Clone
* Add
* Commit
* Push
* Pull 
* Fork
* Pull request
* [Git Documentation](https://git-scm.com/docs)

### Gitflow
![Gitflow diagram](https://stxnext.com/media/filer_public_thumbnails/filer_public/d4/41/d4414c91-483b-4904-9c1b-fc92c899678c/gitflow.png__1011x520_q85_crop_subsampling-2_upscale.png "Gitflow diagram")

## Git 
Toute la partie Git du tp doit être réalisée individuellement
### 0 : Création de compte Github
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

### 1.5 : Naviguer des les logs
Sur votre copie locale, afficher les logs des commits précédents.

Faites attention au fait que tout est loggué depuis le début du projet. Vous pouvez trouver les messages, les dates mais aussi les auteurs des commits passés.

### 1.? : WAAAAAAAAARPZOOOOOOOOOOONE
Question Bonus: A l'aide de l'outil utilisé dans la question précédente, trouver la couleur de mon chat ?!! 




## Jenkinsfile example
```groovy
// Specify the name of the pod which will be created
def label = "mypod-${UUID.randomUUID().toString()}"

// Specify the configuration of jenkins slave pod which will execute commands
podTemplate(label: label, containers: [
    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true)
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {

    // Execute following command in following node
    node(label) {

      // Name of stage in pipeline
      stage('Run in Docker') {
        container('docker') {
          sh 'hostname'
          sh 'hostname -i'
          sh 'docker ps'
        }
      }
    }
}
```
