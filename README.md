# Docker_wp_volume_Route53
WordPress is a free and open source blogging tool and a content management system (CMS) based on PHP and MySQL, which runs on a web hosting service.In this project the wordpress is installed and attached to a volume mounted on local machine and the website is access via a public domain


### Prerequisites
Need to install docker on your machine
Add your username to docker group

### Docker Installation

```
sudo yum install docker -y &> /dev/null
sudo systemctl restart docker.service
sudo systemctl enable docker.service
sudo usermod -a -G docker ec2-user
```
![docker_installation](https://github.com/Nisha-Sugathan/Docker-Bind_mounting/assets/134600837/ba7797c4-9a73-4ce6-b593-2befa5850e0d)

### Setup network and volume
```
docker network create wp-network
docker volume create mysql-volume
docker volume create wordpress-volume
```
### Create MySQL container
```
docker container run -d --name mysql-server --network wp-network   \
-p 3306:3306 \
--restart always \
-v mysql-volume:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD="mysqlroot123"  \
-e  MYSQL_DATABASE="wordpress" \
-e MYSQL_USER="wpuser"  \
-e MYSQL_PASSWORD="wpuser123"  \
mysql:8.0.33-debian
```
### Create wordpress container
```
docker container run -d --name wordpress  --network wp-network  \
-p 80:80 \
--restart always \
-v wordpressl-volume:/var/www/html \
-e WORDPRESS_DB_HOST="mysql-server"  \
-e WORDPRESS_DB_USER="wpuser"  \
-e WORDPRESS_DB_PASSWORD="wpuser123"  \
-e WORDPRESS_DB_NAME="wordpress" \
wordpress:latest
```

### List the coreesponding volume path 
```
sudo ls -al /var/lib/docker/volumes/mysql-volume/_data

sudo ls -al /var/lib/docker/volumes/wordpress-volume/_data
```

###  Add publicIP on Route53 AWS servcie


http://blog.devopstest2023.online/


We can change any modification on the current website like theme or add any plugins.

### Remove the container using stop and rm

```
docker container stop mysql-server
docker container rm  mysql-server
docker container stop wordpress
docker container rm wordpress
```
### Recreate the conatiner will show your previous site contents

```
docker container run -d --name mysql-server --network wp-network   \
-p 3306:3306 \
--restart always \
-v mysql-volume:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD="mysqlroot123"  \
-e  MYSQL_DATABASE="wordpress" \
-e MYSQL_USER="wpuser"  \
-e MYSQL_PASSWORD="wpuser123"  \
mysql:8.0.33-debian



docker container run -d --name mysql-server --network wp-network   \
-p 3306:3306 \
--restart always \
-v mysql-volume:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD="mysqlroot123"  \
-e  MYSQL_DATABASE="wordpress" \
-e MYSQL_USER="wpuser"  \
-e MYSQL_PASSWORD="wpuser123"  \
mysql:8.0.33-debian
63961f1bf7ebba150b37d78ca919aa290208ef9a27b264cd517667e9a7707b33

```

### Output URL

http://blog.devopstest2023.online/

