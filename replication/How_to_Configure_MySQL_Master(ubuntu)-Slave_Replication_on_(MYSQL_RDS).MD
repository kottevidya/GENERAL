#Step 1: Install MySQL Server On Server 1 (Master):
* The first step is installing MySQL server on server 1 with IP address 11.11.11.11. 
* So SSH to your server and run the below commands:
```$ sudo apt-get update
   $ sudo apt-get install mysql-server
```
#Step 2: Edit MySQL Configuration File on Server 1(Master):
* 1.Next, edit the MySQL configuration file in order for MySQL server to allow remote access

```$ sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf```

# 2.Change  bind-address = 127.0.0.1 to bind-address =0.0.0.0

        *  (This will cause MySQL to allow inbound connections.)

   
#3.Next, add the below lines near the end of that configuration file:
        
       ```server-id = 1
         log_bin = /var/log/mysql/mysql-bin.log
         log_bin_index =/var/log/mysql/mysql-bin.log.index
         relay_log = /var/log/mysql/mysql-relay-bin
         relay_log_index = /var/log/mysql/mysql-relay-bin.index ```
#4.save the file.

#5.Restart mysql
 ```    $ sudo service mysql restart ```  

#Step 3: Create a New User for Replication Services on Server 1(Master):
* 1.Next you need to create a new user that will handle replication services on server 1. So, log in to your MySQL on server 1.
 ``` $ sudo mysql -u root -p ```
* 2.Enter your password when prompted to do so and hit Enter to continue.
* 3.On the MySQL prompt that appears, enter the below SQL queries to create the user:
``` mysql > create user 'replication_user'@'%' identified by 'PASSWORD'; ```
``` mysql > GRANT REPLICATION SLAVE ON *.* TO 'replication_user'@'%';```
``` mysql > FLUSH PRIVILEGES; ```

* 4.Remember to replace 'PASSWORD' with a strong value for security purposes.
* 5.Before you exit the MySQL prompt, enter the command below to view information about the position and file of the Master Server


[!image]

   

* Here, you need to note the file name, 'mysql-bin.000001' and Position '154'. You will need these details when configuring RDS(SLAVE). Please note 


* step4.Now slave configuration :

 *  Create mysql RDS 

 *  Login to the RDS ```(mysql -h <> -u <> -p )```

 * Stop Slave

     ```CALL mysql.rds_stop_replication;``` 

*  RUN this command
  


```
call mysql.rds_set_external_master(
  '100.24.65.94',
  3306,
  'replication_user',
  'PASSWORD',
  'mysql-bin.000001',
  154,
  0);
```

* Start Slave
   ```CALL mysql.rds_start_replication;```

#Step 5: Test the Configuration:
* 1.You should now test the configuration and see whether MySQL replication is working as expected. To confirm this, log in to MySQL on server 1
     ```$ sudo mysql -u root -p```
* 2.Enter your root password and hit Enter to continue.
* 3.On the MySQL prompt that appears, create a database (e.g. replica_demo ):
      ```mysql> create database replica_demo;``` 

* 4.Then, log in to MySQL RDS
 ```Mysql -h <> -u <> -p```


follow this links

``` 
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_external_master.html
'''

```
https://www.alibabacloud.com/blog/how-to-configure-mysql-master-slave-replication-on-ubuntu-16-04_593982 ```