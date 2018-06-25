### AMI

Amazon Machine Image

It is an image with special requirements and special tools inside (Amazon Linux, Red Hat Linux, Windows 10, etc.)

### Configure Instance Details


| Key | Value | Notes
|-----|-------|-----
| Number of instances | 1 | 
| Purchasing option | - | 
| Network | vpc-59... (default) | 
| Auto-assign Public IP      | Enable | 
| IAM role | None | Allows this machine to work with other services.
| Shutdown behavior | Stop | `Stop` vs `Terminate`. Terminate will delete instance. Stop can be rerun.
| Enable termination protection | - | Additional popup
| Monitoring | - | Monitor CPU/memory/instance types/.. Requires additional charges
| Tenancy | Shared | `Shared` vs `Dedicated` vs `Dedicated host`. Should your machine be dedicated physical machine or just virtual machine?
| T2 Unlimited | - |
 
### Add Storage

Root - /dev/xvda - 30GB - ...

### Add Tags

It is not possible to assign name to instance from the scratch. But it is possible to tag them.

*e.g. `Aws-training`*

### Configure Security Group

| Key | Value | Notes
|-----|-------|-------
| Assign a security group | `Create a new security group` | You can use existing security groups
| Security group name | http-and-ssh | any names

| Type | Protocol | Port Range | Source | Description
|------|----------|------------|--------|------------
| SSH | TCP | 22 | Anywhere (or My IP or ...) |
| HTTP | TCP | 80 | Anywhere (...) |

*It's a good practice to limit access by IP.*

### Select an existing key pair or create a new key pair

If you go to `Network and Security` tab in EC2, you will see all the key pars. (public-private keys for protected connections)

You can `import key pair` here or `create new key pair`.

### Launch

Click `Launch` and wait around 2-3 minutes.

You can name it somehow.

| Name | Instance ID | Instance Type | Availability Zone | Instance State | Status Checks | Alarm Status | Public DNS (IPV4) | IPv4 Public IP | Security Groups
|------|-------------|---------------|-------------------|----------------|---------------|--------------|-------------------|----------------|----------------
| Aws-training | i-09...     | t2.micro      | eu-central-1b     | running        |               |              | ...               | ...            | (link to group)

Below you can see:
* Public IP
* Public DNS 
* Private IPs
* Private DNS (IP + region + suffix)
* Root device type (`ebs`)
* Root device `/dev/xvda`
* Some metrics
* other props

### Connect

1. Click `Connect`
2. Locally `chmod 400 you_perm_key.pem`
3. Locally `ssh -i "you_perm_key.pem" ubuntu@ec2-...compute.amazonaws.com`
