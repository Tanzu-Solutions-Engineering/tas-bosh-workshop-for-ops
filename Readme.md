## Tanzu Application Service 
## Hands On Lab for Platform Operators 

<br/>

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
4. [Introduction to Bosh](#example)
   - What is Bosh?
   - Bosh Concepts (ie. stemcells, CPI, releases, manifests, etc. ) 

### Operator Experience 
1. [Demonstrate Day 2 Operations with Tanzu Application Service ](#example2)
   - Walk through Zero Downtime patching of Windows & Linux Operating Systems using Ops Manager 
   - Walk through Zero Downtime upgrades and patching of platform services using Ops Manager

-------

<br/>
<br/>


## High Level Overview 

VMware Tanzu Application Service is an opinionated, application-aware platform that provides a rich set of developer tooling and a prepaved path to production to build and deploy software quickly and securely on any compliant public cloud or on-premises vCenter Data Center.


VMware Tanzu Application Service is a modern application platform for enterprises that want to continuously deliver and run microservices or traditional applications across clouds or on-premises.   
[Read more...](https://tanzu.vmware.com/application-service)

<br/>


# A modern runtime for microservices and traditional applications 
 ![image](https://user-images.githubusercontent.com/73367284/158693192-1b5d6762-f88f-4d8c-ab57-d399344c50f8.png)

<br/>



<br/>

-----



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
---
