# Infrastructure Automation Kubernetes Lab #3

## Resources

See [Jeff's class notes](https://github.com/jeff-anderson-cscc/intro-to-kubernetes) plus examples from previous cohorts [here](https://github.com/ColumbusStateWorkforceInnovation/infrastructure-kubernetes-week3-examples) and [here](https://github.com/ColumbusStateWorkforceInnovation/infrastructure-kubernetes-week4-examples).

## Objectives

You will use what we have learned in the previous weeks to create and run a [WordPress](https://hub.docker.com/_/wordpress) application for this lab.

## Getting Started and Lab info:

Copy the starter code from here into a new, __private__ repository in your personal GitHub account. When adding a collaborator, be sure to add the instructor ("CSCC-Instructor").

The WordPress application we will deploy has two major parts: A MySQL database used to store persistent data and the actual WordPress container.

You should have multiple (6) manifest files that fulfill the below requirements for this lab.

### *Tips*
- Use the previous examples and online resources.
- You will need to make use of labels and selectors.
- Some components might have to be created before you can create others that reference it.
- The requirements are not in order.


## Understanding the Starter Code

In the `manifests` directory, you will find the manifest files you must update.

- dep-wordpress.yml (deployment)
- pvc-wordpress.yml (persistent volume claim for the app)
- secret-mysql.yml (secret for MySQL)
- sts-mysql.yml (MySQL stateful set)
- svc-wordpress.yml (service for the app)
- svc-mysql.yml (service for MySQL)

Each yml is partially complete, so you will need to either fill in values or add additional values/keys to specific sections. The following section lists the requirements for each component of the application.

---

## Completing the Lab

### For Every Resource

Use the [Recommended Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/) for:
 - The name of the application.
 - The component within the architecture.

### Secret

In [secret-mysql.yml](./manifests/secret-mysql.yml):
- Label it as described above.
- Make it an `opaque` secret.
- Use `supersecretpassw0rd` for the `password`.

### MySQL Database

In [sts-mysql.yml](./manifests/sts-mysql.yml):
- Label it as described above.
- Create **1** replica.
- Use the `mysql:5.6` image.
- For the `MYSQL_ROOT_PASSWORD` environment variable, get the password from the secret you created above.
- Mount the `mysql-data` volume claim with path of `/var/lib/mysql`.
- For `volumeClaimTemplates`:
 - Use the "ReadWriteOnce" access mode.
 - Use the `standard` storage class.
 - Request `1Gi` of space.

### MySQL Database Service

In [svc-mysql.yml](./manifests/svc-mysql.yml):
- Label it as described above.
- Use `port` 3306.
- Set the selector to match resources from the stateful set.
- Make it a [Headless Service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services).

### Persistent Volume Claim for the WordPress Application

In [pvc-wordpress.yml](./manifests/pvc-wordpress.yml):
- Label it as described above.
- Use the "ReadWriteMany" access mode.
- Request `1Gi` of space.

### WordPress Deployment

In [dep-wordpress.yml](./manifests/dep-wordpress.yml):
- Label it as described above.
- Create **2** replicas.
- Use the [update strategy](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy) where all existing Pods are killed before new ones are created.
- Use the `wordpress:4.9-apache` image.
- For the `WORDPRESS_DB_HOST` environment variable, set the value to `mysql`.
- For the `WORDPRESS_DB_PASSWORD` environment variable, get the password from the secret you created above.
- Use 80 for the container port.
- Mount the `wordpress` volume with a path of `/var/www/html`
Use the persistent volume claim you created in the previous step for the volume.


### WordPress Service

In [svc-wordpress.yml](./manifests/svc-wordpress.yml):
- Label it as described above.
- Expose the application via a `NodePort` of 30180.
- Set the `port` to 80 so it routes back to the webserver.
- Use selectors that match the WordPress deployment you created above.

## Checking your work

Use the `minikube service` command to access the deployment when everything is running. First, you should see the WordPress setup screens. Next, go through the setup process. Once you have completed the setup, log in and make sure you get to the WordPress dashboard.

**TIP:** Once you login to WordPress, the service will bounce you from pod to pod and you may see the login screen multiple times. To prevent this, update [svc-wordpress.yml](./manifests/svc-wordpress.yml) to have a `sessionAffinity` of `ClientIP`. Once you add this property, reapply the file to reconfigure the service.

__Don't forget to delete everything including the PVC and secret once you are done testing and are ready to submit your work.__

---

## Submitting Your Work

1. From the command line at the root directory of your project, execute the following command: 
 ```
 git bundle create <firstname>-<lastname>-infrastructure-kubernetes-part3-lab-<date>.bundle --all
 ```
 __NOTE:__ firstname and lastname are your first and last names, and date is in the format of mmddyyyy
 
1. Submit your lab solution in Blackboard with the link to your Pull Request as well as the bundle file you just created.