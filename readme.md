# IMT Nord Europe, TD 2 Docker

**Clément Meiller - le 15 Février 2022**

Système hôte utilisé pour la réalisation de ce TD : 

```bash
$ system_profiler SPSoftwareDataType
Software:

    System Software Overview:

      System Version: macOS 12.1 (21C52)
      Kernel Version: Darwin 21.2.0
      Boot Volume: Macintosh HD
      Boot Mode: Normal
      Computer Name: MacBook Air de Camilo
      User Name: Camilo (camilo)
      Secure Virtual Memory: Enabled
      System Integrity Protection: Enabled
      Time since boot: 4:09
```

## Exercice 1

1. Installer la dernière version du logiciel Docker
   
   ```bash
   $ brew search docker #Recherche de l'application Docker
   $ brew home docker #vérification de la page d'accueil de l'app pour m'assurer que j'installe la bonne app
   $ brew install docker #installation de docker
   ```

2. Vérifier que la version de docker est bien installée ( *docker version* )
   
   ```bash
   $  docker --version                              
   Docker version 20.10.12, build e91ed57
   ```

## Exercice 2

1. Afficher tous les conteneurs actuellement lancés sur votre machine.
   
   ```bash
   $ open -a docker #démarrage de docker
   $ docker ps #Liste de tous les containers actuellement lancés
   CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
   ```

2. Créer un conteneur ubuntu.
   
   * recherche sur le docker hub de l'image officielle d'ubuntu : https://hub.docker.com/_/ubuntu/
   * Création du conteneur 
   
   ```bash
   $ docker pull ubuntu #récupération de l'image sur le docker hub
   Using default tag: latest
   latest: Pulling from library/ubuntu
   Digest: sha256:669e010b58baf5beb2836b253c1fd5768333f0d1dbcb834f7c07a4dc93f474be
   Status: Image is up to date for ubuntu:latest
   docker.io/library/ubuntu:latest
   
   $ docker image ls #vérification que mon image soit bien disponible
   REPOSITORY                    TAG       IMAGE ID       CREATED         SIZE
   ubuntu                        latest    54c9d81cbb44   13 days ago     72.8MB
   
   $ docker run --name MyUbuntu ubuntu # je démarre mon conteneur
   
   $ docker ps -a #vérification que mon conteneur a bien été executé puis quitté
   CONTAINER ID   IMAGE     COMMAND   CREATED              STATUS                          PORTS     NAMES
   f2524cc3b526   ubuntu    "bash"    About a minute ago   Exited (0) About a minute ago             MyUbuntu
   ```

3. Créer un conteneur nginx ( détaché du terminal )
   
   * recherche sur le docker hub de l'image officielle de nginx : https://hub.docker.com/_/nginx/
   
   * Création du conteneur en mode détaché
     
     ```bash
     $ docker pull nginx
     Using default tag: latest
     latest: Pulling from library/nginx
     5eb5b503b376: Pull complete 
     1ae07ab881bd: Pull complete 
     78091884b7be: Pull complete 
     091c283c6a66: Pull complete 
     55de5851019b: Pull complete 
     b559bad762be: Pull complete 
     Digest: sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
     Status: Downloaded newer image for nginx:latest
     docker.io/library/nginx:latest
     
     $ docker image ls
     REPOSITORY                    TAG       IMAGE ID       CREATED         SIZE
     ubuntu                        latest    54c9d81cbb44   13 days ago     72.8MB
     nginx                         latest    c316d5a335a5   2 weeks ago     142MB
     
     $ docker run -d --name MyNginx nginx # je démarre mon conteneur en mode détaché
     f15ee04dd74af4845d6a401e3ae12d9f64dd9e6292af00bc2df1ad06bed988df
     
     $ docker ps -a # je vérifie que le conteneur est bien up & running
     CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                     PORTS     NAMES
     f15ee04dd74a   nginx     "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds               80/tcp    MyNginx
     f2524cc3b526   ubuntu    "bash"                   7 minutes ago   Exited (0) 7 minutes ago             MyUbuntu
     ```

