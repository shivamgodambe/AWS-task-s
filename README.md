# RDS DOCUMENTATION
This repository contains instructions for setting up a Student Registration Application using Amazon RDS for the database, Apache Tomcat as the server, and Docker for containerization.

## Prerequisites
AWS Account: Ensure you have an AWS account and have launched:
RDS Instance: A MySQL/MariaDB instance for the database.
EC2 Instance: A virtual machine to deploy the application.
Security Groups: Whitelist the following ports in your security groups:
SSH: 22
HTTP: 80
MySQL: 3306
Tomcat: 8080
Steps for Setup
1. Connecting to RDS from EC2
SSH into your EC2 instance:

        ssh -i key.pem ec2-user@<ec2-public-ip>
2. Install MariaDB to connect to RDS:

        sudo apt update
        sudo apt install mariadb-server -y
        sudo systemctl start mariadb
        sudo systemctl status mariadb
3. Connect to the RDS instance:

        mysql -h <rds-endpoint> -u admin -p
Once connected, use the MySQL command line to manage the RDS instance.

4. Setting Up Apache Tomcat
Download Apache Tomcat:

        wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.78/bin/apache-tomcat-8.5.78.tar.gz
        tar -xvzf apache-tomcat-8.5.78.tar.gz
5. Change directory into Tomcat:

        cd apache-tomcat-8.5.78
6. Deploying the Application
Download the student.war file:


        cd webapps
        wget https://s3-us-west-2.amazonaws.com/studentapi-cit/student.war
7. Download the MySQL Connector:

        cd ../lib
        wget https://s3-us-west-2.amazonaws.com/studentapi-cit/mysql-connector.jar
8. Modify the context.xml configuration file:

        cd ../conf
        vim context.xml
9. Add the following resource configuration:

xml

    <Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
      maxTotal="500" maxIdle="30" maxWaitMillis="1000"
      username="admin" password="your-password"
      driverClassName="com.mysql.jdbc.Driver"
      url="jdbc:mysql://<rds-endpoint>:3306/studentapp"/>
10. Starting Tomcat
Give execution permission and start Tomcat:

        cd ../bin
        chmod +x catalina.sh
        ./catalina.sh start
11. Access the application by visiting:

        http://<ec2-public-ip>:8080/
12. Database Operations
Access the database:

        mysql -h <rds-endpoint> -u admin -p
13. Use the database:

sql

    USE studentapp;
    SELECT * FROM students;
14. Taking Backups
Full Backup (Data + Schema):


        sudo mysqldump -h <rds-endpoint> -u admin -p studentapp > backup.sql
Schema Only:


    sudo mysqldump -h <rds-endpoint> -u admin -p --no-data studentapp > schema_backup.sql
Data Only:


    sudo mysqldump -h <rds-endpoint> -u admin -p --no-create-info studentapp > data_backup.sql
