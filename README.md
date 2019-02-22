# Cloud Auditing Framework (through GCP)

*Project goal is centered around Building a custom auditing framework through Docker utilizing google cloud services [GKS], kubernetes management service [Rancher], and utilizing an auditing tool [Security Monkey]*

### Introduction
- With the rise in cloud technologies utilized in the Idustry today, hosting services on the cloud is becoming an industry best standard centered around efficiency and availability. 
Utilizing docker containers we have to ability to expose specific external network points, allowing for external traffic into ports we decide. With this docker has the ability 
to increase the overall security of our services. Docker defined "Namespaces" provide the first and most straightforward form of isolation: processes running within a 
container cannot see, and even less affect, processes running in another container, or in the host system. Next, Each container also gets its own network stack, meaning 
that a container doesn’t get privileged access to the sockets or interfaces of another container.

- In combination with Kubernetes docker becomes somewhat complex due to network level access issues, but through all that, it makes running containers extrememely 
simple and easy to manage. For the management side of kuberenetes I have chosen to use an open source service called Rancher. Rancher was built to manage Kubernetes everywhere it runs. It can easily deploy new clusters from scratch, launch EKS, GKE and AKS clusters, or even import existing Kubernetes clusters. 

- For my blog my goal is to implement a local rancher server instance, connect kubernetes clusters from Google Cloud Platform, and Implement security monkey to audit existing gcp infastructure.


### Hypothesis
- In my opinion I believe this model will be versitile with multiple cloud enviroments, even if you do not prefer to use gcp. Security Monkey by Netflix has the ability to audit multiple AWS,GCP, and even Github accounts in unions. Utilizing the capabilites of GCP compute engine and GKS we can establish a highly scalable outocome with a capibility of growing as the size of cloud infastructure increases. For example, with my project which utilizes GKS and Security Monkey intially I most likely will start with one scheduler to keep track of around 10 workers which perform task neccsarry to audit my gcp services. For an enterprise level company, this is utilized at a much larger scale with often multiple schedulers working with clusters of workers that all eventually push out information to a master scheduler. So for the current state of the industry I believe this is vital to allow companies the ability to properly audit their cloud services and reduce their overall risk of intrusion!

### Research Sources:
https://rancher.com/docs/rancher/v2.x/en/
- "Rancher adds significant value on top of Kubernetes, first by centralizing role-based access control (RBAC) for all of the clusters and giving global admins the ability to control cluster access from one location. It then enables detailed monitoring and alerting for clusters and their resources, ships logs to external providers, and integrates directly with Helm via the Application Catalog. If you have an external CI/CD system, you can plug it into Rancher, but if you don’t, Rancher even includes a pipeline engine to help you automatically deploy and upgrade workloads."

https://docs.docker.com/
- Documentation used for the installation/creation of docker containers

https://kubernetes.io/docs/home/
- Documentation used as reference for the creation of clusters and integration with gcp and security monkey

https://cloud.google.com/docs/
- Documentation used when setting up services on/through GCP - compute engine resources/services accounts for access/etc.

# Steps to reproduce

 1. [Setup your Docker Enviroment](docs/docker_install.md)
 2. [Install Rancher on your Local Machine](docs/rancher_setup.md)
 3. [Link Rancher Server Cluster to Google Cloud](docks/gks_setup.md)
 4. [Setup Security Monkey Container on GCP](docs/secmonk_gcp.md)
 5. [Initalize/Configure Security Monkey](docs/secmonk_init.md)



