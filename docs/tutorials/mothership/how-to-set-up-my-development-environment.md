# How to set up my development environment ?

## Prerequesites

We are going to deploy multiple external services that are needed in order to get Odin working. All the services are Open-sources to assure a complete stack without *vendor lockin*.

These are the depedencies :  
- **PostgreSQL** : the main database of the application, it contains all the informations for the application to work correctly.

On your development environment, you will need docker compose and nodejs environment.


## A. Clone the project from our official repo

The first step is to create your own personal GitHub account.
Then, go on this repository and clone it : [Monolith API](https://github.com/odin-oss/monolith-api)

You will have the API working as expected, however the dashboard will be coming in the next months (ETA summer 2026 ☀️).

## B. Launching external services

You will find in the cloned folder the file `docker-compose.yml` :
```YAML
services:
  postgres_db:
    image: postgres:15-alpine
    container_name: odin-oss-psql
    restart: always
    environment:
      POSTGRES_USER: odin
      POSTGRES_PASSWORD: odin
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
volumes:
  postgres_data:
```

From now, you can launch the deployment : 
```shell
docker compose up -d
```

## C. Configuring the environment variables

You just have to copy the `.env.template` and adapt the values to get the API connecting to the services.
```
cp .env.template .env.local
``` 

## D. NPM scripts

There are multiple npm scripts in the project, here is a little explanation for each. But first, you need to install packages: 
```
npm i --include=dev
```

### Start the local environment


### Unit testing 

We are using Mocha, Sinon and Chai for unit testing. We will refuse every feature that is not properly unit tested with 100% of coverage.

For unit testing only : 
```
npm run test
```

If you want the c8 coverage also :
```
npm run test:coverage
```

When the PR is accepted, it will automatically update the result of SonarQube execution on SonarCloud.