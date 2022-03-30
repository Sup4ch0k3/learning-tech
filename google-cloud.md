# Google Cloud

## Getting Started
```
gcloud auth list
gcloud config list project
```

## Configure your environment

See what your default region and zone settings are

```
gcloud config get-value compute/zone
gcloud config get-value compute/region
```

Identify your default region and zone

```
gcloud compute project-info describe --project <your_project_ID>
```

Set environment variables

```
export PROJECT_ID=<your_project_ID>
export ZONE=<your_zone>
echo $PROJECT_ID
echo $ZONE
```

## Create a virtual machine with the gcloud tool

```
gcloud compute instances create nucleus-jumphost-905 --machine-type f1-micro --zone $ZONE
```

Command details

- `gcloud compute` allows you to manage your Compute Engine resources in a format that's simpler than the Compute Engine API.

- `instances create` creates a new instance.

- `gcelab2` is the name of the VM.

- The `--machine-type` flag specifies the machine type as n1-standard-2.

- The `--zone` flag specifies where the VM is created.

- If you omit the `--zone` flag, the `gcloud` tool can infer your desired zone based on your default properties. Other required instance settings, such as `machine type` and `image`, are set to default values if not specified in the `create` command.

Get help

```
gcloud compute instances create --help
```

## Explore gcloud commands

```
gcloud -h

gcloud config --help
```

**Note**: Press ENTER or the spacebar to scroll through the help content. To exit the content, type Q.

```
gcloud help config

gcloud config --help
```

List config

```
gcloud config list
```

See all properties and their settings

```
gcloud config list --all
```

List your components

```
gcloud components list
```

## Install a new component

```
sudo apt-get install google-cloud-sdk
```

Enable the `gcloud interactive` mod

```
gcloud beta interactive
```

When using the interactive mode, press TAB to complete file path and resource arguments. To try this feature, start typing the following command.

```
gcloud compute instances describe <your_vm>
```
A list of commands is displayed below the Cloud Shell pane. Pressing F2 toggles the active help section to ON or OFF.

## Connect to your VM instance with SSH

```
gcloud compute ssh gcelab2 --zone $ZONE
```

## Create a new instance from the Cloud Console

1. In the Cloud Console, on the **Navigation menu**, click **Compute Engine** > **VM Instances**.

2. To create a new instance, click CREATE INSTANCE

3. There are many parameters you can configure when creating a new instance.

4. Click Create.

5. To use SSH to connect to the virtual machine, in the row for your machine, click SSH.

## Create a new instance with gcloud

```
gcloud compute instances create gcelab2 --machine-type n1-standard-2 --zone us-central1-f
```

See all the defaults, run:

```
gcloud compute instances create --help
```

To exit help, press **CTRL + C**.

In the Cloud Console, on the **Navigation menu**, click **Compute Engine** > **VM instances**.

You can also use SSH to connect to your instance via `gcloud`

```
gcloud compute ssh gcelab2 --zone us-central1-f
```

## Deploy your app

```
gcloud app deploy
```

## View your application

```
gcloud app browse
```

## Create a function

1. In the Cloud Shell command line, create a directory for the function code.

```
mkdir gcf_hello_world
```

2. Move to the gcf_hello_world directory.

```
cd gcf_hello_world
```

3. Create and open index.js to edit.

```
nano index.js
```

4. Copy the following into the index.js file:

```
/**
* Background Cloud Function to be triggered by Pub/Sub.
* This function is exported by index.js, and executed when
* the trigger topic receives a message.
*
* @param {object} data The event payload.
* @param {object} context The event metadata.
*/
exports.helloWorld = (data, context) => {
const pubSubMessage = data;
const name = pubSubMessage.data
    ? Buffer.from(pubSubMessage.data, 'base64').toString() : "Hello World";
console.log(`My Cloud Function: ${name}`);
};
```

## Create a cloud storage bucket

```
gsutil mb -p [PROJECT_ID] gs://[BUCKET_NAME]
```

## Deploy your function

