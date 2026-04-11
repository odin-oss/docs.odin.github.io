# Presentation

This application has been previously editing and distributed to schools in France.
That is why it was really designed in order to be easy for teacher and student to use.

## 0. Profiles

There are three current types of profile :

- `ADMINISTRATOR` : this is for the team that manages the whole application. It has all the permissions and rights on it.

- `TEACHER`: this one is for teachers. It gives them the right to have application and manages sessions of applications.

- `STUDENT` : this is the RO profile. It can only uses applications that have been added to its profile.

## 1. Terms

Now it is time to give you all the informations about the terms we are going to use : 

- `Application` : final environment that can be access by the user.

- `Environment` : it is the template of the application. It can be attributed to multiple interfaces in order to mix the content of application.

- `Interface` : This is the container / VM that is deployed and exposed to the final user.

## 2. Global Schema 

Odin is an agent based software. It is working with two big parts :
- The `mothership` that is deployed with the [Monolith API project](https://github.com/odin-oss/Odin-API). It only needs a PostgreSQL database to work. It brings an global API to manage the whole application.
- The `satellites` that ultra-lightweighted agents that are managing and exposing the final applications. Those agents are developped in Rust and are dedicated to a particular type of cluster (Kubernetes, ProxMox, etc...).

Actual compatibility :
- Kubernetes clusters with K-Agent

To be delivered : 
- Proxmox VE clusters with P-Agent
- Docker Swarm clusters wiht D-Agent

(Tell us if you need something particular...)