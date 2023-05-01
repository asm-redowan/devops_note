# Dockerfile:
```
FROM ubuntu:latest

ARG MYSQL_ROOT_PASSWORD
ARG MYSQL_USER
ARG MYSQL_PASSWORD

##### Install MySQL server and client
RUN apt-get update && \
    apt-get install -y mysql-server mysql-client && \
    rm -rf /var/lib/apt/lists/*

##### Set environment variables
#ENV MYSQL_ROOT_PASSWORD=$MYSQL_ROOT_PASSWORD
#ENV MYSQL_USER=$MYSQL_USER
#ENV MYSQL_PASSWORD=$MYSQL_PASSWORD

##### Start MySQL server and create a new database and user
RUN service mysql start && \
    mysql -e "CREATE DATABASE mydb" && \
    mysql -e "CREATE USER '${MYSQL_USER}'@'%' IDENTIFIED WITH mysql_native_password BY '${MYSQL_PASSWORD}'" && \
    mysql -e "GRANT ALL PRIVILEGES ON *.* TO '${MYSQL_USER}'@'%'" && \
    mysql -e "FLUSH PRIVILEGES"

RUN chown mysql:mysql /var/run/mysqld/mysqld.sock && \
    chmod 660 /var/run/mysqld/mysqld.sock

RUN touch /tmp/red.txt
RUN echo $MYSQL_USER >> /tmp/red.txt
RUN echo $MYSQL_PASSWORD >> /tmp/red.txt
RUN echo $MYSQL_ROOT_PASSWORD >> /tmp/red.txt

##### Expose MySQL port
EXPOSE 3306

##### Start MySQL server
#CMD ["mysqld"]
CMD ["mysqld", "--user=mysql", "--console"]
```
# Build:

#### Build Docker image using Dockerfile
*`#docker build -t mysqlv1.0 --build-arg MYSQL_ROOT_PASSWORD=root@123 --build-arg MYSQL_USER=myuser --build-arg MYSQL_PASSWORD=mypassword .`*
#### Create Container using docker image
*`#docker run -p 3306:3306 -dit --name dbserver mysqlv1.0`*
#### Access to docker container
*`#docker exec -it dbserver /bin/bash`*