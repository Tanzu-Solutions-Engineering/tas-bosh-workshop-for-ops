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


### [Site Reliability Engineer]
### Find VM where Cloud Controller is installed

- `bosh vms` - to list all VMs across their foundation looking for: `cloud_controller/<VM-GUID>`

SSH into the `cloud_controller` VM 
- `bosh ssh <VM-Type>/<VM-GUID>  -d <cf-DeploymentName>`

Once logged in the SRE team member can run `sudo su` to gain root access and then run `monit summary` to list all processes.   
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

Lets stop the cloud controller process.

- `monit stop cloud_controller_ng`

Now the SRE team member can start all monit processes that are stopped or start them individually 
- `monit start all`