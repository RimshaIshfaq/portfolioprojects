Hey guys, I am Rimsha Ishfaq. I am a last-semester student of Industrial and Manufacturing Engineering at UET-Lahore. Here I will tell you about my first SQL project.

Introduction:
In data analysis, unraveling the stories hidden within vast datasets requires adept tools and methodologies. Amidst the global pandemic, understanding the trends of coronavirus-related data has become paramount. Using the wealth of publicly available data from ourworldindata.org, this article delves into a comprehensive exploration of coronavirus deaths and vaccinations through SQL.

By harnessing SQL’s querying capabilities, I embark on this journey to unearth patterns, correlations, and trends within the coronavirus data. Through this exploration, I aim to comprehend the current state of the pandemic and pave the way for informed strategies and interventions. Join me as I embark on this journey with SQL. SQL

Questions of Interest:
Here are the questions of interest:

· Which countries have the highest infection rates relative to their population size?

· What are the countries with the highest death count per population?

· How do death counts vary by continent?

· What is the Death percentage for COVID-19 in Pakistan and how does it change over time?

· What is the distribution of vaccinations globally, and how does it compare to the total population of each country?

I started the data cleaning process in Excel and used the filter function to make the work go faster. Now that the data was prepared, I could concentrate on choosing the most important indicators to examine. First, the overall number of COVID-19 cases and the associated death toll were compared.
select *
from articulate-fort-406914.covid_dataset.Covid_deaths
order by 3,4


select *
from articulate-fort-406914.covid_dataset.Covid_Vaccination
order by 3,4


--select data that we are going to use
select location, date, total_cases,new_cases,total_deaths,population
from articulate-fort-406914.covid_dataset.Covid_deaths
order by 1,2


--looking at total cases vs. total deaths
select location, date, total_cases,total_deaths,(total_deaths/total_cases)*100 as DeathPercentage
from articulate-fort-406914.covid_dataset.Covid_deaths
order by 1,2


select location, date, total_cases,total_deaths,(total_deaths/total_cases)*100 as DeathPercentage
from articulate-fort-406914.covid_dataset.Covid_deaths
where location = "United States"
order by 1,2
--shows likelihood of dying if you contract covid in Pakistan
select location, date, total_cases,total_deaths,(total_deaths/total_cases)*100 as DeathPercentage
from articulate-fort-406914.covid_dataset.Covid_deaths
where location = "Pakistan"
order by 1,2


--looking at total cases vs population
--shows what percentage of population got covid
select location, date,population,  total_cases,(total_cases/population)*100 as Percentpopulationinfected
from articulate-fort-406914.covid_dataset.Covid_deaths
where location = "Pakistan"
order by 1,2


--looking at countries with highest infection rate compared to population
select location,population,  max(total_cases) as highestinfectioncount,max(total_cases/population)*100 as Percentpopulationinfected
from articulate-fort-406914.covid_dataset.Covid_deaths
group by location,population
order by 1,2




--looking at countries with highest infection rate compared to population
select location,population,  max(total_cases) as highestinfectioncount,max(total_cases/population)*100 as Percentpopulationinfected
from articulate-fort-406914.covid_dataset.Covid_deaths
group by location,population
order by Percentpopulationinfected desc




--showing countries with highest death count per population
select location, MAX(total_deaths) as TotalDeathcount
from articulate-fort-406914.covid_dataset.Covid_deaths
group by location
order by TotalDeathcount desc


--showing countries with highest death count per population
select location, MAX(cast(total_deaths as int)) as TotalDeathcount
from articulate-fort-406914.covid_dataset.Covid_deaths
group by location
order by TotalDeathcount desc

--showing countries with highest death count per population
select location, MAX(cast(total_deaths as int)) as TotalDeathcount
from articulate-fort-406914.covid_dataset.Covid_deaths
where continent is not null
group by location
order by TotalDeathcount desc

--Let's break things down by continent
select continent, MAX(cast(total_deaths as int)) as TotalDeathcount
from articulate-fort-406914.covid_dataset.Covid_deaths
where continent is not null
group by continent
order by TotalDeathcount desc


--global numbers
select date,sum(new_cases)
from articulate-fort-406914.covid_dataset.Covid_deaths
where continent is not null
group by date
order by 1,2


select date,sum(new_cases), sum(cast(new_deaths as int))
from articulate-fort-406914.covid_dataset.Covid_deaths
where continent is not null
group by date
order by 1,2






select date, sum(new_cases), sum(cast(new_deaths as int)), sum(cast(new_deaths as int))/sum(new_cases)*100 as Deathpercentage
from articulate-fort-406914.covid_dataset.Covid_deaths
where continent is not null
group by date
order by 1,2




select date, sum(new_cases) as total_cases, sum(cast(new_deaths as int)) as total_deaths, sum(cast(new_deaths as int))/sum(new_cases)*100 as Deathpercentage
from articulate-fort-406914.covid_dataset.Covid_deaths
where continent is not null
group by date
order by 1,2




select sum(new_cases) as total_cases, sum(cast(new_deaths as int)) as total_deaths, sum(cast(new_deaths as int))/sum(new_cases)*100 as Deathpercentage
from articulate-fort-406914.covid_dataset.Covid_deaths
where continent is not null
--group by date
order by 1,2




….
select *
from articulate-fort-406914.covid_dataset.Covid_Vaccination
 ______________
select *
from articulate-fort-406914.covid_dataset.Covid_deaths as dec
join articulate-fort-406914.covid_dataset.Covid_Vaccination as vac
on dec.location = vac.location
and dec.date = vac.date
………………………………….


--Looking at Total population vs. Vaccinations
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
from articulate-fort-406914.covid_dataset.Covid_deaths as dea
join articulate-fort-406914.covid_dataset.Covid_Vaccination as vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3
—-------------------


--Looking at Total population vs. Vaccinations
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, sum(cast(vac.new_vaccinations as int)) OVER (Partition by dea.location order by dea.location, dea.date) as RollingPeopleVaccinated
from articulate-fort-406914.covid_dataset.Covid_deaths as dea
join articulate-fort-406914.covid_dataset.Covid_Vaccination as vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3
—------------------


--temp table
Drop table if exists #PercentPopulationVaccinated
CREATE TABLE #PercentPopulationVaccinated
(
  continent nvarchar(255),
  location nvarchar(255),
  date datetime,
  population numeric,
  New_vaccinations numeric,
  RollingPeopleVaccinated numeric
)


insert into #PercentPopulationVaccinated
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, sum(cast(vac.new_vaccinations as int)) OVER (Partition by dea.location order by dea.location, dea.date) as RollingPeopleVaccinated
from articulate-fort-406914.covid_dataset.Covid_deaths as dea
join articulate-fort-406914.covid_dataset.Covid_Vaccination as vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null


select *, (RollingPeopleVaccinated/Population)*100
from #PercentPopulationVaccinated






Finally, to assess the correlation between total population and vaccination coverage, I executed the SQL query.


Thank you for taking the time to review my project. I am open to any suggestions or feedback you may have. 
