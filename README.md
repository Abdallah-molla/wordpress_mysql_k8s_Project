### wordpress_mysql_k8s_Project
![Screenshot (1647)](https://github.com/user-attachments/assets/c4df9ca8-7023-4c1d-b1ab-7a44ace681d1)
![Screenshot (1648)](https://github.com/user-attachments/assets/117aebe1-c1fb-47e8-a3c3-6cd77faa29a3)
###Project Describtion:
WordPress with MySQL on AWS Using Kubernetes

This guide demonstrates how to deploy a WordPress site with a MySQL database on AWS using Kubernetes. The architecture leverages EC2 instances, EBS for MySQL persistence, EFS for shared storage for WordPress, and an Application Load Balancer (ALB) for high availability.
Prerequisites
    AWS account with appropriate permissions.
    Kubernetes cluster (can be set up using EKS or manually).
    kubectl configured to interact with the cluster.
Project Overview
    MySQL Database: Deployed with a single pod and persistent storage provided by EBS.
    WordPress Site: Deployed with shared storage provided by EFS, allowing multiple pods to access the data.
    High Availability: An Application Load Balancer (ALB) is used to distribute traffic to WordPress pods.
Steps
1. Set Up MySQL Database
    Create MySQL Secret:
        First, create an IAM user with the necessary permissions for EBS and EFS.
        Next, create a role that includes permissions for EBS and EFS, then attach this role to all nodes in the Kubernetes cluster.
        Store the IAM access key and secret access key in a Kubernetes secret for MySQL. This will allow your MySQL pod to authenticate and access AWS resources.
![Screenshot (1652)](https://github.com/user-attachments/assets/0ed62ddb-88d9-40b4-86aa-6a159dd50492)
    Create a Storage Class for MySQL:
        Define the storage type as EBS and set volumeBindingMode to WaitForFirstConsumer. This ensures that the volume is only created when a pod is scheduled to use it.

    Create a Persistent Volume Claim (PVC):
        Specify the desired storage capacity for MySQL (e.g., 5Gi) and link it to the previously created storage class (EBS).
![Screenshot (1654)](https://github.com/user-attachments/assets/2b44f7fe-e9a3-4862-9ef8-a74b4366a55d)

    Create MySQL Deployment:
        Deploy a single MySQL pod using the PVC to persist data. The MySQL deployment should reference the secret for database credentials and mount the PVC to the appropriate directory in the container.

    Create MySQL Service:
        Set up a ClusterIP service for MySQL to ensure it is only accessible within the Kubernetes cluster. This service will expose MySQL on port 3306.

2. Set Up WordPress Site

    Create EFS on AWS:
        Set up an EFS (Elastic File System) instance using the AWS Management Console. EFS will be used to provide shared storage for WordPress.

    Create a Storage Class for WordPress:
        Define a new storage class that uses EFS as the provisioner. This will ensure Kubernetes pods can use EFS for persistent storage.

    Create Persistent Volume (PV) for WordPress:
        Create a PV that references the EFS file system. Specify the EFS volume ID in the PV configuration and ensure that it is accessible in read-write mode by multiple pods.

    Create a Persistent Volume Claim (PVC) for WordPress:
        Create a PVC for WordPress that references the storage class defined for EFS. Specify the desired capacity (e.g., 5Gi).

    Create WordPress Deployment:
        Deploy the WordPress pods and configure them to connect to the MySQL service by referencing the MySQL service name and credentials stored in the secret. The WordPress pods will mount the PVC to persist data in EFS.

    Create WordPress Service:
        Set up a LoadBalancer type service for WordPress to expose the application to the internet. This service will automatically provision an external IP and integrate with the AWS Application Load Balancer (ALB).

3. Configure High Availability with ALB

    Create an Application Load Balancer (ALB) in AWS:
        In the AWS Management Console, create an ALB. This load balancer will distribute incoming traffic to the WordPress pods.

    Create a Target Group for WordPress:
        Set up a target group with port 80, and associate it with the LoadBalancer service created for WordPress. The target group will handle the routing of traffic to the WordPress pods.
![Uploading Screenshot (1655).png…]()

    Attach the Target Group to the ALB:
        Link the target group to the ALB to enable traffic distribution to the WordPress pods.
![Uploading Screenshot (1656).png…]()

4. Verify Access

    Once the WordPress and MySQL services are deployed, retrieve the external IP address of the WordPress service by running kubectl get svc wordpress-service.

    Access the WordPress site using the external IP provided by the LoadBalancer service, and complete the WordPress setup.
