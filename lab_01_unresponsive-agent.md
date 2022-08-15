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

We will begin this lab with the Developer role.   

#### Context

As a developer with many time sensitive features to deliver having a system readily avaialble to support the development, testing and delivery functions becomes even more critical.  

As part of a new development project our development team has been asked to support a new service which offers music suggestions to our customers.  Our marketing team wants to implment this functionality immediately and is only giving the team a day to make the neccessary changes.  

Luckily our star developer is on the job and knowns exactly how and what changes need to be made to meet marketing's latest requirements.   

Our developer is ready to kick the tires on a prototype they had been working on in anticipation of this new change.   

<br/>

### [Developer] 
1. Git clone repo with prototype code 

```
git clone https://github.com/jrobinsonvm/spring-music.git
```

2. Deploy the Application using CF CLI

```
cd spring-music && cf push
```

Oh no!  There seems to be an issue with pushing apps today.  That's unusual with this platform.  


3. Before alerting my SRE Team, let's check our app logs to see if there's anything reporting back obvious errors.   

```
cf logs spring-music
```

Hmm, there seems to be an issue with the availablity of diego cells.  I wonder if we're out of capacity?   Let's go ahead and alert our SRE team who more than likely is already working on a resolution and root cause.    

<br/>

### [Site Reliability Engineer]

Hmm, we have far more capacity than what's needed today.   There seems to be an issue with VMs within our East Coast region.   I guess we should have paid extra to add additional availablity zones.  :)   So far we're only leveraging one AZ for our development foundations.   

Nevertheless, we need to get to the root cause of this issue.  Our monitoring tools have been alerting on cf push slowness and now it seems like we're not able to push any apps at all.  Ops Manager is also showing errors related to my Diego Cells which gives me a clue for where to start.  

Let's start our investigation in bosh!  

Our Site reliability engineering team will investigate the issue by logging into their jump host so that they can connect to the bosh director to begin their troubleshooting.   



1.  Once logged into their jumphost and connected to bosh.  The engineer working the issue will issue the bosh deployments command to list all deployments across their foundation.   

```
bosh deployments
```


See example output below which lists all deployments with associated releases and stemcells. 

```
ubuntu@cypress-ops-manager:~$ bosh deployments
Using environment '10.0.0.5' as client 'ops_manager'

Name                     Release(s)                            Stemcell(s)                                     Team(s)  
cf-aca0fbd04df6cc023e6c  backup-and-restore-sdk/1.18.46        bosh-google-kvm-ubuntu-xenial-go_agent/621.261  -  
                         binary-offline-buildpack/1.0.45                                                         
                         bosh-dns/1.31.0                                                                         
                         bosh-dns-aliases/0.0.4                                                                  
                         bosh-system-metrics-forwarder/0.0.22                                                    
                         bpm/1.1.18                                                                              
                         capi/1.127.2                                                                            
                         cf-autoscaling/249.0.13                                                                 
                         cf-cli/1.38.0                                                                           
                         cf-networking/3.9.0                                                                     
                         cflinuxfs3/0.309.0                                                                      
                         credhub/2.12.6                                                                          
                         diego/2.62.0                                                                            
                         dotnet-core-offline-buildpack/2.3.43                                                    
                         garden-runc/1.20.6                                                                      
                         go-offline-buildpack/1.9.47                                                             
                         haproxy/11.11.0                                                                         
                         java-offline-buildpack/4.49                                                             
                         log-cache/2.11.11                                                                       
                         loggregator/106.6.7                                                                     
                         loggregator-agent/6.4.2                                                                 
                         mapfs/1.2.6                                                                             
                         metric-registrar/1.2.6                                                                  
                         metrics-discovery/3.1.0                                                                 
                         mysql-monitoring/9.15.0                                                                 
                         nats/45                                                                                 
                         nfs-volume/7.1.1                                                                        
                         nginx-offline-buildpack/1.1.38                                                          
                         nodejs-offline-buildpack/1.7.71                                                         
                         notifications/62                                                                        
                         notifications-ui/40                                                                     
                         php-offline-buildpack/4.4.63                                                            
                         push-apps-manager-release/676.0.3                                                       
                         push-offline-docs-release/1.0.94                                                        
                         push-usage-service-release/674.0.24                                                     
                         pxc/0.43.0                                                                              
                         python-offline-buildpack/1.7.54                                                         
                         r-offline-buildpack/1.1.29                                                              
                         routing/0.235.0                                                                         
                         ruby-offline-buildpack/1.8.54                                                           
                         silk/3.9.0                                                                              
                         smb-volume/3.1.0                                                                        
                         smoke-tests/4.5.0                                                                       
                         staticfile-offline-buildpack/1.5.30                                                     
                         statsd-injector/1.11.19                                                                 
                         syslog/11.8.0                                                                           
                         system-metrics/2.0.18                                                                   
                         system-metrics-scraper/3.2.6                                                            
                         uaa/74.5.45                                                                             

1 deployments

Succeeded

```


2.  Let's now issue the bosh vms command to list all VMs across our foundation.   

```
bosh vms
```

Notice the wealth of information here.   

