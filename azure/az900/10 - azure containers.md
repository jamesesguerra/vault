## containers
- use containers if you want to run **multiple instances of an app on a single host**
- they’re designed to allow you to respond to changes on demand since they’re lightweight, and dynamic (in terms of creation, scaling, and stopping)

## VMs vs containers
### VMs 
- **pros**
  - flexibility
  - full control over OS, software, and hardware
- **cons**
  - dependency issues ie if multiple services need different runtime environments
  - not performant; standing one up and shutting one down takes time cos of the overhead of a full OS
### containers
- **pros**
  - performant  & lightweight cos all it has is its app and dependencies (no OS)
  - OS is abstracted away, so multiple containerized apps can run side-by-side
  - can be orchestrated with k8s
  - portable (dockerfile, dockerhub)
- **cons**
  - not flexible, no control over OS, hardware

## azure container instances
- paas offering
- upload containers and service will run containers for you

## azure container apps
- similar to container instances except they have extra benefits such as **load balancing** & **scaling**

## azure k8s service
- **container orchestration service** - manages the lifecycle of containers

## microservice architecture
- containers are often used to create solutions using a microservice architecture
- break solution into smaller, independent pieces
- allows you to separate pieces of your app into logical sections that can be maintained, scaled, or updated independently
- **tldr** - you can make a change to one part of your app without impacting other components