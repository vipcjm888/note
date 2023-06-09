::: {.only}
not (epub or latex or html)

WARNING: You are looking at unreleased Cilium documentation. Please use
the official rendered version released here: <https://docs.cilium.io>
:::

Installation using Kubespray {#k8s_install_kubespray}
============================

The guide is to use Kubespray for creating an AWS Kubernetes cluster
running Cilium as the CNI. The guide uses:

> -   Kubespray v2.6.0
> -   Latest [Cilium released
>     version](https://github.com/cilium/cilium/releases) (instructions
>     for using the version are mentioned below)

Please consult [Kubespray
Prerequisites](https://github.com/kubernetes-sigs/kubespray#requirements)
and Cilium `admin_system_reqs`{.interpreted-text role="ref"}.

Installing Kubespray
--------------------

``` {.shell-session}
$ git clone --branch v2.6.0 https://github.com/kubernetes-sigs/kubespray
```

Install dependencies from `requirements.txt`

``` {.shell-session}
$ cd kubespray
$ sudo pip install -r requirements.txt
```

Infrastructure Provisioning
---------------------------

We will use Terraform for provisioning AWS infrastructure.

### Configure AWS credentials

Export the variables for your AWS credentials

``` {.shell-session}
export AWS_ACCESS_KEY_ID="www"
export AWS_SECRET_ACCESS_KEY ="xxx"
export AWS_SSH_KEY_NAME="yyy"
export AWS_DEFAULT_REGION="zzz"
```

### Configure Terraform Variables

We will start by specifying the infrastructure needed for the Kubernetes
cluster.

``` {.shell-session}
$ cd contrib/terraform/aws
$ cp contrib/terraform/aws/terraform.tfvars.example terraform.tfvars`
```

Open the file and change any defaults particularly, the number of
master, etcd, and worker nodes. You can change the master and etcd
number to 1 for deployments that don\'t need high availability. By
default, this tutorial will create:

> -   VPC with 2 public and private subnets
> -   Bastion Hosts and NAT Gateways in the Public Subnet
> -   Three of each (masters, etcd, and worker nodes) in the Private
>     Subnet
> -   AWS ELB in the Public Subnet for accessing the Kubernetes API from
>     the internet
> -   Terraform scripts using `CoreOS` as base image.

Example `terraform.tfvars` file:

``` {.bash}
#Global Vars
aws_cluster_name = "kubespray"

#VPC Vars
aws_vpc_cidr_block = "XXX.XXX.192.0/18"
aws_cidr_subnets_private = ["XXX.XXX.192.0/20","XXX.XXX.208.0/20"]
aws_cidr_subnets_public = ["XXX.XXX.224.0/20","XXX.XXX.240.0/20"]

#Bastion Host
aws_bastion_size = "t2.medium"


#Kubernetes Cluster

aws_kube_master_num = 3
aws_kube_master_size = "t2.medium"

aws_etcd_num = 3
aws_etcd_size = "t2.medium"

aws_kube_worker_num = 3
aws_kube_worker_size = "t2.medium"

#Settings AWS ELB

aws_elb_api_port = 6443
k8s_secure_api_port = 6443
kube_insecure_apiserver_address = "0.0.0.0"
```

### Apply the configuration

`terraform init` to initialize the following modules

> -   `module.aws-vpc`
> -   `module.aws-elb`
> -   `module.aws-iam`

``` {.shell-session}
$ terraform init
```

Once initialized , execute:

``` {.shell-session}
$ terraform plan -out=aws_kubespray_plan
```

This will generate a file, `aws_kubespray_plan`, depicting an execution
plan of the infrastructure that will be created on AWS. To apply,
execute:

``` {.shell-session}
$ terraform init
$ terraform apply "aws_kubespray_plan"
```

Terraform automatically creates an Ansible Inventory file at
`inventory/hosts`.

Installing Kubernetes cluster with Cilium as CNI
------------------------------------------------

Kubespray uses Ansible as its substrate for provisioning and
orchestration. Once the infrastructure is created, you can run the
Ansible playbook to install Kubernetes and all the required
dependencies. Execute the below command in the kubespray clone repo,
providing the correct path of the AWS EC2 ssh private key in
`ansible_ssh_private_key_file=<path to EC2 SSH private key file>`

We recommend using the [latest released Cilium
version](https://github.com/cilium/cilium/releases) by passing the
variable when running the `ansible-playbook` command. For example, you
could add the following flag to the command below:
`-e cilium_version=v1.11.0`.

``` {.shell-session}
$ ansible-playbook -i ./inventory/hosts ./cluster.yml -e ansible_user=core -e bootstrap_os=coreos -e kube_network_plugin=cilium -b --become-user=root --flush-cache  -e ansible_ssh_private_key_file=<path to EC2 SSH private key file>
```

If you are interested in configuring your Kubernetes cluster setup, you
should consider copying the sample inventory. Then, you can edit the
variables in the relevant file in the `group_vars` directory.

``` {.shell-session}
$ cp -r inventory/sample inventory/my-inventory
$ cp ./inventory/hosts ./inventory/my-inventory/hosts
$ echo 'cilium_version: "v1.11.0"' >> ./inventory/my-inventory/group_vars/k8s_cluster/k8s-net-cilium.yml
$ ansible-playbook -i ./inventory/my-inventory/hosts ./cluster.yml -e ansible_user=core -e bootstrap_os=coreos -e kube_network_plugin=cilium -b --become-user=root --flush-cache -e ansible_ssh_private_key_file=<path to EC2 SSH private key file>
```

Validate Cluster
----------------

To check if cluster is created successfully, ssh into the bastion host
with the user `core`.

``` {.shell-session}
$ # Get information about the basiton host
$ cat ssh-bastion.conf
$ ssh -i ~/path/to/ec2-key-file.pem core@public_ip_of_bastion_host
```

Execute the commands below from the bastion host. If `kubectl` isn\'t
installed on the bastion host, you can login to the master node to test
the below commands. You may need to copy the private key to the bastion
host to access the master node.

Delete Cluster
--------------

``` {.shell-session}
$ cd contrib/terraform/aws
$ terraform destroy
```
