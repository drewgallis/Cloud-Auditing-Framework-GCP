# Cloud-Auditing-Framework-GCP-
Building a custom auditing framework through Docker utilizing google cloud services[GKS], kubernetes management service[Rancher], and an auditing tool[Security Monkey]

•	Introduction
With the rise in cloud technologies utilized in the Idustry today, hosting services on the cloud is becoming an industry best standard centered around efficiency and availability. 
Utilizing docker containers we have to ability to expose specific external network points, allowing for external traffic into ports we decide. With this docker has the ability 
to increase the overall security of our services. Docker defined "Namespaces" provide the first and most straightforward form of isolation: processes running within a 
container cannot see, and even less affect, processes running in another container, or in the host system. Next, Each container also gets its own network stack, meaning 
that a container doesn’t get privileged access to the sockets or interfaces of another container.

In combination with Kubernetes docker becomes somewhat complex due to network level access issues, but through all that, it makes running containers extrememely 
simple and easy to manage. For the management side of kuberenetes I have chosen to use an open source service called Rancher. Rancher was built to manage Kubernetes everywhere it runs. 
It can easily deploy new clusters from scratch, launch EKS, GKE and AKS clusters, or even import existing Kubernetes clusters. 

For my blog my goal is to implement a local rancher server instance, connect kubernetes clusters from Google Cloud Platform, and Implement security monkey to audit existing gcp infastructure.


•	Hypothesis
In my opinion I believe this model will be versitile with multiple cloud enviroments, even if you do not prefer to use gcp. Security Monkey by Netflix has the ability to audit multiple AWS,GCP, and even Github accounts in unions. Utilizing the capabilites of GCP compute engine and GKS we can establish a highly scalable outocome with a capibility of growing as the size of cloud infastructure increases. For example, with my project which utilizes GKS and Security Monkey intially I most likely will start with one scheduler to keep track of around 10 workers which perform task neccsarry to audit my gcp services. For an enterprise level company, this is utilized at a much larger scale with often multiple schedulers working with clusters of workers that all eventually push out information to a master scheduler. So for the current state of the industry I believe this is vital to allow companies the ability to properly audit their cloud services and reduce their overall risk of intrusion!

•	Research Sources:
https://rancher.com/docs/rancher/v2.x/en/
"Rancher adds significant value on top of Kubernetes, first by centralizing role-based access control (RBAC) for all of the clusters and giving global admins the ability to control cluster access from one location. It then enables detailed monitoring and alerting for clusters and their resources, ships logs to external providers, and integrates directly with Helm via the Application Catalog. If you have an external CI/CD system, you can plug it into Rancher, but if you don’t, Rancher even includes a pipeline engine to help you automatically deploy and upgrade workloads."

https://docs.docker.com/
Documentation used for the installation/creation of docker containers

https://kubernetes.io/docs/home/
Documentation used as reference for the creation of clusters and integration with gcp and security monkey

https://cloud.google.com/docs/
Documentation used when setting up services on/through GCP - compute engine resources/services accounts for access/etc.

•	Steps to reproduce

# Installing Docker (local machine):

### Script only works if sudo caches the password for a few minutes:

`sudo true`


`sudo apt-get update`


### Alternatively you can use the official docker install script

`wget -qO- https://get.docker.com/ | sh`

### Install docker-compose

``COMPOSE_VERSION=`git ls-remote https://github.com/docker/compose | grep refs/tags | grep -oP "[0-9]+\.[0-9][0-9]+\.[0-9]+$" | tail -n 1``

``sudo sh -c "curl -L https://github.com/docker/compose/releases/download/${COMPOSE_VERSION}/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose"``

`sudo chmod +x /usr/local/bin/docker-compose`

`sudo sh -c "curl -L https://raw.githubusercontent.com/docker/compose/${COMPOSE_VERSION}/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose"]`

### Install docker-cleanup command (good for cleaning up your messy docker listings)

`cd /tmp`

`git clone https://gist.github.com/76b450a0c986e576e98b.git`

