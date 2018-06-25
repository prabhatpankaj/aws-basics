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
| alex | i-09...     | t2.micro      | eu-central-1b     | running        |               |              | ...               | ...            | (link to group)

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
3. Locally `ssh -i "you_perm_key.pem" ec2-user@ec2-...compute.amazonaws.com`

Amazon AMI contains:
yum, java, python, ruby, ...

### Price

You pay for seconds of running instance.
*t2.micro - 0.0116 per hour*

## Instance Storage - Elastic Block Store (EBS)

It is a network drive located in a special dedicated server.

`Instance Volume` (separated SSD) vs `EBS Volume + EBS Volume Snapshot` (virtual block storage) vs `EFS (elastic file system)` (file system storage).

* EBS has ~125MB per sec, but depends on instance.
* SSD is good for cache, it will be clear after usage

*0.1$ per GB + replication*
*0.1-0.2% AFR*

There are special ebs optimized instances.

### Attach new volume

If you want to add new volume, there is a `Elastic Block Store` tab in EC2 bar.

1. Create new volume

| Key | Value | Notes
| --- | ----- | ------
| Volume Type | General Purpose SSD (GP2)
| Size (GB) | 100 
| IOPS | 300/3000
| Availability Zone | eu-central-1b
| Throughput | N/A
| Snapshot ID | - |
| Encryption | - | Allow to encrypt volume

Volumes:

| Name | Volume ID | Size | Volume Type 
|------|-----------|------|-------------
| -    | vol-..    | 100  | gp2         


2. Click `Attach volume`

| Key | Value
|-----|------
| Volume | vol-...
| Instance | i-..(alex)

3. Connect to machine
`ssh -i "you_perm_key.pem" ec2-user@ec2-18-...compute.amazonaws.com`

4. Check that disk is available
`sudo df -h` or `lsblk`

5. Mount disk

5.1. `sudo file -s /dev/xvdf`
If `data`, then it's ok, the disk is empty

5.2. Create ext4 file system
`sudo mkfs -t ext4 /dev/xvdf`

5.3. Create directory
`sudo mkdir /data`

5.4. Mount disk to new directory
`sudo mount /dev/xvdf /data`

5.5. Change directory owner to ec2-user
`sudo chown -R ec2-user: /data`

5.6. Create new files and have fun :smile:
`nano my_file.txt`

5.7. Allow to show volume as a part of system size
`sudo resize2fs /dev/xvdf/`

* It is possible to change disk size
* It is possible to create snapshot of disk

*Snapshots contains difference, just like git. So do not delete old snapshots*

*You pay for usage + read/write*

## Amazon Lightsail

There is an analogue of EC2 with simple configurations

It has nice UI. Prices equal to Digital Ocean machines.

*One month free*

* You can create snapshots
* You can add storage
* You can add load balancers

Note - they can go to S3 or smth like that.