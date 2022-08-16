# Tanzu Application Service 
# Hands On Lab for Platform Operators 

<br/>

## High Level Overview 

VMware Tanzu Application Service is an opinionated, application-aware platform that provides a rich set of developer tooling and a prepaved path to production to build and deploy software quickly and securely on any compliant public cloud or on-premises vCenter Data Center.


VMware Tanzu Application Service is a modern application platform for enterprises that want to continuously deliver and run microservices or traditional applications across clouds or on-premises.   
[Read more...](https://tanzu.vmware.com/application-service)

<br/>


### A modern runtime for microservices and traditional applications 
 ![image](https://user-images.githubusercontent.com/73367284/158693192-1b5d6762-f88f-4d8c-ab57-d399344c50f8.png)

<br/>



<br/>

-----
## Lab Agenda 
### SRE Focus -   
1. [Introductions](#example)
   - VMware Team 
   - Customer
2. [Lab Setup](#example)
   - Jumphost / Ops Manager Access (HTTPS and SSH Outbound Connectivity is required)  
   - CF CLI Installed Locally  (optional)
3. [Introduction to Bosh](#example)
   - What is Bosh?
   - Bosh Concepts (ie. stemcells, CPI, releases, manifests, etc. ) 
4. [Hands on Labs](#example)
   - Unresponsive Agent Lab
   - Shutdown Cloud Controller Lab


-------

<br/>
<br/>





## TAS Lab Setup Instructions

### Goal

Familiarization with Workshop Lab environment.

<br/>

### Workshop Lab Architecture: Tanzu Application Service

<img width="914" alt="image" src="https://user-images.githubusercontent.com/73367284/184056387-f61ace4a-48cc-4fa0-91a5-76a715e059c1.png">

<br/>

### Using the TAS Operations Manager VM as a Jumpbox

<br/>

The TAS Operations Manager VM can be used as a jump box to access and inspect a TAS deployment infrastructure, given that it is usually deployed inside of the TAS infrastructure network along with the BOSH Director VM and that it contains PCF and BOSH management tools pre-installed (e.g. uaac and bosh CLI's). ([TAS Knowledge Base article](https://community.pivotal.io/s/article/Using-bosh-and-uaac-cli-to-inspect-and-manage-pivotal-cloud-foundry-from-an-ops-manager-vm)).

<br/>

### Part 1: Connect to the Ops Manager VM

<br/>

1. Provide TAS Ops Manager VM's fully-qualified domain name from provided pre-workshop email

1. Provide corresponding ssh private key content from provided pre-workshop email

1. SSH into the jumpbox VM of your Lab environment

    ```execute
    
    ssh -o "StrictHostKeyChecking no" -i cypress.priv ubuntu@pcf.cypress.cf-app.com
    ```


1. Test connectivity with the Ops Mgr web interface

    Launch the Ops Manager user interface [link](https://pcf.cypress.cf-app.com)

    Login with credentials: `pivotalcf` / `< password is provided by your instructor>`


1. Configure an alias for BOSH commands  

    The BOSH CLI requires several parameters to run commands, such as the targeted BOSH Director environment and credentials for authentication. 

    To make that easier, you can define an alias containing all of those parameters. And the Ops Manager web interface makes the content for that alias readily available in its credentials tab page.

    From the Ops Mgr web UI > Bosh Tile > Credentials tab ([link](https://pcf.cypress.cf-app.com/api/v0/deployed/director/credentials/bosh_commandline_credentials)), copy the contents of "Bosh Command line Credentials" and then define the alias with the following command:  

    ```copy-and-edit
    alias bosh="<command-from-ops-mgr-panel>"
    ```

    Example: 

    `alias bosh=""BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=........ BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate BOSH_ENVIRONMENT=10.0.0.5 bosh "`


<br/>

### Part 2: Inspect the Bosh Director environment

<br/>

1. Check which BOSH deployments exist 

    ```execute
    bosh deployments
    ```

1. Check for BOSH managed VMs for the existing deployments

    ```execute
    bosh vms
    ```

<br/>

------------------------------

# Introduction to Bosh 

## What is BOSH?

<br/>

- BOSH is an open source tool for release engineering, deployment, lifecycle management, and monitoring of distributed systems

- BOSH installs and updates software packages on large numbers of VMs over many IaaS providers with the absolute minimum of configuration changes

- BOSH orchestrates initial deployments and ongoing updates that are:  
  - Predictable, repeatable, and reliable  
  - Self-healing  
  - Infrastructure-agnostic  

<br/>

--- 

### What can BOSH Deploy & Manage?

<br/>

- Cloud Foundry Application Runtime  
![image](https://user-images.githubusercontent.com/73367284/184049824-3b965da9-3603-4c97-aa64-f2ce0c4660b7.png)


<br/>

- Kubernetes  
![image](https://user-images.githubusercontent.com/73367284/184049991-f657d6d5-2b18-491e-a1a0-5152a476946b.png)

<br/>

- Databases  
![image](https://user-images.githubusercontent.com/73367284/184050042-a9aef593-fbd3-4db9-bc7b-e6931deb5725.png)

<br/>

- Messaging Clusters  
![image](https://user-images.githubusercontent.com/73367284/184050057-9bd2c995-009e-4077-8312-fee6ab8a82f8.png)

<br/>

- CI/CD Servers  
![image](https://user-images.githubusercontent.com/73367284/184050124-845581d8-e58a-4b24-b281-13500e36ded0.png)

<br/>
---

### What IaaS providers does BOSH support?

![image](https://user-images.githubusercontent.com/73367284/184050152-8288e688-7526-49d8-9759-8b4c5fc9a6cd.png)
  
<br/>

---

### What Operating Systems Does BOSH Support?

![image](https://user-images.githubusercontent.com/73367284/184050166-401d7740-7d58-4e10-bafb-e149514f2acb.png)

<br/>

---

### The BOSH Project

<br/>

- BOSH was created in 2010 by VMWare 
- 2013 Pivotal was spun out of VMWare taking BOSH with it
- Pivotal donated the BOSH project the the Cloud Foundry Foundation 
- BOSH is a project of the Cloud Foundry Foundation
- BOSH uses the Apache 2.0 License
- Actively developed since 2010
- 244 Contributors 
- Major contributions from CF Foundation members Pivotal, IBM, SAP, VMWare, Microsoft, Google and others 
- Resources   
  - http://bosh.io    
  - http://ultimateguidetobosh.com/ (Free Book)  
  - http://mariash.github.io/learn-bosh/ (Quick Tutorial)  
  - https://cloudfoundry.org/bosh/   
  - https://github.com/cloudfoundry/bosh   

<br/>

---

### BOSH User Interaction

![image](https://user-images.githubusercontent.com/73367284/184050231-bfc816f5-e6c9-4d0c-b5c8-c29419e05065.png)

- The BOSH director is a collection of components that orchestrates the initial deployments and ongoing updates of bosh managed services 
- BOSH is a distributed system that can be deployed on multiple VM’s. However it is commonly deployed on a single VM
- A single BOSH director VM can manage thousands of other VMs 
- The BOSH CLI is a single go binary that can be installed on a Mac, Windows, or Linux to drive the BOSH director 

<br/>

---

### Reference: Installing the BOSH CLI

<br/>

Your workshop environment has the BOSH CLI already installed. 
For future reference on how to install it on your local environment, here is the documentation link for the setup steps:
- https://www.bosh.io/docs/cli-v2-install/

<br/>

---



### What about Stemcells?

<br/>

- In biology a stemcell is a cell that can morph into a specialized cell  
  - stemcell → bone cell  
  - stemcell → liver cell  
  - stemcell → skin cell

- In BOSH a stemcell is a bare minimum OS image that can be morphed into a VM running specific software   
  - stemcell → VM running Redis 
  - stemcell → VM running HAProxy 
  - stemcell → VM running all the components of a Kubernetes worker node 

<br/>

### BOSH Stemcell

- Secured, hardened, and versioned operating system image wrapped with IaaS specific packaging

- Contains a bare minimum OS skeleton with a few common utilities pre-installed, a BOSH Agent, and a few configuration files to securely configure the OS by default
- Stemcells are Windows or Linux based
- Maintained by BOSH team and available at http://bosh.io/stemcells  

- Reference: Building and Examining a BOSH Stemcell  
  - Official Stemcell documentation: https://bosh.io/docs/stemcell/  
  - Stemcell Builder: https://github.com/cloudfoundry/bosh-linux-stemcell-builder   

<br/>

### Stemcell Metadata

- stemcell.MF
- packages.txt

<br/>

### Stemcell Versions


- MAJOR.MINOR format
- Major version is incremented when new features are added to stemcells
- Minor versions are incremented if certain security fixes backported on top of existing stemcell line. 
- We recommend to continuously bump to the latest major stemcell version to receive latest updates.

<br/>

### Light Stemcells

- Available for public IaaS providers only 
  - AWS  
  - GCP  
  - Azure  
  - SoftLayer

- Does not contain the OS image rather references the OS image hosted by the IaaS provider 

 
![image](https://user-images.githubusercontent.com/73367284/184050723-ed11f7a1-ef83-406f-b4fb-13ad29bbada8.png)

<br/>

- Small size file only about 20KB thus the name light stemcell

![image](https://user-images.githubusercontent.com/73367284/184050763-c2cf150b-6f8a-4121-9629-5d2f64ae7c1d.png)

<br/>

### The concept of stemcell solves the following problems

- Provides a base operating system image for installing software packages on 

- Versions changes to the OS image.   
  - If a library is changed for example openssl a new stemcell version is released  
  - Provides traceability of what is running on a VM  
  - Guarantees reproducibility

- Reusing base Operating System images across VMs of different types  
  - Eliminates snowflakes in your systems  
  - Simplifies patching / upgrades / maintenance   
  - Simplifies troubleshooting 

- Reusing base Operating System images across different IaaS providers  
  - BOSH software release only need to be built once and they will work the same on all IaaS providers 

- Stemcells are pre-hardened according to security best practices   
  - Building on top of a stemcells means more secure software
  - https://docs.pivotal.io/pivotalcf/security/pcf-infrastructure/stemcell-hardening.html  

<br/>

-------


### Cloud Provider Interface (CPI)

<br/>

- The bosh director uses the CPI to interact with an IaaS to create and manage stemcells, VMs, and disks. 
- The CPI abstracts infrastructure differences from the rest of BOSH.
- The CPI is a command line executable that can be called to perform any of the actions below 
- CPIs are typically maintained by the IaaS provider 
- https://bosh.io/docs/cpi-api-v2/
- https://github.com/cloudfoundry/bosh-cli/blob/master/docs/architecture.md#deploy-command-flow

![image](https://user-images.githubusercontent.com/73367284/184050994-c3eee4af-0e2d-4c43-b94f-25a41246a184.png)

<br/>

--- 

<br/>

### BOSH Component Architecture (Simplified) 

![image](https://user-images.githubusercontent.com/73367284/184051011-70c841a8-69be-4ed1-bd7b-f561dfef8f0a.png)

<br/>

---

### How do I abstract the cloud away?

<br/>

- The specific details about the size of the servers is specific to each cloud infrastructure. 

- Take the makeup of a VM as an example:  
  - On vSphere, you will want to specify the explicit allocation of CPUs, RAM, and ephemeral disk.   
  - On Amazon Web Services you might choose between a list of Instance Types such as m4.large or t2.medium.   
  - On GCP you choose a Machine Type from a list of predefined sizes which are slightly different than Amazon such as n1-standard-1.

- The Cloud Config is where BOSH takes specific infrastructure details to configure the CPI being used while abstracting the complexities away from the software you are deploying.

<br/>

--- 

### Example GCP cloud-config.yml

<br/>

```
azs:
- name: z1
  cloud_properties: {zone: us-central1-a}
- name: z2
  cloud_properties: {zone: us-central1-f}

vm_types:
- name: default
  cloud_properties:
    machine_type: n1-standard-4
    root_disk_size_gb: 20
    root_disk_type: pd-ssd

disk_types:
- name: default
  disk_size: 3000

networks:
- name: default
  type: manual
  subnets:
  - range:   10.0.0.0/16
    gateway: 10.0.0.1
    dns:     [8.8.8.8, 8.8.4.4]
    reserved: [10.0.0.0-10.0.0.10]
    azs:     [z1, z2]
    cloud_properties:
      ephemeral_external_ip: true
      subnetwork_name: bosh-1-sub
      network_name: bosh-1-net
      ephemeral_external_ip: true
      tags: [bosh]
- name: vip
  type: vip

compilation:
  workers: 3
  reuse_compilation_vms: true
  az: z1
  vm_type: default
  network: default
```

<br/>

--- 



### Suppose you deploying a very simple 2 VM service 

![image](https://user-images.githubusercontent.com/73367284/184051966-99b945a1-f86a-4499-815d-3bac0f73a158.png)

<br/>

---

### Typical Deployment Steps 

<br/>

- Setup Postgres  
  - We need CentOS 7 machine  
  - With 8 GB RAM, 10GB Disk, and one IP Address  
  - Provision the required VM on IaaS (vSphere, AWS, etc)  
  - Get the source for postgres   
  - Compile postgres and install it   
  - Configure postgres as a monitored service  
  - Start postgres and note it’s ip address / port   

- Setup Tomcat   
  - We need an Ubuntu 16.04 LTS machine  
  - With 4 GB RAM, 10GB Disk, and one IP Address  
  - Provision the required VM on IaaS (vSphere, AWS, etc)  
  - Install the Java Runtime Environment on the VM   
  - Install the Tomcat on the OS   
  - Configure Tomcat as a monitored service   
  - Configure Tomcat data source pointing to postgres   
  - Deploy app into Tomcat   
  - Start tomcat  

<br/>

---

### Deployment Steps Mapped to BOSH Concepts 

![image](https://user-images.githubusercontent.com/73367284/184051931-a43be66a-91ad-49b8-a0de-b3c610e13d3c.png)

<br/>

---

### BOSH Release

<br/>

- A BOSH release is similar to a collection of traditional software packages, such as Debian APT or MacOS Homebrew packages.

- A traditional software package focused on installing a single piece of software.  
  - Executable (The software)  
  - Library (Dependencies)

- A traditional software package might also configure and run a service.   
  - Default Config Files  
  - Startup Script  
  - Monitoring

- A BOSH release combines all these functions   
  - Compile all packages for the target operating system and architecture  
  - Install runtime dependencies via BOSH packages  
  - Configuring the software (clustering)  
  - Initialize, and run processes using Monit (monitoring system)

<br/>

---

### release.MF - A YAML metadata file describing the release

```
name: sample-bosh-release
version: 0+dev.1
commit_hash: a2b19b8
uncommitted_changes: false
jobs:
- name: sample_job
 version: 3772798c13278206d9bdf5d58872f7bc8fa77521
 fingerprint: 3772798c13278206d9bdf5d58872f7bc8fa77521
 sha1: f9888b65ad57263475369004a73d28c1ac6c6328
packages:
- name: sample_app
 version: 3698d0cc632d3162a6a2fedcd36ac00364a7cd64
 fingerprint: 3698d0cc632d3162a6a2fedcd36ac00364a7cd64
 sha1: d224739b881e9dd957988bcd7513a37edff69205
 dependencies: []
```

<br/>

---

### BOSH Packages

<br/>

- A BOSH release contains a set of software packages that can be installed 
- Each package is stored as a single .tgz file inside of the packages/ directory of the release  
- A BOSH package must contain a shell script called packaging that will invoked to produce the binaries that will be installed on a VM
- A BOSH package must contain the code/binaries that the packaging script uses to produce the bosh package  

<br/>

---

### BOSH Jobs

<br/>

- A BOSH job is a set of processes that are configured on a VM 
- Each job is stored as a single .tgz file inside of the jobs/ directory of the release 
- A job must contain a job.MF YAML file that contains metadata about the job 
- A job must contain a monit file that describes how to run and monitor the job 
- A job may contain a collect of ruby ERB templates that are used to generate the startup and configuration scripts for job 

<br/>

---

### JOB.MF - A YAML metadata file describing the release

```
---
name: sample_job

templates:
 ctl.erb: bin/ctl

packages:
- sample_app

properties: {}
```

<br/>

---



### How does BOSH know what we want to deploy?

<br/>

Hint: manifests

<br/>

#### Manifests 

<br/>

Typically when creating a machine to run software on you perform the following:
- Open an admin console to a server virtualization platform.
- Create a virtual machine
- Create a disk and add it to the virtual machine
- Create a network and add it to the virtual machine
- SSH into the virtual machine and Install software

With BOSH this is all encapsulated into a deployment manifest.

A deployment manifest is the explicit declaration of what a software system requires to run successfully.

The same deployment manifest deployed today should produce the same running system if you deployed it again in five years time.

<br/>

#### Example sample-bosh-manifest.yml

<br/>

```
name: sample-bosh-deployment

releases:
- {name: sample-bosh-release, version: latest}

stemcells:
- alias: trusty
  os: ubuntu-trusty
  version: latest

update:
  canaries: 1
  max_in_flight: 10
  canary_watch_time: 1000-30000
  update_watch_time: 1000-30000

instance_groups:
- name: sample_vm
  instances: 1
  networks:
  - name: default
  azs: [z1]
  jobs:
  - name: sample_job
    release: sample-bosh-release
  stemcell: trusty
  vm_type: default
```

<br/>

---


--------


<br/>


# TAS  LAB 01

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

Step 1. Git clone repo with prototype code 

```
git clone https://github.com/jrobinsonvm/spring-music.git
```

Step 2. Deploy the Application using CF CLI

```
cd spring-music && cf push
```

Oh no!  There seems to be an issue with pushing apps today.  That's unusual with this platform.  


Step 3. Before alerting my SRE Team, let's check our app logs to see if there's anything reporting back obvious errors.   

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

<br/>


Step 1. Once logged into their jumphost and connected to bosh.  The engineer working the issue will issue the bosh deployments command to list all deployments across their foundation.   

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


Step 2. Let's now issue the bosh vms command to list all VMs across our foundation.   

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


Step 3. Now try running the bosh instances command along with the process flag to list out any processes that may be failing on the VMs


```
bosh instances --ps 
```


There's a lot of output here.  Lets try using grep to only show our diego cells.    


```
bosh instances --ps |grep diego
```



Notice the failing processes which could be potentially causing or a side effect of a larger issue that causing our VMs to fail.  Let's see if we can restart the failed/stopped processes to at least restore service then develop a root cause so that this issue doesn't happen again.   

Step 4. Lets now SSH into each of the failing VMs to attempt to restart all required processes.   

```
bosh ssh <VM-Type>/<VM-GUID>  -d <cf-DeploymentName>
```

Step 5. Once logged in the SRE team member can su to root and then run monit summary to list all non-running processes that are required for TAS 

```
monit summary 
```


Step 6. Now the engineer can start all monit processes that are stopped or start them individually using the command below.  

```
monit start <process-name>
```


Now lets repeat steps 3 though 5 for every Diego Cell that has stopped processes.    

<br/>


### [Developer] 

Step 4. Once all Diego cells have been remediated the developer can try pushing their application again.    

```
cf push
```

<br/>

### [Site Reliability Engineer]

After verifying all services have been restored we will need to take a look to see what exactly happened to determine root cause.   

From the Ops Manager CLI lets take a look at our monit log file to see if there were any errors or changes logged to help us better understand what caused our processes to stop running.   

Step 7. Change to the monit directory

```
cd /var/vcap/monit
```

Step 8. Now view monit the log file and grep for "stop service"

```
cat monit.log |grep "stop service"
```

Expected output: 
```
[UTC Aug 10 16:41:06] info     : stop service 'system-metrics-agent' on user request
[UTC Aug 10 16:41:06] info     : stop service 'bosh-dns-healthcheck' on user request
[UTC Aug 10 16:41:06] info     : stop service 'bosh-dns-resolvconf' on user request
[UTC Aug 10 16:41:06] info     : stop service 'bosh-dns' on user request
[UTC Aug 10 16:41:06] info     : stop service 'iptables-logger' on user request
[UTC Aug 10 16:41:06] info     : stop service 'bosh-dns-adapter' on user request
[UTC Aug 10 16:41:06] info     : stop service 'smbdriver' on user request
[UTC Aug 10 16:41:06] info     : stop service 'nfsv3driver' on user request
[UTC Aug 10 16:41:06] info     : stop service 'vxlan-policy-agent' on user request
[UTC Aug 10 16:41:06] info     : stop service 'netmon' on user request
[UTC Aug 10 16:41:06] info     : stop service 'silk-daemon' on user request
[UTC Aug 10 16:41:06] info     : stop service 'prom_scraper' on user request
[UTC Aug 10 16:41:06] info     : stop service 'loggr-udp-forwarder' on user request
[UTC Aug 10 16:41:06] info     : stop service 'loggr-forwarder-agent' on user request
[UTC Aug 10 16:41:06] info     : stop service 'metrics-agent' on user request
[UTC Aug 10 16:41:06] info     : stop service 'metrics-discovery-registrar' on user request
[UTC Aug 10 16:41:06] info     : stop service 'loggr-syslog-agent' on user request
[UTC Aug 10 16:41:06] info     : stop service 'loggregator_agent' on user request
[UTC Aug 10 16:41:06] info     : stop service 'garden' on user request
[UTC Aug 10 16:41:06] info     : stop service 'route_emitter' on user request
[UTC Aug 10 16:41:06] info     : stop service 'rep' on user request
[UTC Aug 15 14:51:14] info     : stop service 'rep' on user request
[UTC Aug 15 14:51:25] info     : stop service 'garden' on user request

```

The following output shows me that a user who has admin access requeted to stop the above services.    


Step 9. Let's pretend that this issue was not as obvious to determine.  In this case an engineer may be required to upload a log bundle to a support ticket.   Please see the following example to generate a log bundle for each affected VM.  

```
bosh logs -d  VM-Type/VM-GUID  [--dir DESTINATION_DIRECTORY]
```

Example output 
  
```
ubuntu@cypress-ops-manager:~$ bosh logs -d cf-aca0fbd04df6cc023e6c diego_cell/b8fc2b05-ce4c-4959-be81-944fdcdbd51f
Using environment '10.0.0.5' as client 'ops_manager'

Using deployment 'cf-aca0fbd04df6cc023e6c'

Task 299

Task 299 | 16:10:44 | Fetching logs for diego_cell/b8fc2b05-ce4c-4959-be81-944fdcdbd51f (2): Finding and packing log files (00:00:02)

Task 299 Started  Mon Aug 15 16:10:44 UTC 2022
Task 299 Finished Mon Aug 15 16:10:46 UTC 2022
Task 299 Duration 00:00:02
Task 299 done

Downloading resource 'bad71754-0b7c-44e4-bdbc-cec23986a1dd' to '/home/ubuntu/cf-aca0fbd04df6cc023e6c.diego_cell.b8fc2b05-ce4c-4959-be81-944fdcdbd51f-20220815-161046-974791195.tgz'...

Succeeded
  
```
  

To retrieve logs from all VMs deployed within a deployment just leave off the VM name.   
  
Example Output: 

```
ubuntu@cypress-ops-manager:~$ bosh logs -d cf-aca0fbd04df6cc023e6c
Using environment '10.0.0.5' as client 'ops_manager'

Using deployment 'cf-aca0fbd04df6cc023e6c'

Task 300

Task 300 | 16:12:51 | Fetching logs for diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for diego_cell/b8fc2b05-ce4c-4959-be81-944fdcdbd51f (2): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for log_cache/5ec7e740-a45e-43f6-95e6-348e29d7abef (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for nfs_server/684737dd-e80c-4777-a8c4-b42926ffaf8a (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for doppler/90cafec5-3b53-41fc-998a-ab2059a028a3 (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for loggregator_trafficcontroller/81705678-612a-469b-a355-bdc8b5bcb4a5 (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for cloud_controller/2df4d42f-e2f9-43d6-ad7b-0e74d9f1c55a (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for clock_global/a66a334e-f1a1-49f6-b838-5dbaed7e30b5 (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for uaa/16a1feb8-d7b3-4314-850d-7a7803a9ef58 (0): Finding and packing log files
Task 300 | 16:12:51 | Fetching logs for router/16eab2ca-02ed-4fa3-9168-849920b5ca27 (0): Finding and packing log files
Task 300 | 16:12:52 | Fetching logs for nfs_server/684737dd-e80c-4777-a8c4-b42926ffaf8a (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:52 | Fetching logs for doppler/90cafec5-3b53-41fc-998a-ab2059a028a3 (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:52 | Fetching logs for router/16eab2ca-02ed-4fa3-9168-849920b5ca27 (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:52 | Fetching logs for loggregator_trafficcontroller/81705678-612a-469b-a355-bdc8b5bcb4a5 (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:52 | Fetching logs for log_cache/5ec7e740-a45e-43f6-95e6-348e29d7abef (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:53 | Fetching logs for diego_database/2c4aa147-f16e-4752-a920-3d59db8d7af8 (0): Finding and packing log files
Task 300 | 16:12:53 | Fetching logs for mysql_proxy/7b78691d-04c3-4031-a2b0-d30c16b4932a (0): Finding and packing log files
Task 300 | 16:12:53 | Fetching logs for backup_restore/96385b0a-1acf-45ad-941b-7eaab3dcd763 (0): Finding and packing log files
Task 300 | 16:12:53 | Fetching logs for diego_cell/ddafd0de-f27e-4054-9b92-6bf4bab1d4fd (1): Finding and packing log files
Task 300 | 16:12:53 | Fetching logs for mysql/5e839194-4d70-46d6-bfcc-6d550bab9b7b (0): Finding and packing log files
Task 300 | 16:12:53 | Fetching logs for diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f (0): Finding and packing log files (00:00:02)
Task 300 | 16:12:53 | Fetching logs for diego_cell/b8fc2b05-ce4c-4959-be81-944fdcdbd51f (2): Finding and packing log files (00:00:02)
Task 300 | 16:12:54 | Fetching logs for clock_global/a66a334e-f1a1-49f6-b838-5dbaed7e30b5 (0): Finding and packing log files (00:00:03)
Task 300 | 16:12:54 | Fetching logs for backup_restore/96385b0a-1acf-45ad-941b-7eaab3dcd763 (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:54 | Fetching logs for mysql_monitor/ddad4a6b-e2cd-4d1e-88d4-d19bccf6198e (0): Finding and packing log files
Task 300 | 16:12:54 | Fetching logs for cloud_controller_worker/c2302b70-d3dc-428f-a05d-daeaf8dcd37f (0): Finding and packing log files
Task 300 | 16:12:54 | Fetching logs for mysql_proxy/7b78691d-04c3-4031-a2b0-d30c16b4932a (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:54 | Fetching logs for credhub/0de1db29-1610-4312-95cc-069dec56617f (0): Finding and packing log files
Task 300 | 16:12:54 | Fetching logs for nats/db5a0c47-941d-434e-a80a-de15b47dba65 (0): Finding and packing log files
Task 300 | 16:12:54 | Fetching logs for diego_brain/baab04be-d01a-45e3-9e33-f853f3fe32a6 (0): Finding and packing log files
Task 300 | 16:12:54 | Fetching logs for uaa/16a1feb8-d7b3-4314-850d-7a7803a9ef58 (0): Finding and packing log files (00:00:03)
Task 300 | 16:12:55 | Fetching logs for mysql/5e839194-4d70-46d6-bfcc-6d550bab9b7b (0): Finding and packing log files (00:00:02)
Task 300 | 16:12:55 | Fetching logs for mysql_monitor/ddad4a6b-e2cd-4d1e-88d4-d19bccf6198e (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:55 | Fetching logs for credhub/0de1db29-1610-4312-95cc-069dec56617f (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:55 | Fetching logs for cloud_controller_worker/c2302b70-d3dc-428f-a05d-daeaf8dcd37f (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:55 | Fetching logs for nats/db5a0c47-941d-434e-a80a-de15b47dba65 (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:55 | Fetching logs for diego_brain/baab04be-d01a-45e3-9e33-f853f3fe32a6 (0): Finding and packing log files (00:00:01)
Task 300 | 16:12:56 | Fetching logs for diego_database/2c4aa147-f16e-4752-a920-3d59db8d7af8 (0): Finding and packing log files (00:00:03)
Task 300 | 16:12:56 | Fetching logs for diego_cell/ddafd0de-f27e-4054-9b92-6bf4bab1d4fd (1): Finding and packing log files (00:00:03)
Task 300 | 16:12:58 | Fetching logs for cloud_controller/2df4d42f-e2f9-43d6-ad7b-0e74d9f1c55a (0): Finding and packing log files (00:00:07)
Task 300 | 16:12:58 | Fetching group of logs: Packing log files together

Task 300 Started  Mon Aug 15 16:12:51 UTC 2022
Task 300 Finished Mon Aug 15 16:12:58 UTC 2022
Task 300 Duration 00:00:07
Task 300 done

Downloading resource '1264c1e1-ec6c-4e98-a31a-c142291ae069' to '/home/ubuntu/cf-aca0fbd04df6cc023e6c-20220815-161306-169112597.tgz'...

[-------------------------------------------->______________________] 66.06%   ?
Succeeded
  
```

Let's say you are doing some active troubleshooting and would like to review the logs in real-time on each VM.   
To do this you will need to SSH into the affected VM and switch to the appropriate logging directory on the host depending on the type of logs you would like to review.   

For example there are job logs, errand logs, agent logs and monit logs which you are now most familar with.   


### Job logs {: #job-logs }

Release jobs on VMs produce logs throughout different lifecycle events. Release authors are strongly encouraged to place release job logs into `/var/vcap/sys/log/<release_job_name>/*.log`, providing a consistent place for the operator to find them.

Step 10.  For example `garden` release job will create two log files.  Let's cat both the standard error and standard out files.   

```
cat /var/vcap/sys/log/garden/garden.stdout.log
```

```
cat /var/vcap/sys/log/garden/garden.stderr.log
```


---
### Errand logs {: #errand-logs }

Unlike regular job logs BOSH does not automatically redirect errand logs to `/var/vcap/sys/log/*` directory, though we are planning to do so in future.

Errand's stdout and stderr output will be shown by the CLI when it's smaller than 1MB. If you expect errand to generate output larger than 1MB, currently it needs to be redirected to a file (by convention to `/var/vcap/sys/log/<job_name>/stdout.log`) from the errand script and then downloaded, or error will be returned upon errand completion.

To save output from an errand VM:

     In the errand run script, redirect the output to a log.
Step 11.  Using the CLI, run `bosh run-errand X` with the `--download-logs` option to download the logs.

    By default, the CLI downloads the logs to your present working directory. Use the `--logs-dir destination_directory` option to change this directory.

```shell
bosh run-errand smoke_tests -d <deployment-name>  --download-logs --logs-dir ~/smoke-tests-logs.txt
```

!!! note
    By default upon errand completion errand VM is deleted, so you cannot access logs saved to disk by the errand. You can use <code>--keep-alive</code> flag when running an errand to keep the VM with its logs.

---
### Monit logs {: #monit-logs }

Step 12.  The Agent uses Monit to start, restart, and stop release job processes as specified by the release jobs. Monit detects errors and outputs often useful information to its log. Use `tail` to examine the `monit.log` on a VM:

```shell
sudo tail -f -n 200 /var/vcap/monit/monit.log
```

---
### Agent logs {: #agent-logs }

Step 13.  Agent logs contain configuration and runtime information from the Agent running on a VM. Review these logs if the Director sees VM as unresponsive or the Director fails to contact it during its creation.

The Agent stores logs in `/var/vcap/bosh/log/` and outputs most recent content to `/var/vcap/bosh/log/current`.

```shell
sudo tail -f -n 200 /var/vcap/bosh/log/current
```

!!! note
    Agent logs are only accessible to the root user.
    
    
-----



# TAS  LAB 02

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

13.  Now that you have the GUID, you can run the following command to identify details around the application and where its running.   


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


You can also use cfdot to pull metrics regarding your diego cell resources.   

14.  Run the following command to view resources of the current diego cell.  

```
cfdot cell-states | jq '{cell_id, TotalResources, AvailableResources}'
```

Example Output: 

```
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f:~$ cfdot cell-states | jq '{cell_id, TotalResources, AvailableResources}'
{
  "cell_id": "b8fc2b05-ce4c-4959-be81-944fdcdbd51f",
  "TotalResources": {
    "MemoryMB": 13016,
    "DiskMB": 106617,
    "Containers": 249
  },
  "AvailableResources": {
    "MemoryMB": 7928,
    "DiskMB": 91257,
    "Containers": 234
  }
}


```

----
