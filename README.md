# AutoML starter project

This project describes steps developer should pass to start local development.

## List of available commands

1. Building docker images.
```bash
yarn build [--all] [--prune] [--update] [<service>][...]
```

2. Updating local copy of services sources from remote repostiroy
```bash
yarn update [--all] [<service>][...]
```

3. Setup initial configuration for services
```bash
yarn configure
```

4. Run services
```bash
yarn start [--all] [<service>][...]
```

5. Stop services
```bash
yarn stop [--all] [<service>][...]
```

6. Restart services
```bash
yarn restart [--all] [<service>][...]
```

## 1 Prerequisites

You need the following software to be installed:
* nodejs LTS version (with yarn installed)
* docker (minimum 18.06.1-ce version)
* docker-compose (minimum 1.22.0 version)

Read access to the repositories:
* https://github.com/csu-automl/auth
* https://github.com/csu-automl/backend
* https://github.com/csu-automl/shell

SSH key to access github repositories
To add ssh key to your account follow the [instructions](https://help.github.com/articles/connecting-to-github-with-ssh/)

### 1.1 nodejs
Install the latest LTS version of the nodejs.
To see instructions for your OS visit official download [page](https://nodejs.org/en/download/package-manager/)

Install yarn by executing the following:
```bash
npm i -g yarn
```

### 1.2 python3
Best option is to install Anaconda distribution.
To see instructions go to Anaconda download [page](https://www.anaconda.com/download/)

### 1.3 docker

Installation instructions for all OS can be found [here](https://docs.docker.com/install/)

Unix users should also follow [post-installation steps](https://docs.docker.com/install/linux/linux-postinstall/) guide

### 1.4 docker-compose

This step is not required for Windows users because docker-compose will be automatically installed after setting up docker

Installation instructions for all OS can be found [here](https://docs.docker.com/compose/install/)
Mac Users can use Linux instructions for updating docker-compose.

To check compose version type the following:
```bash
docker-compose --version
```

Unix users should also run the following
```bash
sudo chgrp docker /usr/local/bin/docker-compose
```

## 2. Building the required images
To start development process certain docker images should be built and run.

All the commands listed below should be run under the starter project root.

### 2.1 Building images
Before building run the following command to get the latest versions of allservices
```bash
yarn update --all
```

To build all required images run the following

For Unix systems:
```bash
yarn build --all
```
For Windows (git bash):
```bash
./bin/build --all
```

To build specific service instead of option *--all* you can specify list of services you want to build. To get list of services available to build run *build* command without any arguments.

Build doesn't update local copy of git repository so if you want to make sure that you have latest versions of each service provide additional option *--update* during build

## 3. Configuration
Before starting the project there are some steps required to configure your environment.

### 3.1 Services configuration
To initialize default configuration for images run the following

For Unix systems:
```bash
yarn configure
```
For Windows (git bash):
```bash
./bin/configure
```

This will copy default configurations to `./config/` directory

## Networking

When starting containers docker creates a network that is shared between all containers (network section in the docker-compose file). Every container is configured to use static IP to simplify work.

### IP addresses
The following table shows IP mapping for every service that will be run

| Service     | IP address    | Description | Ports |
| ----------- | ------------- | ----------- | ----- |
| mongo       | 192.165.13.2  | MongoDB | 27017 |
| rabbitmq    | 192.165.13.3  | RabbitMQ | 5672 (amqp), 15672 (management) |
| nginx       | 192.165.13.15 | Nginx as reverse proxy to services | 80 |
| auth        | 192.165.13.20 | Authorization service | 3000 |
| backend     | 192.165.13.21 | Backend service | 3000 |
| shell       | 192.165.13.22 | Frontend server | 3000 |

### Static hosts
Specify the following static hosts resolution in _hosts_ file:
```
192.165.13.15   rabbit-management.automl.local
192.165.13.15   auth.automl.local
192.165.13.15   backend.automl.local
192.165.13.15   automl.local

192.165.13.2    mongo.automl.local
192.165.13.3    rabbit.automl.local
```

After you add these lines to the _hosts_ file you will be able to access services by human readable strings as well as ip.

Note that most of the hosts (all that work over http) are pointing to the Nginx host so Nginx is required to be run. All nginx configurations are located in ./etc/nginx directory.

### Add static route (for Windows only)
Services that are run as docker images use specific docker network - 192.165.13.*
To be able to access this network directly (not from docker container) new route should be added to the system.
Execute the following command (administrator/root privileges are required):
```bash
route add 192.165.13.0 mask 255.255.255.0 <docker_ip> -p
```
Where _docker_ip_ is IP of the Docker VM (usually 10.0.75.2)

## Commands

### Start services

To start all the available services run the following:
```bash
yarn start --all
```

Instead of running all services you can specify which ones you want to run. To see all the available services type `yarn start`

### Stop services
To stop all the available services run the following:
```bash
yarn stop --all
```

Instead of stopping all services you can specify which ones you want to shut down. To see all the available services type `yarn stop`

### Restart services
To restart all the available services run the following:
```bash
yarn restart --all
```

Instead of restarting all services you can specify which ones you want to restart. To see all the available services type `yarn restart`
