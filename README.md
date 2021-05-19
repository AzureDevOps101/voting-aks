# Example Voting App

## Getting started

Download [Docker](https://www.docker.com/products/overview). If you are on Mac or Windows, [Docker Compose](https://docs.docker.com/compose) will be automatically installed. On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).

Run in this directory:

```shell
docker-compose up
```

The app will be running at [http://localhost:5000](http://localhost:5000), and the results will be at [http://localhost:5001](http://localhost:5001).

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:

```shell
docker swarm init
```

Once you have your swarm, in this directory run:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

## Architecture

![Architecture diagram](architecture.png)

* A Python webapp which lets you vote between two options
* A Redis queue which collects new votes
* A .NET worker which consumes votes and stores them in…
* A Postgres database backed by a Docker volume
* A Node.js webapp which shows the results of the voting in real time

## Deploy to Kubernetes

Note: if you are attending leansoftX.com hosted training sessions, {acr-password} will be provided by the trainer. Otherwise you will need to replace all {acr-address} to your own ACR instance.

```shell

# create a secret according to your Azure Container Registry settings
kubectl create secret docker-registry regcred \
--docker-server={acr-address} \
--docker-username={acr-name} \
--docker-password={acr-password} \
--docker-email={your email}

# run kubectl to deploy
cd kompose/pipeline
kubectl apply -f ./
```

It will take some time for kubernetes to pull down image, run the container and setup load balancer in Azure. You can run the following command to watch for the services status until the EXTERNAL-IP is shown up.

```shell
kubectl get services --watch
```

Once the EXTERNAL-IP is showing up, you can copy them into your browser and open the vote and result pages.

## Note

The voting application only accepts one vote per client. It does not register votes if a vote has already been submitted from a client.