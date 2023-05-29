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
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/33923685-ead4-4a2c-9d03-cd75355e13af)  
Description:
Data from wikipedia page is extracted using `BeautifulSoup` library. Using `GET` function from `requests`, I parse the data that is in html format using `BeautifulSoup`. The actual table that contain the data is in the third table. I extracted all relevant columns from that table using function that was defined by IBM. After getting all columns name, I created a dictionary with them as keys. Iterate through the table elements to extract the data needed and store it based on the dictionary keys created earlier. Construct a dataframe from the dictionary so it is easier to read and export for further use.

## 2. Data Wrangling
Now that I have collected the data, I need to understand the data, the columns, and the values. Identifying is the main aspect in this stage including calculating the number of missing values. All launch site names, orbit names and the number of launches as well as all type of mission outcomes need to be identified here.
There are 4 launch sites:
- CCAFS SLC 40
- CCAFS LC 40
- KSC LC 39A   
- VAFB SLC 4E  

Most of the rockets were launched at CCAFS SLC 40 followed by KSC LC 39A, and VAFB SLC 4E. Each launch aims to a dedicated orbit. Here is the common orbit types:

* <b>LEO</b>: Low Earth orbit (LEO)is an Earth-centred orbit with an altitude of 2,000 km (1,200 mi) or less (approximately one-third of the radius of Earth),[1] or with at least 11.25 periods per day (an orbital period of 128 minutes or less) and an eccentricity less than 0.25.[2] Most of the manmade objects in outer space are in LEO <a href='https://en.wikipedia.org/wiki/Low_Earth_orbit?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01'>[1]</a>.

* <b>VLEO</b>: Very Low Earth Orbits (VLEO) can be defined as the orbits with a mean altitude below 450 km. Operating in these orbits can provide a number of benefits to Earth observation spacecraft as the spacecraft operates closer to the observation<a href='https://www.researchgate.net/publication/271499606_Very_Low_Earth_Orbit_mission_concepts_for_Earth_Observation_Benefits_and_challenges?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01'>[2]</a>.


* <b>GTO</b> A geosynchronous orbit is a high Earth orbit that allows satellites to match Earth's rotation. Located at 22,236 miles (35,786 kilometers) above Earth's equator, this position is a valuable spot for monitoring weather, communications and surveillance. Because the satellite orbits at the same speed that the Earth is turning, the satellite seems to stay in place over a single longitude, though it may drift north to south,” NASA wrote on its Earth Observatory website <a  href="https://www.space.com/29222-geosynchronous-orbit.html?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01" >[3] </a>.


* <b>SSO (or SO)</b>: It is a Sun-synchronous orbit  also called a heliosynchronous orbit is a nearly polar orbit around a planet, in which the satellite passes over any given point of the planet's surface at the same local mean solar time <a href="https://en.wikipedia.org/wiki/Sun-synchronous_orbit?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01">[4] <a>.
    
    
    
* <b>ES-L1 </b>:At the Lagrange points the gravitational forces of the two large bodies cancel out in such a way that a small object placed in orbit there is in equilibrium relative to the center of mass of the large bodies. L1 is one such point between the sun and the earth <a href="https://en.wikipedia.org/wiki/Lagrange_point?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01#L1_point">[5]</a> .
    
    
* <b>HEO</b> A highly elliptical orbit, is an elliptic orbit with high eccentricity, usually referring to one around Earth <a href="https://en.wikipedia.org/wiki/Highly_elliptical_orbit?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01">[6]</a>.


* <b> ISS </b> A modular space station (habitable artificial satellite) in low Earth orbit. It is a multinational collaborative project between five participating space agencies: NASA (United States), Roscosmos (Russia), JAXA (Japan), ESA (Europe), and CSA (Canada)<a href="https://en.wikipedia.org/wiki/International_Space_Station?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01"> [7] </a>


* <b> MEO </b> Geocentric orbits ranging in altitude from 2,000 km (1,200 mi) to just below geosynchronous orbit at 35,786 kilometers (22,236 mi). Also known as an intermediate circular orbit. These are "most commonly at 20,200 kilometers (12,600 mi), or 20,650 kilometers (12,830 mi), with an orbital period of 12 hours <a href="https://en.wikipedia.org/wiki/List_of_orbits?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01"> [8] </a>


* <b> HEO </b> Geocentric orbits above the altitude of geosynchronous orbit (35,786 km or 22,236 mi) <a href="https://en.wikipedia.org/wiki/List_of_orbits?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01"> [9] </a>


* <b> GEO </b> It is a circular geosynchronous orbit 35,786 kilometres (22,236 miles) above Earth's equator and following the direction of Earth's rotation <a href="https://en.wikipedia.org/wiki/Geostationary_orbit?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01"> [10] </a>


* <b> PO </b> It is one type of satellites in which a satellite passes above or nearly above both poles of the body being orbited (usually a planet such as the Earth <a href="https://en.wikipedia.org/wiki/Polar_orbit?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork865-2023-01-01"> [11] </a>

![](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DS0701EN-SkillsNetwork/api/Images/Orbits.png)  

For the mission outcome, there are 8 different combinations:
- `True ASDS`
- `None None`
- `True RTLS`
- `False ASDS`
- `True Ocean`
- `False Ocean`
- `None ASDS`
- `False RTLS`

We need to separate the good and bad outcomes. Good outcomes are those that have 'True' and bad outcomes have either 'None' or 'False'. From there, I separated them for labelling and created a `class` column. I found out that the success rate for the data is 67%. Here are some explanation about the mission outcomes: 
 `True Ocean` means the mission outcome was successfully  landed to a specific region of the ocean while `False Ocean` means the mission outcome was unsuccessfully landed to a specific region of the ocean. `True RTLS` means the mission outcome was successfully  landed to a ground pad `False RTLS` means the mission outcome was unsuccessfully landed to a ground pad. `True ASDS` means the mission outcome was successfully  landed to a drone ship `False ASDS` means the mission outcome was unsuccessfully landed to a drone ship. `None ASDS` and `None None` these represent landing failure.

## 3. EDA
Tools to do the EDA are SQL and data visualisation with Python. With data visualisation I created plots for observing relationships between two numerical variables, two categorical variables, and numerical with categorical variables. With SQL, I explored some simple queries to find out more about detailed information. After that, I can do feature engineering and also one-hot encoding for categorical variables (data preparation). Summary results from EDA:
- Different launch sites have different success rate. VAFB SLC 4E and KSC LC 39A have the highest successful rocket launches
- Some sites depend on the payload mass to achieve a successful outcome.
- Orbits SSO, HEO, GEO, and ES-L1 have the highest success rate (100%).
- Some orbits depend on the number of flights to achieve a successful outcome.
- There is a weak positive relatonship between payload mass and outcome
- After 2013, the success rate for landing rockets have increased where it peaks in 2019 (+-90%).
 
In addition, I also performed analysis using geospatial features with `folium'. Particularly, we are intersted in visualising success/fail launches based on each site, proximity place of interest (city, railway, highway, coastline). Here is the pictures of the map:
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/5850499b-4d3c-4b1e-8e85-bcb9a13a6fa7)  
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/cdaf58cc-9f61-442d-ba02-c232f38954df)
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/f902cd1f-961d-4732-801c-d8ea98c26aa5)
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/d2b8c881-2374-49a5-a3ea-b5345c72700c)
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/dbd3fa6f-f1e1-4014-b87b-db751f36b079)
![image](https://github.com/BrianLoe/Coursera_capstone/assets/58500773/7e7f5ffe-448c-453c-8dd9-210d022bdacb)

## 4. Interactive visual analytics using `Dash`





