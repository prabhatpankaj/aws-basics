# aws-training

Amazon Web Services common overview

- [Elastic Compute Cloud (EC2)](ec2/README.md)

# additional resources 

- [understanding-aws-tenancy](https://theithollow.com/2017/10/16/understanding-aws-tenancy)
- [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html)

# problem and solution
* Linux warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
```shell
vi /etc/environment
```
add these lines...
```shell
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
```
