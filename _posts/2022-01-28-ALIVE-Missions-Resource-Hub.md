---
title: "ALIVE(K) Missions Resource Hub"
output: html_notebook
category: Projects
tags:
 - r
 - shiny
 - docker
 - deployment
 - projects
---

Hi, Welcome to ALIVE (K) Missions Resource Hub.      

ALIVE (K) is a movement of Seventh-Day Adventist students and young professionals living in view of eternity. ALIVE(K) aims to inspire, train and mobilize Seventh-Day Adventist young adults for effective involvement in evangelism, directly impacting the African Continent.  The movement conducts missions in urban areas and to rural areas as part of its activities.   

This resource was developed to function as a monitoring and reporting tool for the missions department. The missions coordinator and his/her committee will use it to analyse the performance of mission activities conducted and further use the learnings thereof to plan for future endevours. The dashboard will also serve as a reporting tool such that the members can get details of missions conducted presented in a manner that is visually appealing and that communicates insights quickly and effectively. People seldom have time to cruise through five to ten page reports to get information on about certain activities, missions for this case, as such, with this tool one is able to get insights in a quick way.  

To interact with the dashboard click [here](https://limitless-sea-25544.herokuapp.com/#!/home)   


## Home page   

This is the landing page of the dashboard, it shows general information that touches on missions.  


![](/assets/homepage.png)  

The home page contains general details on mission activities. It has six sections which are explained below:   

### 1. Header bar   

![](/assets/header_bar.png)  
The header bar has the organisation logo, dashboard title, dropdown for downloads (Resources) and Upload button for mission mission request form.    


#### * Header bar - Resources     

![](/assets/header_resources.png)        


If you click the resources tab you'll have a drop-down with a list of resources you can download, namely;

   User manual   
   Mission booklet     
   
   
### 2. Welcome note    

![](/assets/welcome_note.png)  
This is a statement about the department and its objective briefly put.  


### 3. Sidebar panel   

![](/assets/sidebar_navigation_panel.png)  

The dashboard has pages which you access using the sidebar navigation panel. To go to a page, click on the menu item and the page will open in the main panel which is to the left side of the sidebar.  

### 4. Overall statistics  

![](/assets/overall_statistics.png)  

Just below the welcome note is a section that has overall statistics on impact for the missions conducted so far.  


### 5. Next move   

![](/assets/next_move.png)  

Upcoming mission details are capture in this card. There are two cards within. 

#### * Countdown and calendar   

![](/assets/countdown_calendar.png)   

The count down shows the count of days, hours, minutes and seconds adrift the upcoming mission commencement date and a calendar showing the dates the mission covers from beginning to the end of it.  

#### * Site details  

![](/assets/site_details.png)   


Summary details about the upcoming mission site are given here on the map pop-up. The map is interactive, you can zoom in and out.
Above the map we have details on the site name and a link which takes you to the registration page.  

### 6. Coverage  

![](/assets/coverage.png)   
The map shows an overview of all the mission sites covered so far. The map is interactive, you can zoom in and out to see the labels with names of the mission sites.  

### 7. Missions pages  

![](/assets/missions_page.png)   



This page has impact details and general information about the mission site. It has three sections:   

#### * Site details  

The missions pages (under Rural missions) have a similar structure as such, the explanation wil apply to the rest of the mission sites.      

#### * Visitation and literature coverage section   

![](/assets/gaugeplots.png)   

For this section we have gauge graphs showing statistical counts of people engaged through door-to-door visitations and literature distributions.   


#### * Children ministry program  

![](/assets/children_ministry_program.png){width="70%" height="70%"}
The card shows statistical count of children who were engaged in the children ministry programs.  

#### * Bible study class   

![](/assets/bible_study_class.png)

One of the programs ALIVE(K) conducts is a bible study class out of which calls are made for baptism. This card shows details on the number of people who were baptised.  


## The technical side of things   


The following list has links to some of the frameworks and software that were used to developed the dashboard:  

  - [R](https://www.r-project.org/) : base language for statistical programming    
  - [Shiny](https://shiny.rstudio.com/) : R library for developing web applications.  
  - [shiny.fluent](https://github.com/Appsilon/shiny.fluent) : R library for creating dashboard user interface (Its a wrapper for Miscrosoft fluent UI).  
  - Other libraries which can be accessed on the project repository on github.  
    
    
    



