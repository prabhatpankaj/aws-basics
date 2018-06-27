# Virtual Private Cloud (VPC)

Many configured instances with different sets of IP addresses :smile:

Isolated part of AWS cloud, 

Allows:
* IP-addresses range
* Sub-networks
* VPN
* Routing tables

*Create EC2 -> .. -> Configure Instance Details -> Create new VPC*


VPN Gateway -> Router: [ Subnets, isolated AWS resources ]

## Range of IPv4 addresses for the VPC

`192.168.0.0 - 192.168.0.254` === `192.168.0.0/24`

16 is a count of bytes here : `192.168`

Example:
* `192.168`.0.0/16 - vpc range
* `192.168.0`.0/24 - subnet 1
* `192.168.1`.0/24 - subnet 2

VPC is in all availability zones.

CIDR Classless Inter-Domain Routing

Destination | Target
------------|-------
10.0.0.0/16 | local

VPC:
* 10.0.0.0/16
* 10.0.0.0/8
* 10.0.1.0/8

## Private IP-address

* TODO

## Public IP-address

* Public IP is lost when the instance is reloaded

## Elastic IP-address

*Network & Security -> Elastic IPs -> Allocate new address*

* Free
* Associated with AWS
* Only `5 elastic IP-s` in zone
* Can be reassigned to another instance
* Small price if more IP-s required

*`192.168...` are usually used cause of no conflicts with Internet*

## Router and route table

* VPC with one public network in Availability Zone A
* Size 256 addresses
* Internet Gateway is configured in VPC

Destination | Target
------------|-------
10.0.0.0/16 | local
0.0.0.0/0   | igw-id

## Internet Gateway

Translates IP-address.
EVen if network is public, it doesn't mean that it is routed from internet.

When the request is sent from VPC to global internet, it sends request to Internet Gateway. 
And internet gateway sends request to `google.com`

## VPC with Public Subnets

Public subnet is a subnet with Internet Gateway which allows other internet users to ping public IP.

[Public Subnet](/images/public-subnet.png)

VPC Dashboard -> Start VPC Wizard

## 1. Select a VPC Configuration

* VPC with a Single Public Subnet
* VPC with Public and Private Subnets
* VPC with Public and Private Subnets and Hardware VPN Access
* VPC with a Private Subnet Only and Hardware Access

## 2. 

Key | Value
--- | -----
VPC name| alex1
Availability ZOne | eu-west-1a
Subnet-name | alex11
Service | com.amazonaws.eu-wet-1.s3
Enable DNS hostnames | Yes

Create VPC

## 3. Successfully Created

## VPC with Public and Private Subnets

[Public and Private Subnets](/images/private-and-public-subnets.png)

Public network - with internet gateway 
Private - with NAT gateway

NAT gateway is located in Public network and link to NAT is stored in Private subnet.
Access to Internet is done from Public network.

## 1. Select a VPC Configuration

VPC with Public and Private Subnets

## 2.

Key | Value
--- | -----
VPC name | alex 2
Availability Zone | eu-west-1a
Public subnet name | alex-pub
Private subnet name | alex-priv
Elastic IP Allocation ID | eipalloc-f3..
Enable DNS hostnames | Yes


## Access to private subnets

* How to access to private subnet? (it is allowed to ssh to public network, use ssh to private network)

* What about keys? Use ssh-agent (ssh -a)

## Create instance in VPC

### 1. Create private instance 

Key | Value
--- | -----
Network| alex1
Subnet| alex-priv
SSH | TCP | 22 | Anywhere
HTTP | TCP | 80 | Anywhere

(It will not be accessed)

### 2. Create public instance 

Key | Value
--- | -----
Network | alex1
Subnet | alex-pub
Auto-assign Public IP | Enable
SSH | TCP | 22 | Anywhere
HTTP | TCP | 80 | Anywhere

### 3. View info

Now they are located in different VPC subnets and in the same VPC network

Private instance contains Private IP e.g. `10.0.1.32`

### 4. Log in to public instance

`ssh ec2-user@...`

### 5. Log in to private instance (from public instance)

Google about ssh-agent of how to use the same private key for all ssh requests

`ssh -A ec2-user@34....` -> (from public machine) `ssh ec2-user@10.0.1.32`

*AWS rule. If you have issues - wait ~5 minutes for applying any changes*