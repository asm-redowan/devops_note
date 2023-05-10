# Dockerfile
```
FROM ubuntu:latest

#ARG MYSQL_ROOT_PASSWORD
#ARG MYSQL_USER
#ARG MYSQL_PASSWORD

# Install MySQL server and client
RUN apt-get update && \
    apt-get install -y mysql-server mysql-client && \
    rm -rf /var/lib/apt/lists/*

# Set environment variables
ENV MYSQL_ROOT_PASSWORD=root@123
ENV MYSQL_USER=myuser
ENV MYSQL_PASSWORD=mypassword

# Start MySQL server and create a new database and user
RUN service mysql start
#    mysql -e "CREATE DATABASE mydb" && \
#    mysql -e "CREATE USER '${MYSQL_USER}'@'%' IDENTIFIED WITH mysql_native_password BY '${MYSQL_PASSWORD}'" && \
#    mysql -e "GRANT ALL PRIVILEGES ON *.* TO '${MYSQL_USER}'@'%'" && \
#    mysql -e "FLUSH PRIVILEGES"

RUN chown mysql:mysql /var/run/mysqld/mysqld.sock
RUN chmod 660 /var/run/mysqld/mysqld.sock

RUN touch /tmp/mysql.sh
RUN chmod +x /tmp/mysql.sh
RUN echo "service mysql start" >> /tmp/mysql.sh
RUN echo "#!/bin/bash" >> /tmp/mysql.sh
#RUN echo "chown mysql:mysql /var/run/mysqld/mysqld.sock" >> /tmp/mysql.sh
#RUN echo "chmod 660 /var/run/mysqld/mysqld.sock" >> /tmp/mysql.sh
RUN echo "mysql -u root -p\$MYSQL_ROOT_PASSWORD -e \"CREATE DATABASE mydb\"" >> /tmp/mysql.sh
RUN echo "mysql -u root -p\$MYSQL_ROOT_PASSWORD -e \"CREATE USER '\$MYSQL_USER'@'%' IDENTIFIED WITH mysql_native_password BY '\$MYSQL_PASSWORD'\"" >> /tmp/mysql.sh
RUN echo "mysql -u root -p\$MYSQL_ROOT_PASSWORD -e \"GRANT ALL PRIVILEGES ON *.* TO '\$MYSQL_USER'@'%'\"">> /tmp/mysql.sh
RUN echo "mysql -u root -p\$MYSQL_ROOT_PASSWORD -e \"FLUSH PRIVILEGES\"" >> /tmp/mysql.sh
RUN echo "chown mysql:mysql /var/run/mysqld/mysqld.sock" >> /tmp/mysql.sh
RUN echo "chmod 660 /var/run/mysqld/mysqld.sock" >> /tmp/mysql.sh
#RUN echo "mysqld" >> /tmp/mysql.sh
#RUN chmod +x /tmp/mysql.sh

#RUN chown mysql:mysql /var/run/mysqld/mysqld.sock
#RUN chmod 660 /var/run/mysqld/mysqld.sock

# Expose MySQL port
EXPOSE 3306
#ENTRYPOINT ["/bin/bash", "./tmp/mysql.sh"]
# Start MySQL server
CMD ["mysqld"]
#ENTRYPOINT ["mysqld", "/bin/bash", "./tmp/mysql.sh"]
#CMD ["/var/run/mysqld/mysqld.sock", "./tmp/mysql.sh"]
#ENTRYPOINT ["/tmp/mysql.sh"]
#CMD ["mysqld","chown", "mysql:mysql", "/var/run/mysqld/mysqld.sock","chmod", "660", "/var/run/mysqld/mysqld.sock", "/tmp/mysql.sh"]
```
# Build:

#### Build Docker image using Dockerfile
```
docker build -t dbserver .
```
#### Create Container using docker image
```
docker run -p 3306:3306 -it --name mysql --env MYSQL_USER="test" --env MYSQL_PASSWORD="test@123" --env MYSQL_ROOT_PASSWORD="root@123" --privileged=true  dbserver
```
#### Access to docker container
```
docker exec -it mysql /bin/bash -c ./tmp/mysql.sh

    then

docker exec -it mysql /bin/bash
```