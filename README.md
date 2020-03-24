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

CLI command:  
`nebulae generate microservice --frontend-id emi --api-id emi-gateway --project-context INTERVIEW --template-git-url https://github.com/nebulae-interview/ms-micro-service-template  --git-repo-directory-path interview  --repo-git-url https://github.com/nebulae-interview/ms-interview --crud-entity source`

### Challenges

1. 

### Submit project

Publish the entire µService in your own github account. Feel free to remove the pre-build `.git` directory from the project so you can assign it to your own git repo.  

Send an E-Mail to sebastian.molano@nebulae.com.co detailing:
- who you are
- what did you achieve
- github repo URL
- additional libraries that you had to install on the playground/emi 
- additional libraries that you had to install on the playground/emi-gateway 




**Thanks in Advance, hope you have fun!**
