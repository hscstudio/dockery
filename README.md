# dockery: Docker For Yii 2

Apache + PHP 7 + MySQL

## Step by Step

### Install Docker
1. Install docker, follow guide here http://docker.com
2. Firstly, You should read knowledge about docker

You should know about 
- what is docker?
- what is image?
- what is container?
- commonly command in docker.

### Prepare Environment
1. Create folder for docker project

for example:
  - docker

2. Create folder src in folder docker project that will contain your script of php application
  + docker
	- src
	
3. Create a file docker-compose.yml
```
php:
  build: .
  ports:
    - "9000:80"
  links:
    - mysql

mysql:
    image: mysql:latest
    volumes_from:
        - data
    environment:
        MYSQL_ROOT_PASSWORD: secret
        MYSQL_DATABASE: project
        MYSQL_USER: root
        MYSQL_PASSWORD: 123456

data:
    image: mysql:latest
    volumes:
        - /var/lib/mysql
    command: "true"
```
	
This file will create 3 container php, mysql, and data
base on this https://hub.docker.com/_/php/
	
4. Create "Dockerfile" contain
```
FROM php:7-apache
RUN apt-get update

# Install PDO MySQL
RUN docker-php-ext-install pdo pdo_mysql
# Install PHP Intl
RUN apt-get update && apt-get install -y \
        libicu-dev \
    && docker-php-ext-install -j$(nproc) intl
	
# Install Memcache
RUN apt-get install -y \
        libz-dev \
        libmemcached-dev \
    && pecl install memcached \
    && docker-php-ext-enable memcached

# Install gd
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng12-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd
	
## Copy files in src to web root in docker container
COPY src/ /var/www/
```

5. In folder src add your file of Yii2 application, then rename folder web to html

6. Build Your customize container, by run this command in docker terminal 
```
docker-compose build --no-cache
```

7. Check IP Address of docker
```
docker-machine ip
```
will show for example 192.168.99.100

7. Run Your container
```
docker-compose up -d
```

8. Open browser, and access Your Yii application
http://192.168.99.100:9000