`cd 76b450a0c986e576e98b`

`sudo mv docker-cleanup /usr/local/bin/docker-cleanup`

`sudo chmod +x /usr/local/bin/docker-cleanup`

---

# Setting up Rancher 2.0 (local machine):

1. Provision a single Linux host:
All hosts must be provisioned according to Rancher's [Requirements](https://rancher.com/docs/rancher/v2.x/en/installation/requirements/) to launch your Rancher Server.

2. Running rancher/rancher and rancher/rancher-agent on the Same Node:

- In the situation where you want to use a single node to run Rancher and to be able to add the same node to a cluster, you have to adjust the host ports mapped for the rancher/rancher container.

- If a node is added to a cluster, it deploys the nginx ingress controller which will use port 80 and 443. This will conflict with the default ports we advice to expose for the rancher/rancher container.

- Please note that this setup is not recommended for production use, but can be convenient for development/demo purposes.

- To change the host ports mapping, replace the following part `-p 80:80 -p 443:443` with `-p 8080:80 -p 8443:443:`

3. To start the server and agent run this command on your locally hosted machine:

    `docker run -d --restart=unless-stopped -p 8080:80 -p 8443:443 rancher/rancher:latest`


- This spins up the Rancher Server container, which also acts as the master node in your default Kubernetes cluster. You can also use Rancher Server to manage an existing Kubernetes cluster on other services such the IBM Cloud Container Service as well.

---

# Linking Google GKS to Rancher 2.0:

    1. Once the container has is started issue command `sudo docker ps` to check what external port your container was set push out from.

    2. Then navigate to http://hostmachine:insertporthere and you’ll be presented with the initial interface of Rancher's Dashboard.
    
    3. You will then be prompted to insert your default password to login to the web interface with and then with:

    4. After that you will then be asked to specify your access URL for all rancher related services (make sure the ip is publicy accessable from GCP)

    5. Once everything is set up you will then 


### Troubleshooting:

    Your rancher server is behind the public network so the GKS or EKS's callback cannot connect to your rancher server successfully, you will need to add a proxy to expose your rancher server and make sure the proxy IP and port and configured properly within the rancher settings `https://your-rancher-server-url/g/settings/advanced`.

    ![alt text](deployment_ports.png)

    Note: if you check your Google GKS the newly added k8s cluster should already be there, remember to remove it if you have created in previous!
---

# Installing Security Monkey through GCP (Netflix Documentation):

### Install gcloud
---------------

If you haven't already, install *gcloud* from the [downloads](https://cloud.google.com/sdk/downloads) page.  *gcloud* enables you to administer VMs, IAM policies, services and more from the command line.

### Setup Service Account
---------------------

To restrict which permissions Security Monkey has to your projects, we'll create a [Service Account](https://cloud.google.com/compute/docs/access/service-accounts) with a special role.

