---
title: "Analysing salaries for United States using R"
author: "Clinton" 
category: R
tags:
 - r
 - data analysis
 - data visualization
---

   
The objective of this article is to answer below questions that touch on salaries in United States of America. The idea is to understand the various factors that determine differences in salaries for professionals.  

  * Which industry pays the most?    
  * How do the salaries of men and women compare?   
  * Does an increase in years of experience lead to more salary?    
  * Is there a significant difference in salaries for professionals with different levels of education?   
  * How do age groups compare in terms of head count in Computing or Tech?  

## A brief on the data  
The data used in this analysis was obtained from a [Salary Survey](https://www.askamanager.org/2021/04/how-much-money-do-you-make-4.html) done by askamanager.org. By the time I downloaded the data it had hit about 27k records, as of the time of writing this article its probably way above 27k. The data obtained has further been cleaned and wrangled and the script for that can be accessed [here](https://oyogo.github.io/r/2022/04/22/Data-cleaning.html).  

N/B: Data analysed is for the submissions from United States only.   

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, warning = FALSE, message = FALSE)
```

## Load libraries  
```{r}
library(data.table)
library(plotly)
```

## Import data into R 

```{r}
salary_data.US <- fread("data/salary_data_cleaned.csv")
```

## 1. Which industry pays the most?    

The list of industries is quite long, we will therefore plot the first 10, that's sufficient enough.  

```{r which industry pays the most?}

# calculate the average annual salary per industry
salary.by.industry <- salary_data.US[,.(avg.salary=mean(annual_salary)),
                                  by=.(industry)][,.(avg.salary = sort(avg.salary, decreasing = TRUE),industry)]

plot_ly(data = salary.by.industry[1:11,],
        x = ~reorder(industry,-avg.salary),
        y = ~avg.salary,
        type = "bar") %>% 
  layout(title = "Annual average Salary per Industry in U.S in 2021",
         yaxis = list(title = "Average annual salary", tickformat = "$"),
         xaxis = list(title = "Industry")
         )%>%
       config(displayModeBar = FALSE, displaylogo = FALSE, 
              scrollZoom = FALSE, showAxisDragHandles = TRUE, 
              showSendToCloud = FALSE)

```


As we can clearly see, Education, Accounting & Banking, Nonprofit industries are the top three most paying industries as per the data that was collected in the year 2021.  

## 2. How do the salaries for men and women compare?
First of all let's summarize our data grouped by gender to see the submissions per gender type.  

```{r}
salary_gender_summarized <- salary_data.US[,.(count=.N),by=.(gender)]

knitr::kable(salary_gender_summarized)
```

We filter the data to where the gender variable has Woman / Man. Let's drop the rest of the options.  

```{r}
gender.data <- salary_data.US[gender %in% c("Woman","Man" ),]
```

We can also note that there was such a huge difference in the total number of submissions from Women as compared to men. 
Anyway, back to the question we were addressing.   
Let's get the averages for the two groups and see how they compare.   

```{r}
# calculate the averages and select only the required variables 
gender.data <- gender.data[,.(avg.salary = mean(annual_salary)), by=.(gender)]
  
plot_ly(gender.data, 
        x = ~gender,
        y = ~avg.salary,
        color = ~gender,
        showlegend = FALSE,
        type = "bar") %>%
  layout(title = "Average annual salary - gender comparison in U.S in 2021",
         
         xaxis = list(title = "Gender"),
         yaxis = list(tickformat = "$", title = "Average annual salary"))%>%
       config(displayModeBar = FALSE, displaylogo = FALSE, 
              scrollZoom = FALSE, showAxisDragHandles = TRUE, 
              showSendToCloud = FALSE)
```

And we get to know that on average a man is payed more than a woman as per the data that was collected in the year 2021 in United States of America.   

## How do salaries compare with respect to years of experience    

The next question we migh want to address is how the relationship between salary and years of experience. Does salary change with years of experience ?  
To answer this question we will need to first of all order the column with years of experience into an ordered factor.   


```{r}
# change the column professional_experience_years into an ordered factor. 
salary_data.US$professional_experience_years <- factor(salary_data.US$professional_experience_years, levels = c("1 year or less","2 - 4 years","5-7 years",
                                                                                                                "8 - 10 years","11 - 20 years","21 - 30 years",
                                                                                                                "31 - 40 years","41 years or more"),ordered = TRUE)


# let us now calculate the average annual salary with respect to years of experience.  

