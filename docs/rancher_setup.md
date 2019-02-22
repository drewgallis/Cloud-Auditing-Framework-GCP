## Setting up Rancher 2.0 (local machine):
=====================

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

[Back to home](../README.md)

[On to the next step](secmonk_gcp.md)