1. Deploy the function to a pub/sub topic named hello_world, replacing [BUCKET_NAME] with the name of your bucket:

```
gcloud functions deploy memories-thumbnail-creator \
  --stage-bucket [BUCKET_NAME] \
  --trigger-topic thumbnail \
  --runtime nodejs8
```

2. Verify the status of the function.

```
gcloud functions describe memories-thumbnail-creator
```

## Test the function

```
DATA=$(printf 'Hello World!'|base64) && gcloud functions call helloWorld --data '{"data":"'$DATA'"}'
```

## View logs

```
gcloud functions logs read helloWorld
```

## Set a default compute zone

```
gcloud config set compute/zone us-east1-b
```

## Create a GKE cluster

```
gcloud container clusters create [CLUSTER-NAME]
```

## Get authentication credentials for the cluster

```
gcloud container clusters get-credentials [CLUSTER-NAME]
```

## Deploy an application to the cluster

Create a new Deployment `hello-server` from the `hello-app` container image

```
kubectl create deployment hello-app --image=gcr.io/google-samples/hello-app:2.0
```

Create a Kubernetes Service

```
kubectl expose deployment hello-app --type=LoadBalancer --port 8081
```

In this command:

- `--port` specifies the port that the container exposes.
- `type="LoadBalancer"` creates a Compute Engine load balancer for your container.

Inspect the hello-server Service:

```
kubectl get service
```

View the application from your web browser

```
http://[EXTERNAL-IP]:8080
```

## Deleting the cluster

```
gcloud container clusters delete [CLUSTER-NAME]
```

## Set the default region and zone for all resources

In Cloud Shell, set the default zone

```
gcloud config set compute/zone us-central1-a
```

Set the default region:

```
gcloud config set compute/region us-central1
```

## Create web server instances

Create three new virtual machines in your default zone and give them all the same tag

```
gcloud compute instances create www2 \
  --image-family debian-9 \
  --image-project debian-cloud \
  --zone us-central1-a \
  --tags network-lb-tag \
  --metadata startup-script="#! /bin/bash
    apt-get update
	apt-get install -y nginx
	service nginx start
	sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html"
```

Create a firewall rule to allow external traffic to the VM instances: 

```
gcloud compute firewall-rules create accept-tcp-rule-673 \
    --target-tags network-lb-tag --allow tcp:80
```

Run the following to list your instances.

```
gcloud compute instances list
```

## Configure the load balancing service

Create a static external IP address for your load balancer

```
gcloud compute addresses create network-lb-ip-1 \
 --region us-central1
```

Add a legacy HTTP health check resource:

```
gcloud compute http-health-checks create basic-check
```

Add a target pool in the same region as your instances. Run the following to create the target pool and use the health check, which is required for the service to function:

```
gcloud compute target-pools create www-pool \
    --region us-central1 --http-health-check basic-check
```

Add the instances to the pool:

```
gcloud compute target-pools add-instances www-pool \
    --instances www1,www2
```

Add a forwarding rule:

```
gcloud compute forwarding-rules create accept-tcp-rule-673 \
    --region us-central1 \
    --ports 80 \
    --address network-lb-ip-1 \
    --target-pool www-pool
```

## Sending traffic to your instances

Enter the following command to view the external IP address of the www-rule forwarding rule used by the load balancer:

```
gcloud compute forwarding-rules describe accept-tcp-rule-673 --region us-central1
```

## Create an HTTP load balancer

1. First, create the load balancer template:

```
gcloud compute instance-templates create lb-backend-template \
   --region=us-central1 \
   --network=default \
   --subnet=default \
   --tags=allow-health-check \
   --image-family=debian-9 \
   --image-project=debian-cloud \
   --metadata=startup-script='#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- \'s/nginx/Google Cloud Platform - \'"\$HOSTNAME"\'/' /var/www/html/index.nginx-debian.html
'
```

2. Create a managed instance group based on the template:

```
gcloud compute instance-groups managed create lb-backend-group \
   --template=lb-backend-template --size=2 --zone=us-central1-a
```

