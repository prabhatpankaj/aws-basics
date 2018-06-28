# Virtual Private Cloud (VPC)

Joining the market among the three major cloud Services like Public, Private and Hybrid are Virtual Private Clouds (VPC).

VPC is a private cloud located inside a public cloud that enables you to experience the benefits of a virtualized network while using public cloud resources.

It is related to the public cloud, but they are not the same. Instead of sharing resources and space in a public infrastructure, you get a changeable allotment of resources to configure.
![vpc](/images/vpc.jpg)

Virtual Private Clouds and private clouds are the same. Whereas Private clouds are entirely dedicated to the organization including hardware, Virtual Private clouds do not have the same hardware dedication and just creates a more secure environment on public infrastructure.

Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a virtual network that you have defined.

This virtual network closely resembles a traditional network that you’d operate in your own data center, with the benefits of using the scalable infrastructure of AWS.
 
VPC Setup

You can set up the VPC in the following five fluid steps

Let's look at the steps to create a custom Virtual Private Cloud in an AWS account. This VPC will be created using IPv4 Classless Inter-Domain Routing (CIDR) block. It will have one public subnet and one public-facing instance in this subnet. It will also have one private subnet and one instance in private subnet. For instance, for a private subnet to access the internet, we will use a NAT gateway in a public subnet. This VPC will have security groups and network ACL configured to allow egress and ingress internet traffic along with routes configured to support this scenario:

* Create a VPC with a /16 IPv4 CIDR block such as 10.0.0.0/16.
* Create an internet gateway and attach it to this VPC.
* Create one subnet with /24 IPv4 CIDR block, such as 10.0.0.0/24, and call it a public subnet. Note that this CIDR block is a * subset of a VPC CIDR block.
* Create another subnet with /24 IPv4 CIDR block, such as 10.0.1.0/24 and call it a private subnet. Note that this CIDR block is a subset of a VPC CIDR block and it does not overlap the CIDR block of a public subnet.
* Create a custom route table and create a route for all traffic going to the internet to go through the internet gateway. Associate this route table with the public subnet.
* Create a NAT gateway and associate it with the public subnet. Allocate one Elastic IP address and associate it with the NAT gateway.
* Create a custom route in the main route table for all traffic going to the internet to go through NAT gateway. Associate this route table with the private subnet. This step will facilitate the routing of all internet traffic for instances in the private subnet to go through the NAT gateway. This will ensure IP addresses for private instances are not exposed to the internet.
* Create a network ACL for each of these subnets. Configure rules that will define inbound and outbound traffic access for these subnets. Associate these NACLs with their respective subnets.
* Create security groups for instances to be placed in public and private subnets. Configure rules for these security groups as per the access required. Assign these security groups with instances.
* Create one instance each in the public and private subnet for this VPC. Assign a security group to each of them. An instance in a public subnet should have either a public IP or an EIP address.
* Verify that the public instance can access the internet and private instances can access the internet through the NAT gateway.


Once all steps are completed, our newly created custom VPC will have the following architecture. Private instances are referred to as database servers and public instances are referred to as web servers in the diagram. Note that the NAT gateway should have the Elastic IP address to send traffic to the internet gateway as the source IP address. This VPC has both the public and private subnet in one availability zone; however, in order to have a highly available and fault-tolerant architecture, you can have a similar configuration of resources in additional availability zones:

![routetablesvpc](/images/routetablesvpc.png)

