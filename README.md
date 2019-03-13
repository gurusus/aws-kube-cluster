# AWS Deployer 
**This repo is for non-production purposes**
The code provided is for demo purposes only. 

This repo contains Terraform and Ansible projects that can be used to create a simple 3 node Kubernetes cluster. 

Upload your SSH key to AWS through the console

## Terraform 
Update the `variables.tf` file with the following:

* Name of SSH key uploaded in previous step 
* Path to private and public SSH keys
* AWS region you want to deploy to 
* VPC ID for chosen AWS region 
* Subnet ID for chosen VPC. 

Before running Terraform you must have you aws credentials in ~/.aws/credentials, which looks like this.
```
[default]
aws_access_key_id = ASDFWEF1983739
aws_secret_access_key = cDrL82349723984Dllkjo
``` 

After you have added the credentials initialize Terraform 
```
terraform init 
``` 

You can see that it has now downloaded the `AWS` module. 

Now use the `plan` option to confirm Terraform will be creating the cluster as expected. 
```
terraform plan 
```

Look at the output and confirm it is creating 4 objects
* Security group 
* 1 Master node 
* 2 Worker nodes 

Now apply the changes 
```
terraform apply 
```

You will be prompted to type `yes` to confirm. 


If everything is setup correctly you should see something simliar to: 

```
k8s-master public_ips = [
    13.57.207.155
]
k8s-node public_ips = [
    54.183.110.11,
    54.215.244.146
]
``` 


## Ansible 

There are two Ansible playbooks, `docker.yml`, which is used to install and setup Docker on the hosts and `k8s-cluster.yml` which installs Kubeadm and creates the Kubernetes cluster. 

I have also provided an inventory folder for us-west, if you decide to use a different region update the ec2.ini file for your region. 

Run the following: 
```
ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -u ubuntu -b --key-file ~/.ssh/k8s-test  -i inventory_us-west/ docker.yml k8s-cluster.yml
```

Replace the `--key-file` with path to your SSH private key. 

After this is complete you should be able to SSH into the master IP 
```
 ssh -i ~/.ssh/k8s-test ubuntu@54.193.125.233
```

Confirm the Kubernetes cluster was created successfully. 
```
kubectl get nodes
```

Output:
```
NAME             STATUS   ROLES    AGE   VERSION
ip-10-0-102-43   Ready    <none>   18m   v1.13.4
ip-10-0-102-46   Ready    <none>   18m   v1.13.4
ip-10-0-102-50   Ready    master   19m   v1.13.4
```
