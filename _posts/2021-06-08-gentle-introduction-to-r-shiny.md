---
title: Gentle Introduction to R Shiny
author: Clinton
date: '2021-06-08'
slug: gentle-introduction-to-r-shiny
categories: []
tags: []
---

![](/assets/simple_app.jpg)

```r
knitr::opts_chunk$set(echo = TRUE, warning = FALSE,message = FALSE)
```


_fig1: The screenshot above is for a simple shiny app._

# What's so shiny about Shiny?

Shiny is an R library that makes it easy to build web applications without necessarily knowing HTML.  

*Why Shiny?*    

 - With Shiny you are able to share analysis results in form of interactive graphics and tables.  
 - With Shiny you can customize your application to your liking.  
 - It has an active and rich community.  
 
 Good, with that in mind let's now dive into the steps guiding us in the development process.  
 
 
  
 
# Installation and loading of  libraries  

We'll need the following libraries as we proceed, install (in case you hadn't) and load them.

 > Some tip!
     to install a package use: _install.packages("packagename")_
     to load a package use: _library(packagename)_. 
     
     
```r
#install.packages("shiny")
#install.packages("dplyr")
#install.packages("data.table)
#install.packages("ggplot2") 
#install.packages("plotly)


library(shiny)
library(dplyr) # for data wrangling
library(data.table) # for data wrangling and import
library(ggplot2) # for data visualization
library(plotly) # for data visualization
```
 
# data import    
 
