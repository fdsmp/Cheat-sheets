# Docker Swarm cheat-sheets

### Create a new swarm :
```shell
docker swarm init --advertise-addr <MANAGER-IP>
```
### Add worker nodes :

Run the following command on a manager node to retrieve the join command for a worker :
```shell
docker swarm join-token worker
```

Example:
```
    To add a worker to this swarm, run the following command:
    
        docker swarm join \
        --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
        192.168.99.100:2377
```

> Run de command produced in the worker nodes.
## Manage nodes in a swarm

1. View the current state of the swarm :
```shell
docker info
```

2. View information about nodes :
```shell
docker node ls
```

3. Inspect an individu :
```shell
docker node inspect <NODE-ID>
```

> `--pretty` flag to print the results in human-readable format

```shell
docker node inspect --pretty <NODE-ID>
```
4. Change node availability :

```shell
docker node update --availability drain <NODE-ID>
```
- Drain a manager node so that it only performs swarm management tasks and is unavailable for task assignment.
- Drain a node so you can take it down for maintenance.
- Pause a node so it can't receive new tasks.
- Restore unavailable or paused nodes availability status.

```shell
docker node update --availability active <NODE-ID>
```

5. Promote or demote a node :
To promote a worker node to manager role : 
```shell
docker node promote <NODE-ID>
```

To demote a node :
```shell
docker node demote <NODE-ID>
```

6. Remove a node from the swarm :

On the worker node :
```shell
docker swarm leave
```

> Pass the `--force` flag to remove a manger node.

Then on the manager node :
```shell
docker node rm <NODE-ID>
```


## Docker Swarm Services
### `docker service` Commands and Options [link](https://docs.docker.com/reference/cli/docker/service/create/)

#### Créer et mettre à jour des services

- **create**: Créer un nouveau service.
    - `--name`: Nom du service.
    - `--replicas`: Nombre de répliques du service.
    - `--env`: Variables d'environnement.
    - `--network`: Réseau auquel le service sera attaché.
    - `--constraint`: Contraintes de placement du service.
    - `--publish`: Publier un port pour le service.
    - `--limit-cpu`: Limite de CPU pour le service.
    - `--limit-memory`: Limite de mémoire pour le service.
- **update**: Mettre à jour un service existant.
    - `--image`: Mettre à jour l'image du service.
    - `--replicas`: Mettre à jour le nombre de répliques.
    - `--env-add` / `--env-rm`: Ajouter ou supprimer des variables d'environnement.
    - `--constraint-add` / `--constraint-rm`: Ajouter ou supprimer des contraintes de placement.
    - `--publish-add` / `--publish-rm`: Ajouter ou supprimer des ports publiés.
    - `--limit-cpu`: Mettre à jour la limite de CPU.
    - `--limit-memory`: Mettre à jour la limite de mémoire.
    - `--force`: Forcer la mise à jour.

#### Gestion des services

- **ls**: Lister les services.
    
    - `--filter`: Filtrer les services par propriété (nom, état, etc.).
    - `--format`: Format de sortie.
    - `--quiet`: Afficher uniquement les IDs des services.
- **inspect**: Obtenir des détails sur un ou plusieurs services.
    
    - `--pretty`: Afficher les détails de manière lisible.
    - `--format`: Format de sortie.
- **ps**: Lister les tâches d'un service.

	- `--filter`: Filtrer les tâches par propriété.
    - `--format`: Format de sortie.
    - `--quiet`: Afficher uniquement les IDs des tâches.
- **rm**: Supprimer un ou plusieurs services.
    
    - `--force`: Forcer la suppression.
- **scale**: Mettre à l'échelle un ou plusieurs services.
    
    - `<service_name>=<replica_count>`: Spécifier le nom du service et le nombre de répliques.

#### Autres options utiles

- **logs**: Afficher les logs d'un service.
    
    - `--details`: Afficher les détails des logs.
    - `--follow`: Suivre les logs en temps réel.
    - `--since`: Afficher les logs depuis une durée spécifique.
    - `--tail`: Afficher seulement les dernières lignes de logs.
    - `--timestamps`: Afficher les timestamps des logs.
- **rollback**: Revenir à une version précédente du service.
    
    - `--rollback-parallelism`: Nombre de tâches à annuler simultanément.
    - `--rollback-delay`: Délai entre les rollback de chaque lot de tâches.
    - `--rollback-failure-action`: Action à entreprendre en cas d'échec de rollback (pause, continuer, etc.).

#### Exemples pratiques

1. **Créer un service simple** :
```shell
docker service create --name my_service --replicas 1 -p 80:80 nginx:1.25
```

> --replicas : specify the number of replica tasks you want.
> --mode global : to start a service  on every node in the swarm.
> --reserve-cpu : to reserve a given amount number of CPUs.
> --reserve-memory : to reserve a given amount number of memory.
> --constraint : to control the nodes a service can be assigned to.
> --placement-pref : set a placement preference.

```shell

docker service create --replicas 9 --name redis_2 \
  --placement-pref 'spread=node.labels.datacenter' redis:3.0.6
```

2. **Mettre à jour le nombre de répliques d'un service** :
```shell
docker service update --update-delay 10s --image nginx:1.26 my_service
```

> --update-delay : the time delay between updates to a service task or sets of tasks.
> --update-parallelism : the maximum number of service tasks that the scheduler updates simultaneously.
> --update-failure-action : control the update behavior when a task returns `FAILED`.
> --update-max-failure-ratio : fraction of tasks can fail during an update.

```shell
docker service create --replicas 10 --name my_web --update-delay 10s \
  --update-parallelism 2 --update-failure-action continue alpine
```

3. **Lister les services avec un filtre** :

```shell
docker service ls --filter name=my_service
```

4. **Obtenir les détails d'un service** au format non-json:

```shell
docker service inspect --pretty my_service
```
  
5. **Afficher les logs d'un service** :

```shell
docker service logs my_service
```

6. **Mettre à l'échelle un service** :
```shell
docker service scale my_service=1
```

7. Afficher le(s) nom(s) de(s) noeud(s) qui exécutent les services : 
```shell
docker service ps my_service
```

8. Supprimer un service :
```shell
docker service rm my_service
```

9. Faire un rollback :
```shell
docker service update --rollback my_web
```
