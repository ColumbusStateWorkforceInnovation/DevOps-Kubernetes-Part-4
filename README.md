# Infrastructure Automation Kubernetes Lab 6/7

Examples for this week's content can be found [here](https://github.com/ColumbusStateWorkforceInnovation/infrastructure-kubernetes-week3-examples) and [here](https://github.com/ColumbusStateWorkforceInnovation/infrastructure-kubernetes-week4-examples)

## Objectives

In this lab you are first tasked with creating a running [WordPress][wordpress] application.

You will use all what we have learned in the previous weeks to create a wordpress application that is backed by a secure mysql instance with a persistent volume, and replicated

---
## Getting Started and Lab info:

1 - Copy the starter code from here into a new, private repository in your personal GitHub account. When adding a collaborator, be sure to add the instructor ("CSCC-Instructor").

Make sure to have gone through the material for helm (lectures/slides) before starting the lab.


The WordPress application we are going to deploy has two major parts: A MySQL database that will be used to store persistent data and the actual WordPress container.

For this lab you should have multiple (6) manifest files that fulfill the below requirements.

### *Tips*
- Use the previous examples and online resources
- You will need make use of labels and selectors
- Some components might have to be created before you can create ones that reference it.
- The requirments are not in order.
- you will use selectors heavily.
  - *hint* use a selector for the app named maybe 'wordpress' and another selector for the component the resource is for
- Using the dry run command we learned before can help you figure out what you need to do.
---

## Understanding the Starter Code

In the `manifests` directory you will find the manifest files you will have to update.

1 - dep-wordpress.yml (deployment)
2 - pvc-wordpress.yml (persistent volume claim for app)
3 - secret-mysql.yml (secret for mysql)
4 - sts-mysql.yml (mysql stateful set)
5 - svc-wordpress.yml (service for app)
6 - svc-mysql.yml (service for mysql)


- Each yml is partially complete, you will need to either fill in values, or add additional values/keys to certain sections.
- there are requirements listed in the next section for each component of the application

## Requirements
---

### Secret
- For the mysql database password, create an `opaque` Secret for the database to use.
- the key and value you use here will be used in both the mysql and wordpress components

---
### Stateful MySQL Database and Service

#### Database
- For the actual database we want a `StatefulSet`
- Use *1* replica
- use the image `mysql:5.6`
- Should have a volume mount using volume claim
- 'standard' storage class with 'ReadWriteOnce' access
- This StatefulSet will need to use the MYSQL_ROOT_PASSWORD environment variable
  - *hint* use secretKeyRef
    

#### Service
- Create a `Service` for the database
- Should exposed only to the wordpress instance
  
*tips*
- We do not need the PVC to be created ahead of time for its storage. Use the volumeClaimTemplates feature of the StatefulSet


### WordPress PVC

- For the wordpress to be able to write data it needs a PVC.
- Create a PVC with a 'ReadWriteMany' access and requested storage
- remember the `name` key under metadata of this pvc will be referenced by components using it in a volume 
- for resources, just use a single storage request of size `1Gi`
---
### Wordpress Deployment and Service
#### Deployment
- Here we want the actual wordpress instance as a `Deployment`
- *2* replicas using the `wordpress:4.9-apache` image
- will need a volumemount at `/var/www/html`
- this volume used for this volumemount should be the named persistent volume claim you made 
- the mysql instance will need two env variables
  - WORDPRESS_DB_HOST (the value of this should be 'mysql')
  - WORDPRESS_DB_PASSWORD (this this value of this should be the secret you created before) 
    - *hint* use secretKeyRef
  
#### Service
- Expose our wordpress instance using a `NodePort`

*tips*
- when everything is running use the `minikube service` command to access the deployment.

---


## Submitting Your Work

1-  Publish your repository as a private repo, and ensure that you have pushed the latest version

2-  Submit the assignment in Blackboard with the link to your repo


[wordpress]: https://wordpress.org/