salary.experience <- salary_data.US[,.(avg.salary=mean(annual_salary)),by=.(professional_experience_years)] %>% arrange(professional_experience_years)


# plot a line graph to see the relationship  

plot_ly(salary.experience,
        x = ~professional_experience_years,
        y = ~avg.salary,
        type = "scatter",
        line = list(color = "pink", width = 5),
        mode = "lines+markers") %>%
  layout(title="Average annual salary with respect to professional years of experience", 
         yaxis = list(title = "Average annual salary", tickformat = "$"),
         xaxis = list(title = "Professional years of experience"))%>%
       config(displayModeBar = FALSE, displaylogo = FALSE, 
              scrollZoom = FALSE, showAxisDragHandles = TRUE, 
              showSendToCloud = FALSE)
```


From the above linegraph its observable that there's likelihood of increase in salary with an increase in years of experience.  This tells us that experience level is an important factor in determining one's salary.    


## Your level of education, does it determine how much you'll earn?  
Let's see what the data says.  

```{r}

   salary.edulevel <-  salary_data.US[,.(avg.salary=mean(annual_salary)),by=.(highest_edu_level)]# %>% #arrange(-avg.salary) %>%
     salary.edulevel %>%  arrange(-avg.salary) %>%  
     plot_ly(
          type = 'scatterpolar',
          mode = "lines+markers",
          r = ~round(avg.salary,0),
          theta = ~highest_edu_level,
          fill = 'toself',
          text = ~ paste0("Level of education: ", salary.edulevel$highest_edu_level,"\n", "Average annual salary: ", round(salary.edulevel$avg.salary)),
          hoverinfo = "text"
        )%>%
        layout(
          title = "Which level of education attracts more salary?",
          polar = list(
            radialaxis = list(
              tickformat = "$",
              visible = T,
              range = c(0,max(salary.edulevel$avg.salary))
            )
          ),
          plot_bgcolor  = "rgba(0, 0, 0, 0)",
          paper_bgcolor = "rgba(0, 0, 0, 0)",
          fig_bgcolor   = "rgba(0, 0, 0, 0)",
          showlegend = F
          
        ) %>%
       config(displayModeBar = FALSE, displaylogo = FALSE, 
              scrollZoom = FALSE, showAxisDragHandles = TRUE, 
              showSendToCloud = FALSE)
        
```

Wow! nice to see how the radar chart shows an increase in salary as you go up the education level ladder. This information could be of help perhaps to one desiring to further their studies from say College degree to a Masters degree. As we saw above, years of experience result to a positive change in salary similar to the level of education. So then one might be asking themselves, Should I go for a masters or just gather more experience in the lapse of time which also results to salary increment? will the return on investment be worth it?   


## How do age groups compare in terms of head count in Computing or Tech   

Uhm, I am a tech person and I'd be interested in knowing what age bracket were most of the respondents in the Computing or Tech industry, at least from the sample.   
 

```{r}

salary.tech <- salary_data.US[industry == "Computing or Tech",.(count=.N),by=.(age)]  

# For this plot let us do a bubble chart just to explore the various data visualizations.  

salary.tech %>% 
  plot_ly() %>%
        add_trace(x = ~reorder(age, -count), 
                  y = ~count,
                  size = ~count,
                  color = ~age,
                  alpha = 1.5,
                  type = "scatter",
                  mode = "markers",
                  marker = list(symbol = 'circle', sizemode = 'diameter',
                                line = list(width = 2, color = '#FFFFFF'), opacity=0.8)) %>%
        add_text(x = ~reorder(age, -count), 
                        y = ~age, text = ~count,
                        showarrow = FALSE,
                        color = I("black")) %>%
        layout(
          showlegend = FALSE,
          title="Most tech guys are from which age group?",
          xaxis = list(
            title = "Age group"
          ),
          yaxis = list(
            title = "Number of Tech people from the sample."
          )
        ) %>%
        config(displayModeBar = FALSE, displaylogo = FALSE, 
              scrollZoom = FALSE, showAxisDragHandles = TRUE, 
              showSendToCloud = FALSE)
```

Amazing !   
Quite interesting to note that most Computing or Tech guys are mostly between 25 - 44years, at least from the sample. This may not be conclusive seeing that our sample is not MECE (Mutually exclusive and Collectively exhaustive), but it does serve to give indications that are close to what all know, I mean, anyone can almost guess that most techies are probably young.    

