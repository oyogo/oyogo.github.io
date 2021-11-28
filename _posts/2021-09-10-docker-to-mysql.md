---
title: "Connecting docker container to mysql database"
output: html_notebook
editor_options: 
  chunk_output_type: inline
  
category: R
tag: docker  
---


You have containerized your application and you could be wondering how to link it to a database (mysql for example), be it locally or on some remote server. Well, I hope this simple guide will be of help to you.  

 Assuming you already have mysql installed (if not please check here), you will need to make some adjustments on configurations of user(s) and access privileges to the database. By default, MySQL is configured to only listen for local connections, hence the need to make some adjustments. 
 
 There are two scenarios where you will need to connect docker container to mysql through an IP address: 
 
   * Remote connection : the database is hosted on another machine   
   * Local connection : the database is hosted on the same machine where the docker system is hosted.  
   
   N/B: It is important to note that for both scenarios above, you will need to use the IP address of the machine that is hosting the database.  
   
 Well, you might be wondering as to why we need an external IP address for scenario 2 yet the docker container is running on the same machine where mysql database is hosted. The reason is a docker container is always treated as some kind of a machine, as such, its assigned an address (172.17.0.2) which is different from host machine's IP address.    
 
## 1.Configure mysql to listen to external IP address.   
  Step 1: open mysqld.cnf    
  
  
         sudo nano /etc/mysql/mysql.conf.d/mysql.cnf
         
  Step 2: Check for the line that has _bind-address = 127.0.0.1_    
         The default value 127.0.0.1 means that mysql will listen for local connections only. So to open it up to external address, we will change that line to _bind-address  = 0.0.0.0_. Save the changes then exit the nano editor.
         N/B: Sometimes the _bind-address = 127.0.0.1_ may not be in the mysqld.cnf, in that case, just add the line at the bottom (in this case _bind-address = 0.0.0.0).    
         
         
  Step 3: You might need to also open port 3306 in the firewall.     
  
          sudo ufw allow 3306
         
  Step 4: Restart mysql     
  
        sudo systemctl restart mysql    
        
  Step 5: Create a user that you will use to connect from the docker container (which is treated as a 'remote server') to mysql on the host machine.       
       mysql> CREATE USER 'somename'@'docker_container_ip' IDENTIFIED BY 'password';    
       
       N/B: use *hostname -i*  to check the docker container ip address
       
       
  Step 6: Give the user/account privileges as per the needs basis.   
  
       mysql> GRANT CREATE, ALTER, INSERT, UPDATE, SELECT on *.* TO 'somename'@'docker_ip_address' WITH GRANT OPTION;   
       
       
  Step 7: Free up the cached memory using *FLUSH PRIVILEGES* command then exit mysql *mysql> exit*    
  
## 2.Now let's go back to our code. Where you are connecting to mysql database, have your credential as below:    
  N/B: Remember to replace the details as per your credentials.   
  
  
           myconnection <- dbPool(drv = RMySQL::MySQL(),
                                  dbname = "yourdb",
                                  host = "host_machine_ip_address", 
                                  username = "somename", # This is the user that we created in mysql (somename)
                                  password = "thepasswordyouassigned") 
                                  
  
       
## 3.Build your docker image from the dockerfile then run it and go to http://127.0.0.1:3838 to see your application live.   
  
### And hurraaaaaaaaay !!! we're dooooone !!
   
   
        

