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

### Jenkins
* CI/CD
* Pipeline: Configuration as code

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

### 2.3 : Point sur les outils
Jenkins permet la définition des tâches à effectuer à travers l'intermédiaire de fichiers descripteur de pipelines d'execution appelés Jenkinsfile.

Pour avoir un aperçu du fonctionement des Pipelines Jenkins, [voir l'introduction](https://jenkins.io/doc/book/pipeline/).
Pour plus de documentation sur la syntaxe des Jenkinsfile, voir [la documentation officielle](https://jenkins.io/doc/book/pipeline/syntax/).

> La documentation ci-dessus sera indispensable pour pouvoir mener à bien le tp.

### 2.4 : Création d'un Multibranch Pipeline
Jenkins apporte un type d'objet appelé Multibranch Pipeline. Cet objet permet à Jenkins de scanner les différentes branches d'un repository, d'y détecter les changements et d'exécuter des actions différentes en fonction des branches scannées.

De cette façon, il est possible de décorréler des actions à réaliser en intégration d'autres actions à réaliser lors d'un cycle de release vers la production.

Afin d'illustrer le point ci-dessus, sur la [page d'accueil de Jenkins](http://65.52.64.111:8080/), cliquer sur **New Item**.

Dans la fenêtre suivante, entrer un nom pour le pipeline (en l'occurence, entrer première lettre du prénom+nom de famille en minuscule) et choisir le type **Multibranch Pipeline**.

Dans la fenêtre de configuration du Pipeline, dans la section **Branch Sources**, cliquer sur **Add source** et entrer l'url (https) de votre repository git.

> Ex: git@github.com:arthurmauvezin/simple-java-maven-app.git

Dans la section **Scan Multibranch Pipeline Triggers**, cocher **Periodically if not otherwise run** et sélectionner un interval de **1 minute**, puis cliquer sur **Save** en bas de page.

Le pipeline devrait se lancer au bout de quelques minutes et compiler l'application puis afficher un build success.

### 2.5 : Ajout des tests
Pour l'instant, le build ne fait que compiler le code Java présent dans les sources. Ajouter un stage de test après le build.

HELP: La commande à ajouter au Jenkinsfile est la suivante:
```bash
mvn test
```

> Pour ajouter des étapes de build à Jenkins, il suffit d'éditer votre Jenkinsfile, de le commiter, puis de le pusher. Jenkins va scanner automatiquement les changements apportés au code source toutes les minutes et exécuter son pipeline en conséquence.


### 2.6 : Création de l'image Docker
Jusqu'ici le pipeline build et test l'applicatif mais aucun package n'est ŕealisé ou persisté. Nous allons créer une image Docker afin de persister notre applicatif à la fin du build.

Pour cela, créer un fichier Dockerfile à la racine de votre projet contenant le code suivant:
```dockerfile
FROM maven:3.3.9-jdk-8-alpine

COPY target/my-app-1.0-SNAPSHOT.jar /root/my-app-1.0-SNAPSHOT.jar

CMD ["java", "-jar", "/root/my-app-1.0-SNAPSHOT.jar"]
```

Ajouter un stage de création de l'image Docker au Jenkinsfile

> Attention : Le container à utiliser pour lancer l'étape de build de l'image Docker n'est pas un container maven comme dans les étapes précédentes. Chercher dans le Jenkinsfile peut donner des idées :)

HELP: La commande à lancer dans Jenkins est la suivante:
```bash
docker build -t my-app:$BUILD_NUMBER .
```

> $BUILD_NUMBER est une variable auto incrémentée par Jenkins à chaque build. Elle permet de s'assurer de l'unicité des livrables en cas de rejeux des builds.


### 2.7 : Lancement du container Docker
Après avoir créé l'image Docker, l'intégration continue peut lancer un container à partir de cette image pour valider le livrable.

Ajouter un stage de run du container Docker capable de lancer l'image précédemment créée.

```bash
docker run my-app:$BUILD_NUMBER
```

### 2.8 : Afficher les résultats des tests
Ajouter le code suivant après la fermeture de la balise stages dans le jenkinsfile pour activer l'affichage des résultats de tests par rapport aux exécutions précédentes.

```groovy
stages {
	...
  }

  post {
    always {
      junit 'target/surefire-reports/*.xml'
    }
  }
```

Après l'ajout de cette balise post, un graph apparaitra en haut à droite de l'écran de visualisation des pipelines permettant de visualiser facilement l'évolution du passage des tests au fur et à mesure des builds.

### 2.9 : Création d'une nouvelle branche
Le Jenkinsfile est maintenant prêt à exécuter les stages définis en son sein quelles que soient les branches qui soient créées. 

Créer un branch feature/test-build à partir de la branch develop et constater que le build est automatiquement appliqué sur cette branche.

### 2.10 : Limitation par type de branche
Bien que Jenkins soit capable d'appliquer le même pipeline quelles que soient les branches, il est possible d'affiner les actions qu'on souhaite réaliser en fonction des branches.

Ainsi, un branche représentant la production n'aura pas les mêmes actions CI/CD qu'un branch représentant une nouvelle feature demandant d'avoir des retours rapides et réguliers.

Pour réaliser cet affinage, il faut ajouter des conditions aux différents stages:
```groovy
// Pour s'exécuter uniquement sur la branche master
stage('Stage name') {
      when {
        branch 'master';
      }
      steps {
        ...
      }
    }

// Pour s'exécuter sur la branches master et develop
stage('Stage name') {
      when {
        anyOf {
          branch 'master';
          branch 'develop'
        }
      }
      steps {
        ...
      }
    }
```

Modifier votre Jenkinsfile pour builder l'image Docker uniquement sur les branches master et develop, et pour lancer les container Docker uniquement sur la branche master.

### 2.11 : Conclusion
Vous pouvez maintenant créer plusieurs branches features et vous apercevoir que ces branches ne se verront appliquées que les étapes de build et test.

Toute cette configuration permet d'avoir un système de validation au plus près sans perdre de temps sur les feedback rapides attendu notamment dans le dev.