4. Afficher l’adresse IP du container nginx.
   
   * Après une recherche dans le manuel, j'ai utilisé la commande docker inspect qui renvoi bien l'information mais  noyée dans la masse d'autres informations : 
     
     ```bash
     $ docker inspect MyNginx
     [...
       "Networks": {
                       "bridge": {
                           "IPAMConfig": null,
                           "Links": null,
                           "Aliases": null,
                           "NetworkID": "b0cacfbe8ea11fd9fa0c157832dad46d1ef6c7613b3c5a479fe0c1e811d6baa2",
                           "EndpointID": "3f43a5e1c664694b2fe849b5e6e2bb79a9bbe50920146a506af5e42649431857",
                           "Gateway": "172.17.0.1",
                           "IPAddress": "172.17.0.2",
                           "IPPrefixLen": 16,
                           "IPv6Gateway": "",
                           "GlobalIPv6Address": "",
                           "GlobalIPv6PrefixLen": 0,
                           "MacAddress": "02:42:ac:11:00:02",
                           "DriverOpts": null
                       }
     ...]
     ```
   
   * J'ai donc fait une recherche internet pour filtrer uniquement l'information qui m'interesse : 
     
     ```bash
     $ docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' MyNginx
     
     172.17.0.2
     ```

5. Afficher le contenu de la page index.html dans votre navigateur.
   
   * relance du conteneur en publiant le port 80 du conteneur sur le port 8080 de l'hôte
   
   ```bash
   $ docker stop MyNginx #Arrêt du conteneur
   $ docker rm MyNginx #suppression du conteneur
   $ docker run -d -p 8080:80 --name MyNginx nginx #Relance avec publication du port 80 du conteneur vers le port 8080 de l'hôte 
   b8cca35ea7d86e53a764b283597a84a83d27716b09cca14479544425291475aa
   ```
   
   * Affichage de la page d'accueil de nginx sur le navigateur de l'hôte
     
     ![Capture d’écran 2022-02-16 à 10.36.44.png](./Capture%20d’écran%202022-02-16%20à%2010.36.44.png)

6. A l’aide de la ligne de commande exporter **uniquement le nom** de tous les conteneurs actifs dans un fichier appelé conteneur-actif.txt
   
   ```bash
   $ docker ps --format "{{.Names}}" > conteneur-actif.txt
   $ cat conteneur-actif.txt  
   MyNginx
   ```

7. Supprimer votre conteneur nginx et votre conteneur ubuntu.
   
   ```bash
   $ docker stop MyNginx
   $ docker rm MyUbuntu ubuntu
   MyUbuntu
   MyNginx
   ```

## Exercice 3

1. Lister tous les volumes présents sur votre machine.
   
   ```bash
   $ docker volume ls
   DRIVER    VOLUME NAME
   local     ansible_ansible_vol
   ```

2. Stocker le **nom** de tous les volumes dans un fichier nommé volume.txt.
   
   ```bash
   $ docker volume ls > volume.txt
   $ cat volume.txt
   DRIVER    VOLUME NAME
   local     ansible_ansible_vol
   ```

3. Créer un volume appelé html-nginx.
   
   ```bash
   $ docker volume create html-nginx
   html-nginx
   ```

4. Monter ce volume sur le path /usr/share/nginx/ d’un conteneur nginx.
   
   ```bash
   $ docker run -d -p 8080:80 --name MyNginx --mount source=html-nginx,target=/usr/share/nginx/ nginx
   ```

5. Monter ce volume sur le path /usr/html/file d’un conteneur ubuntu.
   
   ```bash
   $ docker run -dt --name MyUbuntu --mount source=html-nginx,target=/usr/html/file ubuntu
   ```

