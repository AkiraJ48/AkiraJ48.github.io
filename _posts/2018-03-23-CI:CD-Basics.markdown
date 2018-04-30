---
layout: default
title:  "Setting up CI/CD"
date:   2018-03-23 11:07:02 +1100
categories: AWS
---

# [](#header-1) What are the following services?
1. IAM (Identity and Access Management)
2. Docker Registry
3. Travis
4. S3 buckets
5. CodeDeploy
6. ECR (Elastic Container Registry)

* * *

## [](#header-2) IAM: Users, Groups, Roles and Policies
Understanding how AWS delegates permissions and authentication to these 4 concepts
is pivotal in understanding why your CI/CD does not work.

***Policies:*** A policy contains a set of defined permissions/actions that are allowable (under certain conditions)
for a certain entity (in this case, the users, groups and roles). These permissions are
for AWS services and api's, for example defining whether we can read/write to AWS S3 buckets.
These policies are also applicable to the services as well. For example for a specific S3 bucket,
we have to identify who has permission to read and write to that specific S3 bucket.

***Users***: A user is an entity that is used to interact with AWS services. When you create a
user, you can define whether its part of a group, or what policies it contains. By adding those
policies, you give it those permissions that you've defined. To use said user, you have to make
use of its access/secret keys.

***Groups***: A group is simply a collection of users. You can define a set of policies for said group,
and as a result, each user within that group will similarly have those policies.

***Roles***: A role is very similar to a user, in that it is an entity that contains policies that
determines what it can/cannot do. The difference, is that a role does not contain access/secret keys.
The idea behind this, is that a role can be "used"/"assumable" by any individual who needs it.
A person can assume that given role to temporarily make use of a set of policies defined for that role.

_Temporary Credentials_: These are primarily used with roles. Instead of permanently giving credentials
to a role, we can request temporary credentials for the role. This is useful as we can restrict
what tasks are done.

Note: When you manually login to an EC2 instance (e.g. via ssh), you are under
the permissions of your AWS account in regards to what services you can access.
When you make use of another application to do something in that EC2 instance, for example
making use of CodeDeploy to execute a script within that instance, you are under the
permissions of your IAM role that has been assigned to that EC2 instance.

* * *

## [](#header-2) Docker Registry & ECR
***Docker Registry:*** A docker registry is basically a server side application that allows
you to store and distribute docker images. Using a registry allows you:
    1. tightly control where your images are being stored
    2. fully own your images distribution pipeline
    3. integrate image storage and distribution tightly into your in-house development workflow

Basically a Docker Registry is the equivalent of a Git Repository, you're able to create your own registry, push and pull to it etc. In this case DockerHub is very similar to what GitHub is.

***ECR (Elastic Container Registry):*** is essentially the AWS version of DockerHub, it is
simply a repository that can contain a registry of images, in which a user can manipulate
(i.e. push/pull).

* * *

## [](#header-2) S3 Buckets
***S3 Bucket:*** An S3 bucket is basically a storage unit for AWS, in which users can store data into them.
Buckets are also region allocated, that is you can create and store data in a bucket in which exists in
a different region (e.g. Europe). This can be really useful, for companies such as Netflix,
where they can push their movies to a bucket in each region (e.g. from America, push their movies
to a bucket that exists in Australia). This will reduce the cost of loading each movie from America when requested by someone in Australia.

Important:
If you access a bucket programmatically, note that Amazon S3 supports RESTful architecture in which your
buckets and objects are resources, each with a resource URI that uniquely identifies the resource.

* * *

## [](#header-2) CodeDeploy
***Code Deploy:*** Is an automatic deployment service from AWS, that basically allows users to
"deploy (push)" data from one platform to another. In my scenario, I used CodeDeploy to take content from
an S3 bucket, and deploy that data (and execute) onto an EC2 instance (as Travis doesn't have the
capability to directly deploy to a given EC2 instance).

* * *

## [](#header-2) Travis
***Travis:*** Travis  is a CI (Continuous Integration) service, that basically allows
its users to easily build, test and deploy their given code. In this case, travis allows us to
define how to build our code (e.g. can use docker), and then on what platforms we test our code against.
It then provides support for deployment services (such as CodeDeploy), which can handle automatic deployment to our production server. For example, for my project, travis would push our content to
the S3 bucket, and from there CodeDeploy can pick up that content,
and push it to our EC2 instance.

* * *

# [](#header-1) Setting up CI/CD w/ the following services:
1. IAM (Identity and Access Management)  
2. Docker Registry  
3. Travis  
4. S3 buckets  
5. CodeDeploy  
6. EC2  
7. ECR (Elastic Container Registry)  

* * *

