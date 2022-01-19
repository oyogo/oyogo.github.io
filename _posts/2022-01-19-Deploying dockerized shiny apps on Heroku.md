---
title: "Deploying dockerized shiny apps on Heroku"
output: html_notebook
category: Shiny
tags:
 - r
 - shiny
 - docker
 - deployment
---

Heroku is a platform as a service (PaaS) that enables developers to build, run, and operate applications entirely in the cloud. They offer free services where you can run up to 5 apps but if you verify your account with a credit card you can get more than 5.    

Get to the sign up page and create an account.After you're done you can now follow the following steps to deploy your dockerized shiny app.     

# Install Heroku CLI
  For ubuntu 16+ 
    
    $ sudo snap install --classic heroku   
    
  For mac 
   
    $ brew tap heroku/brew && brew install heroku  
    
# Login to heroku

Navigate to the app directory through the terminal and enter the following command.  

    $ heroku login  

This will open the login page on your browser, enter the required details and if its successful you will see a *logging in... done* then *Logged in as _youremail_*   

# Creating a Heroku app   

Navigate to your app directory and enter the following comand    

    $ heroku create    

The above command will create a Heroku app on Heroku servers.    

# Deploying code
## Login to heroku container registry  

    $ heroku container:login   

_ensure there is no space before and after the colon._
_you need to have docker installed in your computer_  


## Build the image  

    $ heroku container:push web    
    
Navigate to your projects directory then enter the above command. 
This will build the image and push it to the container registry. 
You need to have a Dockerfile on the directory containing your app code.  

## Pushing an existing image  

Alternatively you can push an existing image to heroku registry by using the commands below:  

    $ docker tag yourimage registry.heroku.com/<app>/<process-type>   
    $ docker push registry.heroku.com/<app>/process-type>   
    
This will push the image to heroku registry as you would to docker hub.      

## Release the image to your app  

    $ heroku container:release web  

This will release the image to your application on heroku servers.  

## Seeing your application  

    $ heroku open  

The application can be seen and interacted with by using the above command.  
And yes, you have managed to deploy your dockerized shiny app. 
Well, remember the service in not entirely free given that you only have 550 dyno hours, as such, you may want to stop the running application so that you save the dyno hours. If that be the case you could use the following commands to stop and spin up your application.   

## To disable your app   

    $ heroku ps:scale web=0

## To restart your app   

    $ heroku ps:scale web=1