6. Au sein du conteneur ubuntu modifier le fichier index.html ajouter dans le body
   
   ```html
   <p> Hello world </p>
   ```
   
   * Pour  modifier le fichier et éviter d'installer vim sur le conteneur MyUbuntu, je vais me connecter à la VM docker-desktop puis modifier le fichier index.html en utilisant vi : 
   
   ```bash
   $ stty -echo -icanon && nc -U ~/Library/Containers/com.docker.docker/Data/debug-shell.sock && stty sane
   / `#` vi  /var/lib/docker/volumes/html-nginx/_data/html/index.html
   
   # Modification puis enregistrement du fichier dans vi
   ```

7. Rendez-vous sur le conteneur nginx.
   
   ```bash
   $ docker exec -ti MyNginx bash 
   ```

8. Installer curl.
   
   * curl est déjà présent sur le conteneur

9. Lancer curl localhost.
   
   ```bash
   root@b2518b0b104c:/`#` curl localhost
   <!DOCTYPE html>
   <html>
   <head>
   <title>Welcome to nginx!</title>
   <style>
   html { color-scheme: light dark; }
   body { width: 35em; margin: 0 auto;
   font-family: Tahoma, Verdana, Arial, sans-serif; }
   </style>
   </head>
   <body>
   <h1>Welcome to nginx!</h1>
   <p> Hello World </p>
   <p>If you see this page, the nginx web server is successfully installed and
   working. Further configuration is required.</p>
   
   <p>For online documentation and support please refer to
   <a href="http://nginx.org/">nginx.org</a>.<br/>
   Commercial support is available at
   <a href="http://nginx.com/">nginx.com</a>.</p>
   
   <p><em>Thank you for using nginx.</em></p>
   </body>
   </html>
   ```
   
   > on remarque que notre hello world a bien été pris en compte sur ce conteneur alors que la modification a été réalisée sur le conteneur MyUbuntu

10. Qu’en concluez-vous ?
    
    * On peut en conclure que le volume *html-nginx* est partagé entre les conteneurs *MyNginx* et *MyUbuntu*

## Exercice 4

1. Clonage du projet : https://gitlab.com/Jimmy.Trackoen/docker-simple-api.git
   
   ```bash
   $ git clone https://gitlab.com/Jimmy.Trackoen/docker-simple-api.git
   Clonage dans 'docker-simple-api'...
   remote: Enumerating objects: 5, done.
   remote: Counting objects: 100% (5/5), done.
   remote: Compressing objects: 100% (4/4), done.
   remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
   Réception d'objets: 100% (5/5), fait.
   ```

2. Analyser le fichier app.js du projet pour en comprendre son fonctionnement.

3. Créer une image permettant de lancer l’application en utilisant la méthode “commit” à partir de l’image UBUNTU, nommer cette image simple-webapp:1.0.0
   
   ```bash
   $ docker run -dt --name MyUbuntu ubuntu  
   $ docker exec -ti MyUbuntu bash 
   root@9c3f622fe928:/`#` apt-get update
   ...
   root@9c3f622fe928:/`#` apt-get install nodejs
   ...
   root@9c3f622fe928:/`#` node -v
   v10.19.0
   root@9c3f622fe928:/`#` apt-get install npm
   ...
   root@9c3f622fe928:/`#` npm -v
   6.14.4
   root@9c3f622fe928:/opt`#` apt-get install git
   ...
   root@9c3f622fe928:/opt`#` cd /opt/
   root@9c3f622fe928:/opt`#` git clone https://gitlab.com/Jimmy.Trackoen/docker-simple-api.git
   root@9c3f622fe928:/opt`#` cd docker-simple-api/
   root@9c3f622fe928:/opt/docker-simple-api`#` npm install
   ...
   ```
   
   * Sur une seconde CLI
   
   ```bash
   $ docker commit --change='CMD cd /opt/docker-simple-api;npm run start' MyUbuntu simple-webapp:1.0.0
   ```
   
   > **Remarque** : Je n'ai volontairement pas respecté la convention de nommage classique `<repository>/<image-owner>/<image-name>` puisque je n'ai pas encore pour le moment de compte Docker et que cette image n'est pas destinée à être déposée sur un repository distant

4. Lancer l'application dans le conteneur sur le port 8080
   
   ```bash
   $ docker run -d -p 8888:8080 --name MySimpleWebapp simple-webapp:1.0.0
   ```

5. Accéder à l’application via votre navigateur sur le port 8888
   
   <img title="" src="./Capture d’écran 2022-02-16 à 16.37.30.png" alt="Capture d’écran 2022-02-16 à 16.37.30.png" width="763">

6. Créer une image permettant de lancer l’application en utilisant la méthode “dockerfile” à partir de l’image qui vous semble la plus **pertinente,** nommer cette image simple-webapp:2.0.0
   
   ```dockerfile
   
   ```

7. Lancer l’application dans le conteneur sur le port 8082 ( sans modifier le code source de l’application )

8. Accéder à l’application via votre navigateur sur le port 8899

## Exercice 5

1. Cloner le projet: https://gitlab.com/Jimmy.Trackoen/docker-db-connection.git

2. Analyser le fichier app.js.

3. Lancer un conteneur à partir de l’image mysql écoutant sur le port 3306

4. Lancer un conteneur à partir du projet cloner à l’étape 1.

5. Rendez-vous via votre navigateur sur la page localhost:8787 et assurez vous que votre application vous affiche bien le message “Connection à la DB successful."

6. Créer un compte sur https://hub.docker.com/

7. Nommer votre image docker-db-connection:1.0.0

8. Pousser votre image dans un repository public.

9. Documenter votre repository afin que de futurs utilisateurs sachent quels paramètres à
   
   renseigner pour que l’application fonctionne convenablement.