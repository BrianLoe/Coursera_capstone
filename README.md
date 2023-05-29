# Coursera IBM Applied Data Science Capstone
## Introduction
Space exploration aims to gather and discover scientific knowledge that could expand human understanding about the universe. 
With great interest, many space companies have tried to compete with each other to seek if any other planets are suitable for habitation.
However, the cost for launching rockets are not economical. So, Space X aims to reuse their first stage rocket launch to reduce the cost. This could prove much savings for rocket launches in the future and greatly expand our knowledge about the space.  

In this project, I used CRISP-DM process to make a prediction on a rocket launch first stage landing succession based on historical data of Space X Falcon 9 rocket launches. From business understanding, this project aims to share information to alternate companies that wants to bid against Space X for rocket launch. The information provided is to help determining whether I can predict if the first stage of rocket launch will be successfull and thus can detemine the cost. I start with data collection, data wrangling, exploratory data analysis (EDA) with seaborn library and SQL, visual analytics, and predictive analytics.

## Methodology
1. Data collection (Space X API and webscraping)
2. Data wrangling
3. Exploratory data analysis (EDA)
4. Interactive visual analytics (Dashboard) using dash
5. Predictive analytics (Classification)

## 1. Data Collection
The data was collected through 2 methods: Space X API wit REST and webscraping Space X wikipedia page.
### a. Space X API
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/7dcfad97-b526-4a01-b5dc-343de2052383)  
Description:  
Data from Space X API is requested using REST API. The requested data is in json format so, I need to transform it into format that is readable by pandas dataframe. To do that, I just need to use `json_normalize` function from pandas. It allows quick normalisation from json format into pandas dataframe. There was some features that are not interesting in the raw data. So, I only captured features such as rocket (booster name), payloads (mass and orbit type), launchpad (site name, latitude, longitude), cores (landing outcome, type, number of flights, gridfins use, reused cores, legs use, landing pad use, block of the core, number of times core has been reused, and the core serial).  

There is a small cleaning here, for example, rows with multiple cores (falcon rockets with 2 extra boosters and rockets with multiple payloads), converting date type, and restrict the launch dates into before 14/11/2020. After getting the four features, I constructed a new dataframe to contain all the features. We are only interested in Falcon 9 rocket launches so I filtered the data into only containg those rockets data.
### b. Webscraping wikipedia page
