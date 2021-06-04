# Test AWS DevOps

Créer un pipeline de livraison continue avec AWS à partir d'un répertoire Git.

[lien tuto](https://aws.amazon.com/fr/getting-started/hands-on/create-continuous-delivery-pipeline/?e=gs2020&p=devops)

## Prérequis

1. Avoir un compte et se connecter à la console AWS
2. Disposer d'un repository git du code source hébergé sur Github

## Configurer une application avec AWS Elastic Beanstalk

[lien tuto](https://aws.amazon.com/fr/getting-started/hands-on/create-continuous-delivery-pipeline/module-two/)

Aller sur la [console AWS Elastic Beanstalk](https://console.aws.amazon.com/elasticbeanstalk/home?region=us-west-2#/welcome) et cliquer sur le bouton `Créer une nouvelle application`.

Donner un nom avotre application _(ex: testAWSdevops)_ et cliquer sur `Créer`.

Créer un environnement pour votre app en cliquant sur `Créer un nouvel environnement`.

Pour une application web, sélectionner `Environnement de serveur web` pui cliquer sur `Sélectionner`.

Sur l'onglet suivant, dans la section `Informations sur l'environnement` vous pouvez choisir un sous-domaine et inscrire une description de votre app mais ceci est facultatif.

Dans la section `Plateforme`, sélectionner le type de serveur voulu. Pour les besoins de ce tuto nous allons choisir ici `Node.js`.

Dans la section `Code de l'application`, sélectionner `Exemple d'application`, car nous allons mette notre code plus tard.

Cliquer sur `Créer un environnement` et attendre la fin de la création, celà peut prendre quelques minutes.

## Créer un projet de génération avec AWS Code Build

[lien tuto](https://aws.amazon.com/fr/getting-started/hands-on/create-continuous-delivery-pipeline/module-three/)

Aller sur la [console AWS CodeBuild](https://console.aws.amazon.com/codesuite/codebuild/start?region=us-west-2) et cliquer sur le bouton `Créer un projet`.

Donner au projet le nom de l'app suivi de -build _(ex: testAWSdevops-build)_.

Dans la section `Source`, sélectionner `Github` puis choisissez votre repo contenant l'application.

Dans la section `Webhook`, sélectionner `Reconstruire à chaque modification du code` puis choisissez `Une seule génération`.

Dans la section `Environnement`, sélectionner `image gérée` puis `Amazon Linux 2` comme OS. Sélectionner l'environement d'éxecution `standard` puis `aws/codebuild/amazonlinux2-x86_64-standard:3.0` comme image et cliquer sur `Nouveau rôle de service`.

Dans la section `Fichier buildspec`, Choisir `utiliser un fichier buildspec` s'il y a un fichier `buildspec.yml` à la racine de votre projet, sinon choisir `Insérer des commandes de génération`, basculer vers l'éditeur et copier ces lignes :

```sh
version: 0.2
phases:
    build:
        commands:
            - npm i --save
artifacts:
    files:
        - '**/*'
```

Cliquer sur `Créer un projet de génération` puis sur `Démarrer la génération` et attendre la fin du build.

## Créer un pipeline de livraison continue avec AWS Code Pipeline

[lien tuto](https://aws.amazon.com/fr/getting-started/hands-on/create-continuous-delivery-pipeline/module-four/)

Aller sur la [console AWS CodePipeline](https://us-west-2.console.aws.amazon.com/codesuite/codepipeline/start?region=us-west-2) et cliquer sur le bouton `Créer un pipeline`.

Donner au projet le nom de l'app suivi de -pipeline _(ex: testAWSdevops-pipeline)_, s'assurer que `nouveau rôle de service` est coché puis cliquer sur `Suivant`.

Dans l'onglet suivant, choisir son fournisseur de source et s'y connecter. Dans notre cas il s'agit de Github. Une fois connecté, choisir le bon repo et la branche `master` ou `main`. Vérifier que la détection des modifications est bien cliquée et cliquer sur `Suivant`.

Dans l'onglet `Ajouter une étape de génération`, choisir `AWS code build` comme fournisseur de build, choisir le nom du projet créé précédement et cliquer sur `Suivant`.

Dans l'onglet `Ajouter une étape de déploiement`, choisir `AWS Elastic Beanstalk` comme fournisseur de déploiement, choisir le nom de l'application créé précédement, son environnement et cliquer sur `Suivant`.

Vérifier la configuration et cliquer sur `Créer un pipeline`.

Une fois la phase `Deploy` passée en vert et le message `Succeeded`, cliquez sur « AWS Elastic Beanstalk ». Vos environnements AWS Elastic Beanstalk figurent dans un nouvel onglet.

L'application à été déployée sur l'URL figurant sur la ligne `Devopsgettingstarted-env`.
