# aws-training

Amazon Web Services common overview

- [Elastic Compute Cloud (EC2)](ec2/README.md)

# additional resources 

- [understanding-aws-tenancy](https://theithollow.com/2017/10/16/understanding-aws-tenancy)
- [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html)
- [Creating and managing a Node.js server on AWS, part 1](https://hackernoon.com/tutorial-creating-and-managing-a-node-js-server-on-aws-part-1-d67367ac5171)
- [Creating and managing a Node.js server on AWS, part 2](https://hackernoon.com/tutorial-creating-and-managing-a-node-js-server-on-aws-part-2-5fbdea95f8a1)


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
