# STAGE 4 - CODE DEPLOY

In this stage, you will configure automated deployment of the cat pipeline application to ECS Fargate

## Configure a load balancer

First, you're going to create a load balancer which will be the entry point for the containerised application

Go to the EC2 Console (https://us-east-1.console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:) then Load Balancing -> Load Balancers -> Create Load Balancer.  
Create an application load balancer.  
Call it `catpipeline`  
Internet Facing
IPv4
For network, select your default VPC and pick ALL subnets in the VPC.  
Create a new security group (this will open a new tab)
Call it `catpipeline-SG` and put the same for description
Delect the default VPC in the list
Add an inbound rule, select HTTP and for the source IP address choose 0.0.0.0/0
Create the security group.  

Return to the original tab, click the refresh icon next to the security group dropdown, and select `catpinepine-SG from the list` and remove the default security group.  

Under `listners and routing` make sure HTTP:80 is configured for the listner.  
Create a target group, this will open a new tab
call it `catpipelineA-TG`, ensure that `IP`, HTTP:80, HTTP1 and the default VPC are selected.  
Click next and then create the target group, for now we wont register any targets.  
Return to the original tab, hit the refresh icon next to target group and pick `catpipelineA-TG` from the list.  
Then create the load balancer. 
This will take a few minutes to create, but you can continue on with the next part while this is creatign.


## Configure a Fargate cluster

Move to the ECS console (https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#/getStarted)
Clusters, Create a Cluster.    
Move on, and name the cluster `allthecatapps`
We will be using the default VPC so make sure it's selected and that all subnets in the VPC are listed.  
Create the cluster. 

## Create Task and Container Definitions

Go to the ECS Cluster (https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#/clusters)  
Move to `Task Definitions` and create a task definition.   
Call it `catpipelinedemo`.  
In `Container Details`, `Name` put `catpipeline`, then in `Image URI` move back to the ECR console and clikc `Copy URI` next to the latest image.  
Scroll to the bottom and click `Next`  

and for `operating system family` put `Linux/X86_64`   
Pick `0.5vCPU` for task CPU and `1GB` for task memory.   
Select `ecsTaskExecutionRole` under task role and task execution role.  
Click `Next` and then `Create`.  

It should look like this 

<img width="940" height="531" alt="image" src="https://github.com/user-attachments/assets/4ba6e2a5-7ab4-4fcc-a454-084d054e2e86" />


## DEPLOY TO ECS - CREATE A SERVICE
Click Deploy then `Create Service`.
for `Launch type` pick `FARGATE`
for `Service Name` pick `catpipelineservice`  
for `Desired Tasks` pick 2
Expand `Deployment Options`, then for `Deployment type` pick `rolling update`
Expand `Networking`.  
for `VPC` pick the default VPC
for `Subnets` make sure all subnets are selected.  
for `Security Group`, choose `User Existing Security Group` and ensure that `Default` and `catpipeline-SG` are selected.  
for `public IP` choose `Turned On`  
for `Load Balancer Type` pick `Application Load Balancer`
for `Load balancer name` pick `catpipeline`  
for `container to load balance` select 'catpipeline:80:80'  
select `Use an existing Listener` select `80:HTTP` from the dropdown
for choose `Use an existing target group` and for `Target group name` pick `catpipelineA-TG` 
Expand `Service auto scaling` and make sure it's **not** selected.  
Click `create`  
Wait for the service to finished deploying.  

The service is now running with the :latest version of the container on ECR, this was done using a manual deployment

## TEST

Move to the load balancer console (https://us-east-1.console.aws.amazon.com/ec2/v2/home?region=us-east-1#LoadBalancers)  
Pick the `catpipeline` load balancer  
Copy the `DNS name` into your clipboard  
Open it in a browser, ensuring it is using http:// not https://  
You should see the container of cats website - if it fits, i sits.. in a container.

Like so

<img width="940" height="522" alt="image" src="https://github.com/user-attachments/assets/16d8a182-91ea-4fc5-84b7-86cd2cd5df83" />




## ADD A DEPLOY STAGE TO THE PIPELINE

Move to the code pineline console (https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=us-east-1)
Click `catpipeline` then `edit`
Click `+ Add Stage`  
Call it `Deploy` then Add stage  
Click `+ Add Action Group`  
for `Action name` put `Deploy`  
for `Action Provider` put `Amazon ECS`  
for `Region` pick `US East (N.Virginia)`  
for `Input artifacts` select `Build Artifact`  (this will be the `imagedefinitions.json` info about the container)  
for `Cluster Name` pick `allthecatapps`  
for `Service Name` pick `catpipelineservice`  
for `Image Definitions file` put `imagedefinitions.json`  
Click Done
Click Save & Confirm

Once the deploy stage is created, it should look like this

<img width="940" height="425" alt="image" src="https://github.com/user-attachments/assets/4e0a888d-17aa-43e4-9fe5-7df0558fceda" />

When creating the deploy stage I received this error:

<img width="741" height="311" alt="image" src="https://github.com/user-attachments/assets/06d58e09-afe5-4f74-acef-10e27c1bb182" />

In this case, I recreated the deploy stage after the build stage:

<img width="940" height="330" alt="image" src="https://github.com/user-attachments/assets/65b03b3e-c424-46f9-98ac-13065cb8250a" />

I then recedived this error:

<img width="698" height="309" alt="image" src="https://github.com/user-attachments/assets/13c43a66-0551-4a6c-8b9c-41da43d9447f" />

Checking the codebuild, I found syntax errors back on stage 3.

<img width="940" height="97" alt="image" src="https://github.com/user-attachments/assets/d278023e-c268-4eb3-90b6-5e84ede1dec9" />

I then went back and edited the code to create the file (changing code editor):

That then worked, as Buildartif then worked<img width="940" height="76" alt="image" src="https://github.com/user-attachments/assets/769bf10f-bead-44b0-ab3e-a28559e98999" />

 but I then got a different error:

<img width="940" height="197" alt="image" src="https://github.com/user-attachments/assets/247f758b-907b-4166-a2ca-75ad5cf6163a" />

This one required me editing the current IAM role, this was the added code:

<img width="736" height="254" alt="image" src="https://github.com/user-attachments/assets/19909550-d4d8-45da-990e-279b27851aa4" />



## TEST

in the local repo edit the `index.html` file and add ` - WITH AUTOMATION` to the `h1` line text.  Save the file.  
then run

```
git add -A .
git commit -m "test pipeline"
git push
```
  
watch the code pipeline console (https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines/catpipeline/view?region=us-east-1)

make sure each pipeline step completes

Go back to the tab with the application open via the load balancer, refresh and see how the text has changed.  