3. Create the `fw-allow-health-check` firewall rule. This is an ingress rule that allows traffic from the Google Cloud health checking systems (`130.211.0.0/22` and `35.191.0.0/16`). This lab uses the target tag `allow-health-check` to identify the VMs.

```
gcloud compute firewall-rules create fw-allow-health-check \
    --network=default \
    --action=allow \
    --direction=ingress \
    --source-ranges=130.211.0.0/22,35.191.0.0/16 \
    --target-tags=allow-health-check \
    --rules=tcp:80
```

4. Now that the instances are up and running, set up a global static external IP address that your customers use to reach your load balancer.

```
gcloud compute addresses create lb-ipv4-1 \
    --ip-version=IPV4 \
    --global
```

Note the IPv4 address that was reserved:

```
gcloud compute addresses describe lb-ipv4-1 \
    --format="get(address)" \
    --global
```

5. Create a health check for the load balancer:

```
gcloud compute health-checks create http http-basic-check \
    --port 80
```

6. Create a backend service:

```
gcloud compute backend-services create web-backend-service \
    --protocol=HTTP \
    --port-name=http \
    --health-checks=http-basic-check \
    --global
```

7. Add your instance group as the backend to the backend service:

```
gcloud compute backend-services add-backend web-backend-service \
    --instance-group=lb-backend-group \
    --instance-group-zone=us-central1-a \
    --global
```

8. Create a URL map to route the incoming requests to the default backend service:

```
gcloud compute url-maps create web-map-http \
    --default-service web-backend-service
```

9. Create a target HTTP proxy to route requests to your URL map:

```
gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map-http
```

10. Create a global forwarding rule to route incoming requests to the proxy:

```
gcloud compute forwarding-rules create http-content-rule \
    --address=lb-ipv4-1\
    --global \
    --target-http-proxy=http-lb-proxy \
    --ports=80
```

## Create and Manage Cloud Resources Badgets

### Creating multiple web server instances using instance template.

1. In Cloud Shell, create a startup script to be used by every virtual machine instance

```
#! /bin/bash 
apt-get update
 apt-get install -y nginx
 service nginx start 
sed -i — 's/nginx/Google Cloud Platform — '"\$HOSTNAME"'/'' /var/www/html/index.nginx-debian.html 
EOF
```

2. Create an instance template, which uses the startup script:

```
gcloud compute instance-templates create nginx-template --metadata-from-file startup-script=startup.sh
```

### Access your web servers using their external IP address

1. You need to create a target pool. A target pool allows a single access point to all the instances in a group and is necessary for load balancing

```
gcloud compute target-pools create nginx-pool
```

2. Now create a managed instance group using the instance template we created and add them to the target pool.

```
gcloud compute instance-groups managed create nginx-group -- base-instance-name nginx -- size 2 -- template nginx-template --target-pool nginx-pool
```

3. Now configure a firewall so that you can connect to the machines on port 80 via the EXTERNAL_IP addresses:

```
gcloud compute firewall-rules create www-firewall --allow tcp:80
```

### Create a Network Load Balancer

1. Create an L3 network load balancer targeting your instance group:

```
gcloud compute forwarding-rules create nginx-lb --region us-central1 --ports=80 --target-pool nginx-pool
```

2. List all Google Compute Engine forwarding rules in your project.

```
gcloud compute forwarding-rules list
```

### Create a HTTP(s) Load Balancer ( Application Load Balancer )

1. Create a health check. Health checks verify that the instance is responding to HTTP or HTTPS traffic:

```
gcloud compute http-health-checks create http-basic-check
```

Define an HTTP service and map a port name to the relevant port for the instance group. Now the load balancing service can forward traffic to the named port:

```
gcloud compute instance-groups managed set-named-ports nginx-group --named-ports http:80
```

2. Create a backend service:

```
gcloud compute backend-services create nginx-backend --protocol HTTP --http-health-checks http-basic-check --global
```

3. Add the instance group into the backend service:

```
gcloud compute backend-services add-backend nginx-backend --instance-group nginx-group --instance-group-zone us-central1-a --global
```

