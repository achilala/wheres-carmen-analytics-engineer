# Skill Assessment: _dbt_ Analytics Engineer

## Background

For this assessment, please build a dbt project that addresses the problem listed below. Ensure that you have it cloned to a git-based sharable repository, and that you have a README.md file in the repository's root discussing your analysis for the problem. You may use whichever SQL database flavor you prefer, just state which was chosen in your README.md. We use Snowflake, and the platform offers a free two-week trial, which is ideal for this kind of exercise. For dbt, you can use either dbt Core or dbt Cloud. We use dbt Cloud, which offers a free developer license.

---

## Problem: *Where in the World is Carmen Sandiego*? 

<table>
  <tr>
    <td> <img src="https://www.mobygames.com/images/covers/l/32898-where-in-the-world-is-carmen-sandiego-deluxe-edition-dos-front-cover.jpg" alt="WIWICSD" width=150px/></td>
    <td width=450px> 
        Some may know this <a href="https://en.wikipedia.org/wiki/Carmen_Sandiego_(video_game_series)">popular game franchise</a> in which players answer questions to solve the titular riddle. Your task today is an homage to this game! You've recently come aboard Interpol's team as a data engineer. Their dedicated data team has collected, parsed, and assembled several agent field reports over the years, but in Excel only. Their final collection is provided in the next subsection - your task is to engineer this data to provide analytical answers to the Interpol team!
    </td>
  </tr>
</table>

---

## Data Sources & Common Model Development

The data is contained in the attached Excel workbook [*carmen_sightings_20220629061307.xlsx*](./carmen_sightings/carmen_sightings_20220629061307.xlsx). Note the sheets are organized by eight (nearly continential) **regions** - there is an Interpol agency HQ in a city of each region to which the agents report. Each agency HQ originally compiled their regional reports using their own language or dialect for column names, but those reports are in [first normal form (1NF)](https://en.wikipedia.org/wiki/First_normal_form). 

> 1. _The first step of your task is to extract data from Excel workbook, treating as initial sources._
> **HINT:** _CSV exports into `seeds` - whether by Excel or pandas - is a great way to start..._ :eyes:

Originally, agencies were free to name report columns according to their custom - let's call each "yablaka" an apple! :apple: For this exercise, though, each source's columns have already been renamed to a _common data dictionary_ of 

| Column | Description | Type |
| ------------ | ----------- | -----|
| date_witness | Date of witness sighting | date |
| witness | Name of witness sighting the perpetrator | string |
| agent | Name of field agent filing the report | string |
| date_agent | Date of field agent filing the report | date |
| city_agent | HQ city where field agent files the report | string |
| country_code | ISO 3166-1 alpha-2 country code of sighting | string | 
| city | City of sighting | string |
| latitude | Latitude of sighting | float |
| longitude | Longitude of sighting | float |
| has_weapon | Was the perpetrator observed to be armed? | boolean |
| has_hat | Was the perpetrator wearing a hat? | boolean |
| has_jacket | Was the perpetrator wearing a jacket? | boolean |
| behavior | Short description of perpetrator behavior | string |

 > `FYI: For Analytics Engineers, the first step has already been completed for you, as we are not testing your Python coding abilities. The extracted CSV files are available in the carmen_sightings/ folder, with column names already matching the data dictionary above. Therefore, you can skip step 1 and proceed directly to step 2. :thanks:`

> 2. _The second step of your task is to create a staging view model per source, one for each of the eight regions, applying any type casting or light cleanup needed so each conforms cleanly to the common data dictionary above._ 
> **HINT:** _You can do this as you wish - via CTE stages, macros, go wild! The end result however must be a view model for each source._ 

Now that you have eight models, all with the same columns - join them together, but with a caveat:

> 3. _The third step of your task is to join the eight different views into ONE schema that goes beyond 1NF (\[2-6\]NF, BCNF). You have a great deal of design freedom here, so get creative! Just persist final resulting schema as tables into a new schema - please present your design's entity-relation-diagram or dbt DAG in your README.md._

---

## Analytics

* This new schema includes the >1NF model you've just developed, as tables. From this model, it ought be fairly straightforward for you to create analytical view(s) to answer the following questions:

    a. For each calendar month (i.e. aggregating all Januaries together, all Februaries together, etc. across every year in the data), which agency region has the highest number of sightings?

    b. Also for each calendar month, out of all sightings recorded in that month, what proportion show Ms. Sandiego as armed __AND__ wearing a jacket __AND NOT__ wearing a hat? Briefly note any pattern you observe (e.g. seasonality, a trend by region, etc.).

    c. Across the entire dataset, what are the three most occurring behaviors of Ms. Sandiego?

    d. For each calendar month, out of all sightings recorded in that month, what proportion exhibit one of the three behaviors identified in question (c)?

> 4. _Create analytical views in your new schema to answer the four above questions. Document your steps and logic in your README.md._
> **HINT:** _`dbt docs` (and its screenshots) are a great resource!_

---

## Submission

>**NOTE:** Throughout, I've referenced `README.md`. If you are unfamiliar with [GitHub Markdown](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax), feel free to use your most convenient method: Word document, Google Doc, textfile (with image attachments), html - however you can best communicate your thoughts and ideas!

* **Ensure that your project runs fully BEFORE submission!** 

* Verify that you have the four analytical questions answered in your README.md, and you are confident with your presentation. 

* Push and merge into your git repository's main branch.

Enjoy the challenge and good luck!



## Setting up an environment if using Snowflake and dbt Cloud

### Snowflake
```sql
-- drop environment objects if already exist
drop database if exists wheres_carmen_db;
drop user if exists wheres_carmen_user;
drop role if exists wheres_carmen_role;
drop warehouse if exists wheres_carmen_wh;

-- create environment objects
create database wheres_carmen_db;
create role wheres_carmen_role;
create warehouse wheres_carmen_wh warehouse_size = 'XSMALL';
create user wheres_carmen_user default_role = wheres_carmen_role default_warehouse = wheres_carmen_wh;

-- grant warehouse to role engineer
grant usage on warehouse wheres_carmen_wh to role wheres_carmen_role;
grant operate on warehouse wheres_carmen_wh to role wheres_carmen_role;

-- grant all privileges on db to role engineer
grant all privileges on database wheres_carmen_db to role wheres_carmen_role;

-- grant engineer role to user trademe
grant role wheres_carmen_role to user wheres_carmen_user;

-- set a user password
alter user wheres_carmen_user reset password;
```

### dbt Cloud
[Example dbt Cloud environment configuration](./example_dbt_cloud_credentials.png)