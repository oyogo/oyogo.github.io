# Automated GPM Data download & processing pipeline   
Precipitation data is one of the highly sort satelite data owing to its contribution to agriculture, energy and climate related studies. The data can be obtained from gpm through google earth engine. A good example of its use is in climate/weather analyses/forecasts and this often times necessitates the need to access the data frequently or have it updated daily,monthly or quarterly depending on the use case.  
Updating the data consumes time which can be engaged elsewhere and this is what lead me to automate the process so that I can concentrate on other tasks.  
Below are the steps needed to set up the pipeline.   

## Create a Google earth engine account  
First of all you need a google earth engine account. Visit [here](https://earthengine.google.com/) and sign up.
I won't go to the details of how google earth engine works, if you're new to it you might want to spend some time to understand how the interface works. Check out their [documentation](https://developers.google.com/earth-engine/guides) for more details. 

## A service account  
We will need a service account for our workflow reason being we're building a pipeline which will be running on a server and given the nature of Earth Engine's authentication, we won't be able to attend to prompts on the browser in order to access the assets and services. This is what necessitates a service account. With this, our data download script will run from a server without having someone respond to the prompts on the browser (what happens when you authenticate to GEE with a user account).   

Use the steps detailed [here](https://developers.google.com/identity/protocols/oauth2/service-account#creatinganaccount) to Create a service account. 
Download the key and put it in a folder of your choice. You will reference the key on the python script (data download script).

Note: Ensure you give an absolute path to your private key on the python script.

## Getting your environment ready  
1. Create a virtual environment (python) 
This is where you'll install the required packages for the pipeline. Its always a good practice to install the dependencies for your projects in a virual environment. 
Open your terminal and enter the below command to create a virtual environment. 
 N/B assuming you have already installed python in your system. 
```
python3 -m venv /path/to/your/environment 

```
Note: replace the _/path/to/your/environment_ accordingly ! 

2. Activate the environment and install libraries 
To activate your environment and install the required libraries do the following: 

 * navigate into your environment and get into the bin folder
 
 ```
source /path/to/your/environment/bin/

 ```

  * Install the libraries  

 ```
 pip install geemap  #one by one 
 pip install -r requirements.txt  #all at once 
 
 ```
 
The pipeline has two scripts: 
 * A python script for downloading the data and 
 * R script for wrangling the data and versioning it on the server.  
 
The two scripts are run as cron jobs, more on the automation section.    

## Data download  
The python script fetches the images from NASA and preprocesses it on GEE (Google Earth Engine). The resulting data can either be sent to google drive or downloaded using a url. Of importance when creating the download script is to create a date variable for the previous month that enables the script to fetch data for the previous month and names it with the month variable.  

## Data wrangling  
The data processing and versioning is done using R. The R and Python scripts are linked to a common folder to which the python script downloads the data and from which the R scipts accesses the data for preprocessing.  The resulting preprocessed data is then versioned using the pins package such that one can revert back to previous state in case of any corruption/loss of data. Take into consideration the date variable as well (previous month) as the script will need to use the variable to fetch thr right image from the data folder. You will also need a shapefile of the region of interest which will be used to clip the data to the region of interest.   

## Automation    
With crontab, schedule two cronjobs that runs say once a month whereby you could set the download script to run on the 10th day of the month at 10:00am followed by the wrangling/processing script on the same day at 10:20am.  With this in place we have our pipeline set and the data will be updated automatically. 

There's a catch on when to set up your scripts to run, notice, I've mentioned the 10th day, this is due to the fact that some satelite data products are not readily available as in the case of GPM Data (NASA/GPM_L3/IMERG_V06 product) which goes through some processing, this takes time hence the need to give some time allowance. However, this really depends on the product you want to get, do your diligence to research more about it as you'll always get such details in the process.    
 

```
30 11 10 * * /path/to/your/gpm_datadownload.py >> /path/to/pylog.txt 2>&1
50 11 10 * * /usr/local/bin/Rscript "/path/to/your/script.R" >> /path/to/munge.txt 2>&1  

``
The python script runs at 11:30am on the 10th day of every month and the R script 20mins later.   
Note: you could put a shebang line on the R script so that you call it without necessarily referencing the executable.      

## Logging  
The following bit of the cronjob creates a log file named munge.txt (for the munge_update.R script) and pylog.txt (for the gpm_datadownload.py script) inside the folder where you've directed it to. With this, we'll be able to check the logs of the scripts afterwards.  

```
  ...  >> /path/to/munge.txt 2>&1 

```

Happy coding !!