4. Create a default URL map that directs all incoming requests to all your instances:

```
gcloud compute url-maps create web-map --default-service nginx-backend
```

5. Create a target HTTP proxy to route requests to your URL map:

```
gcloud compute target-http-proxies create http-lb-proxy --url-map web-map
```

6. Create a global forwarding rule to handle and route incoming requests. A forwarding rule sends traffic to a specific target HTTP or HTTPS proxy depending on the IP address, IP protocol, and port specified. The global forwarding rule does not support multiple ports.

```
gcloud compute forwarding-rules create http-content-rule --global --target-http-proxy http-lb-proxy --ports 80
```

7. List the forwarding rules

```
gcloud compute forwarding-rules list
```

## CLI/SDK (Storage data - GS Bucket)

Upload file to bucket

```
gsutil cp ada.jpg gs://YOUR-BUCKET-NAME
```

Download

```
gsutil cp -r gs://YOUR-BUCKET-NAME/ada.jpg .
```

Copy 

```
gsutil cp gs://YOUR-BUCKET-NAME/ada.jpg gs://YOUR-BUCKET-NAME/image-folder/
```

List contents of a bucket or folder

```
gsutil ls gs://YOUR-BUCKET-NAME
```

List details for an object

```
gsutil ls -l gs://YOUR-BUCKET-NAME/ada.jpg
```

Make your object publicly accessible

```
gsutil acl ch -u AllUsers:R gs://YOUR-BUCKET-NAME/ada.jpg
```

Remove public access

```
gsutil acl ch -d AllUsers gs://YOUR-BUCKET-NAME/ada.jpg
```

Delete objects

```
gsutil rm gs://YOUR-BUCKET-NAME/ada.jpg
```

## Cloud SQL

Connect

```
gcloud sql connect myinstance --user=root
```

## Cloud Endpoints

### Deploying the Endpoints configuration

- To publish a REST API to Endpoints, an OpenAPI configuration file that describes the API is required. The lab's sample API comes with a pre-configured OpenAPI file called `openapi.yaml`.

- Endpoints uses Google `Service Management`, an infrastructure service of Google Cloud, to create and manage APIs and services. To use Endpoints to manage an API, you deploy the API's OpenAPI configuration to Service Management.

The script then deploys the OpenAPI configuration to Service Management using the command: 
```
gcloud endpoints services deploy openapi.yaml
```

Content file `openapi.yaml`

```
# Copyright 2017 Google Inc. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
---
swagger: "2.0"
info:
  title: "Airport Codes"
  description: "Get the name of an airport from its three-letter IATA code."
  version: "1.0.0"
# This field will be replaced by the deploy_api.sh script.
host: "YOUR-PROJECT-ID.appspot.com"
schemes:
  - "https"
paths:
  "/airportName":
    get:
      description: "Get the airport name for a given IATA code."
      operationId: "airportName"
      parameters:
        -
          name: iataCode
          in: query
          required: true
          type: string
      responses:
        200:
          description: "Success."
          schema:
            type: string
        400:
          description: "The IATA code is invalid or missing."
```

### Deploying the API backend

```
gcloud app create --region="$REGION"
```

### Tracking API activity

In the Console, click on **Endpoints** in the **Tools** section to look at the activity graphs for your API. It may take a few moments for the requests to be reflected in the graphs. You can do this while you wait for data to be displayed:

- If the **Permissions** side panel is not open, click +Permissions. The Permissions panel allows you to control who has access to your API and the level of access.

- Click the **Deployment history** tab. This tab displays a history of your API deployments, including the deployment time and who deployed the change.

- Click the **Overview** tab. Here you'll see the traffic coming in. After the traffic generation script has been running for a minute, scroll down to see the three lines on the Total latency graph (50th, 95th, and 98th percentiles). This data provides a quick estimate of response times.

At the bottom of the Endpoints graphs, under Method, click the **View all logs** link for GET/airportName. The Logs Viewer page displays the request logs for the API.

### Add a quota to the API

1. Deploy the Endpoints configuration that 

