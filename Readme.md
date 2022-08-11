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
   - CF CLI Installed Locally  
3. [Introduction to Bosh](#example)
   - What is Bosh?
   - Bosh Concepts (ie. stemcells, CPI, releases, manifests, etc. ) 
4. [Hands on Labs](#example)
   - Unresponsive Agent Lab
   - Missing Diego Cell Lab
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
