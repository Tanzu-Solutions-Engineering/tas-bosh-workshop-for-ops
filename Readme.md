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

#### [Step 1.] 
Find VMs  
- `bosh vms` - to list all VMs across their foundation looking for: `cloud_controller/<VM-GUID>`

#### [Step 2.] 
SSH to VM 
SSH into the `cloud_controller` VM 
- `bosh ssh <VM-Type>/<VM-GUID>  -d <cf-DeploymentName>`

#### [Step 3.] 
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

#### [Step 4.] 
Lets stop the cloud controller process.

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


#### [Step 5.] 
Now the SRE team member can start all monit processes that are stopped or start them individually 

- `monit start all`
</br>or
- `monit start cloud_controller_ng`

#### [Step 6.] 
Verify cloud_controller_ng is running
- `monit summary`

### [Developer]

As the developer, let's check on our applications logs again.

Now the `cf` CLI commands will work since the Cloud Controller is running again.


<br/>

### [Site Reliability Engineer]

Now lets assume we have a noisey neighbor situation that's happening on one of our diego cells and instead of scaling resources we would like to identify the problem process or application.   

This raises the question of how do we identify what applications are running on our diego cells.    


To do this we will leverage a tool called cfdot "CF Diego Operator Toolkit".   

#### [Step 7.] 
From the Ops Manager VM SSH to one of your diego cells.   

#### [Step 8.] 
Type the cfdot command without sudo'ing to root.

#### [Step 9.] 
Run the following command to discovery applications that are running within a selected diego cell.   

```
cfdot cell-state <guid-of-diego-cell> | jq -r '. | "\ndiego_cell:", .cell_id, "\n", "App-Guids:", .LRPs[].process_guid[0:36]'
```

The GUID of the VM can be found within the name of the VM resource in the following format   `<VM-Type>/<GUID>`

```
ubuntu@cypress-ops-manager:~$ bosh vms
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f                     running        us-central1-f  10.0.4.17  vm-0573608e-ffc7-49ea-74bd-08080ab0ffe2  toolsmiths.n1-highmem-2.128  true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
diego_cell/b8fc2b05-ce4c-4959-be81-944fdcdbd51f                     running        us-central1-f  10.0.4.24  vm-0d5b1820-7ad3-407d-49e0-040459e64ad0  toolsmiths.n1-highmem-2.128  true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  
diego_cell/ddafd0de-f27e-4054-9b92-6bf4bab1d4fd                     running        us-central1-f  10.0.4.23  vm-e5d8ed8f-99e4-4cda-594f-1631cb4e66b5  toolsmiths.n1-highmem-2.128  true    bosh-google-kvm-ubuntu-xenial-go_agent/621.261  

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


#### [Step 10.] 
Open a separate terminal to run the following CF CLI command. 

```
cf curl /v2/apps/<guid-of-app>/stats | grep name | uniq
```

Example Output: 

```
root@cypress-ops-manager:~# cf curl /v2/apps/1f70ffeb-a927-487e-8349-ade2ceb683d3/stats | grep name | uniq
         "name": "apps-manager-js-green",

```

Running cf CLI command for multiple guid's can also be automated as shown below:


#### [Step 11.] 
Copy all the app guid to a file and you can run something like the below for loop against the file (replace the filename in the below loop)


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

#### [Step 12.] 
Set command as variable 


```
PGUIDS=$(cfdot desired-lrps | grep <APP_ROUTE> | jq -r '.process_guid')
```

Example Output: 


```
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f:~$ echo $PGUIDS
09901e52-1eb6-4f18-ac69-e56f4d04eb9e-05ee8942-b1dd-411d-b21e-e58864721187
```

#### [Step 13.] 
Now that you have the GUID, you can run the following command to identify details around the application and where its running.   


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

#### [Step 14.] 
Run the following command to view resources of the current diego cell.  

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

#### [Step 1.] 
Find VMs  
- `bosh vms` - to list all VMs across their foundation looking for: `cloud_controller/<VM-GUID>`

#### [Step 2.] 
SSH to VM 
SSH into the `cloud_controller` VM 
- `bosh ssh <VM-Type>/<VM-GUID>  -d <cf-DeploymentName>`

#### [Step 3.] 
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

#### [Step 4.] 
Lets stop the cloud controller process.

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


#### [Step 5.] 
Now the SRE team member can start all monit processes that are stopped or start them individually 

- `monit start all`
</br>or
- `monit start cloud_controller_ng`

#### [Step 6.] 
Verify cloud_controller_ng is running
- `monit summary`

### [Developer]

As the developer, let's check on our applications logs again.

Now the `cf` CLI commands will work since the Cloud Controller is running again.


<br/>

### [Site Reliability Engineer]

Now lets assume we have a noisey neighbor situation that's happening on one of our diego cells and instead of scaling resources we would like to identify the problem process or application.   

This raises the question of how do we identify what applications are running on our diego cells.    


To do this we will leverage a tool called cfdot "CF Diego Operator Toolkit".   

#### [Step 7.] 
From the Ops Manager VM SSH to one of your diego cells.   

#### [Step 8.] 
Type the cfdot command without sudo'ing to root.

#### [Step 9.] 
Run the following command to discovery applications that are running within a selected diego cell.   

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


#### [Step 10.] 
Open a separate terminal to run the following CF CLI command. 

```
cf curl /v2/apps/<guid-of-app>/stats | grep name | uniq
```

Example Output: 

```
root@cypress-ops-manager:~# cf curl /v2/apps/1f70ffeb-a927-487e-8349-ade2ceb683d3/stats | grep name | uniq
         "name": "apps-manager-js-green",

```

Running cf CLI command for multiple guid's can also be automated as shown below:


#### [Step 11.] 
Copy all the app guid to a file and you can run something like the below for loop against the file (replace the filename in the below loop)


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

#### [Step 12.] 
Set command as variable 


```
PGUIDS=$(cfdot desired-lrps | grep <APP_ROUTE> | jq -r '.process_guid')
```

Example Output: 


```
diego_cell/74ea458a-5c1c-445f-94e5-777b7810998f:~$ echo $PGUIDS
09901e52-1eb6-4f18-ac69-e56f4d04eb9e-05ee8942-b1dd-411d-b21e-e58864721187
```

#### [Step 13.] 
Now that you have the GUID, you can run the following command to identify details around the application and where its running.   


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

#### [Step 14.] 
Run the following command to view resources of the current diego cell.  

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