## [](#header-2) Recommendations (Blogs to read through prior)
1. A good starting blog on how to deploy to an EC2 instance, making use of: Travis, IAM, EC2, S3 and CodeDeploy.  
[Deployment with Travis & EC2 Blog](https://medium.com/@itsdavidthai/comprehensive-aws-ec2-deployment-with-travisci-guide-7cafa9c754fc)  
I would recommend going through this, to get an understanding of how permissions are used for
AWS services, and how to actually write a simple travis build file that uses different services.

2. This is another good blog that details how to build a docker image and push that image to Amazons ECR.   
[Pushing to ECR with Docker](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html#use-ecr)   
However, this does not go into detail in regards to creating permissions for an entity for allowing access
to an ECR instance -- so if you do come across errors relating to authentication, look at whether you actually
have permission to push to that registry with your given credentials.

3. A good blog that describes how to orient your travis.yml file in regards to the build step.  
[Understanding the Travis lifecycle](https://docs.travis-ci.com/user/customizing-the-build)   
Recommend this in order to understand the build lifecycle.

4. A good blog detailing how access management works within AWS.    
[Understanding IAM ](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html)   

5. A good blog detailing how to authorize your AWS credentials for a given Amazon defined user.  
[Configuring AWS credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)   
Recommended for manual authorization of a users credentials, but doesn't reflect how to use secrets as a method
of authorization.

* * *

## [](#header-2) Setting up the necessities

* * *

### [](#header-3) Permissions with IAM
Firstly we have to set up all relevant entities and policies that are necessary
to interact with the following services: ECR, S3 and CodeDeploy.

***Policies:*** We will have 5 policies:
1. A policy, that has permission to put objects into our S3 bucket (to be used by Travis).   
2. A policy, that has permission to read objects from our S3 bucket (to be used by CodeDeploy).  
3. A policy, that has permission to create/use deployments from CodeDeploy (to be used by Travis).  
4. A policy, that has permission to push/pull from ECR (to be used by Travis and our EC2 Instance).  
    -> There is a default AWS policy that you can use, that allows for full permissions for ECR.   
5. A policy, that provides services to expand tags and interact with Auto-Scaling.
(It's a default AWS policy)

***Entities:*** There are 3 entities that we have to create:
1. A role that we will assign to our instance.
    -> This will contain policies 2 & 4.
2. A role that we will assign to our CodeDeploy application.
    -> This will contain policy 5.
3. A user, whose credentials we will use to push our docker image to ECR.
    -> This will contain policies 1, 3, & 4.

Note: for the policy associated with creating/using deployments for CodeDeploy,
we will have to assign the given CodeDeploy application instance with that
given policy.

Once all the relevant policies and entities are set up, we have to make use of them
in some manner. For our _EC2 Instance_ we will assign in our create "role" entity.

* * *

### [](#header-3) Creating AWS applications
Next we have to create all the relevant application services: EC2, ECR, S3, Travis and CodeDeploy.

***EC2:*** This should be fairy simple to create, all you should have to remember is to
assign the "role" entity that contains the necessary policies to it.

***ECR:*** When creating an ECR instance, you have to ensure that you add permissions
for both your "role" and "user" that you've created.

***S3:*** Creating a S3 bucket is fairly simple, when you access it, you will only
need the credentials of the user (who should have permission to access it) and
its bucket name (that you would define when creating it).

***Travis:*** Similarly fairly simple to create, you can follow a tutorial on how
to create it and link up your GitHub account to it.

***CodeDeploy:*** When creating a CodeDeploy application, ensure that your application
name for it, is the same as the name you've put in for your policy, which dictates
your authority in creating/deploying content. You will also have to add a key value
pair, which basically represent the name of your EC2 instance.  You will also assign
the CodeDeploy "role" that we've created as the Service Role ARN.

Once all services have been created, and we've added all necessary permissions,
then "hopefully" you shouldn't have to run into and authentication/credential problems
along the way.

* * *

### [](#header-3) Creating your travis.yml and appspec.yml files

***Travis.yml File:*** Your travis.yml file will contain certain necessary information, so that
travis will firstly be able to push your docker image to ECR, and then deploy the necessary
scripts to S3 and your EC2 instance to then pull that docker image and build it within your instance.
This information will contain the following:  
    1. Access/Secret Keys (that have been encrypted) for a user who has the necessary permissions
    to push to ECR and relevant S3 / CodeDeply functionality.   
    2. Docker Services     
    3. Building your docker image   
    4. Pushing the docker image to ECR  
    5. Deploying a script to your EC2 instance to then pull the docker image from ECR   

***Appspec.yml File:*** Your appspec.yml file will contain the necessary information involved in
the deployment process (via CodeDeploy). Essentially within the file, you will define within which
directory you are deploying your files, with what permissions they are being deployed with,
and which scripts you are then executing when they are deployed to the EC2 instance.

* * *

### [](#header-3) Pieces of advice
1. If you're presented with issues of authentication/credentials, take a look at the policies
you've designed and/or what policies you've assigned to which entities. Most likely, you have not
given the relevant policy to enable that entity to do something.
2. If applicable, run the given commands on a local machine or your EC2 instance instead
of running them within Travis' build pipeline -- otherwise you'll always be waiting a couple of minutes
(especially if you're building a docker image every time).

* * *