```
ubuntu@cypress-ops-manager:~$ bosh vms
Using environment '10.0.0.5' as client 'ops_manager'

Task 263. Done

Deployment 'cf-aca0fbd04df6cc023e6c'

Instance                                                            Process State  AZ             IPs        VM CID                                   VM Type                      Active  Stemcell  
backup_restore/96385b0a-1acf-45ad-941b-7eaab3dcd763                 running        us-central1-f  10.0.4.9   vm-4ffa33f2-761a-40f5-59d5-5481d2618615  micro                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
clock_global/a66a334e-f1a1-49f6-b838-5dbaed7e30b5                   running        us-central1-f  10.0.4.20  vm-9c8f0cda-dbfd-4553-6817-e45bd83fe865  toolsmiths.custom-1-2.32     true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
cloud_controller/2df4d42f-e2f9-43d6-ad7b-0e74d9f1c55a               running        us-central1-f  10.0.4.12  vm-ea0c779a-bbf0-4751-6676-48f5a35977ad  toolsmiths.custom-1-4.32     true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
cloud_controller_worker/c2302b70-d3dc-428f-a05d-daeaf8dcd37f        running        us-central1-f  10.0.4.13  vm-cb6802d4-c738-4cd0-6308-e7edc1bd1e6f  micro                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
credhub/0de1db29-1610-4312-95cc-069dec56617f                        running        us-central1-f  10.0.4.22  vm-493ffa5f-f293-4cdc-7390-3adc39be1db9  large                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
diego_brain/baab04be-d01a-45e3-9e33-f853f3fe32a6                    running        us-central1-f  10.0.4.14  vm-ef2d0f5d-5879-4bf8-55a5-7e99504ea282  small                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f                     running        us-central1-f  10.0.4.17  vm-0573608e-ffc7-49ea-74bd-08080ab0ffe2  toolsmiths.n1-highmem-2.128  true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
diego_cell/b8fc2b05-ce4c-4959-be81-944fdcdbd51f                     running        us-central1-f  10.0.4.24  vm-0d5b1820-7ad3-407d-49e0-040459e64ad0  toolsmiths.n1-highmem-2.128  true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
diego_cell/ddafd0de-f27e-4054-9b92-6bf4bab1d4fd                     stopped        us-central1-f  10.0.4.23  vm-0a18d937-0264-4e95-7bcd-e1da77a136cc  toolsmiths.n1-highmem-2.128  true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
diego_database/2c4aa147-f16e-4752-a920-3d59db8d7af8                 running        us-central1-f  10.0.4.10  vm-2e82d266-b34a-4f10-7250-19c3bc8b2fff  micro                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
doppler/90cafec5-3b53-41fc-998a-ab2059a028a3                        running        us-central1-f  10.0.4.21  vm-b367552a-11ca-43aa-7e60-b978b01c4a68  toolsmiths.custom-1-4.32     true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
log_cache/5ec7e740-a45e-43f6-95e6-348e29d7abef                      running        us-central1-f  10.0.4.19  vm-7229f65e-a252-49a8-7327-31fd66a38557  toolsmiths.custom-1-4.32     true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
loggregator_trafficcontroller/81705678-612a-469b-a355-bdc8b5bcb4a5  running        us-central1-f  10.0.4.18  vm-f9fa6c9a-0af6-4c4a-64cf-fdef4a85fcbd  micro                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
mysql/5e839194-4d70-46d6-bfcc-6d550bab9b7b                          running        us-central1-f  10.0.4.8   vm-e19181f6-2008-4a3d-78c5-faab0014ba5d  large.disk                   true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
mysql_monitor/ddad4a6b-e2cd-4d1e-88d4-d19bccf6198e                  running        us-central1-f  10.0.4.16  vm-da85018d-9c1a-458e-49d3-ab66c21defea  micro                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
mysql_proxy/7b78691d-04c3-4031-a2b0-d30c16b4932a                    running        us-central1-f  10.0.4.7   vm-e316a0cc-6394-41b3-78f4-2bb8da3bbfab  micro                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
nats/db5a0c47-941d-434e-a80a-de15b47dba65                           running        us-central1-f  10.0.4.5   vm-17a7d06a-39cc-485e-6aac-504f5a3669b1  micro                        true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
nfs_server/684737dd-e80c-4777-a8c4-b42926ffaf8a                     running        us-central1-f  10.0.4.6   vm-4acb94c5-9558-4c3e-7815-699d57bf1b10  medium.disk                  true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
router/16eab2ca-02ed-4fa3-9168-849920b5ca27                         running        us-central1-f  10.0.4.15  vm-05ad9d2a-3104-4669-6fad-3f5e3da5f8b4  micro.cpu                    true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
uaa/16a1feb8-d7b3-4314-850d-7a7803a9ef58                            running        us-central1-f  10.0.4.11  vm-d2b6bcf5-b5b3-4ce1-5ee9-6dd522326f0d  toolsmiths.custom-1-2.32     true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  

20 vms

Succeeded

```

From this view we can clearly see that there's an issue with several of our diego cells. 


2. Now try running the bosh instances command along with the process flag to list out any processes that may be failing on the VMs


```
bosh instances --ps 
```


There's a lot of output here.  Lets try using grep to only show our diego cells.    


```
bosh instances --ps |grep diego
```



Notice the failing processes which could be potentially causing or a side effect of a larger issue that causing our VMs to fail.  Let's see if we can restart the failed/stopped processes to at least restore service then develop a root cause so that this issue doesn't happen again.   

3.  Lets now SSH into each of the failing VMs to attempt to restart all required processes.   

```
bosh ssh <VM-Type>/<VM-GUID>  -d <cf-DeploymentName>
```

4.  Once logged in the SRE team member can su to root and then run monit summary to list all non-running processes that are required for TAS 

```
monit summary 
```


5.  Now the engineer can start all monit processes that are stopped or start them individually using the command below.  

```
monit start <process-name>
```

6.  Now lets repeat steps 3 though 5 for every Diego Cell that has stopped processes.    


### [Developer] 

4.  Once all Diego cells have been remediated the developer can try pushing their application again.    

```
cf push
```

### [Site Reliability Engineer]

7.  After verifying all services have been restored we will need to take a look to see what exactly happened to determine root cause.   




