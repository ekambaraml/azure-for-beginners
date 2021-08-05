# Deployment of OpenShift 4.6x cluster using Azure User provisioned Infrastruction(UPI)



# 1.0 Requirement

```


```

# 2.0 Aure Account setup

This document is a guide for preparing a new Azure account for use with OpenShift. It will help prepare an account to create a single cluster and provide insight for adjustments which may be needed for additional clusters.

Follow along with the steps and links below to configure your Azure account and provision an OpenShift cluster:

## 1. [DNS Zone](dnszone.md)

Azure's DNS Zone service is used by the OpenShift installer to configure cluster DNS resolution and provide name lookup for the cluster to the outside world. To    use OpenShift, you must have created a public DNS zone in Azure in the same subscription as your OpenShift cluster. You must also ensure the zone is              "authoritative" for the domain. There are two ways to do this outlined below: root domain and subdomain. A root domain is example.com. A subdomain is of the form clusters.example.com.
      Step 1: Acquire Domain
      
      Step 2: Create Public Hosted Zone
      public/hosted zone is required for the deploying the OpenShift
      
      Example: 
      Root domain:   example.com
      Subdomain : cluster.example.com
          
How to purchase the custom domain in Azure
https://docs.microsoft.com/en-us/azure/app-service/manage-custom-dns-buy-domain

2. [Limits](limits.md)
Azure subscription and service limits, quotas, and constraints
https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits

## 2.1 VNet

Each cluster creates its own VNet. The default limit of VNets per regions is 1000 and will allow 1000 clusters. To have more than 1000 clusters, you will need to increase this limit. The default installation creates a single VNet.

## 2.2 Network Interfaces
The default installation creates 6 network interfaces. The default limit per region is 65536. Additional network interfaces are created for additional machines and load balancers created by cluster usage and deployed workloads.


## 2.3 Public and Private IP addresses

By default, the installer distributes control-plane and compute machines across all availability zones within a region to provision the cluster in a highly available configuration. Please see this map for a current region map with availability zone count. We recommend selecting regions with 3 or more availability zones. You can provide an install-config to configure the installer to use specific zones to override the defaults.

The installer creates two external load balancers and one internal load balancer. The external load balancers each have a public IP address while the internal load balancer has a private IP address. Two subnets are created within the VNet. One of the subnets is for the control-plane nodes while the other subnet is for the compute nodes. A description of what each load balancer does is here.

Each VM gets a private IP address. The default install creates 6 VM's for a total of 6 private addresses. The internal load balancer has a private IP address while the external load balancers have public IP addresses. This sums up to 7 private IP addresses and 2 public IP addresses.

A public IP address is also created for the bootstrap machine during installation. This is so that it is available via SSH should anything go wrong during the install. The bootstrap machine and public IP address are destroyed once installation is complete.

## 2.4 Network Security Groups

Each cluster creates network security groups for every subnet within the VNet. The default install creates network security groups for the control plane and for the compuete node subnets. The default limit of 5000 for new accounts allows for many clusters to be created. The network security groups which exist after the default install are:

controlplane
This allows the control-plane to be reached on port 6443 from anywhere
node
This allows worker nodes to be reached from the internet on ports 80 and 443

## 2.5  Instance Limits

By default, a cluster will create:

One Standard_D4s_v3 bootstrap machine (removed after install)
Three Standard_D8s_v3 master nodes.
Three Standard_D2s_v3 worker nodes.
The specs for the VM sizes (Dsv3-series) are as follows:

Standard_D8s_v3

8 vCPU's, 32GiB ram
IOPs / Throughput (Mbps): (cached) 16000 / 128
IOPs / Throughput (Mbps): (uncached) 12800 / 192
NICs / Bandwidth (Mbps): 4 / 4000
64 GiB temp storage (SSD)
16 data disks max
Standard_D4s_v3

4 vCPU's, 16GiB ram
IOPs / Throughput (Mbps): (cached) 8000 / 512
IOPs / Throughput (Mbps): (uncached) 6400 / 1152
NICs / Bandwidth (Mbps): 2 / 2000
NICs / Bandwidth (Mbps): 2 / 1000
32 GiB temp storage (SSD)
8 data disks max
Standard_D2s_v3

2 vCPU's, 8GiB ram
IOPs / Throughput (Mbps): (cached) 4000 / 256
IOPs / Throughput (Mbps): (uncached) 3200 / 384
NICs / Bandwidth (Mbps): 2 / 1000
16 GiB temp storage (SSD)
4 data disks max

Azure VM specifications https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-general

The default subscription only allows for 20 vCPU's and will need to be increased to at least 22. If you intend to start with a higher number of workers, enable autoscaling and large workloads or a different instance type, please ensure you have the necessary remaining instance count within the instance type's limit to satisfy the need. If not, please ask Azure to increase the limit via a support case.


## 2.6 Load Balancing

By default, each cluster will create 3 network load balancers. The default limit per region is 1000. The following load balancers are created:

1. default 
  * Public IP address that load balances requests to ports 80 and 443 across worker nodes
2. internal
  * Private IP address that load balances requests to ports 6443 and 22623 across control-plane nodes
3. external
  * Public IP address that load balances requests to port 6443 across control-plane nodes

Additional Kuberntes LoadBalancer Service objects will create additional [load balancers][load-balancing]. 

## 2.7 Increasing Limits

Reach out to account admin to increase the increase your default limit 



4. [Account](account.md)

## 2.8 [Credentials](credentials.md)
6.
7. [Cluster Installation](install.md)



#### 3.0 Bastion Preparation

3.1. Download Installer

wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable/openshift-install-linux.tar.gz

3.2 Download OpenShift CLI

wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable/openshift-client-linux.tar.gz

3.3 Download Openshift PullSecret
