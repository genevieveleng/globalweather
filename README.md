# PURPOSE OF REPOSITORY
To answer one of Deloitte's question on:
Develop a RESTful API using RAML and the latest version of Mule EE runtime that exposes the following web service.
http://www.webservicex.com/globalweather.asmx?WSDL


# TECHNICAL SPECIFICATION FOR CLIENTS
## ABOUT
Title: Project Zircon VI - Convert WSDL to Restful API
Date: July 5, 2020
Author: Genevieve
Team: Genevieve

## OVERVIEW
Currently, Company OldenDay is using Soap API for their legacy systems. Data are extracted in XML format. However, with the rise of new and upcoming systems, there is a need to build a gap to allow these new systems to extract data from the legacy systems using Resful API methods. There have been other existing legacy endpoints that have been included into the Mule environment. This project would add on 2 more endpoints (GetCitiesByCountry and GetWeather) from the legacy global weather system (GW SYS).

This is part 6 of Project Zircon to continue mining data from old legacy systems for more modern use cases.
It will take around 2 working days for a single engineer.

### PRODUCT REQUIREMENT
The solution will focus on being a medium to:
- Receive request from solution's endpoint (localhost:8081/{api_version}/{path})
- Convert request params (uriParams/queryParams) into XML payload - params should not be case sensitive
- POST XML payload into GW SYS endpoint (localhost:8080/GlobalWeather)
- Convert response from XML into JSON
- Return response in JSON format

Other notes on data structure:
- Solution is able to read GetCitiesByCountry which is in XML format (Note: for my learning purposes)
- Solution is able to read GetWeather which is in CDATA format (Note: added Table tags to make it an array to enable filtering of data)

### GOALS
Allow new systems to query legacy systems using Restful API.

### ASSUMPTIONS
- Solution only allows for the retrieval of data, hence only GET method is allowed
- Set up within an intranet environment, data is available to all internal systems, hence no need for security measures i.e. JWT and etc, rate limiter

### OUT-OF-SCOPE
- Solution will not allow POST, PUT, PATCH, DELETE as these actions are done via legacy systems' UI
- Solution will not allow for the sorting of results

### OPEN QUESTIONS
What's the process for managing solution's downtime?

## APPROACH
There are several ways for the new systems to retrieve data from the legacy systems:
- build a feature in the legacy system to translate XML data into JSON format
- build a feature in new systems to translate XML data into JSON format
- use an endpoint to translate legacy apis output format into JSON format, which is accepted by the new system

The third option is the most feasible as it has the speed-to-market and is cost-efficient.
Also, as most of the environment is already configured using Mule EE Runtime and RAML, the solution should ideally be built upon existing infrastructure.

### COMPONENTS
The solution will run on MAC OS, and will open the port of 8081 using Anypoint Studio.
- MAC OS Catalina : 10.15.5
- Anypoint Studio : 7.5.1
- Mule Server EE : 4.3.0
- APIKit for SOAP : 1.2.6
- Web Service Consumer : 1.5.8
- HTTP : 1.5.17
- APIKit : 1.3.12

## OPERATIONS
### ROLLOUT PLAN
This is not a new solution, but an add-on to the existing infrastructure. Development will take place in staging environment using staging data. Once testing is completed, it will be rolled out to the production environment.

### LONG-TERM SUPPORT
The infrastructure team owns the solution and will maintain it going forward.

As noted in the “Out of Scope” section in the beginning, adding/deleting/updating of Country, City and Weather is not an engineering project, but a process involving several teams.

## TIMELINE
- PLANNING
    - Understand the tools and infrastructure (30 mins)
    - Plan the environment (1 hour)
- DEVELOPMENT
    - Set up RAML (2 hour)
    - Set up environment on Mule EE Runtime (16 hours)
- TESTING
    - Draw out test scenarios (1 hour)
    - Ensure different test scenarios are catered for (1 hour)
- DEPLOYMENT (out-of-scope in this exercise)
    - Deploy to production for real usage (30 mins)

# COMMENTARY
## DESIGN DECISIONS
Endpoint decisions:
- GlobalWeather has only 1 endpoint, which accepts 2 different types of Soap body payload - GetCitiesByCountry and GetWeather.
- Assumed that GetCitiesByCountry is purely used to get cities of a certain country
- Assumed that GetWeather is purely used to get the weather of a city, and user should know the city name, or obtained it via GetCitiesByCountry before using this
- NodeJS GlobalWeather did not allow for the filtering of the results even though it is part of the WSDL design, hence I ensure that the solution will take that into consideration
- Noticed query/uriParams are case sensitive, hence added function to let it be case insensitive e.g. http://localhost:8081/v1/getcountry?countryName=Australia or http://localhost:8081/v1/getcountry?countryName=australia both works fine.

Read up on Restful API Design via this link: https://medium.com/@dilankam/restful-api-design-best-practices-principles-ded471f573f3
- Ensure API path are noun
- Ensure appropriate HTTP method is used - GET
- Included all scenarios for error handling
- Included a version for the API
- Try 2 different methods of querying:
    - GetWeather is obtained via queryParam
    - GetCitiesByCountry is obtained via uriParam

## CHALLENGES
### 1. FOREIGNER TO RAML, MULE EE RUNTIME, WSDL, SOAP, DATAWEAVE
This is the first time I have seen terms like 'RAML', 'Mule EE Runtime' and etc. It took me awhile to research on all the terms.
RAML is pretty straightforward, basically it is a useful tool to model API and its design.
Mule EE Runtime is rather foreign to me, mainly because I worked in a startup with little need to interact with legacy systems or systems that used Web Services Description Language (WSDL).
Soap is like another form of API that generate XML responses.
DataWeave converts XML into JSON response.

The concept is easy to grasp, however, the most challenging part is to learn how to use the tool, Anypoint Studio, which has its own way of working and syntaxes.

### 2. WHAT IS THE GOAL OF THE ASSIGNMENT?
As I never knew about WSDL, I am not sure what the goal of the assignment is. However, I managed to connect the dots after I chanced upon Mulesoft website for Mule runtime engine, which is the "industry's only runtime that can be used for a broad range of use cases from legacy, SaaS, and B2B integrations all the way to API implementation and management". Link: https://www.mulesoft.com/platform/mule. Webservicex is perpetually down so I downloaded the weatherExerciseDockerFile and saw SOAP for the first time.

I made a broad assumption that the goal is to allow newer systems to obtain data from legacy systems using a API bridge.

## LIST OF ERRORS FACED
### Anypoint Studio 7.3.5 unable to open on MAC OS Catalina
Resolved via https://help.mulesoft.com/s/question/0D52T00004nvCde/unable-to-run-anypoint-studio-735-on-mac-os-catalina-beta-version

### Anypoint Studio / JVM Kept on Crashing
Think it is taking up alot of memory, and my computer is not powerful enough, especially when it is hosting 2 ports.
