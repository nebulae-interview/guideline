# Interview Guidelines


## Intro

This GitHub organization and its repositories are intended for people wanting to become part of Nebula Engineering development team.  
NebulaE solutions are based on our own MicroService Framework.  The main difference or advantage of our framework is that we have achieved truly fullstack microservice, each microservice is totally independent and its repo contains the whole stack in it: FronEnd, API, Backend, Persistance + IT Deployment. 

The idea is that you can run the interview chanllenge within the framework itself, to do so we are going to give you some guidelines to understand a little bit about the microservice framework and as well the guideline for the test itself.  

If you have some inquiry please write to: sebastian.molano@nebulae.com.co

## Architecture overview

In order to being able to understand the porject structure and the reasoning behind NebulaE's Framework please review the [Architecture Overview](../master/architecture_overview.MD)
 
## Setup your own fullstack micro-service local environment

Our Framework delivers a truly fullstack local environment for the micro-service you will be working on.  Please reade the [development environment setup](../master/development-environment-setup.MD) guide so you can start with the code challenge.

## Code challenge

The idea is to build a news viewer from different news sources.  We are going to use [News API](https://newsapi.org/) as our main news source.  
The first step is to register at [News API](https://newsapi.org/), get an [API KEY](https://newsapi.org/register) and understand the [documentation](https://newsapi.org/docs).  

Then we are going to create a CRUD for [News Sources](https://newsapi.org/docs/endpoints/sources), the CRUD is composed by a listing and a detail for each news source.  In the detail page we are going to be able to query the news source and retrieve news from it, these news are going to be displayed as cards on the detial page.  

### Generate µService
Generate a µService using `nebulae CLI`
- micro-service name: ms-interview-[first_name]-[last-name], Eg.  ms-interview-sebastian-molano
- crud aggregate: source  

*CLI command:*  
`nebulae generate microservice --frontend-id emi --api-id emi-gateway --project-context INTERVIEW --template-git-url https://github.com/nebulae-interview/ms-micro-service-template  --git-repo-directory-path interview  --repo-git-url https://github.com/nebulae-interview/ms-interview --crud-entity source`

Note: Please add your [first_name]-[last-name] to the above line.

This command is going to generate a fullstack microservice with a simple (and fully functional) CRUD of `sources`.  This is the baseline to code the whole challenge.  

### Challenges

#### 00 General aspects for the challenge
- Everything must be in spanish and english using i18n
- Everything must be with responsive-design in mind. being PC and mobile phones the two most important screen sizes
- Use components from [material-ui](https://v4-3-3.material-ui.com/components) whenever possible
- Use tailwind-css whenever possible
- In the Backend try to use RxJS as much as possible


#### 01 Add the the fields needed for each source.

##### 01.1 Add fields in the detail page
in the detail page (http://localhost:4000/source-mng/sources/new) in the `basic info` tab set these fields:  
- newsApiSourceId (String): refers to the id used by NewsAPI for each source  
  - Validation: regex: lowercase-alphanumeric and dash only.  (eg: `abc-news`, `bbc-news`, `il-sole-24-ore`, `independent` )  
  - Validation: Must bu unique  
- name (String): News source name  
- description (String): News source description  
  - Validation: Min size 0 characters, Max size 100 characters.  
- url (String): News source URL  
  - Validation: Regex for valid URL (http and https addresses)  
- category (String): News source category  
  - Possible options: `business` `entertainment` `general` `health` `science` `sports` `technology` 
- language (String): News source language  
  -  Possible options: `ae` `ar` `at` `au` `be` `bg` `br` `ca` `ch` `cn` `co` `cu` `cz` `de` `eg` `fr` `gb` `gr` `hk` `hu` `id` `ie` `il` `in` `it` `jp` `kr` `lt` `lv` `ma` `mx` `my` `ng` `nl` `no` `nz` `ph` `pl` `pt` `ro` `rs` `ru` `sa` `se` `sg` `si` `sk` `th` `tr` `tw` `ua` `us` `ve` `za`
- country (String): News source  
  - Possible options: lowercase ISO-3166 Alpha-2 codes  

Adding fields means adding them in all needed layers so they can be readed and updated.

##### 01.2 Add fields in the listing page
Now that we have all the needed fields in our Aggregate then we can add some of these fields in the listing table:  
- newsApiSourceId
- name
- category
- country

We must be able to sort from any of the columns.  

##### 01.2 Add filters in the listing page
Now that we have new fileds in the listing table now we are ready to add some filters:
- category
- country

##### 01.3 Bonus
Add a button in the listing page to import all the sources from the [NewsAPI sources](https://newsapi.org/docs/endpoints/sources), the workflow should be:
1. Click 'Import all Sources' in the listing page
2. The system must query all the sources from News API and persist them in the Materialized-View
3. If the command was succesfully executed then a message is displayed and the table must be updated
3. If the command had an exception then the error must be shown to the user

#### 02 Query News

##### 02.1 Create a tab to query news from source
Create a new tab in the source detail page, the tab must be named 'News'.  
The news tab must must have the following controls to query news:
- Keywords: Keywords or phrases to search for in the article title only.
- from: A date and time for the oldest article allowed.
- to: A date and time for the newest article allowed.
- sortBy: Possible options: `relevancy` `popularity` `publishedAt`
  - relevancy: articles more closely related to q come first.
  - popularity: articles from popular sources and publishers come first.
  - publishedAt: newest articles come first.
- pageSize: The number of results to return per page. 20 is the default, 100 is the maximum
- page: Use this to page through the results.
- Query button: retrieve news from the source

The usuability of these control depends on you.  

After quering the news these must be displayed to the user you can use the components that works best for you.  The idea is being able to see:
- status: If the request was successful or not. Options: ok, error.  
- totalResults: The total number of results available for your request.
- Articles: per article you must display:
  - author: The author of the article
  - title: The headline or title of the article.
  - description: A description or snippet from the article.
  - url: The direct URL to the article.
  - image: relevant image for the article.
  - publishedAt: The date and time that the article was published, in GMT-05 (Colombian Time)

Your are free to show these news on cards, listing or the component you think is better. 


### Submit project

Publish the entire µService in your own github account. Feel free to remove the pre-build `.git` directory from the project so you can assign it to your own git repo.  

Send an E-Mail to sebastian.molano@nebulae.com.co detailing:
- who you are
- what did you achieve
- github repo URL
- additional libraries that you had to install on the playground/emi 
- additional libraries that you had to install on the playground/emi-gateway 

Note: if an newsApiSourceId already existed then it must be updated with the data from News API source.  


**Thanks in Advance, hope you have fun!**
