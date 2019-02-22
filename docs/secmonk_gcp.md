## Installing Security Monkey through GCP [Netflix Docs](https://github.com/Netflix/security_monkey/tree/develop/docs)
=====================

### Install gcloud


If you haven't already, install *gcloud* from the [downloads](https://cloud.google.com/sdk/downloads) page.  *gcloud* enables you to administer VMs, IAM policies, services and more from the command line.

### Setup Service Account

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


For each GCP project you would like Security Monkey to access, you'll need to enable the IAM API.  Visit the [IAM API page](https://console.cloud.google.com/apis/api/iam.googleapis.com/overview) page in the web console
 and click 'Enable API' at the top of the screen. When dealing with many projects, you might prefer to do this with the gcloud command.  For details on how to enable services with gcloud, visit the
  [service-management](https://cloud.google.com/service-management/enable-disable#enabling_services) page.  The IAM service name is 'iam.googleapis.com'.


### Creating Security Monkey instance on GCP (Ubuntu 16.04)


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