We will use the [students performance](https://raw.githubusercontent.com/oyogo/data/main/StudentsPerformance.csv) data from kaggle. 

  > To read in the data you can either use _read.csv("path/to/your/data.csv")_
    or _fread("path/to/your/data.csv")_
    but I'd prefer fread function from data.table because its quite fast, especially when the data file is big.  
    
    
```r
stud_perf <- fread("https://raw.githubusercontent.com/oyogo/data/main/StudentsPerformance.csv")
```
 
# Structure of a Shiny app
 
 
 A shiny app has three elements;  
 1. ui object : this is where we lay out the appearance of our app.  
 2. server(); server function which contains code for generating outputs which are displayed on the ui.  
 3. shinyApp(); function that combines the two objects (ui and server) in oder to build your app.    
 
 The first two elements (ui and server() ) can either be contained in a single script (app.R) with a shinyApp() function to wrap the two functions or you can alternatively have the two components as separate scripts (ui.R and server.R) of which, for this approach you won't need a shinyApp function.   
 
 If your app is rather too big then the two script approach might suit you but for this tutorial we'll use the single script approach.   
 
A basic shiny app with no content has the following structure.(single file structure)   
 ![](/assets/shinyapp_structure.png)
 
# The UI 

UI simply means user interface, this is the part of the app where we design the layout of our user interface i.e what the user sees and interacts with on their browser window.    
 
## Layout  
 
 There are different layouts we can use to design our UI and the choice of the layout depends on the conceptual layout of your application.     
  - sidebar layout      
  - navbarPage      
  - fluidRow and column (uses the grid system)    
  - a mix of the above layouts.   
 
 
The image below shows a sketch of how the aforementioned layouts look like.    
![](/assets/ui_layouts.png)

For this tutorial we shall be using a mix of the sidebar layout with the fluidRow and column but I'd like to point you to [this](https://shiny.rstudio.com/tutorial/written-tutorial/lesson2/) article for more details on the layouts.  

# Building the application  

Cool! guess we're moving on well. Now let's build our app bit by bit.
Ready for the ride? lets gooooo...


### Step 1 - basic app structure  
To begin with, let us create a shiny app which will render a blank page.  

```r
library(shiny) 

ui <- fluidPage(
  
)

server <- function(input,output){
  
}

# Run the application 
shinyApp(ui = ui, server = server)
```


### Step 2 - Layout  

At this point we can now write the functions for our layout, remember as I had mentioned earlier on, we will use the sidebar layout with a mix of fluidRow and column layouts.  

The sidebarLayout function takes two arguments;

 * sidebarPanel function  
 * mainPanel function  
 
 
The code below produces a blank page but you'll see some greyish section on the left side the page. Now that's our side panel with no content in it.   
Guess we're moving on well, right?..good !  

 
```r

library(shiny) 

ui <- fluidPage(
  
  sidebarLayout(
    sidebarPanel(

    ),
    
    mainPanel(

    )
  )
 
)

server <- function(input,output){
  
  
}

# Run the application 
shinyApp(ui = ui, server = server)

```


### step 3 -  sidebarPanel  
I have used the side panel to contain general texts and any information about our app. I would have perhaps positioned some widgets here but that's outside the scope of this tutorial given that I have not used any form of reactivity in the app. All the same, you'll do well to check [this](https://shiny.rstudio.com/tutorial/written-tutorial/lesson3/) page for more concerning control widgets.  
Notice, I have largely used HTML tag functions on the side panel. See the next step to get an idea of what HTML tags are and what they do.  

```r

## Sidebar----
        sidebarPanel(
            p("This tool was developed to aid teachers and parents 
               and other stakeholders to analyze student performance,
               get insights and do necessary interventions
               to better the students' performance."), #  p tag is for paragraph text.
            
           
            strong(em("Dataset use in this app was obtained from kaggle, 
                      click on the link below to see the data")), #strong makes the text bold and em for italisizing
            
            br(), #This inserts breaks between items
            
      a(href="https://www.kaggle.com/spscientist/students-performance-in-exams?select=StudentsPerformance.csv","Kaggle dataset"), # the (a) tag is used for links
            
            br(),
            br(),
            
            img(src="rstudio.jpg", height=70,width=70) # img tag is for reading images
        )
```


### step 4 -  mainPanel

Now let's arrange the items that will appear on the main panel.     
There are two columns in the main panel each with a 6-wide width.    
The first column has four elements;     

 1.  _*h4 tag within a div tag.*_      
   The h4() tag creates a header of level 4 (the size deacreases with the increment of the level).       
   The div tag function helps to style the elements within it, in this case the text. All the text within the div will be of color blue.      
     
 2.  _*p tag.*_     
    The p() tag function creates a paragraph text.     
    
  3.  _*br.*_      
    The br() function creates a line break between elements, the text and plot below it for this case.      
    
  4. _*plotlyOutput*_    
    Explained in the next section    
 
 The second column has four elements also;    
 
   1. _*h4 tag within a div.*_    
   The explanation given above applies, however, for this case the text within the div will be of color green.   
   
   2.  _*p tag*_   
      
   3.  _*br()*_   
   
   4.  _*plotlyOutput*_  
  
 
See the code chunk below.   
 
```r

   # main section , where we display the two plots.
    mainPanel(
            
 column(6, # First column to contain the first plot and the accompanying text
    div(h4("Subject performance analysis according to gender"), style="color:blue"), 
         p("The graph below suggests to us that females generally
           perform better in reading and writing than their male counterparts.
           On the flip of it, the males are better at math than their female counterparts."),
           br(), # html tag to space the text from the plot below it
           plotlyOutput("barplot_ggplot") 
                       
           ),
 
 column(6, # Second column for the second plot with the respective text
          div(h4("Performance analysis according to ethnic groups"),
          style="color:green"),
          p("Its interesting to see how student performance varies 
             according to their ethnic groups.
             Group C is leading with a huge gap with those of group A."),
          em("The diagram below is a bubble plot, please note that 
              the size of the bubble corresponds to the mean score of the group."),
            br(),
            br(), 
            plotlyOutput("bubble_plot")  
                       
            )
            
        )
```


# server

Apart from the HTML elements that we have on the ui function, other objects are outputs generated from the server side.For example the _plotlyOutput()_ in this case.  
So basically what happens is, we create an object with an id on the ui side and then the code to generate the object sits in the server function. 
The back and forth communication between the ui and the server is made possible through the id which should be unique for each object and the ui and server functions which render the object.       

The diagram below illustrates this concept.   

![](/assets/ui_server_talks.png)


The above image shows just one example of an object that we can display on the ui.   
Perhaps you might be wondering what are the other objects that we can create in our shiny app.   
Well, the image below gives you some more objects and their corresponding output and render functions just to get you started, but then, 
I recommend you visit this page to see some more objects.   

The object can be a table, a plot, a reactive text, a raw HTML etc. The image below lists some of the objects and their corresponding output and render functions just to get you started. For more on this please check out [this](https://shiny.rstudio.com/tutorial/written-tutorial/lesson4/) page, you'll also get to understand more on reactivity.    

![](/assets/output_render_functions2.png)

Back to our app that we were building.   
Below is the server code below that generates the two plots on our app.  

```r

# Define server logic required to generate the two plots
server <- function(input, output) {

    
 # server code for the barplot (first object)
    output$barplot_ggplot <- renderPlotly({
        
        # calculating data summaries for plotting the barplot
        barplot_data <- stud_perf_melt[,.(mean_score=mean(score)),by=c("gender","subject")]
        
        # build the plot with plotly
           plot_ly( barplot_data,
               y = ~mean_score,
               x = ~subject,
               color = ~gender,
               text = ~paste0("Gender: ", gender, "\n", "Subject: ", subject,"\n","Mean score: ",mean_score)) %>%
           add_bars(showlegend=TRUE, hoverinfo='text' ) %>%
           layout(title="Mean gender performance",yaxis=list(title="Mean score"))
    })
    

  # server code for the density ridges plot (our second object)
    
    output$bubble_plot <- renderPlotly({
        
     # calculating data summaries for plotting the bubble plot
      bubbleplot_data <-  stud_perf_melt[,.(mean_score=mean(score)),by=c("race_ethnicity")] 
        
            plot_ly(bubbleplot_data) %>%
            add_trace(x = ~reorder(race_ethnicity, mean_score), 
                      y = ~mean_score,
                      size = ~mean_score,
                      color = ~race_ethnicity,
                      alpha = 4.5,
                      type = "scatter",
                      mode = "markers",
                      marker = list(symbol = 'circle', sizemode = 'diameter',
                                    line = list(width = 2, color = '#FFFFFF'), opacity=2.4)) %>%
            add_text(x = ~reorder(race_ethnicity, -mean_score), 
                     y = ~race_ethnicity, text = ~mean_score,
                     showarrow = FALSE,
                     color = I("black")) %>%
            layout(
                showlegend = FALSE,
                title="Mean score with respect to ethnic groups",
                xaxis = list(
                    title = "Race/Ethnic group"
                ),
                yaxis = list(
                    title = "Mean score"
                )
            ) 
    })
    
})  
```

And we're done with the development process, hurraaay!  
The script below now has the entire code for building our app. 

```r


library(shiny)
library(plotly)
library(ggplot2)
library(data.table)
library(dplyr)

stud_perf_melt <- fread("../data/student_performance_preprocessed_data.csv") #the data is stored inside the data folder in the app directory. The two dots mean the folder is two steps up 


# Define UI for application
ui <- fluidPage(

    # Application title
    titlePanel("Student performance analysis app"),

    # Layout of the app 
    sidebarLayout(
        
        # sidebar section
        sidebarPanel(
            p("This tool was developed to aid teachers and parents and other stakeholders to analyze student performance 
              , get insights and do necessary interventions to better the students' performance."), # for a paragraph text I've used p tag.
            
           
            strong(em("Dataset use in this app was obtained from kaggle, click on the link below to see the data")), # I have used strong to make the text bold and em for italisizing
            
            br(), #This inserts breaks between items
            
            a(href="https://www.kaggle.com/spscientist/students-performance-in-exams?select=StudentsPerformance.csv","Kaggle dataset"), # the a tag is used for links
            
            br(),
            br(),
            
            img(src="rstudio.jpg", height=70,width=70) # img tag is for reading images
        ),

        
        # main section , where we display the two plots.
        mainPanel(
            fluidRow( 
                column(12,
                    column(6,
                          div(h4("Subject performance analysis according to gender"), style="color:blue"), 
                           
                       p("The graph below suggests to us that females generally perform better in reading and writing than their male counterparts.
                         On the flip of it, the males are better at math than their female counterparts."),
                       br(), # this is for putting a space between the text and the plot which will appear below it
                       plotlyOutput("barplot_ggplot")
                       
                ),
                column(6,
                       div(h4("Performance analysis according to ethnic groups"),style="color:green"),
                       p("Its interesting to see how student performance varies according to their ethnic groups.
                         Group C is leading with a huge gap with those of group A."),
                       em("The diagram below is a bubble plot, please note that the size of the bubble corresponds to the mean score of the group."),
                       br(),
                       br(), 
                       plotlyOutput("bubble_plot")  
                       
                )
              )
            )
        )
        
        
    )
)


# Define server logic required to draw a histogram
server <- function(input, output) {

    
    # server code for the barplot
    output$barplot_ggplot <- renderPlotly({
        
        # calculating data summaries for plotting the barplot
        barplot_data <- stud_perf_melt[,.(mean_score=mean(score)),by=c("gender","subject")]
        
        # build the plot with plotly
           plot_ly( barplot_data,
               y = ~mean_score,
               x = ~subject,
               color = ~gender,
               text = ~paste0("Gender: ", gender, "\n", "Subject: ", subject,"\n","Mean score: ",mean_score)) %>%
           add_bars(showlegend=TRUE, hoverinfo='text' ) %>%
           layout(title="Mean gender performance",yaxis=list(title="Mean score"))#,
                  #xaxis = list(title = "Subject"),
                  #legend = list( title = list(text = "<b>Gender</b>"),orientation = "h"))
           
            
    })
    
    # server code for the density ridges plot 
    
    output$bubble_plot <- renderPlotly({
        
     # calculating data summaries for plotting the bubble plot
      bubbleplot_data <-  stud_perf_melt[,.(mean_score=mean(score)),by=c("race_ethnicity")] 
        
            plot_ly(bubbleplot_data) %>%
            add_trace(x = ~reorder(race_ethnicity, mean_score), 
                      y = ~mean_score,
                      size = ~mean_score,
                      color = ~race_ethnicity,
                      alpha = 4.5,
                      type = "scatter",
                      mode = "markers",
                      marker = list(symbol = 'circle', sizemode = 'diameter',
                                    line = list(width = 2, color = '#FFFFFF'), opacity=2.4)) %>%
            add_text(x = ~reorder(race_ethnicity, -mean_score), 
                     y = ~race_ethnicity, text = ~mean_score,
                     showarrow = FALSE,
                     color = I("black")) %>%
            layout(
                showlegend = FALSE,
                title="Mean score with respect to ethnic groups",
                xaxis = list(
                    title = "Race/Ethnic group"
                ),
                yaxis = list(
                    title = "Mean score"
                )
            ) 
    })
    
   
}

# Run the application 
shinyApp(ui = ui, server = server)

```

# Running the app

To run the application hit the Run App button on your RStudio IDE as show in the image below. 

![](/assets/rstudio_ide.jpg)  


# Further reading

1. [shiny tutorial](https://www.google.com/search?client=firefox-b-d&q=introduction+to+shiny) by RStudio    
2. [This book](https://mastering-shiny.org/index.html) by Hadley Wickham  
 