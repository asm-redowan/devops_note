FROM ubuntu:latest

# Install MySQL server and client
/*
RUN apt-get update && \
    apt-get install -y mysql-server mysql-client && \
    rm -rf /var/lib/apt/lists/*
*/

# Start MySQL server and create a new database and user
RUN service mysql start && \
    mysql -u root -e "CREATE DATABASE mydb;" && \
    mysql -u root -e "CREATE USER '$MYSQL_USER'@'%' IDENTIFIED BY '$MYSQL_PASSWORD';" && \
    mysql -u root -e "GRANT ALL PRIVILEGES ON mydb.* TO '$MYSQL_USER'@'%';"

# MySQL server Service start and permission
RUN chown mysql:mysql /var/run/mysqld/mysqld.sock && \
    chmod 660 /var/run/mysqld/mysqld.sock && \
    service mysql start

# Set environment variables
ENV MYSQL_ROOT_PASSWORD=my-secret-pw \
    MYSQL_USER=myuser \
    MYSQL_PASSWORD=mypassword

# Expose MySQL port
EXPOSE 3306

# Start MySQL server
CMD ["mysqld"]