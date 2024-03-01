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
This Docker command creates a container named "mysql-server" running the MySQL 8.0.33 image in detached mode. It's connected to the "wp-network" network for communication isolation. Port 3306 of the container is mapped to the host's port 3306 to enable external access to the MySQL database. The container is set to restart automatically if it fails, ensuring continuous availability. It also creates a volume named "mysql-volume" to persist MySQL data, and environment variables are configured to set the root password, create a WordPress database, and create a user with specific privileges.

Screenshot of corresponding results:

![wp_mysql_server](https://github.com/Nisha-Sugathan/Docker_wp_volume_Route53/assets/134600837/31b64857-8d90-4118-b962-a4866b24548c)

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

This Docker command creates a container named "wordpress" running the latest version of the WordPress image in detached mode. It's connected to a custom network called "wp-network" to isolate its communication. The container's port 80 is mapped to the host's port 80, allowing access to WordPress via HTTP. It ensures the container restarts automatically if it fails, using the --restart always flag. Additionally, it mounts a volume named "wordpressl-volume" to persist WordPress data, and environment variables are set to configure WordPress to connect to a MySQL database with specified credentials and database name.

### Docker Volume Paths for MySQL and WordPress
```
sudo ls -al /var/lib/docker/volumes/mysql-volume/_data

sudo ls -al /var/lib/docker/volumes/wordpress-volume/_data
```

###  Add publicIP on Route53 AWS servcie


http://<domain_name>


To ensure that the containers are reading old data from volumes after stopping and removing the containers, and then recreating both MySQL and WordPress containers, follow these steps:

### Stop and Remove Containers: First, stop and remove both the MySQL and WordPress containers.

```
docker container stop mysql-server
docker container rm  mysql-server
docker container stop wordpress
docker container rm wordpress
```
### Recreate the conatiners will show your previous site contents

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



docker container run -d --name wordpress --network wp-network \
-p 80:80 --restart always \
-v wordpress-volume:/var/www/html \
-e WORDPRESS_DB_HOST="mysql-server" \
-e WORDPRESS_DB_USER="wpuser" \
-e WORDPRESS_DB_PASSWORD="wpuser123" \
-e WORDPRESS_DB_NAME="wordpress" \
wordpress:latest

```
### Verify Data Persistence: 

Ensure that the data from the volumes is being read correctly by checking the content of the WordPress site and verifying that the MySQL database contains the expected data.

### Output URL

http://<domain_name>

