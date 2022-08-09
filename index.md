# Tanzu Application Service - 
-----


# Demo Agenda 
### Developer Experience 
1. [Build and Deploy .NET Framework App (Windows) to TAS from Visual Studio](#example)
   - Deploy App to TAS using TAS's Visual Studio Plugin
   - Walk through what just happened
   - Demonstrate how TAS uses Cloud Native Buildpacks to Build Apps   
2. [Build and Deploy .NET Framework App (Windows) to TAS from Azure DevOps Pipelines ](#example2)
   - Demonstrate how TAS can integrate with Azure DevOps Build & Release Pipelines   
   - Deploy App to TAS using TAS/Cloud Foundry's Azure DevOps Plugin
   - Bind App to an Azure MySQL Database using TAS's Open Service Broker API 
3. [Walk through Apps Manager UI ](#example2)
   - Auto Scaling Apps with Apps Manager    
   - Review Application logs and metrics using Apps Manager 

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

## Day 1 - The Developer Experience 

## cf push Automates Developer + Operator workflows 

### Automation, not Support Tickets 

![image](https://user-images.githubusercontent.com/73367284/158695941-ee0c3804-313d-4dcd-b57e-a6eb64c047e0.png)



### So what happens after you execute the "cf push" cli command?: 
1. Uploads your code
2. Detects and installs required runtime and middleware (“Buildpacks”)
3. Sets up a route (or URL)
4. Creates a load balancing entry
5. Creates SSL termination
6. Creates health monitoring and logging subsystems
7. Starts your app in a healthy state, with the desired number of instances
8. Binds specified backing services

<br/>

<br/>

<br/>


## Let's Compare Deployment Tasks!
### Once code is complete and tested, what do you have to do to get to prod? 


![image](https://user-images.githubusercontent.com/73367284/158695526-b0db8e96-528f-4762-91d7-8e2599ba39e4.png)


<br/>

<br/>


## Dockerfiles:  Typically the most common way developers get started with containers.


###  1. It's a good starting point for most
###  2. Descriptive file specification 
###  3. It's pretty flexible

<br/>


```
FROM somewhere/ubuntu
MAINTAINER Anonymous Person <Anonymous.Person@email.com>

ENV USER root
ENV PASS aiPeekai0AeZ2meephoolais7doo1thu

RUN \
  apt-get update && \
  apt-get -y install \
          mysql-server-5.5 && \
  rm -rf /var/lib/apt/lists/*

COPY my.cnf /etc/mysql/my.cnf
COPY start.sh start.sh

VOLUME ["/var/lib/mysql"]

RUN rm /usr/sbin/policy-rc.d
CMD ["/start.sh"]

EXPOSE 3306

```

<br/>
<br/>
<br/>


### However containers built using dockerfile are hard to productionize and scale 

#### When dev teams build images differently, they introduce vulnerabilities and complexity


![image](https://user-images.githubusercontent.com/73367284/158699415-c539e7c3-3f09-4921-a8f7-691ff219fa6e.png)


### Image updates
1. Hard to debug, fix and maintain non-standard images across the fleet

### Security posture
2. Challenging to keep container images free of vulnerabilities

### Full stack container audits
3. Hard to uniformly track container dependencies

### IT governance
4. Hard to reinforce software compliance


<br/>

<br/>

### Containers done right with Tanzu

#### Tanzu can be an accelerator for enterprises on their app modernization journey


![image](https://user-images.githubusercontent.com/73367284/158700379-ac2341d1-e710-49ce-9966-db2b79b95747.png)


### Automated image updates
1. Automated container re-base on new code commits, dependency and OS updates

### Improved security posture
2. Easy to track and patch containers with latest CVEs

### Full stack container audits
3. Auditing is a breeze with bundled container metadata 

### Agile IT governance
4. Ability to verify policy enforcements reinforces software compliance


<br/>
<br/>
<br/>

## VMware Tanzu provides comprehensive support for containers 

![image](https://user-images.githubusercontent.com/73367284/158700970-c28cbc6e-27af-46b6-8845-a76d27d1380a.png)


<br/>
<br/>


## Extend Custom Apps with Popular Backing Services 
### Use the Open Service Broker API to manage the lifecycle of databases, caches, and more

![image](https://user-images.githubusercontent.com/73367284/158701336-15d40150-ce4f-432b-9036-f9ee9b069386.png)

<br/>

[Read more...](https://tanzu.vmware.com/solutions-hub)

<br/>

<br/>

<br/>

-----

## Day 2 - The Operator's Experience 
<br/>

## How does TAS differ from Cloud Foundry?

![image](https://user-images.githubusercontent.com/73367284/158703228-bcf8733a-6c78-40ae-85ce-a21fafddd1d9.png)


<br/>
<br/>
<br/>


## Automated Management and Upgrades using Ops Manager 

<img width="1660" alt="image" src="https://user-images.githubusercontent.com/73367284/158706446-3208cfe3-44f0-4954-8f85-d093e3a38c41.png">


### LifeCycle Management of all components 
#### ( Including Installing, Upgrading, Patching and overall maintenance )
- Linux and Windows Operating Systems (Diego Cells) 
- Platforms (Tanzu Application Service, Tanzu Kubernetes Grid Integrated) 
- All Services (Messaging, Databases, Caching, etc.) 
- Application Buildpacks (Apps) 
- Certificates 


<br/>
<br/>

## Cloud Native Security 
![image](https://user-images.githubusercontent.com/73367284/160543427-46bb7d77-e533-4ce4-b718-ebe3fb11cb7e.png)

- With Operations Manager, enterprises can repave every virtual machine (VM) in their data center from a known good state every few hours without application downtime. 

<br/>
<br/>
<br/>

<br/>

## Securing credentials made easy 
![image](https://user-images.githubusercontent.com/73367284/160645296-14f4ee06-9319-477c-a3ae-4b5236f3c398.png)

- CredHub supports different types of credentials to simplify generating and managing multi-part credentials. For example, a TLS certificate contains three parts: the root certificate authority (CA), the certificate, and the private key. CredHub supports all three parts, which helps keep connection requests from being rejected erroneously.

<br/>

<br/>
<br/>
<br/>

## Tanzu Application Service Deployment Reference Architecture 

![image](https://user-images.githubusercontent.com/73367284/160474128-716ceb8f-8685-448b-a384-d31fb0eb0633.png)

<br/> 
<br/>

## External Client Routing Request Flow  


![image](https://user-images.githubusercontent.com/73367284/160528048-2727f490-1ec2-4da3-b3c8-bd8ca62b5c9e.png)

#### The following process describes how an external client makes a request to an app running on TAS for VMs:

- The external client sends its request.

- Your DNS service sends the request to the HTTP or TCP load balancer based on the prefix of the DNS name in the client request, such as http in http.example.com.

- The load balancer sends the request to the load balancer’s corresponding router.

- The router sends the request to the app.


<br/>
<br/>


## How TAS for VMs Maintains Updated Routing Tables

![image](https://user-images.githubusercontent.com/73367284/160538618-d091e08c-4879-497a-b141-9c0659501928.png)

#### The following process describes how a router obtains information about routes for an app running on TAS for VMs:

- The Cloud Controller component sends app route information to Diego BBS. For HTTP routing, route information includes the host and path of an external URL, as shown in the format of the router.register message in the Gorouter documentation on GitHub. For TCP routing, route information includes the route port on which the TCP connection was received; for more information, see the Routing API documentation on GitHub.

- Diego BBS coordinates the back end IP address and port where each instance of the app runs. When queried by the route emitter, the BBS sends this information along with Cloud Controller’s app route information to the route emitter on the Diego cell where instances of the app are located.

- If a route is HTTP, the route emitter on the Diego cells sends app route, IP, and port information to NATS, which then sends it to the Gorouter. If a route is TCP, the route emitter sends that information to the Routing API, which then sends it to the TCP router.

- The Gorouter and TCP router use the route, IP, and port information from the route emitter to map incoming app requests to back end app instance locations.


<br/>

<br/>

<br/>




## Observability within Tanzu Application Service

![image](https://user-images.githubusercontent.com/73367284/158702065-c94d26cf-4ba9-4eb8-82ad-01d2f733fab6.png)

<br/>
<br/>
<br/>

## Application Autoscaler  

<img width="794" alt="image" src="https://user-images.githubusercontent.com/73367284/158706724-64b6ca64-7f0a-43f9-b8ea-aa4642a773e4.png">

<br/>
<br/>


## Multi-Tenancy with Tanzu Application Service 

![image](https://user-images.githubusercontent.com/73367284/158702439-fed2501d-b063-43f4-b87a-0cfb4c368b0c.png)

<br/>
<br/>
<br/>



## BOSH was built for Cloud Foundry  


![image](https://user-images.githubusercontent.com/73367284/158704927-080f0d51-1987-420e-a007-32acca625b45.png)

BOSH is a project that unifies release engineering, deployment, and lifecycle management of small and large-scale cloud software. BOSH can provision and deploy software over hundreds of VMs. It also performs monitoring, failure recovery, and software updates with zero-to-minimal downtime.

While BOSH was developed to deploy Cloud Foundry PaaS, it can also be used to deploy almost any other software (Hadoop, for instance). BOSH is particularly well-suited for large distributed systems. In addition, BOSH supports multiple Infrastructure as a Service (IaaS) providers like VMware vSphere, Google Cloud Platform, Amazon Web Services EC2, Microsoft Azure, OpenStack, and Alibaba Cloud. There is a Cloud Provider Interface (CPI) that enables users to extend BOSH to support additional IaaS providers such as Apache CloudStack and VirtualBox.

<br/>


![image](https://user-images.githubusercontent.com/73367284/158705252-169fd9e5-5e25-4e6e-8749-93e975587cef.png)




<br/>
<br/>

 -------
 
 
For more details see [Tanzu Application Service](https://tanzu.vmware.com/application-service)