```
gcloud endpoints services deploy openapi_with_ratelimit.yaml
```

```
# Copyright 2017 Google Inc. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
---
swagger: "2.0"
info:
  title: "Airport Codes"
  description: "Get the name of an airport from its three-letter IATA code."
  version: "1.0.0"
security:
  - api_key: []
# This field will be replaced by the deploy_api.sh script.
host: "YOUR-PROJECT-ID.appspot.com"
schemes:
  - "https"
paths:
  "/airportName":
    get:
      description: "Get the airport name for a given IATA code."
      operationId: "airportName"
      x-google-quota:
        metricCosts:
          airport_requests: 1
      parameters:
        -
          name: iataCode
          in: query
          required: true
          type: string
      responses:
        200:
          description: "Success."
          schema:
            type: string
        400:
          description: "The IATA code is invalid or missing."
securityDefinitions:
  # Basic authentication with an API key.
  api_key:
    type: "apiKey"
    name: "key"
    in: "query"

x-google-management:
  metrics:
    - name: airport_requests
      valueType: INT64
      metricKind: DELTA
  quota:
    limits:
      - name: limit-on-airport-requests
        values:
          STANDARD: 5
        unit: "1/min/{project}"
        metric: airport_requests
```

2. Redeploy your app to use the new Endpoints configuration

```
gcloud app create --region="$REGION"
```

3. In the Console, navigate to **Navigation menu** > **API & Services** > **Credentials**.

4. Click **Create credentials** and choose **API key**. A new API key is displayed on the screen.

5. Click the double rectangle icon to copy it to your clipboard.

6. In Cloud Shell, type the following. Replace YOUR-API-KEY with the API key you just created:

```
export API_KEY=YOUR-API-KEY
```

7. Send your API a request using the API key variable you just created:

```
curl -H 'x-api-key: AIzeSyDbdQdaSdhPMdiAuddd_FALbY7JevoMzAB' "https://example-project.appspot.com/airportName?iataCode=SFO"
San Francisco International Airport
```

## Cloud Pub/Sub

Create a Pub/Sub topic

```
gcloud pubsub topics create myTopic
```

Create a virtual environment

```
apt-get install -y virtualenv
python3 -m venv venv
source venv/bin/activate
```

Install the client library

```
pip install --upgrade google-cloud-pubsub
git clone https://github.com/googleapis/python-pubsub.git
cd python-pubsub/samples/snippets
```

Run the publisher script to create Pub/Sub Topic:

```
python publisher.py $GOOGLE_CLOUD_PROJECT create MyTopic
```

This command returns a list of all Pub/Sub topics in a given project:

```
python publisher.py $GOOGLE_CLOUD_PROJECT list
```

You can also view the topic you just made in the Cloud Console. Navigate to **Navigation menu** > **Pub/Sub** > **Topics**.

Create a Pub/Sub subscription for topic with subscriber.py script

```
python subscriber.py $GOOGLE_CLOUD_PROJECT create MyTopic MySub
```

This command returns a list of subscribers in given project:

```
python subscriber.py $GOOGLE_CLOUD_PROJECT list-in-project
```

Publish the message "Hello" to MyTopic:

```
gcloud pubsub topics publish MyTopic --message "Hello"
```

View messages

```
python subscriber.py $GOOGLE_CLOUD_PROJECT receive MySub
```

Click Ctrl+c to stop listening.

## Identity-Aware Proxy

### Deploy to App Engine

```
gcloud app deploy
gcloud app browse
```

### Restrict Access with IAP

1. In the cloud console window, click the **Navigation menu** > **Security** > **Identity-Aware Proxy**.

2. Click **ENABLE API**.

3. Click **GO TO IDENTITY-AWARE PROXY**.

4. Click **CONFIGURE CONSENT SCREEN**.

5. Select **Internal** under User Type and click **Create**.

6. Fill in the required blanks

7. Click **SAVE AND CONTINUE**.

8. For **Scope**s, click **SAVE AND CONTINUE**.

9. For **Summary**, click **BACK TO DASHBOARD**.

