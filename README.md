### wordpress_mysql_k8s_Project
![Screenshot (1647)](https://github.com/user-attachments/assets/c4df9ca8-7023-4c1d-b1ab-7a44ace681d1)
![Screenshot (1648)](https://github.com/user-attachments/assets/117aebe1-c1fb-47e8-a3c3-6cd77faa29a3)
###Project Describtion:
* using kuberenets to deploy A wordpress site with mysql database on aws
* create 2 ec2 instance from type medium3
* create EBS for mysql because we need only one pod write on EBS
* using EFS for wordpress site to save data because allow more than pod can share same EFS 
* using load balancer from application loadbalancer to provide high avalability
###Project-Steps
#firstly,create mysql database :
* create mysql secret for mysql (create iam user has permission on ebs and efs then create role include ebs and efs then attach it to all nodes in the cluster and use iam access key and secret access key in mysql secret)
* create storage class and define storage type EBS then define volumebindingmode(waitforfirstconsumer) then create persistence volume claim and define capacity then create deployment include 1 pod and mysql secret and PVC
* create service from clusterip type to make reachability inside cluster only
#secondly,create wordpress site :
* create efs on aws and create storage class and define provisioner from efs type
* create PV and define efs id in configuration of PV then create pvc and specify capacity
* create deployment include service of mysql and mysql secret to make wordpress access mysql
* create service from loadbalancer type and on aws create target group contain on port of loadbalancer type then attach target group with application loadbalancer on aws
* try to access wordpress site
* it's work 
