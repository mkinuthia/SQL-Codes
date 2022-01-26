In this case study, I used the publicly available covid19 dataset to analyze the case count, deaths and vaccinations regionally and globally from January 28, 2020 to November 28, 2021. These are the queries I ran on Google Bigquery


#Covid19 Deaths Table

--Checking the data on CovidDeaths Table

SELECT *
FROM projectportfolio-254.covid19.covid_deaths
ORDER BY location, date

--Selecting the data we need for our analysis

SELECT 
  location, 
  date, 
  total_cases, 
  new_cases,
  new_deaths, 
  total_deaths, 
  population
FROM projectportfolio-254.covid19.covid_deaths
ORDER BY 
  location,
  date


#Global Cases, Global Deaths and Global Death Rate

--Shows the Total of Global Cases, Global Deaths & Global Death Rate 

SELECT
  SUM(new_cases) AS global_case_count,   
  SUM(new_deaths) AS global_death_count,
  SUM(new_deaths)/SUM(new_cases)*100 AS global_death_rate
FROM projectportfolio-254.covid19.covid_deaths
WHERE continent is NOT NULL 
ORDER BY 
  global_case_count,
  global_death_count








#Covid19 Cases and Deaths by Country 

--Shows covid19 cases and deaths by country over time 

SELECT 
  location, 
  date,  
  new_cases,
  new_deaths, 
  population
FROM projectportfolio-254.covid19.covid_deaths
ORDER BY 
  location,
  date


#Countries with the Highest Infection Rates per Population 

--Highest Infection Rate 
--Shows top 10 countries with the highest infection rate compared to their population

SELECT 
  location, 
  population,
  SUM(new_cases)/population*100 AS population_infection_rate
FROM projectportfolio-254.covid19.covid_deaths
GROUP BY 
  location,
  population
ORDER BY 
  population_infection_rate DESC
LIMIT 10 


--Breaking down by Continent

#Total Deaths by Continent 

--Total Deaths by Continent

SELECT 
  continent,  
  SUM(new_deaths) AS total_death_count
FROM projectportfolio-254.covid19.covid_deaths
WHERE continent is NOT NULL
GROUP BY 
  continent
ORDER BY 
  total_death_count DESC




#Covid19 Stats in Africa People Fully Vaccinated by Continent

SELECT 
  SUM(new_cases) AS Af_case_count,   
  SUM(new_deaths) AS Af_death_count,
  SUM(new_deaths)/SUM(new_cases)*100 AS Af_death_rate
FROM projectportfolio-254.covid19.covid_deaths
WHERE continent = 'Africa' 
ORDER BY 
  Af_case_count,
  Af_death_count



##Covid19 Vaccinations Table

--Checking the data on CovidVaccinations Table

SELECT *
FROM projectportfolio-254.covid19.covid_vaccinations

-- Joining the two tables
-- Showing the join on the deaths and vaccinations tables

SELECT *
FROM projectportfolio-254.covid19.covid_deaths AS dea
 JOIN projectportfolio-254.covid19.covid_vaccinations AS vaxx
  ON dea.location = vaxx.location
  AND dea.date = vaxx.date
	

#People Fully Vaccinated by Continent 
--Shows the number of people fully vaccinated by continent

SELECT 
  continent,
   MAX(people_fully_vaccinated) AS TotalVaxx,
   MAX(total_boosters) AS TotalVaxxBoost
FROM projectportfolio-254.covid19.covid_vaccinations
WHERE continent is NOT NULL
GROUP BY 
  continent
ORDER BY 
  Continent




#Countries with the Most Fully Vaccinated People 
--Shows the top 10 countries with fully vaccinated people in Africa
SELECT *
FROM
(
SELECT 
  continent,
  location,
   MAX(people_fully_vaccinated) AS TotalVaxx,
   MAX(total_boosters) AS TotalVaxxBoost
FROM projectportfolio-254.covid19.covid_vaccinations
WHERE continent is NOT NULL
GROUP BY 
continent,
  location
ORDER BY 
    TotalVaxx DESC,
    location
)
WHERE continent = 'Africa' 
LIMIT 10    



#Daily Cases, Deaths and Number of People Fully vaccinated in Kenya
 
--Daily number of total cases, total deaths, death rate and people fully vaccinated in Kenya

WITH CasesvsDeathsKE AS	
(
SELECT 
  location, 
  date, 
  total_cases,  
  total_deaths,
  people_fully_vaccinated,
  FROM projectportfolio-254.covid19.covid_deaths
WHERE location = 'Kenya'
ORDER BY 
  date
)
SELECT *,  (total_deaths/total_cases)*100 AS percentage_deaths
FROM  CasesvsDeathsKE










#Daily Cases, Deaths and Number of People Fully vaccinated per Country

--Creating a temporary table showing daily cases, daily deaths and number of people fully vaccinated every day per country

WITH GlobalCount AS
(
SELECT 
 death.continent,
 death.location,
 death.date,
 death.new_cases, 
 death.new_deaths,     
 vaxx.people_fully_vaccinated
FROM projectportfolio-254.covid19.covid_deaths AS death
 JOIN projectportfolio-254.covid19.covid_vaccinations AS vaxx
  ON death.location = vaxx.location
  AND death.date = vaxx.date
WHERE death.continent is NOT NULL  
ORDER BY death.location, 
         death.date,
         death.continent  
)
SELECT *,
       IFNULL(new_cases, 0) AS new_case_count,
       IFNULL(new_deaths, 0) AS new_death_count,
       IFNULL(people_fully_vaccinated, 0) AS people_fully_vaxxed,
FROM GlobalCount



##Data Visualizations on my Tableau Page
 
Link to the data visualizations:
https://public.tableau.com/app/profile/maggie.k.

