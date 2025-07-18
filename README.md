#lahinirina mahery christianno
#L1/A/181
# examendocker
# 🐳 Commandes Docker de Base

## 🔹 Images
```bash
docker images                   # Lister les images locales
docker pull <image>            # Télécharger une image depuis Docker Hub
docker rmi <image>             # Supprimer une image
docker build -t <nom>:<tag> .  # Construire une image avec un Dockerfile
```
🔹 Conteneurs
```bash
docker ps                      # Lister les conteneurs en cours
docker ps -a                   # Lister tous les conteneurs (même arrêtés)
docker run -it <image>         # Exécuter un conteneur interactif
docker run -d <image>          # Exécuter en arrière-plan (détaché)
docker stop <container>        # Arrêter un conteneur
docker start <container>       # Démarrer un conteneur arrêté
docker rm <container>          # Supprimer un conteneur
```
🔹 Réseaux & Volumes
```bash
docker network ls              # Lister les réseaux Docker
docker volume ls               # Lister les volumes Docker
docker network create <nom>    # Créer un réseau
docker volume create <nom>     # Créer un volume
```
🔹 Infos et débogage
```bash
docker logs <container>        # Afficher les logs
docker exec -it <container> bash  # Accéder à un conteneur
docker inspect <objet>         # Détails d’un conteneur ou image
docker stats                   # Afficher l’usage des ressources
```
🔹 Docker Compose (fichier docker-compose.yml)
```bash
docker-compose up              # Démarrer les services
docker-compose up -d          # Démarrer en mode détaché
docker-compose down           # Arrêter et supprimer les services
docker-compose ps             # Voir les services en cours
```
🔹 Docker Swarm (Cluster)
```bash
docker swarm init             # Initialiser un cluster Swarm
docker swarm join             # Rejoindre un cluster Swarm
docker node ls                # Lister les nœuds du cluster
docker service ls             # Lister les services dans le Swarm
```
🔹 Gestion des Conteneurs
```bash
docker ps                                 # Lister les conteneurs actifs
docker ps -a                              # Lister tous les conteneurs (y compris arrêtés)
docker run -it <image> bash               # Créer et accéder à un conteneur (mode interactif)
docker run -d <image>                     # Lancer un conteneur en arrière-plan (détaché)
docker start <container_id|nom>          # Démarrer un conteneur existant
docker stop <container_id|nom>           # Arrêter un conteneur
docker restart <container>               # Redémarrer un conteneur
docker rm <container_id|nom>             # Supprimer un conteneur
```
🌐 Docker Network – Gestion des réseaux

Docker permet de connecter des conteneurs entre eux via des réseaux virtuels. Voici les commandes principales pour gérer ces réseaux :

### 🔸 Lister et Inspecter
```bash
docker network ls                          # Lister tous les réseaux Docker
docker network inspect <nom_réseau>        # Voir les détails d’un réseau (IP, conteneurs, etc.)
```
🔸 Créer un réseau
```bash
docker network create <nom_réseau>         # Créer un réseau par défaut (bridge)
docker network create \
  --driver bridge \
  --subnet 192.168.100.0/24 \
  --gateway 192.168.100.1 \
  mon_reseau_perso                         # Créer un réseau personnalisé avec IP fixe
```
🔸 Supprimer un réseau
```bash
docker network rm <nom_réseau>             # Supprimer un réseau (non utilisé)
```
🔸 Connecter/Déconnecter un conteneur à un réseau
```bash
docker network connect <nom_réseau> <container>      # Connecter un conteneur à un réseau
docker network disconnect <nom_réseau> <container>   # Déconnecter un conteneur d’un réseau
```
🔸 Exemple avec docker run
```bash
docker network create mon_reseau
docker run -d --name cont1 --network mon_reseau nginx
docker run -it --name cont2 --network mon_reseau alpine sh

# Depuis cont2 :
ping cont1  # fonctionne si les deux sont sur le même réseau
```
### 🔹 Commandes de base
```bash
docker volume create <nom_volume>         # Créer un nouveau volume
docker volume ls                          # Lister tous les volumes Docker
docker volume inspect <nom_volume>        # Détails (chemin local, conteneurs liés, etc.)
docker volume rm <nom_volume>             # Supprimer un volume (non utilisé)
docker volume prune                       # Supprimer tous les volumes non utilisés
```
🔹 Utilisation dans un conteneur
```bash
docker run -d \
  --name monconteneur \
  -v monvolume:/app/data \
  nginx
```
🔹 Montages avancés
```bash
docker run -v /chemin/local:/chemin/dans/conteneur <image>
```
🔹 Sauvegarder un volume dans une archive .tar
```bash
docker run --rm -v monvolume:/volume -v $(pwd):/backup alpine \
  tar czf /backup/volume_backup.tar.gz -C /volume .
```
🔹 Restaurer un volume depuis une archive .tar
```bash
docker run --rm -v monvolume:/volume -v $(pwd):/backup alpine \
  tar xzf /backup/volume_backup.tar.gz -C /volume
```
🔹 Exemple dans docker-compose.yml
```bash
version: '3'
services:
  db:
    image: postgres
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```


### 🔸 Pourquoi utiliser un volume ?
```bash
- **Persister les données** d’une base de données ou application.
- **Partager des données** entre plusieurs conteneurs.
- Éviter de perdre des fichiers après suppression du conteneur.
- Meilleure **performance** que les montages de fichiers hôte (`bind mount`).
- **Sécurité** : Docker isole le contenu.
```
 
### 🔹 Commandes de gestion des volumes
```bash
docker volume create <nom_volume>         # Créer un volume vide
docker volume ls                          # Lister tous les volumes
docker volume inspect <nom_volume>        # Détails (chemin, conteneurs associés)
docker volume rm <nom_volume>             # Supprimer un volume (s’il n’est pas utilisé)
docker volume prune                       # Supprimer tous les volumes inutilisés
```
🔹 Exemple de volumes multiples
```bash
docker run -v logs_data:/var/log \
           -v db_data:/var/lib/mysql \
           mysql
```
🔹 Lecture seule (Read-Only)
```bash
docker run -v mon_volume:/data:ro <image>
```
🔸 Restaurer un volume depuis une archive
```bash
docker run --rm \
  -v monvolume:/volume \
  -v $(pwd):/backup \
  alpine \
  tar xzf /backup/backup.tar.gz -C /volume
```
🔹 Exemple dans docker-compose.yml
```bash
version: '3.9'
services:
  app:
    image: nginx
    volumes:
      - web_data:/usr/share/nginx/html

  db:
    image: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  web_data:
  pgdata:
``


