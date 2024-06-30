
## Implementing Auto Scaling

Auto scaling in Google Cloud Platform (GCP) refers to the ability to automatically adjust the number of compute resources allocated to your application based on demand. This ensures that your application has the necessary resources to handle current traffic while minimizing costs by not over-provisioning.
## Key Features of Auto Scaling in GCP

1. Automatic Adjustment:
  a. Scale Up: Increase the number of instances or resources when demand increases.

b. Scale Down: Decrease the number of instances or resources when demand decreases, helping to save costs.

2. Customizable Policies:
 You can define custom policies to control how and when the scaling occurs. These policies can be based on various metrics such as CPU utilization, memory usage, or custom metrics from Stackdriver (now part of Google Cloud's Operations Suite).

3. Load Balancing Integration:

Auto scaling works seamlessly with GCP’s load balancing services, ensuring that traffic is efficiently distributed across instances.

4. Instance Groups:

Managed instance groups (MIGs) are often used with auto scaling. MIGs allow you to deploy and manage identical VM instances as a single entity. Auto scaling can then adjust the number of VMs in the group according to the defined policies.

5. Health Checks:

Auto scaling uses health checks to ensure that instances are healthy and capable of handling traffic. Unhealthy instances can be automatically replaced.


## Types of Auto Scaling in GCP

1. Horizontal Pod Autoscaling (HPA) for Kubernetes Engine (GKE): 

  Automatically scales the number of pods in a Kubernetes cluster based on CPU utilization or other selected metrics.

2. Instance Group Auto Scaling:
  
  Automatically scales the number of VM instances in a managed instance group based on metrics such as average CPU utilization, HTTP load balancing serving capacity, or Stackdriver monitoring metrics.



## How Auto Scaling Works

1. Define Auto Scaling Policy:

Create an auto scaling policy that specifies the conditions under which scaling should occur. For example, scale out when CPU usage exceeds 60% and scale in when it drops below 30%.

2. Monitor Metrics:

 Auto scaling continuously monitors the specified metrics. If the metrics exceed or fall below the thresholds defined in the policy, scaling actions are triggered.

3. Adjust Resources:

 Depending on the policy, new instances are added (scaled out) or existing instances are terminated (scaled in) to match the current demand.
## Steps for Implementing auto scaling

Step 1: Set Up Your GCP Environment

1. Create a GCP Project:

a. Go to the Google Cloud Console.

b. Create a new project or select an existing one.

2. Enable Required APIs:

a. Navigate to APIs & Services > Library.

b. Enable the Compute Engine API.

3. Create a Service Account:

a. Navigate to IAM & Admin > Service Accounts.

b. Create a new service account with the Compute Admin role.

c. Download the JSON key file for the service account.

4. Set Up Authentication:

Set the environment variable for the Google Cloud credentials:



    export GOOGLE_CLOUD_KEYFILE_JSON=/path/to/your-service-account-key.json

Step 2: Install Terraform
Download and install Terraform from the 

    official website.

step 3: Write Terraform Configuration Files



       resource "google_compute_autoscaler" "my_autoscaler" {

       name  = "my-autoscaler"

       zone  = "us-central1-b"

       target = google_compute_instance_group_manager.default.id

        autoscaling_policy {

       max_replicas = 5

       min_replicas = 1

         cooldown_period = 60


       metric {

       name = "pubsub.googleapis.com/subscription/num_undelivered_messages"

       filter = "resource.type = pubsub_subscription AND resource.label.  subscription_id = our-subscription"

       single_instance_assignment = 65535

         }

        }

       }



       resource "google_compute_instance_template" "default" {

       name         = "my-instance-template"

       machine_type = "e2-medium"

  

       resource "google_compute_instance_group_manager" "default" {

       name = "my-igm"

       zone = "us-central1-b"


       version {

       instance_template = google_compute_instance_template.

       default.id

       name              = "primary"

       }

       target_pools = [google_compute_target_pool.default.id]
 
       base_instance_name = "autoscaler-sample"

        }

       resource "google_compute_target_pool" "default" {


       name = "my-target-pool"

       }

       data "google_compute_image" "debian_9" {

       family  = "debian-11"

       project = "debian-cloud"

       }

       provider "google-beta" {

       region = "us-central1"

       zone   = "us-central1-a"

       }



step 4: Initialize and Apply the Configuration
Initialize Terraform:



    terraform init

Plan the Configuration:



    terraform plan
Apply the Configuration:


    terraform apply
## Useful Resources
    1. Google Cloud Storage Documentation​ (HashiCorp Developer)​

    2. Terraform GCP Provider Documentation​ (HashiCorp Developer)​

    3. HashiCorp Developer Tutorials​ (HashiCorp Developer)
