# How to build my own environment ? (container)

If you want your users to use a custom environment of your own, here a step-by-step guide to this.
The first version is container only, but dedicated VM guide will be added to the Odin core later in the year.

## A. Create your interfaces 

An `environment` in the Odin context is a group of `interfaces`. 
Each interface is a container, and you can put whatever you want in it.

### How it works ?

On the Caelus Playground, you will be able to display every port exposed of your containers.

Here is the structure of a port :
```json
{
    "port": 3000,
    "label": "internal_label",
    "display_name": "name_displayed_on_playground",
    "icon": "xxxxxxxx", # see the Images part
    "PortType": {
        "label": "strip_path"
    }

}
```

So when you add a new port to an interface in Odin, you can configure it from API in order to expose it through Kong.
That is why, the PortType.label can take those values :
- `strip_path` : the ingress cut the path for the container internal ingress controller (*Request: GET /api/users → Upstream receives: GET /users*). 
- `no_strip_path` : the ingress does not cut the path for the container internal ingress controller (*Request: GET /api/users → Upstream receives: GET /api/users*). 
- `none` : using this value, the port will not be exposed.

### How to create a new interface ?

So here is the variables to put for creation : 
- `label` : this is the label that will be used for identify the interface.  
- `registry_link` : this is your actual docker image address. You will need to add it in a registry where you set the credentials on Datacenter.  
- `exec_command` : this is the execution command that / script that will be used for executing the `service_command`. (strongly recommend to manage your services using *supervisord*).  
- `service_command` : this is the script executed by the exec_command. (can be '').  
- `need_compute_gpu` : does your container need a GPU for computing purpose. Used for node selection. You must have configure the Kubernetes node for it.
- `need_graphical_rendering_gpu` : does your container need a GPU for graphical purpose (graphical application). You must have configure the Kubernetes node for it.  
- `cpu_request` : Kubernetes CPU request variable (check Kubernetes documentation).  
- `cpu_limit` : Kubernetes CPU limit variable (check Kubernetes documentation).  
- `ram_request` : Kubernetes RAM request variable (check Kubernetes documentation).  
- `ram_limit` : Kubernetes RAM limit variable (check Kubernetes documentation).   
- `readiness_probe_initial_delay` : how many seconds before first execution of the probe.sh file. The script must return 0 for the network to come into the container.  
- `readiness_probe_period` : how many seconds between two execution.    
- `liveness_probe_initial_delay` : how many seconds before first execution of the probe.sh file (to check if the services are still running).  
- `liveness_probe_period` : how many seconds between two execution.  
- `privileged` : (boolean) disabled.
- `egress_bandwidth`: bandwidth of egress network that enters into the container.
- `ingress_bandwidth`: bandwidth of ingress network that goes out into the container.
- `id_type` : this is the type of interface (docker / kubernetes / etc).  



```shell
export $MY_ADMIN_TOKEN
curl --request POST \
  --url https://test/interface \
  --header 'Authorization: Bearer $MY_ADMIN_TOKEN' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'label=Label of the new interface' \
  --data registry_link=your.container.com/image:tag \
  --data exec_command=/bin/sh \
  --data service_command=/init.sh \
  --data id_type=1 \
  --data need_compute_gpu=false \
  --data need_graphical_rendering_gpu=false \
  --data cpu_request=100m \
  --data ram_request=500Mi \
  --data cpu_limit=2 \
  --data ram_limit=1Gi \
  --data readiness_probe_initial_delay=11 \
  --data readiness_probe_period=11 \
  --data liveness_probe_initial_delay=11 \
  --data liveness_probe_period=11 \
  --data privileged=false \
  --data egress_bandwidth=1G \
  --data ingress_bandwidth=1G
```

### `Probe.sh` file

You have to create a shell script in `/var/probe.sh` that will check if all the services in the container are running as expected.

Here is an example that checks if the SSH is running : 
```bash
#!/bin/bash

# Check if any active connection exists on port 3000
check_ssh() {
  ps aux | grep '[s]shd' | grep -v 'grep' > /dev/null
  return $?
}

# Run the checks
check_ssh
ssh_status=$?

# Both ports must be active
if [ $ssh_status -eq 0 ]; then
  echo "Readiness probe successful: SSH is running"
  exit 0
else
  echo "Readiness probe failed: SSH is not running."
  exit 1
fi
```

## B. Add mandatory configuration

There are some configurations that you will probably need to set for your services to work as you want.

### Arguments

Arguments are putted at the end of the execution command :
```shell
exec_command service_command --arg1 -arg2
```

So you will need to put the array like this : 
```json
{
    ...
    "args": ["--arg1", "--arg2"]
    ...
}
```

### Environment variables

These are the env variables that will be set into the container system.
```json
{
    ...
    "envs": [ 
        { "id_variable_environment": 2 }, 
        { "key": "USER", "value": "ulfi" }
    ]
    ...
}
```

If you want to add a pre existing variable into the interface you can use : 
```json
{ "id_variable_environment": 2 }
```
Else, just create one new : 
```json
{ "key": "USER", "value": "ulfi" }
```

### Node Selectors

This one is Kubernetes only. You will have to add label on the node to be able to receive the application. (This way you can select which k8s node receive the application).

Here is how to set a label on a node :
```shell
kubectl label node xxxxxx node-role.kubernetes.io/odn-default
```

Here are the default available node selectors :
- `node-role.kubernetes.io/odn-default`. 
- `node-role.kubernetes.io/odn-gpu-gr` : for graphical gpu purpose.  
- `node-role.kubernetes.io/odn-compute` : for compute gpu purpose.  
- `node-role.kubernetes.io/odn-storage` : for storage.  
- `node-role.kubernetes.io/odn-monitoring` : for monitoring.  

You can also add your own in the database.

Here is how to add it on an interface :
```json
{
    ...
    "node_selectors": [1,2] 
    ...
}
```

### Ports

Ports to expose (even internally) on the container :
```json
{
    ...
    "ports": [
        {
            "port": 3000,
            "id_port_type": 1,
            "icon": "xxxxxx",
            "label": "label of the port",
            "display_name": "displayed name on playgraound"
        }
    ] 
    ...
}
```

## C. Create your environment

Now that all your interfaces are correctly created, you have to configure the environment that will connect the all.

```shell
export $MY_ADMIN_TOKEN
curl --request POST \
  --url https://test/environment \
  --header 'Authorization: Bearer $MY_ADMIN_TOKEN' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data label=Label \
  --data icon=xxxxxx
```

The `label` is the final name of your environment.
And the `icon` is the icon that will be displayed on the dashboard. (look at images documentation)

### Add an interface in the existing Environment

When the interface and the environment are created, you just have to add the interface into the environment.

```shell
export $MY_ADMIN_TOKEN
curl --request POST \
  --url https://test/{id_environment}/interface \
  --header 'Authorization: Bearer $MY_ADMIN_TOKEN' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data id_interface=1 \
  --data label=Label 
```

You can also use targeting system for ports.
For example, if you have two interfaces : `terminal` and `debian`.  
You can connect ssh to debian by setting the label to label : `ssh-debian`.

