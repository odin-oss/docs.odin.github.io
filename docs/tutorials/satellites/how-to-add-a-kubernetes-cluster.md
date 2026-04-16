# How to create a Kubernetes Datacenter ?

This is the first K-Agent Odin is bringing as a the almost every application can be deployed in a Kubernetes Cluster.


![K-Agents](images/k-agent.png)


## How to deploy and enroll a new Kubernetes Agent ? 

The deployment is done in two steps :
- First you register it and get the dedicated UUID (needed for encryption)
- Second, get the `MOTHERSHIP_AUTH_TOKEN` in the mothership api logs and put it in the agent **.env**.
- Then you can launch it with the `AGENT_UUID` environment variable that will reach the `MOTHERSHIP_URL`.

### 1. Record and get UUID

In the first launch, **do not set** the `AGENT_UUID` variable but set the proper `MOTHERSHIP_URL` & `AGENT_LABEL`.

### 2. Launch the agent

Now that you have the UUID, you can set the `AGENT_UUID` / `MOTHERSHIP_URL` and launch the agent again. It will now trigger the every 5 sec to tell its status and get the wanted applications state.