10. In Cloud Shell, run this command to disable the Flex API:

```
gcloud services disable appengineflex.googleapis.com
```

11. Return to the Identity-Aware Proxy page and refresh it. You should now see a list of resources you can protect. Click the toggle button in the IAP column in the App Engine app row to turn IAP on.

12. The domain will be protected by IAP. Click TURN ON.

### Add role user

1. In Identity-Aware Proxy page of the console, select the checkbox next to **App Engine app**, and see the sidebar at the right of the page.

2. Click **ADD PRINCIPAL**.

3. Enter your email address.

4. Then, pick the Cloud IAP/IAP-Secured Web App User role to assign to that address. You may enter more addresses or GSuite domains in the same way.

5. Click **SAVE**.

## Google Kubernetes Engine (GKE)

Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend and configure it to run 2 node:

```
gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2
```

After the cluster is created, check your installed version of Kubernetes using the kubectl version command

```
kubectl version
```

Launch a single instance of the nginx container

```
kubectl create deploy nginx --image=nginx:1.17.10
```

View the pod running the nginx container

```
kubectl get pods
```

Expose the nginx container to the Internet:

```
kubectl expose deployment nginx --port 80 --type LoadBalancer
```

View the new service:

```
kubectl get services
```

Scale up the number of pods running on your service

```
kubectl scale deployment nginx --replicas 3
```

Confirm that Kubernetes has updated the number of pods:

```
kubectl get pods
```

Confirm that your external IP address has not changed:

```
kubectl get services
```

## Configuring IAM

### Create role CLI

1. Create a new file called role.yaml, run the following command:

```
nano role.yaml
```

2. Copy the following role definition and paste into the nano editor in Cloud Shell:

```
title: App Viewer
description: Custom role to view apps
stage: ALPHA
includedPermissions:
- compute.instances.get
- compute.instances.list
- appengine.versions.get
- appengine.versions.list
```

3. Create a new role, run the following command:

```
gcloud iam roles create app_viewer --project \
$DEVSHELL_PROJECT_ID --file role.yaml
```

4. List all the custom roles in your project, run the following command:

```
gcloud iam roles list --project $DEVSHELL_PROJECT_ID
```

### Use a custom role

1. On the **Navigation menu**, click **IAM & admin** > **IAM**.
2. In the IAM console, locate the line for Username and click the `Edit principal` button.
3. Click **Add another role**, and then click **Select a role**.
4. In the **Custom category**, select one of the roles you just created.
5. Click Save.

### Modify role permissions

1. To retrieve the `app_viewer` role's definition, run the following command in Cloud Shell:

```
gcloud iam roles describe app_viewer --project \
$DEVSHELL_PROJECT_ID
```

2. Run the following command to create a new YAML file:

```
nano update-role.yaml
```

3. Paste the output from the describe command into the nano editor.
4. Add the permission just below the `includedPermissions:` lines.
5. To update the `app_viewer` role, run the following command:

```
gcloud iam roles update app_viewer --project \
$DEVSHELL_PROJECT_ID --file update-role.yaml
```

### Disable a role

To disable the app_viewer role created earlier in this lab, run the following command in Cloud Shell:

```
gcloud iam roles update app_viewer --project \
$DEVSHELL_PROJECT_ID --stage DISABLED
```

### Delete a role

1. To delete the app_viewer role created earlier in this lab, run the following command in Cloud Shell:

```
gcloud iam roles delete app_viewer --project \
$DEVSHELL_PROJECT_ID
```

2. To list all the roles in the project, run the following command:

```
gcloud iam roles list --project $DEVSHELL_PROJECT_ID
```

3. To list all the roles in the project including the deleted roles, run the following command:

```
gcloud iam roles list --project $DEVSHELL_PROJECT_ID \
--show-deleted
```

### Un-delete a role

Role can be un-deleted within 7 days of being deleted. To un-delete the app_viewer role, run the following command in Cloud Shell:

```
gcloud iam roles undelete app_viewer --project \
$DEVSHELL_PROJECT_ID
```
