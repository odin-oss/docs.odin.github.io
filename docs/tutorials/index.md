# Tutorials

## Mothership 🛸

The mothership is the main application of Odin. It can be easily deployed and act as a API hub for administration and for satellites to reach informations.

[How to set up my development environment ?](how-to-build-my-own-environment.md)

## Satellites 🛰️

The monolith API is made to be able to work with multiple satellites in order to get easy multi-cloud deployment of its application.
Each one of the satellites is a small agent that will work in a distant cluster.

So from here, you will find all the documentations to add a new satellite into Odin's mothership.

- [How to add a Kubernetes cluster (K-Agent) ?](satellites/how-to-add-a-kubernetes-cluster.md)

## Environments 🗂️

The API brings a way for you to create your own custom `environments` that are going to be available for users in compatible datacenter.

### Using containers

If your interfaces are available in containers, you will be able to deploy them following this documentation.

- [How to build my own environment ? (container)](environments/how-to-build-my-own-environment.md)