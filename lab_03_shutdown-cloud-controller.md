# Tanzu Application Service 
# Hands On Lab for Platform Operators 
<br/>

## Game Day!!!

It's finally time for Game Day.   This hands on lab will attempt to provide your SRE teams an opportunity to brush up on their bosh skills as they troubleshoot and remediate a number of self-inflicted incidents all within a safe sandbox environment provided by VMware.   


<br/>

-----

### DevOps Team 
1. [Developer Role](#example)
2. [SRE Role](#example)


-------

<br/>

## Lab Instructions
### Goal

Familiarization with Bosh Concepts.

<br/>

<img width="914" alt="image" src="https://user-images.githubusercontent.com/73367284/184056387-f61ace4a-48cc-4fa0-91a5-76a715e059c1.png">

<br/>


---

### Begin Lab

We will begin this lab with the SRE role.   

#### Context


<br/>



### Find VM where Cloud Controller is installed

1.  Find VMs  
- `bosh vms` - to list all VMs across their foundation looking for: `cloud_controller/<VM-GUID>`

2.  SSH to VM 
SSH into the `cloud_controller` VM 
- `bosh ssh <VM-Type>/<VM-GUID>  -d <cf-DeploymentName>`

3.  Once logged in the SRE team member can run `sudo su` to gain root access and then run `monit summary` to list all processes.   
```
$ monit summary
The Monit daemon 5.2.5 uptime: 17h 22m 

Process 'cloud_controller_ng'       running
Process 'ccng_monit_http_healthcheck' running
Process 'cloud_controller_worker_local_1' running
Process 'cloud_controller_worker_local_2' running
Process 'nginx_cc'                  running
Process 'routing-api'               running
Process 'loggregator_agent'         running
Process 'loggr-syslog-agent'        running
Process 'metrics-discovery-registrar' running
Process 'metrics-agent'             running
Process 'loggr-forwarder-agent'     running
Process 'loggr-udp-forwarder'       running
Process 'prom_scraper'              running
Process 'route_registrar'           running
Process 'statsd_injector'           running
Process 'bosh-dns'                  running
Process 'bosh-dns-resolvconf'       running
Process 'bosh-dns-healthcheck'      running
Process 'system-metrics-agent'      running
System 'system_aee0d44d-eb4d-49d9-8e34-8fc2ba6ba3f1' running
```

We see here that `cloud_controller_ng` is in a running state.

4.  Lets stop the cloud controller process.

- `monit stop cloud_controller_ng`


### [Developer]

As the developer, let's check on our applications logs.  This should return an error.
```
$ cf logs spring-music
API endpoint not found at 'https://api.sys.yourhost.cf-app.com'
FAILED
```

### [Site Reliability Engineer]

Since we are familar with the Cloud Foundry Architecture we know that the cloud controller plays a key role.   The Cloud Controller (CC) directs the deployment of applications. To push an app to Cloud Foundry / Tanzu Application Service, you target the Cloud Controller API.  The Cloud Controller then directs the Diego Brain through the CC-Bridge components to coordinate individual Diego cells to stage and run applications.

![image](https://user-images.githubusercontent.com/73367284/184706224-77de93ee-2213-4cbf-9203-7946829f85b6.png)


5.  Now the SRE team member can start all monit processes that are stopped or start them individually 

- `monit start all`
</br>or
- `monit start cloud_controller_ng`

6.  Verify cloud_controller_ng is running
- `monit summary`

### [Developer]

As the developer, let's check on our applications logs again.

Now the `cf` CLI commands will work since the Cloud Controller is running again.


<br/>

### [Site Reliability Engineer]

Now lets assume we have a noisey neighbor situation that's happening on one of our diego cells and instead of scaling resources we would like to identify the problem process or application.   

This raises the question of how do we identify what applications are running on our diego cells.    


To do this we will leverage a tool called cfdot "CF Diego Operator Toolkit".   

7.  From the Ops Manager VM SSH to one of your diego cells.   
8.  Type the cfdot command without sudo'ing to root.
9.  Run the following command to discovery applications that are running within a selected diego cell.   

```
cfdot cell-state <guid-of-diego-cell> | jq -r '. | "\ndiego_cell:", .cell_id, "\n", "App-Guids:", .LRPs[].process_guid[0:36]'
```

Example Output: 
```
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f:~$ cfdot cell-state b8fc2b05-ce4c-4959-be81-944fdcdbd51f | jq -r '. | "\ndiego_cell:", .cell_id, "\n", "App-Guids:", .LRPs[].process_guid[0:36]'

diego_cell:
b8fc2b05-ce4c-4959-be81-944fdcdbd51f


App-Guids:
1f70ffeb-a927-487e-8349-ade2ceb683d3
09901e52-1eb6-4f18-ac69-e56f4d04eb9e
8fe82547-1824-4832-899f-7a6ef56563d2
70c253af-1b71-45bc-9720-1c7d81016b77
70c253af-1b71-45bc-9720-1c7d81016b77
488922d0-1b82-4580-bff7-a5b661d3cfd5
488922d0-1b82-4580-bff7-a5b661d3cfd5
1f70ffeb-a927-487e-8349-ade2ceb683d3
57461897-b221-4a4d-8b0d-c4c2e04eaba4
488922d0-1b82-4580-bff7-a5b661d3cfd5
1f70ffeb-a927-487e-8349-ade2ceb683d3
8fe82547-1824-4832-899f-7a6ef56563d2
1f70ffeb-a927-487e-8349-ade2ceb683d3
1f70ffeb-a927-487e-8349-ade2ceb683d3
1f70ffeb-a927-487e-8349-ade2ceb683d3

```

Its kind of hard to tell what applications are running here with just their GUIDs, which is why we will be making a call to the CF API to determine what the actual names are.  


10.  Open a separate terminal to run the following CF CLI command. 

```
cf curl /v2/apps/<guid-of-app>/stats | grep name | uniq
```

Example Output: 
```
root@cypress-ops-manager:~# cf curl /v2/apps/1f70ffeb-a927-487e-8349-ade2ceb683d3/stats | grep name | uniq
         "name": "apps-manager-js-green",

```

Running cf CLI command for multiple guid's can also be automated as shown below:

11.  Copy all the app guid to a file and you can run something like the below for loop against the file (replace the filename in the below loop)

```
for i in $(cat filename); do 
    echo $(cf curl /v2/apps/$i/stats | grep name | uniq)
done
```

Example Output: 

```
ubuntu@cypress-ops-manager:~$ cat filename 
1f70ffeb-a927-487e-8349-ade2ceb683d3
09901e52-1eb6-4f18-ac69-e56f4d04eb9e
8fe82547-1824-4832-899f-7a6ef56563d2
70c253af-1b71-45bc-9720-1c7d81016b77
70c253af-1b71-45bc-9720-1c7d81016b77
488922d0-1b82-4580-bff7-a5b661d3cfd5
488922d0-1b82-4580-bff7-a5b661d3cfd5
1f70ffeb-a927-487e-8349-ade2ceb683d3
57461897-b221-4a4d-8b0d-c4c2e04eaba4
488922d0-1b82-4580-bff7-a5b661d3cfd5
1f70ffeb-a927-487e-8349-ade2ceb683d3
8fe82547-1824-4832-899f-7a6ef56563d2
1f70ffeb-a927-487e-8349-ade2ceb683d3
1f70ffeb-a927-487e-8349-ade2ceb683d3
1f70ffeb-a927-487e-8349-ade2ceb683d3

ubuntu@cypress-ops-manager:~$ for i in $(cat filename); do 
>     echo $(cf curl /v2/apps/$i/stats | grep name | uniq)
> done
"name": "apps-manager-js-green",
"name": "spring-music",
"name": "search-server-green",
"name": "p-invitations-green",
"name": "p-invitations-green",
"name": "autoscale",
"name": "autoscale",
"name": "apps-manager-js-green",
"name": "autoscale-api",
"name": "autoscale",
"name": "apps-manager-js-green",
"name": "search-server-green",
"name": "apps-manager-js-green",
"name": "apps-manager-js-green",
"name": "apps-manager-js-green",
ubuntu@cypress-ops-manager:~$ 

```

You can also use cfdot cli to determine how to find out where a particular app is running based on just the App's FQDN.  

From a diego cell run the following command to identify where the spring-music app is running based on its route.   

12.  Set command as variable 

```
PGUIDS=$(cfdot desired-lrps | grep <APP_ROUTE> | jq -r '.process_guid')
```

Example Output: 

```
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f:~$ echo $PGUIDS
09901e52-1eb6-4f18-ac69-e56f4d04eb9e-05ee8942-b1dd-411d-b21e-e58864721187
```

13.  Now that you will the GUID, you can run the following command to identify details around the application and where its running.   

```
cfdot actual-lrp-groups | grep "$PGUIDS" | jq
```


Example Output: 
```
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f:~$ cfdot actual-lrp-groups | grep "$PGUIDS" | jq
Command "actual-lrp-groups" is deprecated, use "actual-lrps" instead.
{
  "instance": {
    "process_guid": "09901e52-1eb6-4f18-ac69-e56f4d04eb9e-05ee8942-b1dd-411d-b21e-e58864721187",
    "index": 0,
    "domain": "cf-apps",
    "instance_guid": "7cc78dd9-b977-4b17-6a06-687e",
    "cell_id": "b8fc2b05-ce4c-4959-be81-944fdcdbd51f",
    "address": "10.0.4.24",
    "ports": [
      {
        "container_port": 8080,
        "host_port": 61000,
        "container_tls_proxy_port": 61001,
        "host_tls_proxy_port": 61002
      },
      {
        "container_port": 8080,
        "host_port": 61000,
        "container_tls_proxy_port": 61443,
        "host_tls_proxy_port": 61003
      },
      {
        "container_port": 2222,
        "host_port": 61001,
        "container_tls_proxy_port": 61002,
        "host_tls_proxy_port": 61004
      }
    ],
    "instance_address": "10.255.21.242",
    "preferred_address": "HOST",
    "crash_count": 0,
    "state": "RUNNING",
    "since": 1660585473951299800,
    "modification_tag": {
      "epoch": "4de70e08-ba48-46e4-4a5e-7ceff74f1da8",
      "index": 2
    },
    "presence": "ORDINARY"
  }
}

```