- Access the [Google console](https://console.cloud.google.com/home/dashboard).
- Under "IAM & Admin", select "Service accounts."
- Select "Create Service Account".
  - Name: "securitymonkey"
  - Add Role "IAM->SecurityReviewer"
  - Add Role "Project->Viewer"
  - **NOTE**: If you're going to monitor your GCP services from an external system (i.e. an AWS instance), check the box "Furnish a new private key" and ensure JSON is selected as the Key type. The filesystem path to this JSON is what is supplied to the `creds_file` parameter when creating a GCP account.
    - If you are going to be monitoring your GCP services from a GCP instance, then you don't need to generate a key. You will instead launch your GCP instance with this service account.
  - Hit "Create"

 - Select the newly created "securitymonkey" services account and click on "Permissions".
   -  Type in your Google email adddress and select the Owner role.
   -  Press "Add".

### Enable IAM API
---------------

For each GCP project you would like Security Monkey to access, you'll need to enable the IAM API.  Visit the [IAM API page](https://console.cloud.google.com/apis/api/iam.googleapis.com/overview) page in the web console
 and click 'Enable API' at the top of the screen. When dealing with many projects, you might prefer to do this with the gcloud command.  For details on how to enable services with gcloud, visit the
  [service-management](https://cloud.google.com/service-management/enable-disable#enabling_services) page.  The IAM service name is 'iam.googleapis.com'.


### Security Monkey installation on GCP:
=====================

Create an instance running Ubuntu 16.04 LTS using our 'securitymonkey' service account.

Navigate to the [Create Instance page](https://console.developers.google.com/compute/instancesAdd). Fill in the following fields:

-   **Name**: securitymonkey
-   **Zone**: If using GCP Cloud SQL, select the same zone here. [(Zone List)](https://cloud.google.com/compute/docs/regions-zones/regions-zones#available)
-   **Machine Type**: 1vCPU, 3.75GB (minimum; also known as n1-standard-1)
-   **Boot Disk**: Ubuntu 16.04 LTS
-   **Service Account**: securitymonkey (This is provisioned in the [IAM GCP instructions](https://github.com/Netflix/security_monkey/blob/develop/docs/iam_gcp.md).)
-   **Firewall**: Allow HTTPS Traffic

Click the *Create* button to create the instance.

Install gcloud
--------------

If you haven't already, install *gcloud* from the [downloads](https://cloud.google.com/sdk/downloads) page. *gcloud* enables you to administer VMs, IAM policies, services and more from the command line.

Connecting to your new instance:
--------------------------------

We will connect to the new instance over ssh with the gcloud command:

    $ gcloud compute ssh securitymonkey --zone <ZONE>

Or by initalizing a gloud shell through the gcp suite.

### Postgres on GCP
===============

If you are deploying Security Monkey on GCP and decide to use Cloud SQL, it's recommended to run [Cloud SQL Proxy](https://cloud.google.com/sql/docs/postgres/sql-proxy) to connect to Postgres. To use Postgres on Cloud SQL, create a new instance from your GCP console and create a password for the `postgres` user when Cloud SQL prompts you. (If you ever need to reset the `postgres` user's password, refer to the [Cloud SQL documentation](https://cloud.google.com/sql/docs/postgres/create-manage-users).)

Enable Cloud SQL API
--------------------
To use cloud_sql_proxy you will need to enable the Google Cloud SQL API. Visit the [Cloud SQL API page](https://console.cloud.google.com/apis/api/sqladmin.googleapis.com/overview) and click 'Enable API'.

Create a service account for Cloud SQL Proxy
--------------------------------------------
To be able to run the Cloud SQL Proxy you will need to create a [Service Account](https://cloud.google.com/compute/docs/access/service-accounts) with a special role.

- Access the [Google console](https://console.cloud.google.com/home/dashboard).
- Under "IAM & Admin", select "Service accounts."
- Select "Create Service Account".
  - Name: "cloud-sql-proxy"
  - Add Role "Cloud SQL-> Cloud SQL Client"
- Select "Furnish a new private key"
  - Key type: JSON
- Click "Save"

After the instance is up, run Cloud SQL Proxy:

    $ ./cloud_sql_proxy -instances=[INSTANCE CONNECTION NAME]=tcp:5432 -credential_file=/path/to/gcp/serviceaccount/keys/key.json &

You can find the instance connection name by clicking on your Cloud SQL instance name on the [Cloud SQL dashboard](https://console.cloud.google.com/sql/instances) and looking under "Properties". The instance connection name is something like [PROJECT\_ID]:[REGION]:[INSTANCENAME].

You'll need to run Cloud SQL Proxy on whichever machine is accessing Postgres, e.g. on your local workstation as well as on the GCE instance where you're running Security Monkey.

Connect to the Postgres instance:

    $ sudo psql -h 127.0.0.1 -p 5432 -U postgres -W


