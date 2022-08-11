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







