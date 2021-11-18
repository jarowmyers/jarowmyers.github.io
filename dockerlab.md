# Docker Lab Documentation

This lab was done on a Ubuntu VM. Some steps were taken from [this guide](https://www.hostinger.com/tutorials/run-docker-wordpress).

### Install Docker

Docker was installed using these commands.
```markdown
sudo apt update
sudo apt install docker.io
```
Docker-compose was installed with this command.
```markdown
sudo apt install docker-compose
```

### Preparing Wordpress Docker Compose File.
A directory was made for the docker-compose file.
```markdown
mkdir /~/wordpress
cd /~/wordpress
```
The docker-compose file is then created using nano.
```markdown
touch docker-compose.yml
nano docker-compose.yml
```
The contents of the compose file were taken from [this link](https://docs.docker.com/samples/wordpress/). Version was changed from 3.9 to 3.3.

docker-compose.yml
```markdown
version: "3.3"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
```

The current user was added to the docker group to make sure things worked.
```markdown
sudo usermod -aG docker $USER
```

### Starting Up Wordpress
The compose file was started to start running the wordpress site.
```markdown
sudo docker-compose up -d
```
This was met with an error. Evidently the docker daemon was not running, so it needed to be started. I thought having docker running would handle this but for some reason it did not.

This issue was fixed by opening a new terminal window and using the following command to manually start the daemon:
```markdown
sudo dockerd
```
Then, retrying the startup command worked. It runs and downloads all the wordpress information and starts it on localhost:8000.

In firefox, I then navigated to localhost:8000 and the wordpress page is there.

The initial wordpress page.
![The initial wordpress page.](/images/initialimage.png)

The wordpress setup page.
![The wordpress setup page.](/images/setuppage.png)

The wordpress dashboard.
![The wordpress dashboard.](/images/wordpressdashboard.png)
