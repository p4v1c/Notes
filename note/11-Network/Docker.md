install Gitea
```sh
sudo mkdir /home/$USER/docker/gitea -p && sudo mkdir /home/$USER/docker/mariadb -p

docker run --name mariadb -e MYSQL_ROOT_PASSWORD=p@$$word -e MYSQL_USER=gitea_rw -e MYSQL_PASSWORD=G1te@ -e MYSQL_DATABASE=gitea -v /home/$USER/docker/mariadb:/var/lib/mysql -p 3300:3300 -d mariadb

docker run --name gitea -d -p 3000:3000 -p 222:22 -v /home/$USER/docker/gitea:/data -v /etc/timezone:/etc/timezone:ro -v /etc/localtime:/etc/localtime:ro -e USER_UID=1000 -e USER_GID=1000 -e GITEA__database__DB_TYPE=mysql -e GITEA__database__HOST=localhost:3300 -e GITEA__database__NAME=gitea -e GITEA__database__USER=gitea_rw -e GITEA__database__PASSWD=G1te@ --restart=unless-stopped gitea/gitea:latest
```

Start DockerHub : 
```sh
systemctl --user start docker-desktop 
```

Connect to the same network : 

```sh
docker ps
```

```sh
docker network create mon_reseau
```

```bash
docker network connect mon_reseau mariadb
docker network connect mon_reseau gitea
```