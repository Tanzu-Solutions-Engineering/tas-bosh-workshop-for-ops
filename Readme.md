## Tanzu Application Service 
## Hands On Lab for Platform Operators 

<br/>

### High Level Overview 

VMware Tanzu Application Service is an opinionated, application-aware platform that provides a rich set of developer tooling and a prepaved path to production to build and deploy software quickly and securely on any compliant public cloud or on-premises vCenter Data Center.


VMware Tanzu Application Service is a modern application platform for enterprises that want to continuously deliver and run microservices or traditional applications across clouds or on-premises.   
[Read more...](https://tanzu.vmware.com/application-service)

<br/>


### A modern runtime for microservices and traditional applications 
 ![image](https://user-images.githubusercontent.com/73367284/158693192-1b5d6762-f88f-4d8c-ab57-d399344c50f8.png)

<br/>



<br/>

-----
# Lab Agenda 
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

<br/>

### Goal

Familiarization with Workshop Lab environment.

<br/>

### Workshop Lab Architecture: Tanzu Application Service

Example Pic

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

## Introduction to Bosh 

### What is BOSH?

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



---
