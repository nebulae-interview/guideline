# Development Environment Setup

## Dev machine requiremnets

- [Node 10.16.3](https://nodejs.org/) (LTS)
- [@nebulae/cli 0.5.0](https://www.npmjs.com/package/@nebulae/cli) (Global install)
- [nodemon](https://www.npmjs.com/package/nodemon)  (Global install)
- [gulp](https://www.npmjs.com/package/gulp)  (Global install)
- docker
- docker-copose
- vscode
- Mongo Client (CLI or UI)
- MQTT Client (eg. [MQTT BOX](http://workswithweb.com/mqttbox.html))

## µService generation from Template

```
nebulae generate microservice --frontend-id emi --api-id emi-gateway --project-context INTERVIEW --template-git-url https://github.com/nebulae-interview/ms-micro-service-template  --git-repo-directory-path interview  --repo-git-url https://github.com/nebulae-interview/[µSERVICE-NAME] --crud-entity [AGGREGATE-NAME]
```

Where: 
- '[µSERVICE-NAME]' => MicroServices name.   should start with "ms-"  and then the name you choose. Please use hyphen (-) for composed names.  Valid examples:
  - ms-interview
  - ms-interview-jhon-dow
  - ms-some-name

- [AGGREGATE-NAME] => CRUD aggregate name.  Valid examples:
  - computer
  - computer-service
  - source
  - bird

- for the rest of the params you can view them using ```nebulae generate microservice --help```

Then you can open the µService using code.   eg.   '> code ./ms-zoo/'  

CLI generation sample:
```
# generate ms-interview microservice with source CRUD
nebulae generate microservice --frontend-id emi --api-id emi-gateway --project-context INTERVIEW --template-git-url https://github.com/nebulae-interview/ms-micro-service-template  --git-repo-directory-path interview  --repo-git-url https://github.com/nebulae-interview/ms-interview --crud-entity source


# open vscode 
code ms-interview/ 
```


## Prepare your local full-stack environment

these steps are only neccesary the first time you want to prepare the local dev environment.  after configuring this you can skip to the (Start your local full-stack environment) chapter.

Please open the .sh file at ```playgorund/generate_playground.sh``` there you will find two commands.  One to compose the dev UI environment and other to compose the dev API environment.  this commands MUST BE EXECUTED WITHIN the ```playground``` directory.

### Compose UI

Compose-UI means taking the FrontEnd-Shell of the platform and link it to the µFrontEnd development so developers can have an integrated environment to test the µFrontEnd in the real shell.

Please find the right CLI command in the ```playgorund/generate_playground.sh```.  the following is an example of the ```ms-interview``` µService.
```
# navigate to the Playgrounf folder
cd playground/

# FrontEnd - EMI composition
nebulae compose-ui development --shell-type=FUSE_REACT --shell-repo=https://github.com/nebulae-interview/emi.git --frontend-id=emi --output-dir=emi  --setup-file=../etc/mfe-setup.json
```

### Compose API

Compose-API means taking the API-Shell of the platform and link it to the µAPI development so developers can have an integrated environment to test the µAPI in the real shell.

Please find the right CLI command in the ```playgorund/generate_playground.sh```.  the following is an example of the ```ms-interview``` µService.
```
# navigate to the Playgrounf folder
cd playground/

# API - GateWay composition
nebulae compose-api development --api-type=NEBULAE_GATEWAY --api-repo=https://github.com/nebulae-interview/emi-gateway.git --api-id=emi-gateway --output-dir=emi-gateway  --setup-file=../etc/mapi-setup.json
```

  
## Start your local full-stack environment

### Deploy DB + MQTT using docker compose

Using the CLI navigate to the playground folder and use docker compose to run DB/MQTT containers
```
# navigate to the playgorund folder (within the µService directory)
cd playground/

# start containers
docker-compose up

# for shutting down please use 'ctrl'+c (cmd+c on mac) and then
docker-compose down
```

### Start the BackEnd

Navigate to the µBackend directory, install node_modules and start the server.  
the following is an example for the ```ms-interview``` µService.
``` 
# navigate the the µBackend
cd backend/interview/

# install NPM packages
npm i

# start backend server
nodemon bin/entry-point/server.js


```

### Start the API

Navigate to the playground/emi-gateway directory and start the server on development mode.   dev mode means that a Gulp routine is going to keep your µAPI code in sync with the playground.

``` 
# navigate the the API
cd playground/emi-gateway

# start server
npm run dev
```

After running the server you can navigate to the following links:
- HTTP END POINT: http://localhost:3000/api/emi-gateway/graphql/http
- WEBSOCKET END POINT: ws://localhost:3000/api/emi-gateway/graphql/ws
- GRAPHIQL PAGE: http://localhost:3000/api/emi-gateway/graphql/http


### Start the FrontEnd

Navigate to the playground/emi directory and start the server on development mode.   dev mode means that a routine is going to keep your µFrontEnd code in sync with the playground.

``` 
# navigate the the FrontEnd
cd playground/emi

# start server
yarn run dev
```

After running the server you can navigate to the following link:
http://localhost:4000/
