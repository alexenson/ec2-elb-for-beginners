This repository shows all the steps needed to create EC2 services on AWS. Using AWS Auto Scaling and Elastic Load Balancing. 
All the necessary code and steps are provided. For an even better in-depth experience and explanation, it is best to have access to Digital Cloud Training's AWS EC2 for Beginners course. https://learn.digitalcloud.training/courses




<br>
<br>



## Amazon EC2
 
<br>


## Introduction

Amazon EC2 enables us to deploy virtual servers in the cloud, which is basically virtual machines. We call them instances in EC2. We can launch instances running Windows, Linux and there are even options to launch macOS hosts as well. 
AWS provide management for the underlying platform. Including the hardware, the virtualisation layer and they expose the controls for us to then deploy our instances. 
They also have a service called Amazon EC2 Auto Scaling, for automatically launching and terminating instances. Based on things like performance metrics or a schedule. So we can automatically scale the amount of instances running our application we need at any point in time.
Then we can put a Load Balancer in front, using the Elastic Load Balancing service as well.


## Amazon EC2 Overview   

I'm going to cover the Amazon Elastic Compute Cloud, EC2. EC2 is a service in which we can run EC2 instances in the Cloud and EC2 instances are basically virtual servers. So when we learned about virtualization earlier, this is exactly what we're talking about here. So there's a host server in the AWS data centre or of course, there are hundreds and thousands of these things. And AWS manage those host servers and they run their own virtualization. They use a combination of Zen and Nitro, those are different hypervisors that they use. That's the virtualization layer. We then get to launch EC2 instances, we manage the instance. So here, for example, I have an EC2 instance and it has a certain amount of hardware assigned to it. Of course, we get to choose that. And the way that we choose how much hardware is assigned to our instance is by choosing an instance type.  There are instance families and then within each family there's instance types and they come with varying combinations of CPU, memory, storage and also networking capabilities. So here I've got one with Windows. There are other options as well. Mainly with EC2, you're choosing Windows or Linux. There is a version of EC2 where you can launch a MacOS operating system. It's basically a dedicated piece of hardware. It's quite expensive to run compared to Windows and Linux instances. But it is there for use cases where you need MacOS. So, essentially EC2 is a virtualization stack. It's infrastructure as a service or IaaS. AWS are managing the underlying infrastructure and all we have to do is manage from the operating system upwards. So we get to launch our instances, choose the operating system, choose what the hardware is that we want to be assigned depending of course on our workload and our requirements. And then we manage that operating system and the applications that are running on top of it. 
 ![3](https://github.com/user-attachments/assets/b9d254db-ebb0-4abb-90a5-e664898ba748)


Networking is quite important to understand when it comes to EC2.So let's just look firstly, at the IP address options. We can have a public IP address. If you launch an instance into a public subnet, we'll cover that more shortly. Then by default, it's going to receive a public IP address. Now that means that you can communicate on the internet and you can also have your instance accessible from the internet. So if it's a web server, for example, of course, that's what you're going to want. Now, the thing to know about the public IP address is that they are lost when the instance is stopped. So, if you stop and start your instance. It's going to gain a new public IP address. These are for public subnets, there's no charge for using them and they're actually associated with a private address on the instance. In fact, in the operating system of the instance, if you try to find out what the IP address is, by running a command like ip config or if config, you're not going see the public address.  It's actually mapped externally to the operating system through the elastic network interface. What you will see on the instance is a private IP address. So all instances always have private IP addresses and those are retained when the instance has stopped and then started back up again. And therefore for public and private subnets, it doesn't matter. You always have private addresses. The third type of IP address is the Elastic IP address. This is a static public IP address. So if you need a static public address, then this is what you need to use. Now, you are charged if they're not used. So if you have an Elastic IP address, but your instance is not running or it's not associated with an instance or a Load Balancer, then you are actually imposed a small hourly charge. Also, even while your instance is running. If you have more than one Elastic IP address associated with it, you do pay for those additional ones as well. Now these are again like the Public IP, they're kind of externally mapped through the elastic network interface. So your operating system still has to have the private IP address. And there's an association between the two. These can be moved between instances and elastic network adapters as well.
![1](https://github.com/user-attachments/assets/f80346a2-680c-4f40-be56-6f1ddf15b625)

 


So let's have a look at public and private deployments. Here, we have a VPC, we have an availability zone and we have a public and a private subnet. When we launch our instances, we can choose which subnets and which availability zones we want to launch them into. So here I've launched one in a public subnet. So it's going to have either a public IP or an Elastic IP associated with it. We then have something called an Internet gateway. This is attached to the VPC. The Internet gateway is the pathway out to the internet. Now, subnets have route tables associated with them. This one here you can see it has a local route that's for the overall IP address block range for the VPC. That's called the cider block. So this is the cider block of the VPC. And what this means is that any connections to any IP addresses within this range are going to be routed internally within the VPC locally. Then everything else, the all zeros means everything else is going to go to the Internet gateway ID that's going to actually map to a specific ID for the individual Internet gateway that's associated with this particular VPC. So now the instance can communicate via the Internet gateway out to the Internet. Now, because this is an instance in a public subnet, we can also connect from the Internet to the instance. So if it's a web server, for example, then computers on the Internet will be able to communicate with it and they come in through the Internet gateway. 
![3](https://github.com/user-attachments/assets/4929e01c-bf9c-4480-9385-ee016bbe4c58)



Now we can also launch our instances into private subnets. When we do that, they won't have any Internet connectivity at all by default, and they'll only have private IP addresses. There are no public addresses assigned in a private subnet. Now, that's good for many use cases. We want to use this as much as possible. We want to deploy our instances into private subnets whenever we can, even if they're public facing, because we can put a load balancer into the public subnets. So that's a topic for later on. But you want to try and use private subnets as much as possible for security reasons. Now, sometimes our instances still need to connect to the outside world. They might connect to a public service or they might just simply need to download some updates from the Internet. For that we can deploy something called a NAT gateway. This is an AWS managed device. The private subnet will have a separate route table because we don't want to have the Internet gateway id in the route table that's associated with the private subnet. So we always create separate route tables for the private subnets. And now what we're going to do is say that everything else. So everything outside of the local address range is going to go via the NAT gateway. So then what happens is the instance is going to connect using private IP addresses to the NAT gateway. But the NAT gateway also has an Elastic IP. So a public IP address which allows it to communicate with the outside world. So the outbound traffic can now go via the NAT gateway. But there's no inbound connectivity allowed to the EC2 instance. NAT gateways only allow outbound traffic. So those are a few fundamentals that are important to understand. Of course, the next thing to do is to go and start launching EC2 instances to see this service in action.



## Launching Amazon EC2 Instances

We are going to launch virtual servers on AWS using the Amazon EC2 service.
We will launch a Linux instance and a Windows instance.

When we are launching EC2 instance, we need to first select our instance type.
There are lots of different instance types and they come with varying amounts of CPU, memory and storage. So we will pay depending on the amount of those resources we require. 

The instance type defines the hardware profile and therefore the cost. We also need to select an Amazon Machine Image (AMI). The AMIs defines which operating system we want to use and how it is being configured. It might have for example an application pre-installed on it. You can choose an AMI that has Windows with a Microsoft SQL Server database installed, as an example.
So the AMI define the configuration of the instance, including the operating system and any software that is installed. And how the virtual drives, the EBS volumes are defined. These are backed by what is called a snapshot. So the actual data is stored in a snapshot. Snapshots are actually taken from live instances, as a kind of backup. Then we create an AMI from them and we can keep launching more instances that are the same as the original. So a snapshot is a point in time backup of an EC2 instance. Once we have done that, we can create our own customised AMIs. So for example we might launch an existing AMI, we might make some customisations to it. And then create our own AMI that we can then launch instances from, later on. 
![3](https://github.com/user-attachments/assets/29c7adf6-90b6-472e-bbcc-6c9f5f994b8f)






In the Amazon console – go to EC2.

Click on Launch Instance.<br>
![4](https://github.com/user-attachments/assets/4e0861a6-e8b0-4f0d-b03d-7f324c37c6c5) 

You can name your instance if you want – it is optional.<br>
![5](https://github.com/user-attachments/assets/294df4f4-ec12-4281-8e19-2ddde3adb022)

Further down, you can see the Application and OS images (Amazon Machine Image)
By default it selected the Amazon Linux – that is a version of Linux that has been customised by AWS. It includes variety of things, like certain agents and the command line interface for AWS. The Amazon Linux AMI has been customised by AWS and it includes certain agents and the AWS command line interface. 

Here we have chosen the Amazon Linux 2023 AMI, which has been selected for us.<br>
![6](https://github.com/user-attachments/assets/10c24271-da78-4764-b453-a13511008343)


 
It says free tier eligible – which is good news.


If we scroll down a little, we have the instance type.<br>
![1](https://github.com/user-attachments/assets/9ecbcb39-1e52-418f-a6b7-8cc2e52aff87)


 
The t2.micro has been selected by default. (Also free tier eligible)

If you want to change it, you can click the little arrow.<br> 
![2](https://github.com/user-attachments/assets/44950845-0f1f-4561-926a-fc0c156aa0f9)

 

And you can choose from the list of options. But we will leave it as is.<br>
![3](https://github.com/user-attachments/assets/e84dd512-57f6-45f6-86ce-c68861a75c7f)

 


Next we have the key pair. Key pairs are used for connecting to our instances using the secure shell, if we are connecting from outside AWS.<br>
![4](https://github.com/user-attachments/assets/a8217238-019e-4af3-b7c7-af95f17c5010)

 

But we don’t have any key pairs, so we will create a new one.<br>
![7](https://github.com/user-attachments/assets/8cd32cef-3064-48b6-b69b-2315eb50db3f)

 


We will leave the default RSA and .pem.<br>
![1](https://github.com/user-attachments/assets/a4bc0d69-10fd-43cf-ada1-55bbf28f5306)

 

We will give it a name that is descriptive. For example dct-lab-training-us-east-1. (Suggest you name it to your region, if you decide to go by region or something else that is descriptive)<br>
![2](https://github.com/user-attachments/assets/54c427aa-ec85-4dea-8f62-4c303b1d0d7f)

 
Then click create.<br>
![4](https://github.com/user-attachments/assets/c17111a1-4cd7-4e95-9699-16ce94c94017)

 

What will then happen is it will download a file to your computer. It is using cryptography – public and private keys. The private key is being downloaded to your computer; it is probably in your downloads directory. 
Make sure you move it somewhere, you can find it later on and where it is kept securely - because it is sensitive information. Anybody with that particular file will be able to connect to your instances and manage them. 

Next for network settings we will leave some of the defaults here.<br>
![1](https://github.com/user-attachments/assets/cf1728f3-e659-45c4-8fb8-73653ac0d4ce)

 
But we need to create a new security group.
If you do it from here on the console, it will give it a weird name. 

![1](https://github.com/user-attachments/assets/c9fcc883-a755-4d65-bc4c-fafa516da79e)<br>
So click on Edit
 


Then you can give it a name.<br>
![2](https://github.com/user-attachments/assets/389f508b-364b-47fb-b44c-6474e67fd783)

 

We will call it – WebAccess.<br>
![3](https://github.com/user-attachments/assets/613e814e-3f91-4e34-b35a-3b030e38bdf8)

 

We will copy the name into the description.<br>
![4](https://github.com/user-attachments/assets/1b74004d-8912-429c-ba6c-fd7595a71e2a)

 

Then we have ssh – secure shell<br>
![6](https://github.com/user-attachments/assets/880a5c92-96fd-48df-944b-d615ccb92a3b)

 

It’s going to allow any source address. The 0s mean any source IP address.<br>
![1](https://github.com/user-attachments/assets/ae13282c-e6e5-4399-9a65-f2839916e5ec)

 



Scroll down a bit and leave the Configure storage section as defaults.<br>
![2](https://github.com/user-attachments/assets/1ff6bf32-b359-48b1-bb69-9084427ffb6c)

 

That is all we need for now. There is some advanced details/options. We will look at that in another lesson. 

We just want 1 instance and the summary is fine.
Then click Launch instance.

![3](https://github.com/user-attachments/assets/76c21a38-6a65-464b-b5cc-bfc94764b7e9)

 







So the instance is launching.<br> 
![4](https://github.com/user-attachments/assets/e42a3c8c-51da-4d22-a289-c61a29681e19)

 

We can click on View all instances, and we can see it is pending. That should change to a running state soon.<br>
![5](https://github.com/user-attachments/assets/51f0a9da-e9ed-4854-87f7-db2caba8cd9c)

 

You can see lots of information by clicking on the instance.<br>
![6](https://github.com/user-attachments/assets/ef0b99d9-87a7-48fa-9028-a807e148a7fc)

 
Now you can see its Instance ID – a unique identifier. Public IP address, the Private IP address. It also has a Public and Private DNS name that we can use. 


There are also varies tabs for Monitoring information. We can see the Security group we assigned. It is essentially the firewall that is allowing access on port 22 in this case.<br>
![7](https://github.com/user-attachments/assets/3fd2a354-0a9b-4d24-beb9-0b7dcf3cfe68)

 

There is lots of Networking information. We can see it is in the us-east-1d Availability Zone.<br>
![1](https://github.com/user-attachments/assets/7a01084d-7a77-4285-9c9f-cf53821f915c)

 

This will be deployed in our default Virtual Private Cloud.<br>
![2](https://github.com/user-attachments/assets/d3ed7a24-036e-4c22-aa59-bab1d8b84184)

  


This is running – lets launch another instance.<br>
![3](https://github.com/user-attachments/assets/3e738192-a2c9-4218-9a55-5f4af7646549)

 


We will call it Windows-Server.<br>
![4](https://github.com/user-attachments/assets/0a07bef0-5ceb-41fb-bdd6-79b9f32d7f04)

  

Scroll down and chose the Windows AMI<br>
![5](https://github.com/user-attachments/assets/34976161-2ccd-4dc6-adb2-3e017528419e)

 

If you were to click Browse more AMIs.<br>
![6](https://github.com/user-attachments/assets/1c62efb7-144b-4427-9bb2-c3dc05322f0c)

 

---------------------------------------------------------------------------------------------------------

You are able to see Quickstart AMIs, My AMIs, AWS Marketplace AMIs and Community AMIs.<br>
![7](https://github.com/user-attachments/assets/fca583ac-43c3-4ed6-a4aa-396c1617d2ad)

 

My AMIs is if you have your own custom AMIs.
AWS Marketplace AMIs – this will show you lots of AMIs that includes varies software like VPN servers, Backup & Recovery software, networking, firewall – like Palo Alto, Splunk Enterprise etc. That are built into the instance. You will typically pay higher rate for these, because the software charges are going to be included, however that is not always the case.
CommunityAMIs which are AMIs that people in the community have created and shared for everyone else to use.<br>
![8](https://github.com/user-attachments/assets/40453c1f-4c5d-4568-ad13-167d1e2cf967)

 
----------------------------------------------------------------------------------------------------------------


We will go back to this screen and click on Windows.<br>
![9](https://github.com/user-attachments/assets/d6a3d644-f62a-4d86-b9a6-0b5065b37cd3)

 
It chooses the Microsoft Windows Server Base. 

Again we will select the t2.micro<br>
![1](https://github.com/user-attachments/assets/7b156ba4-ad47-44ba-ad65-af8855520698)

 

For the Key pair, it is very important for the Windows instances that we select this.<br>
![2](https://github.com/user-attachments/assets/2d154455-2f50-448c-8e41-ab75f0673e25)

 


We don’t always need to assign a key pair for our Linux instances, because we can often use a service called CloudShell to connect to them. If we want to connect using the secure shell protocol. 

For Windows, in order to retrieve the password, we have to have a Key pair assigned.
<br>

Under Network settings we will select an existing security group and choose WebAccess.<br>
![1](https://github.com/user-attachments/assets/b7930907-028c-4683-8787-a0157a60f939)

 

In this case WebAccess does not have the rule that we need to connect to Windows at this point in time. So we will edit that in a moment.

That is all you need for now so click on Launch instance.<br>
![2](https://github.com/user-attachments/assets/651d853f-f64a-45d5-9c12-4c4311e062cd)

 
And we will have our Windows instance up and running shortly.<br>
 ![3](https://github.com/user-attachments/assets/bf1879d4-ef64-44b2-8439-0084ca77d5c5)


I need to be able to connect to my Windows server and want to use the remote desktop protocol.
Under Security I can see that I have a security group and which has port 22 open.<br>
![4](https://github.com/user-attachments/assets/c53fc494-b224-4d06-94ca-713b2ffa16a9)

 

That is not for the remote desktop protocol that is for the Linux server when we use the secure shell protocol. 

You can click on the security group to edit it.<br>
![5](https://github.com/user-attachments/assets/07e7be32-c66a-42e3-bd84-9a35bf2f126a)

 
Or on the left hand side under network and security you can select security groups.<br>
![6](https://github.com/user-attachments/assets/c5f60f50-bc19-4004-807c-7ba19085b49e)

 

And you can find the same security group here.<br>
![7](https://github.com/user-attachments/assets/8f487c5b-57bc-41db-a7c9-307bbf5989ce)

 
We will click on the relevant security group ID.<br>
![8](https://github.com/user-attachments/assets/aa618b4e-e61c-4a95-ab56-1bbdb0cbb5fe)

 
We have Inbound and Outbound rules.<br>
![9](https://github.com/user-attachments/assets/e72832a5-6c82-48c7-912d-f99648fa88eb)

 
I only want to edit the inbound rules at the moment.
So we will click Edit inbound rules.<br>
![1](https://github.com/user-attachments/assets/fe1c170d-4a1e-4cf2-a89c-005bf00e3533)

 
Click Add rule.<br>
![2](https://github.com/user-attachments/assets/e6e62b61-3156-4fee-922e-8c35ebe77610)

 
Click on the drop down.<br>
![3](https://github.com/user-attachments/assets/d798675e-5168-4bb8-ac52-41763335fab9)

 

Type in RDP to easily find the RDP protocol.<br>
![4](https://github.com/user-attachments/assets/4ab14ca7-b5c6-42d8-b0b7-d771a529faf2)

 

Which will allow access on port 3389 and select to Anywhere IPv4.<br>
![5](https://github.com/user-attachments/assets/a49fd6cb-9031-4427-9680-5b07274a48d1)

 

Allowing any source address.<br>
![6](https://github.com/user-attachments/assets/a2f179d7-9234-4c44-a83d-1d743dbbacc3)

 
Then Save rules.<br>
![7](https://github.com/user-attachments/assets/8873d8b1-7502-4e95-a07c-ab0e1262b1a4)

<br>
<br>
 

## Connecting to Amazon EC2

Here we will connect to our EC2 instances using the secure shell protocol and the RDP protocol. 

Make sure your Windows and Linux instances are running in EC2.

Back in the EC2 management console, I have selected the Linux server.<br>
![1](https://github.com/user-attachments/assets/060d91fc-6eba-402c-ac0c-80fd300e2a43)

 
We can see that we have a public IP address and a public DNS address as well.<br>
![2](https://github.com/user-attachments/assets/8e90d9e7-4074-49d9-8e98-5a5af6482cab)

 

So if we want to, we can use those to connect from the outside world.

Let’s have a look at the varies different ways we can connect.
If I click on Connect with the instance selected.<br>
![3](https://github.com/user-attachments/assets/91b2dc8e-dc9e-4085-96d8-6f887033df1e)


We can now use the EC2 Instance Connect.<br>
![4](https://github.com/user-attachments/assets/6f6d7060-a481-4be3-bfaf-006bde33ce27)

 

Or we can use Session Manager, this is using the Systems Manager service.
It is one of the features of System Manager. <br>
![5](https://github.com/user-attachments/assets/710a58ed-8513-45f2-bc18-04fb1e7e68da)

![6](https://github.com/user-attachments/assets/2cf2d006-face-4c27-955d-8022f989b219)


 
 
Gives us a very secure way of connecting, without opening any ports. 
We then have the SSH client, if you want to connect from your home computer, this is what you could use.<br>
![7](https://github.com/user-attachments/assets/7c18b987-5676-49fa-92da-a17a191a2f43)

 
You would need your private key file.
Earlier we created a Key pair and it downloaded a file to our computer, that's the private key file. 
It actually gives the full command. The command is ssh –I “then the name of the pem file” that was the file that was downloaded, the key pair. Then ec2-user@ then we have the public DNS name or this could be the public IP address.
This would be the full command you need to connect from your home computer.<br>
![8](https://github.com/user-attachments/assets/30563623-05f7-46da-b1ca-a123ab847f40)

 

It is recommended that if you are using Windows, that you install an SSH client. It is a feature Windows, so you just have to Google how to do that for your particular version of the operating system.

If you are on a Mac for example, you will always have a SSH client installed – same with Linux.


However, we are going to use the EC2 Instance Connect.<br>
![1](https://github.com/user-attachments/assets/686c3aa2-c6b0-4785-aaba-27ad602dcdaf)


![2](https://github.com/user-attachments/assets/1de81852-3afd-48f7-9514-47ed0a3733f9)



 

We are going to use the connection type on the left.
The right hand one is for where our instances are within a private subnet – we would have to create an endpoint first.

However, our instances have been launched into the default VPC – which by default has public subnets only. That means that they have public addresses and we can connect to the instances directly from the internet.




The username has been specified as ec2-user - which is correct.<br>
![3](https://github.com/user-attachments/assets/fdb9ef67-356e-408f-9ebd-469cd757bc41)

 

Then click on Connect.<br>
![4](https://github.com/user-attachments/assets/178f91d6-7c9c-47ca-a0b6-27d9025607c7)


We are now connected to the command line on my EC2 instance.<br>
![5](https://github.com/user-attachments/assets/cad5bf3b-22d9-4a23-bc3f-4c121b80286b)

 

If I were to run ifconfig, it shows me the IP addresses associated with the instance. 
Also, I should be able to ping google.com and get a response. That proves I can actually connect, and I would have to hit Ctrl c to stop the ping.

If you were to have any issues here, a little red banner comes up, saying it can’t connect.

There are two main things which you need to check. 



Firstly, you do need to have public IP address.<br> 
![6](https://github.com/user-attachments/assets/e4b98c5b-655a-43f2-a65e-e6bb4d5b77f5)

 

Secondly, your instances must have port 22 open in its security group.
Click on Security, port 22 with the Source, should be all 0s. That is any source address. If you have those two selected then instance connect should work – with the Amazon Linux AMI.<br>
![7](https://github.com/user-attachments/assets/db1066d4-3aa1-4819-85bc-1522e975cfde)

 
Now we are free to manage the server from the command line.<br>
![8](https://github.com/user-attachments/assets/636364fb-b0ee-45cd-92d4-d10bfe11a017)

 

Next we will move on to Windows.

![9](https://github.com/user-attachments/assets/3b799e25-88fe-4178-b261-35e1fe542ca6)<br>
For Windows we will select the server and click on connect.

The options are slightly different now.<br>
![1](https://github.com/user-attachments/assets/e5add1a0-883e-4a52-847f-ac93d03ee8be)

 
There is an option for Session Manager – for remote PowerShell on the command line.
But we will use the RDP (Remote Desktop Protocol) client.<br>
![2](https://github.com/user-attachments/assets/10630ef5-9c0f-4399-9ac1-2fe001f4b5d5)

 
What we can do here, is to download the remote desktop file. That will download a file to our computer – which we can utilise. Or we can add the information directly. 
For this exercise however, you will need the RDP client (software) on your computer.
If you are using Windows – it is easy – as there is already a RDP client installed. 
If you are using Mac, then you would have to download an installer. (on the Internet just search for RDP client Mac installer)

What we need to do is to Get password for login.<br> 
![3](https://github.com/user-attachments/assets/ba1515df-c91a-41c8-86b6-ed2f7e06f9ef)

 

Firstly, however we will copy the Public DNS name.<br> 
![4](https://github.com/user-attachments/assets/eee0f91e-9a24-42e4-bb06-99eacfe2817f)

 
We can see the username is Administrator.<br>
![5](https://github.com/user-attachments/assets/ba1836e5-f472-4721-9466-0f8ac32eac3f)

 

Now with your remote desktop software, click Add PC.<br>
![6](https://github.com/user-attachments/assets/b206d3bf-0342-4a69-99a7-5c0aed893f7d)
 
![7](https://github.com/user-attachments/assets/86524ea5-c967-436f-b78f-f107c9adba1d)


Enter this as the PC name and click on Add.<br>
![1](https://github.com/user-attachments/assets/2f469ac1-02ec-40f7-8e23-ec647221a0a9)

 

What will happen now, it is going to connect and it will ask for the username and password.<br>
![2](https://github.com/user-attachments/assets/d827ef69-56d5-40ab-b079-c74ae10855d3)

 

But we need to go and retrieve the password first, so click cancel – till we have that information.



To retrieve the password, click on Get password.<br>
![3](https://github.com/user-attachments/assets/7713f4df-9a87-49a7-b75e-192ed3d4c76f)

![4](https://github.com/user-attachments/assets/08fe6396-4fa3-48b5-bf04-50926efd30cd)


Now we need to upload the private key file.<br>
![5](https://github.com/user-attachments/assets/7485731f-848a-4801-bec4-24931b7f352a)

 

Here I have uploaded my private key file. That is the one you have downloaded previously – when you created the Key pair.

![6](https://github.com/user-attachments/assets/f74df4a7-8b11-4d4e-8a8d-ff87d2e97640)


You can select the file and it going to download all the contents for you.<br> 
![1](https://github.com/user-attachments/assets/be7078e6-badf-413c-a308-d6f3b5c2215c)

 
Then click on Decrypt password.<br>
![2](https://github.com/user-attachments/assets/f1f0610f-8ea6-454c-97c1-a2a4edff6f1b)
 

Now we are able to see the password.<br>
![3](https://github.com/user-attachments/assets/e104b4eb-6859-4c30-8c50-2cd9b4654eec)

 

We know the username is Administrator, so copy the password.

Back in the RDP client – we will double click and put in the Administrator username and put in the password.<br>
![4](https://github.com/user-attachments/assets/04f32b08-42df-4dc1-a55e-3634a51d0d3c)

![5](https://github.com/user-attachments/assets/02a33a7a-a528-4ae9-8653-e94423522505)

 
 
Click continue.

Then continue again.<br>
![6](https://github.com/user-attachments/assets/d6141c23-4732-4675-9ee8-679080f9cfd2)

 

This should then connect you to the desktop of the server.<br>
![7](https://github.com/user-attachments/assets/295378b1-bed6-4aae-a98c-486aab1649c1)

![8](https://github.com/user-attachments/assets/79c5aa44-51fb-45f9-aa0b-ef4124873c39)

 

We are being logged into the desktop of a Windows server on AWS.<br>
![9](https://github.com/user-attachments/assets/f341690b-ee7a-467b-9be3-f92bb1abd63c)

 

So here we have the Windows desktop, and this server is now ready to administer.<br>
![1](https://github.com/user-attachments/assets/98530297-bed2-45be-8511-b0f6ee913f7c)



As with the Linux server, it needs to have a certain port open so if it failed, you can go back and check -  by clicking on your Windows server.<br>
![2](https://github.com/user-attachments/assets/5b89ce44-3acc-468f-a3ae-a78e6a44bbc1)

 


Select Security.
Make sure that port 3389 is open and the source has to be from anywhere. This is the Remote Desktop Protocol. <br>
![3](https://github.com/user-attachments/assets/33097f96-ed37-487c-9d27-cd9630965680)

![4](https://github.com/user-attachments/assets/782e2f2e-3177-45fe-9837-4c3c9954ec4f)
 


We have now launched two virtual servers in the Cloud, running Windows and Linux.


In your instances - under instance state (top menu), you can stop or terminate your instances.<br>
![5](https://github.com/user-attachments/assets/3fdfb032-3461-44a8-9ab9-23170f07081f)

 
This means that you are not going to pay for the running compute and memory. You will still pay for the storage that is allocated to this server.

You can reboot instances and terminate instances (this essentially deletes them).<br>
![6](https://github.com/user-attachments/assets/aeaf9599-d9fb-48f8-83f8-40b7061069ab)


When you click terminate, you’ll see that the instance changes to a terminated state quickly.<br>
![7](https://github.com/user-attachments/assets/640935b6-4a82-468d-a7ae-d1037a82e1d2)

 
It will stay in the console for a while. Don’t worry about it, it will disappear after a little while.

The other thing we can do, in terms of administration, is under Action, there are a variety of settings.<br>
![8](https://github.com/user-attachments/assets/cd53da76-ff06-4398-a7b9-614b1d88fe7f)

<br>
<br>

## Access Keys and IAM Roles with EC2

I'm going to talk about Access Keys and IAM roles. Two different ways that we can actually supply permissions to Amazon EC2 instances.   Here we have an instance in a public subnet and AWS CLI has been configured with access keys, because we want to work with a S3 bucket from the command line on this particular instance. Now the actual access keys are associated with an account. So whichever account created the access keys, that's the account they're associated with and they pick up the permissions assigned to any permissions policies assigned to that IAM user. So essentially through this IAM user, we've created access keys, we've configured the command line interface on the instance with those access keys. So now whatever commands we run on that instance, will have the same permissions as that user would have. So now we've found a way to give our instance permissions.  Problem is when we use access keys, these are long term credentials and we want to try and avoid using them as much as possible. Because if they're compromised and someone gets access to those keys, they get access essentially to our account.  And they are actually stored in plain text on the actual instance itself. So not really the most secure configuration.  
![1](https://github.com/user-attachments/assets/da742aac-8693-4ce9-b235-12e504d720a0)


Instead, what we can do is we can utilise IAM roles.  Roles have policies assigned to them. So now we can supply the permissions that we want our instance to have. There are no credentials stored on the instance. So we don't have that security exposure that we have with those access keys. Now, the instance is going to assume the role and gain the access permissions that it needs on the S3 bucket.  So two different ways of performing the same thing. But the second way, this way is more secure than using access key. So we want to try and use this method whenever we can. One thing to note when we're using IAM roles is it is utilizing the AWS security token service, AWS STS, in order to gain credentials. So it's actually gaining essentially access keys. But those access keys have a much shorter expiration and the instance will automatically renegotiate with STS and get some new credentials before they expire. So it's all happening automatically in the background. And those shorter term credentials are of course more secure than if we have the long term ones stored in plain text on the computer. So this is the best option we're going to use these as much as possible. 
![2](https://github.com/user-attachments/assets/8ce56d01-b4cd-4498-9f62-4509b212a282)

<br>
<br>

## Practice with Access Keys and IAM Roles

We're going to work with Access keys and IAM roles on Amazon EC2 instances. So let's head over to the console, back in the console - I still have a Linux server running has a public IP address and I'm able to connect to this instance.<br>
![1](https://github.com/user-attachments/assets/e7a24c73-da97-42dd-b7a8-880e7e7eea94)


So let's go ahead and connect using EC2 Instance Connect.<br> 
![2](https://github.com/user-attachments/assets/027c1cb4-ab26-4147-856c-23889d7a2cbd)

![3](https://github.com/user-attachments/assets/880d00f0-1a08-43e8-93fb-bc93619fa329)
 

So I'm now logged into the console. I can run commands on Windows.<br>
![4](https://github.com/user-attachments/assets/fd4e30b2-d42e-48d7-afae-38ebd7b8d9f2)



Now, the great thing about the Amazon Linux 2023 AMI or one of the great things is it has the AWS command line interface already installed. So I can run commands like aws s3 ls. <br>
![5](https://github.com/user-attachments/assets/0c1db074-a89c-4220-8a6a-819c20babdee)

 

But when I do so, I get this message unable to locate credentials. You can configure credentials by running aws configure. So it basically means we do not have any permissions. That makes sense. Even though I have permissions under my user account.  Linux, the operating system does not have any permissions. That's a good thing. We don't want it to inherit permissions from us. In fact, we're actually logged in as a user called EC2 user. That user account does not have too many permissions on Linux. Certainly doesn't have any permissions to any AWS services. So, what we need to do is supply those permissions. Now, there's two ways of doing that. One is through access keys. That's when we use the AWS configure, the other is an IAM role. So let's head over to the IAM service and open that up in a new tab.<br>
![6](https://github.com/user-attachments/assets/66c5976d-7fc7-4aef-a624-94d5af02a719)

 

And what I need to do, to get some access keys.<br>

![7](https://github.com/user-attachments/assets/ffe80158-de1a-48e9-9154-b18c1b272b80)

 

I'm going to click on users, choose my user account, go to security credentials and then we're going to come down here to where it says create access key. 

![1](https://github.com/user-attachments/assets/5e120891-74da-47e9-bca7-3e4810d93644)
 
![2](https://github.com/user-attachments/assets/01c96b70-82c1-4530-8e5c-734b7f8811d2)
 
![3](https://github.com/user-attachments/assets/8ee92d07-51a4-4e45-ba30-17cec54a6ee9)

 

I'm going to choose Command Line Interface (CLI) as the use case. <br>
![4](https://github.com/user-attachments/assets/d307664f-9f50-4d02-9fb7-22042688fe21)

 

And straight away you can see there's a bit of a warning here. There's better ways of doing this and there certainly are. This is not the recommended way to actually administer your servers or provide permissions to your servers so that they can access AWS applications. The role is the better way, for now I'm ok with this because I just want to demonstrate it to you. Let's click on next. I don't need a description and create access key.

![5](https://github.com/user-attachments/assets/ccf38912-12cf-4089-bdaa-2175bd472a59)

 

Now this information is displayed to you now.<br>
![6](https://github.com/user-attachments/assets/471939eb-72b5-4586-a28e-74a9a1216449)

 

You can retrieve the access key later on. You can only retrieve the secret access key now and optionally you can download it. This is very important information. This is essentially like a user name and password. Because anyone with this access key and secret access key are able to perform API actions in your account and they will inherit your user permissions. So very dangerous. Watch out for that. 
Now, I'm going to copy the access key come back to the server and I'm going to run that AWS configure command. <br>
![7](https://github.com/user-attachments/assets/5c796df0-b4b3-4913-b7f6-0e662d29e3e8)

 
It's now going to ask me for the access key ID, I'll paste it in. <br>
![8](https://github.com/user-attachments/assets/6d723c3d-716c-4c52-900e-e036f73218fe)


It's going to ask for the secret access key.<br>
![9](https://github.com/user-attachments/assets/99137df9-710f-438f-9aa4-429e3e38ae9a)

 

I'm going to copy that come back, paste it in and then it wants the region name or the default region.<br>
![1](https://github.com/user-attachments/assets/ea91a182-ae53-4329-85dc-b482c072c69f)

 
I'm going to set mine to us-east-1 with dashes in between and press enter and enter again. So now let's try and rerun that command from earlier aws s3 ls and no response there. That actually means that we didn't get an error. That's good news. So that just means we now have permissions. In fact, I can run a service like aws s3 mb to make a bucket. <br>
![2](https://github.com/user-attachments/assets/0b2175d5-c5d9-4eda-a035-ad0263b2a3b7)

 
This will create a container. I'll call it my bucket and then make it unique with a bunch of random characters that creates a bucket which is basically like a folder that you can store data into. So now when I rerun that command, it actually comes back with a response and it shows me the buckets in my account. <br>
![3](https://github.com/user-attachments/assets/95538627-7e19-4f79-b974-f77b778debc0)

 
So that's just proven that we now have access to AWS services. Sounds good. However, there are some security problems here. If I change directory to this weird directory path, we can see and by the way that's the ~/.aws. And now we can see these two files, config and credentials. Interesting. What's in those? I'm going to use the cat utility. So cat config and in here we can see some of that information I entered when I ran aws configure, the default region that we specified. But what about the other one, cat credentials. Now I can see my access key and my secret access key. Remember, this is highly sensitive information. It's there on the hard drive of this computer. <br>
![4](https://github.com/user-attachments/assets/d6468dfe-22ca-4e3f-8f00-68c4f38644f0)

 

It's there in plain text for anyone who can get into this user account. If there was some kind of compromise of the server and they found this, they've now compromised your entire AWS account. So it's not really very good. Here's what I'm going to do. I'm going to change directory up a level.  <br>
![5](https://github.com/user-attachments/assets/aa391d1a-bb3f-4345-b68b-da3433d8fe11)

 

So now we've got those two commands. Let me just clear screen. So now I'm back in my user directory. I am going to run rm –rf ~/.aws/*. So that has removed those credentials. Let's rerun aws s3 ls. <br>
![1](https://github.com/user-attachments/assets/f823a513-52d4-46ff-a49c-9eb78a17c6c2)




No credentials. They're gone. Now, let's do it the better way. Let's go back to IAM and we're going to use a role. Now, I've actually shown you my access keys, which is not very secure of course. Because that's very sensitive information. So what we can do with our access keys is we can always deactivate them and after you've deactivated them, you can delete them. <br>
![2](https://github.com/user-attachments/assets/0d629ddb-d961-4d6a-b486-4673a2e967c2)

![3](https://github.com/user-attachments/assets/7b3aa54f-7294-4348-b33e-be9c86d3ec6d)
 
![4](https://github.com/user-attachments/assets/ad4e3831-8b3b-4bd1-b606-1c0fa27d55ac)

![5](https://github.com/user-attachments/assets/4198bc1c-c242-458a-b424-2e211e48b039)

![6](https://github.com/user-attachments/assets/30eacf22-4793-4672-abfd-aab9aa7f25ff)

![7](https://github.com/user-attachments/assets/51cde37c-a474-495d-8548-ac1a2f258787)



 
So now it's no use to anyone. So let's just copy this into the confirmation delete. Now, my account is secure again. <br>
![8](https://github.com/user-attachments/assets/9129c402-cf1e-4a24-80aa-459c79cdca5b)

<br>
<br>

So let's go to roles.<br> 
![9](https://github.com/user-attachments/assets/e71766c7-376d-4c79-9d3d-23c4f36510bd)

 

We're going to create a role for EC2 and here we're going to choose the AWS service this time. <br>
![1](https://github.com/user-attachments/assets/665bdb38-41cb-46be-afb6-a63e0fd82d70)


<br>


So under use case with AWS service selected, I'm going to type EC2, choose EC2 and then I'll leave the default option. <br>
![2](https://github.com/user-attachments/assets/f370ba73-541a-4d7c-a85e-9434b32b70f9)

 

Click on next. <br>
![3](https://github.com/user-attachments/assets/0c66758c-07d8-465a-a221-c37fc5acd59a)

 
Now, I need to supply some permissions. Let's provide AmazonS3ReadOnlyAccess. <br>
![4](https://github.com/user-attachments/assets/b2d8ca0b-cf73-42d4-9c43-842f99588dc2)



It's a useful permission to have. I'll call this one S3ReadOnly and then it's just scroll down. <br>
![5](https://github.com/user-attachments/assets/eef749cb-ae79-4115-88c0-9c0e72026c79)


We can see a couple of things here. <br>
![6](https://github.com/user-attachments/assets/b0fec38f-5129-455f-91ef-e3e6587fd081)

 
So the trust policy is very important. Remember, the trust policy with a role defines who is allowed to assume the role, who is able to perform this action, sts:AssumeRole who or what. In this case, it's a service. So it's the principle is a service and it's EC2. And when EC2 assumes this role, they will, it will gain these permissions.

![7](https://github.com/user-attachments/assets/8e5cf270-2386-45d9-909d-c24a5f1d3783)

 
So let's create the role that's done. <br>
![8](https://github.com/user-attachments/assets/af282df7-91e1-4ba2-b375-88693e9f7a0c)

![9](https://github.com/user-attachments/assets/c0b464a5-3bf2-4a03-a446-9d6e61907d77)
 


Now, we need to go back to EC2. Select the instance, go to Actions, Security and Modify IAM role.<br>
![1](https://github.com/user-attachments/assets/1ed7f1c3-33b2-47e1-bb33-26ee7f66a0fd)

 

Now, the roles that appear here are the ones that have that trust policy. <br>
![2](https://github.com/user-attachments/assets/5296ed70-090a-4838-89c9-b86c34a2f14e)

 
So we've just got the one at the moment S3ReadOnly. Choose that option, select update IAM role and that's done. <br>
![3](https://github.com/user-attachments/assets/177ed9d6-c9b6-4e80-a66e-c34e2f36f241)

![4](https://github.com/user-attachments/assets/50fba843-e5d8-42ee-85eb-8951d57a2aa5)

 

That should take instant effect. Let's rerun that command and now we get our bucket returned. <br>
![1](https://github.com/user-attachments/assets/32645bf9-1394-41df-9b0f-a9225d75cf62)

 
So now we have credentials again. But guess what? There's nothing on the hard drive of this computer, that directory with the credentials in does not exist anymore. I deleted those files. They're gone. My hard drive is secure. There's no plain text data stored in here for credentials that will essentially supply people access to my account. So much better to use roles which leverage those temporary credentials which don't get stored on the computer. So that's it for this lesson. I will leave that role where it is because it is useful. Sometimes we'll use it in other labs, but I'm finished with this particular Linux server. So I'm going to terminate this instance and that's it. We're all cleaned up.

![2](https://github.com/user-attachments/assets/2e3be69b-ed2a-490d-8293-3ecd71050771)

![3](https://github.com/user-attachments/assets/5375c3c8-3017-4411-94d3-620fcb2a8a05)
 
![4](https://github.com/user-attachments/assets/7de90d9c-efd1-4d3e-90b5-029db1ff0dcf)

 

 
<br>
<br>

## Create a Website with User Data

In this lesson we are going to launch an EC2 instance using user data. The user data is going to install a website on the server.
User data is a way that we can supply lines of code that is executed in the form of a script, as the instance is launching for the time. 
This only ever happens, the first time the instance actually launches. 
Here we can see a simple script that is going to install a webserver on this EC2 instance. So the code is run when the instance starts for the first time only. Then the instance is launched, the user data has run and so a website is installed.
User data is limited to 16 KB, but that is quite a lot of room to run some code as we launch our instance.
Batch and PowerShell scripts can also be run on Windows instances. 
What we will do is go over to AWS console and we will launch a Linux instance, with some code that is going to install a customised webserver.
![5](https://github.com/user-attachments/assets/722fee6c-2fe6-451f-8f14-7ccf2a302096)
 
<br>
<br>

We will go over the AWS console and launch a Linux instance with some code that is going to install a customised webserver. 

<br>
<br>

![Capture](https://github.com/user-attachments/assets/330b4125-f6fb-4d14-ba6e-3c6d69760063)


<br>
<br>

### Explanation of the code:

This line will update the system with the latest patches.  -> yum update -y <br>
Then we will install httpd the Apache webserver. -> yum install -y httpd

Then using the system control we will start the Apache webserver and enable it so it starts again after a reboot. <br>
->    systemctl start httpd
         systemctl enable httpd
 
The next section of code is going to assign a couple of variables. <br>
Firstly ->  TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`

This is authenticating, it is gathering an authentication token that can then be used on the following line of code.

AZ=`curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone`

The purpose of this line is to assign a value to the variable AZ. That is the Availability Zone. The way it does it, after authenticating using the token, it is going to call the instance meta data service - which can gather information about the instance. The instance meta data service is always available at http://169.254.169.254/latest/meta-data <br>
Then you can find certain information by calling various different paths. <br>
/placement/availability-zone <br>
In this case it is going to identify the availability zone the instance is in and assign it to the variable AZ.

After that we will create the index.html file. This is the web page that will be displayed when we visit the particular instance.

## Create the index.html file <br>
![Capture](https://github.com/user-attachments/assets/136487da-c431-4630-a38a-0b94e6ba5baa)
<br>
<br>


**Here we are setting some colour and formatting.** <br>
![Capture](https://github.com/user-attachments/assets/ad0b2c5d-c7ad-44bd-8181-47441a89a7ac)



And finally, this line of code, which we will see on the page:<br>
![Capture](https://github.com/user-attachments/assets/88c99297-b49c-47c0-99cc-3a386cea7f27)<br>
This instance is located in Availability Zone and $AZ is going to resolve to the availability zone of the instance. It will let us know which availability zone the instance is in.
<br>
<br>


So copy all this code:<br>
![Capture](https://github.com/user-attachments/assets/c358dc7b-fb20-4309-9b23-1c83688d8f5d)




Go the AWS console under EC2 and click Launch instances.<br>
![1](https://github.com/user-attachments/assets/c7afaac9-5716-4691-9961-27b7903b9961)

 

We will name the instance WebServer.<br>
![1](https://github.com/user-attachments/assets/ac3aa853-e670-4264-bf7d-db9ef59b7dda)
 

We will use the Amazon Linux AMI.<br>
![2](https://github.com/user-attachments/assets/729d5841-6f73-4909-9c0b-5181971bfd28)
 

It will be a t2.micro instance type as it falls under the free tier.<br>
![3](https://github.com/user-attachments/assets/fa3a436c-2ae5-4e34-93e1-42688b5c27bd)
 

We don’t need a Key pair.<br>
![4](https://github.com/user-attachments/assets/1c593c31-1924-4dc4-8b7a-576691dcaa63)
 

For Network settings we will select one of the existing security groups. The WebAccess group.<br>
![5](https://github.com/user-attachments/assets/54a0073c-d96e-4946-9efe-4d27b1a2b3b8)<br>
We will adjust the rules on that security group shortly.


Scroll down to Advanced details and expand it.<br>
![6](https://github.com/user-attachments/assets/ed3637e6-73f4-4f2c-93f0-e23141d2272e)<br>
Scroll all the way to the bottom of the page.



This is where we can enter the user data. All I have to do is paste my user data in here.<br>
![7](https://github.com/user-attachments/assets/de3be2a3-59fb-42de-9a0e-b9ddbb8633c9)

![8](https://github.com/user-attachments/assets/8cf94e05-0c8b-4740-a5e3-e21e6185d3aa)

 
Then we are going to launch the instance.<br>
![1](https://github.com/user-attachments/assets/6cd9385b-eefe-44a4-89b8-360ea3038a59)
 
![2](https://github.com/user-attachments/assets/cff13c2c-cb74-437b-8d39-04cfd0fad527)

 

The code is going to run as the instance boots. 


Now we need to go and check the security group, as mentioned earlier.<br>
![3](https://github.com/user-attachments/assets/9e3cd12b-ce60-4876-a0b7-17343c30ff29)
 


So select your WebServer and go to Security.<br>
![4](https://github.com/user-attachments/assets/fb0f66e7-e3be-48fb-bb02-462d1918a104)

 

We have these two ports open for management but we don’t have port 80 open, which is what we need for a website.<br>
![5](https://github.com/user-attachments/assets/fdc80337-6c27-4097-835b-cd1eeec06983)

 

So click on the security group.<br>
![6](https://github.com/user-attachments/assets/1d96ba50-5efd-4d18-96d8-70924176c2be)

 

For inbound rules click Edit Inbound rules.<br>
![7](https://github.com/user-attachments/assets/dca91584-2563-4764-942c-5791b28512a4)
 
![8](https://github.com/user-attachments/assets/57aa985c-2421-4ed1-860f-b58ec0f8fca4)


Then click Add rule.<br>
![9](https://github.com/user-attachments/assets/a2cca362-ac2e-4b79-ae7d-904dcafd6cb0)

 
Custom<br>
![1](https://github.com/user-attachments/assets/6d5073db-6962-4612-880d-e60557f9339c)<br>
Type HTTP. 
We will choose HTTP as we do not have a certificate for HTTPS - which is the encrypted version.<br>
![2](https://github.com/user-attachments/assets/c6e49990-b962-4baf-9156-aa6bc4fe24f0)
 
<br>

We want to enable access from anywhere, because we want people on the Internet to be able to connect to our web server.<br>
![3](https://github.com/user-attachments/assets/121c0e97-827f-4a0d-a341-c18c1bb09065)
 

 
Then click Save rules.<br>
![4](https://github.com/user-attachments/assets/96c3ec22-8203-48ad-ae42-14198c6d4190)

 

Now we have set up the rules correctly for access.<br>
![5](https://github.com/user-attachments/assets/5d0b94fe-73dc-4b85-b8ee-1c2faa383b30)




Hopefully the code has run already, so let’s go over to the public IPv4 address, copy the address.<br>
![1](https://github.com/user-attachments/assets/2980e146-9f5a-4443-a785-a804a35843f8)

 

Open a new browser window and paste address into the URL.<br>
![2](https://github.com/user-attachments/assets/89fef89c-c726-42f7-9059-3c98bdec0e10)

 

It is working correctly.<br>
![3](https://github.com/user-attachments/assets/d8bdf8dc-20c3-4e9e-a042-cb8e9860e85c)<br>
We can see a message on the screen saying the instance is located in Availability Zone: us-east-1d



We can always validate on the Networking page.<br>
![4](https://github.com/user-attachments/assets/74c67059-ed02-42fa-99a5-c12248606d0f)

 

So the code is working exactly as expected. It has gone through and run the code as the system launches.

If you want to view the user data associated with an instance. You can click on Action > Instance settings > Edit user data.<br>
![5](https://github.com/user-attachments/assets/987b146f-13f4-4a39-8f79-803d802dc614)

![6](https://github.com/user-attachments/assets/e5bd7ee5-3d69-4222-83ec-e8d24aec33cb)<br>
It tells you here that if you want to edit the instance user data, you would have to stop the instance.

That’s fine, you can do that, but it won’t run again anyway. It only runs the first time the instance launches.

We can scroll and see the user data that is being run on the system.

As we have finished with our instance, remember to terminate it.<br>
![7](https://github.com/user-attachments/assets/ec31a27d-b1cc-4114-a4df-3f7e51f0daa1)

![1](https://github.com/user-attachments/assets/d9f1a223-2b6c-4e28-988f-02e75efb8400)

 
<br>
<br>


## Amazon EC2 Auto Scaling

I'm going to cover Amazon EC2 Auto Scaling. Auto Scaling is a really important service for maintaining the availability and automatic scaling of our EC2 instances. What it does is it will automatically launch or terminate instances based on whether the instance needs to be replaced or potentially you need to increase or decrease the capacity of your cluster. So you get to maintain the availability of your application and then scale it in response to certain things like changes in demand. It works with many different services. So we have EC2, it's actually launching and terminating EC2 instances, the Elastic Container Service (ECS) where it can be used to launch or terminate the nodes on which the actual containers run and the same for the Elastic Kubernetes Service (EKS) as well. It integrates with quite a few different AWS services. A few of the important ones are firstly CloudWatch for monitoring and scaling. Your instances are constantly sending information to CloudWatch and that can be information such as metrics on the CPU utilisation, that information can be utilised by auto scaling to determine whether it needs to scale the cluster out or in, so adding more nodes or terminating nodes. We've got Elastic Load Balancing for distribution of connections. If you're automatically scaling your instances. So auto scaling is adding instances to the deployment, then you want to make sure that the load balancer knows about that so it can send incoming connections. So there's an integration there between the ASG the Auto Scaling Group and the load balancer. And EC2 spot instances for cost optimisation. Amazon VPC of course, because we want the EC2 auto scaling group to deploy the instances within a VPC and across availability zones. <br>
![2](https://github.com/user-attachments/assets/d5386562-3698-438b-a913-9ebfa0921958)
 


So let's have a look at what it looks like in a nice diagram here. So here we have two Availability Zones each with a subnet, it doesn't matter whether it's public or private. This one's public and we create an Auto Scaling Group and then we can define the number of instances we want to be running in that Auto Scaling Group. So here we might have to find that 4 is an acceptable number. That's how many we want to run at a steady state. Now, there's two different scenarios, I'm going to show you here. One is automatic scaling. So what's happening all the time is the instances are sending through information to CloudWatch. Depending on the type of monitoring, either basic or detail that's either every five minutes or one minute. So they're sending metrics to CloudWatch, things like the CPU utilisation. Now, if the metric reports that the nodes have in aggregate exceeded 80% utilisation of their CPUs. CloudWatch can notify Auto Scaling to actually add a new node. So it's going to launch a new instance into that Auto Scaling Group. And this is all happening within CloudWatch using CloudWatch alarms. Alarms get created with certain thresholds for CPU utilisation. And once those are breached, either instances need to be launched or they need to be terminated to bring the cluster back down to a number that's appropriate to the amount of demand at that point in time. So we want to cost optimise as much as we can. Now, the second scenario is maintaining availability. Of course instances can fail for some reason. Here, the status checks have failed on one of these instances. So again, EC2 Auto Scaling is able to launch a replacement because that node failed. So those are the fundamentals of Auto Scaling. It's maintaining availability and scaling based on demand. So scaling out when we need more instances and scaling back in again by terminating instances when we no longer need them. And of course, it's going to talk to the load balancer to make sure the load balancer knows where to send incoming connections. <br>
![3](https://github.com/user-attachments/assets/c98d6842-94c8-412d-b03c-871c26925cf0)

 

With Auto Scaling, the scaling is horizontal, we're scaling out. So we're adding instances or we're terminating instances. So it's providing both elasticity and scalability. Elasticity is the scaling out. But then elastic means that it's able to scale back in again as well. So we're not just adding capacity. We're removing it when we no longer need it. Auto Scaling will respond to EC2 status checks as well as CloudWatch metrics. And it can scale based on demand so the performance or we can do it on a schedule instead. So we can say, well, we know that we're going to need more capacity at a certain point in time, maybe 9 a.m. on a Monday morning when people are starting to utilise the application more. So we can scale on a schedule ahead of that point in time to make sure we've got the capacity if we want to. And it's scaling policies that we create, which define how the auto scaling group will change to demand based on those metrics or based on schedules. <br>
![4](https://github.com/user-attachments/assets/3d8a4b00-0681-4884-a96a-103085f3c409)



So let's have a look at the config. We have something called a Launch Template. This specifies the EC2 instance configuration. Few of the things that it includes which are perhaps the most important to point out, is firstly the AMI so the Amazon Machine Image, what's the operating system and configuration of the software within the operating system that we want to use for our instances. So of course, we can create whatever operating system AMI we want with our application preconfigured if we want to. And then Auto Scaling is just going to launch many instances that are exactly the same. We choose the instant type, EBS volumes, things like instance profiles for IAM - if we need to supply permissions to the instances and so on. There's also something called a Launch Config, these are still available, they're older. They've been replaced really by Launch Templates which have more options. So generally we're going to be using Launch Templates. Now in both cases, whichever one you use, we then get to configure things like the purchase options, on-demand versus spot for our Auto Scaling Group. We get to configure the VPC, we want deploy our instances into and which subnets across which Availability Zones. We can optionally attach a Load Balancer as we're deploying our ASG or at a later time as well. And you can configure health checks for both EC2 and Elastic Load Balancing. And you get to configure the group size and the scaling policies. You can either statically define how many instances you always want to have running or you can use the scaling policy then to either adjust dynamically or on a schedule. <br>
![5](https://github.com/user-attachments/assets/ff2a1c18-2fe6-4c57-93b3-fb4654201a70)
 


A few more key facts - so health checks, these are really important. We've got the EC2 health checks that the Auto Scaling group makes. That's essentially an integration into the EC2 status checks. So it's looking at what's coming through from the EC2 status checks. Is the system OK, or is it impaired in some way. We've then got the ELB health checks. These are used in addition to the EC2 status checks. And this just means that we're also able to receive information in the Auto Scaling Group about what the Load Balancer that is seeing happening. So the Load Balancer is also doing its health checks to the instances. If they fail, it's going to report that information back to Auto Scaling. So it knows that it should terminate and restart that instance. We've got something called a health check grace period. This is how long to wait before checking the health of an instance. So we don't want the health check to start too quickly. If for example, we're running some kind of bootstrap scripts or we're installing some applications and maybe the application's not quite ready yet. So we can give a little bit of a grace period to allow the system to come up once it's been launched and become operational. So Auto Scaling doesn't act on health checks until that period expires. <br>
![6](https://github.com/user-attachments/assets/cdb8a24c-7625-4639-8a0e-27f0c17d63a0)



So we have different types of Auto Scaling. We've got manual that just means you're going in and manually making changes to the ASG size. So the number of instances you want deployed in the Auto Scaling Group or we have dynamic and that automatically scales based on demand - so now we're looking at those CloudWatch metrics. There's something called predictive as well, which uses machine learning to predict what it thinks is going to happen based on what it's seen in the past. And then lastly, you got scheduled where you're scaling based on a schedule that you define. So when you expect you're going to need more or less capacity.<br>
![7](https://github.com/user-attachments/assets/016bc605-3220-4838-b4a0-c27ca75353ed)

<br>
<br>


## Create an Auto Scaling Group

We're going to create an Auto Scaling Group for Amazon EC2 instances.  To perform this exercise, we're going to need the user data for installing a web server. Now this user data is in the course download last lesson of section one and in the Amazon EC2 folder, it's called user–data-web-server. sh. 

Code:<br>

![Capture](https://github.com/user-attachments/assets/f8014ed4-dae7-40ff-8a1b-054d8e9daf11)



Now this user data is going to install the Apache web server and it's going to set a custom web page which tells us which availability zone the instance is in, based on a variable that we set in the middle of the code here. <br>
AZ=`curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone`



So I'm going to copy all of this user data. This is going to be used for our launch template so that all of our instances, which will be across different availability zones will have a slightly different webpage that tells us which availability zone they are actually deployed into. Now back in the EC2 management console. The first thing I need to do is create something called a launch template. <br>
![1](https://github.com/user-attachments/assets/fd5df38e-2ae2-46eb-93e9-c86e27dd6365)

 

The Auto Scaling Group uses the launch template.<br>
![1](https://github.com/user-attachments/assets/5b534a65-d71a-440d-b35d-4077ea52a51e)

 

This defines the settings that we want to use. So I'm going to click on create launch template. I'm going to give this a name, MyWebServer.<br>
![2](https://github.com/user-attachments/assets/e6a1ffcc-6e1e-4067-8221-10adfbac9b8a)

 
Let's scroll down a little way for application and OS images. I want to choose Amazon Linux, the Linux 2023 AMI.<br>
![3](https://github.com/user-attachments/assets/8c8f0c48-e9d8-4f7a-a78f-110f00a98b60)

 

For instance, type I'm going to select t2.micro. <br>
![4](https://github.com/user-attachments/assets/5da0713b-47da-4292-af09-4725490afa94)

 

For Key pair, I'm not going to include one in the launch template, that’s fine. <br>
![5](https://github.com/user-attachments/assets/3abb02a5-beab-4058-83cd-4bed421f6a1f)



For Security groups, I'm going to select WebAccess, but I'm not going to change any other settings.<br>
![6](https://github.com/user-attachments/assets/eff33aaa-33f8-4ff8-9c33-7ad2589b0fba)

 
Where the instances are deployed is going to be configured in the Auto Scaling Group in terms of the Availability Zones. 
Then I'm just going to scroll to the bottom, go to Advanced details. <br>
![7](https://github.com/user-attachments/assets/3936e7d3-5d14-48d7-bf2b-dbe67afef6ed)

 
Scroll all the way down and simply paste in my user data and then I can create my launch template.<br>
![8](https://github.com/user-attachments/assets/38aaf321-4270-4b30-aca9-4db27247edba)

 
Then click Create launch template.<br>
![9](https://github.com/user-attachments/assets/ad8ed17a-1135-4213-8910-3479410f51b6)
 

That's ready. <br>
![1](https://github.com/user-attachments/assets/1fa6e1f6-9705-4325-886c-00faf63e7795)


Now, I'm going to click back up to EC2.<br>
![2](https://github.com/user-attachments/assets/f41c1fb9-24a7-4c8b-918b-209e73408fb6)

 

Scroll down and on the left hand side, click on Auto Scaling Groups.<br>
![3](https://github.com/user-attachments/assets/121ab5bf-5e2a-4dcd-b44e-7c74585e783b)


 
Here, I'm going to click Create Auto Scaling group.<br>
![4](https://github.com/user-attachments/assets/d7e189fe-c1f8-4c92-a56f-b517bcc96436)

![5](https://github.com/user-attachments/assets/ad191f04-672d-499d-a979-5cfed561f277)
 

I will give this a name, ASG1.<br>
![6](https://github.com/user-attachments/assets/06a4bed0-a515-42d0-9348-f5b48c007df3)
 


And now I need to select the launch template that I want to use.<br>
![7](https://github.com/user-attachments/assets/a7a4127d-25cf-487b-b2cb-00f5bb0a2650)

![8](https://github.com/user-attachments/assets/d02ee493-d93a-4e8b-8003-3301e6fed1a2)
 

 
And of course, this is going to define what's run when the Auto Scaling Group scales. <br>
![9](https://github.com/user-attachments/assets/7bad31b0-22c8-4dc2-95e6-233bdfa52049)

 

Which AMI, which instance type are we going to use?  
It's going to run the user data every time it launches an instance as well. 


So now I can click on next on this page. <br>
![1](https://github.com/user-attachments/assets/ebff1c11-7564-43f8-9e86-2a776f7fe091)

 

I'm going to leave the default VPC selected.<br>
![2](https://github.com/user-attachments/assets/d09ab78f-4343-4c33-9912-c4dbbf6c6b6e)

 

And I'm going to choose two Availability Zones. These are both public,  us-east-1a and us-east-1b. <br>
![3](https://github.com/user-attachments/assets/3902e8f5-ffcd-4bd0-b94f-5e37964dc960)

 

Scroll down, click on next. <br>
![4](https://github.com/user-attachments/assets/4a63c504-b6e7-46a6-91f1-cb9bb368955a)

 

We don't have a Load Balancer, at this point in time. <br>
![5](https://github.com/user-attachments/assets/3b8c5c1b-f3c4-41c3-887e-684d2e32ecb0)<br>
We're not using the VPC Lattice service so we can just scroll down and click on next.<br>
![6](https://github.com/user-attachments/assets/f087a55b-ba92-4325-8679-2a765aed9300)


I'm going to set a static capacity. 
Desired is going to be 2, 
Min is 2 
Max is 2.<br>
![1](https://github.com/user-attachments/assets/e0dbdb22-bc8e-4f9c-8a8c-154638423f36)

 

So this is how many instances we want running. I'm saying I want 2 instances to be running at all times. Minimum is 2 and maximum should be 2 as well, in this case. 
Later on, we'll have a scaling policy, then we can set automatic scaling and then we need to change the maximum at the very least. 
So we've got some room for the scaling to happen. I don't want to set a scaling policy right now. I just want 2 instances running and that's it. 
So I can scroll down, click on next.<br>
![2](https://github.com/user-attachments/assets/5073232e-365c-4427-981c-7f1ae917ec49)

 
Next again.<br>
![3](https://github.com/user-attachments/assets/c9fc6157-409e-44d1-bf74-23f106ccaf81)

 
Next again.<br>
![4](https://github.com/user-attachments/assets/3c960cd2-2aec-4513-82ff-85efcfd28d79)

 

Scroll down and we're ready to create the Auto Scaling Group.<br>
![5](https://github.com/user-attachments/assets/9b54f332-cf18-41ec-a035-b9480aca74c2)

 

The Auto Scaling Group is deploying now and we can see it's telling us it is updating capacity, it tells us the desire capacity and we can see which Availability Zones we enabled for the Auto Scaling Group. <br>
![6](https://github.com/user-attachments/assets/1b4d06b9-f20b-4f52-958e-aadf48d2358b)

 

That's where it's going to launch the instances. By default, it will spread the instances across the available Availability Zones. So here, we should have 1 in each. 

If I click on the name of the Auto Scaling Group. <br>
![7](https://github.com/user-attachments/assets/044b88c7-cc3f-4b6b-b140-ba3dd0c871f7)

 

And go to activity.<br>
![8](https://github.com/user-attachments/assets/180431b5-fde2-428d-8b3e-c13048435043)

 

We can actually see what's going on.<br>
![9](https://github.com/user-attachments/assets/53ef3efe-5b76-43fd-9bb1-ede06f90ee90)<br>
It's a useful place to come to have a look at what's actually happening. So we can see that 2 instances are being launched. They're not yet in service. So that's going to take a couple of minutes. 



If I click back up to EC2.<br>
![1](https://github.com/user-attachments/assets/94e85bb2-4c2d-46bc-921c-e21f2cb42868)

 

And go to Instances. <br>
![2](https://github.com/user-attachments/assets/f6adc47c-44c2-4961-8afc-26a1449f935f)

 

Here we go. I've got my 2 instances here running.<br>
![3](https://github.com/user-attachments/assets/31378a40-aa80-4d5e-96ef-6b32298841be)<br>
So this was a previous one from another lab. It's the two running ones that have just been launched across us-east- 1-a and us-east-1b. 

We need to make sure that we have port 80 open and I certainly do in my Security group.<br>
![4](https://github.com/user-attachments/assets/da134775-f3ea-4c89-aaa3-53785b5edacf)

  

So I've got port 80 from anywhere, which means that once the user data is run. 



I should be able to connect to the public IP address and view the web page.<br>
![5](https://github.com/user-attachments/assets/9e83407a-c7a6-49f3-a91d-3ef652a8eae2)

 

And it should tell me the Availability Zone of the instance, which for this one should be us-east-1a. <br>
![6](https://github.com/user-attachments/assets/5ae94aaa-77b2-4b85-91e4-8fdf9acef222)

 

Let's try that out, there we go.<br>
![7](https://github.com/user-attachments/assets/58106527-d694-47ec-98c2-2bbab96342e2)

![8](https://github.com/user-attachments/assets/aa99e952-3601-4c06-8dbb-d00bd1c8359f)


 

 
It tells us it's in us-east-1a. So that's great. 
So that's launching correctly. That's all I want to do right for now, is to set the Auto Scaling Group to launch 2 instances. They're across two different Availability Zones. <br>
![9](https://github.com/user-attachments/assets/32f5722a-33b0-4a20-a821-47e1d526ed06)<br>
Of course, you can enable more Availability Zones if you want to. And increase the number of instances that are launched. 

Now under Auto Scaling Groups, if we click on the Auto Scaling Group again, we can see the activity. <br>
![1](https://github.com/user-attachments/assets/cbca1a1b-6db5-40c9-9e21-819b26013ce8)
 
![2](https://github.com/user-attachments/assets/1b8e80ee-1373-4f2c-bd41-99df6596f6a4)
 
![3](https://github.com/user-attachments/assets/c399602a-b567-4040-9192-39f993dd019b)

 

So that's been successful now. So that's all good. <br>
![4](https://github.com/user-attachments/assets/4b4e24ac-7a1a-43d5-b2e4-603f382a045d)

 
If we go to Automatic scaling.<br>
![5](https://github.com/user-attachments/assets/59df065b-2920-49e6-80de-ed4cdd7b7167)<br>
![6](https://github.com/user-attachments/assets/fad87129-48b7-4eee-9614-ced6a5e559fd)<br>
In here, we can create dynamic scaling policies, predictive scaling policies and scheduled actions. 

There's instance management where we can take instances in and out of service, for example, set them to stand by set scale-in protection. <br>
![7](https://github.com/user-attachments/assets/aa7f7adf-4c9e-4dfc-99e4-3506522753ce)<br>
So this instance is not terminated. 


We can look at some monitoring information and we can enable certain  Auto Scaling Group metrics collection that will collect more information about all of the instances in the Auto Scaling Group rather than having to view that information individually for each instance, which you can do on the monitoring tab of course. <br>
![8](https://github.com/user-attachments/assets/a2b7ccc9-5f9d-4cef-a334-5f26d2147ce1)

![1](https://github.com/user-attachments/assets/13ec7260-4964-43a0-9f3b-1c171811a7bf)

![2](https://github.com/user-attachments/assets/a851e79e-8d6e-4c07-8990-5146781fc442)
 

Now one of the things you can do to test auto scaling.<br>
![3](https://github.com/user-attachments/assets/40a65fa0-09ff-4b3d-a8f0-7136ececd533)<br>
We set the desired capacity to 2. So it's going to try and always keep 2 instances. 

Let's go and just terminate one of these instances, doesn't matter which one, we're just going to go ahead and terminate it.<br> 
![4](https://github.com/user-attachments/assets/99a9a102-ab8f-43a4-adb6-971ca40bda88)
 
![5](https://github.com/user-attachments/assets/b860f515-4808-4e8f-9bc3-3a65ddf41230)

 

And what should happen is over the course of the next couple of minutes. The Auto Scaling Group will notice that this instance is no longer accessible. <br>
![1](https://github.com/user-attachments/assets/30bd4eed-0d19-45c5-b3f4-6bc6ae2d4058)


It picks up this information from the status checks, the EC2 incident status checks. <br>
![2](https://github.com/user-attachments/assets/08a0b44e-6fa8-4b1a-bcc5-36da582b2bcc)

 

That means it's going to identify the problem. It's going to realise that we are no longer at the desired capacity and it's going to launch a new instance. <br>
![3](https://github.com/user-attachments/assets/6c9c0a51-b4b8-454b-8890-784899ac6894)
 

And you can watch in the Auto Scaling Group on the activity tab for this to happen, it does take a couple of minutes.<br>
![4](https://github.com/user-attachments/assets/2af8544a-044a-4ca3-bbfb-1a16b2c2f7e1)

 
So just leave that to happen, keep an eye on the activity and then go up to instances. And in a couple of minutes time, you should see a new instance launched, just a minute or two later. <br>
![5](https://github.com/user-attachments/assets/0700b4f1-65bb-44e6-9c33-7910c3851d87)<br>
I can now see a couple of new entries in the activity log one said terminating EC2 instance and it says the instance was taken out of service in response to an EC2 health check indicating it had been terminated or stopped. That all makes sense. And now it's launching a new one. 


So if I come back up to instances, let's give this a refresh. <br>
![6](https://github.com/user-attachments/assets/60682161-1b2c-490b-bb85-62a2e3e7d9f7)
 
![7](https://github.com/user-attachments/assets/f3b8a546-5ac2-4e20-8fff-30ac5f03da3c)<br>
And now we can see we've got 2 running instances again. So that's all working exactly as expected. Now that's it for this lesson. I'm going to leave this Auto Scaling Group running because we will be attaching a Load Balancer in a future lesson.

<br>
<br>

## Amazon Elastic Load Balancing

I'm going to cover Amazon Elastic Load Balancing. So load balancers provide high availability and fault tolerance. Essentially they are a single end point. So a single DNS name or IP address behind which a bunch of different instances sit. So it's going to automatically distribute connections to those EC2 instances. Now it's not just EC2 by the way. So targets include EC2 instances, also ECS containers, you've got IP addresses as a target as well and Lambda functions and also other load balancers. So you can actually chain them together. There's some use cases where that's advantageous.<br> 
![1](https://github.com/user-attachments/assets/b29e4373-e925-4f75-8dae-9f30bf2478fb)
 

So let's have a look at it in action. So here we have a few EC2 instances deployed in an ASG an Auto Scaling Group across multiple subnets in different Availability Zones. So when users come in the Load Balancer is automatically distributing connections.<br> 
![2](https://github.com/user-attachments/assets/1b0ba586-3f23-435e-9f6d-b4929e22ac71)
If an instance fails, then it's going to be taken out of action. Now Elastic Load Balancing will perform health checks. So the target group, that's the collection of the targets that you define some characteristics for in some configuration settings. The target group is going to perform health checks. So it's going to check those instances. For example, if they are web servers, it might connect on port 80. So the HTTP port and just check a certain URL path to see if it gets a positive response, say a 200 return code. Now, if it gets that success code, then it's going to assume that, that instance is healthy and working and operational. If it doesn't, if it doesn't get a successful response, then it's going to assume that instance is out of action. So after a few tries, it's going to take it out of action and it's going to redistribute connections to a different EC2 instance. <br>
![4](https://github.com/user-attachments/assets/93429f5b-8e10-4f18-afe6-b88fca580125)
 


This is the point where the Load Balancer can also with integration into Auto Scaling, notify Auto Scaling that this instance is not responding to health checks and Auto Scaling will terminate and then replace that instance. So here User 1 is actually reconnected from Instance 1 to Instance 4 so that their session continues. The ELB takes the Instance 1 out of service because of the failed health check and then Auto Scaling is going to terminate it and then of course, it can replace it.<br> 
![2](https://github.com/user-attachments/assets/1c657943-f896-4887-ae8c-eaa25eeec397)
 


So now we have Instance 5 replacing the first instance that was terminated previously.<br> 
![1](https://github.com/user-attachments/assets/097a3d23-a9e6-45a1-baaa-429f12cf9727)
 


So now we have that level of availability and fault tolerance across multiple Availability Zones as new users come along, of course, they get distributed. <br>
![3](https://github.com/user-attachments/assets/044252b2-c35e-4ed4-8081-ef123a0f0984)
Now by default, Auto Scaling is going to try and spread the load across multiple Availability Zones and the Load Balancer sitting in front is then going to distribute connections to all of the instances. And again, through integration between Auto Scaling and Load Balancing as the Auto Scaling Group launches those instances, it's going to notify the Load Balancer. So the Load Balancer actually knows that they there and then can start sending connections through to them.



Now, we've got a few different types of Load Balancer. So there's an old one called the classic Load Balancer. So I'm not going to cover that because it's really been deprecated quite a long time ago, but it's still there in the console. So the important ones are the Application Load Balancer. So this one is a layer 7 Load Balancer, that means it understands information in the HTTP and HTTPS header, layer 7. So it can perform things like path based routing or host based routing and a few others. So path based routing is the path in the URL. So if it's /orders versus /myaccount, it can actually send the connection to a different set of targets in a different target group. So that's path based routing. And that's obviously a layer 7 function. It actually has to look into that URL. It's not just looking at IP addresses and port numbers. Now, these listeners are always HTTP or HTTPS. <br>
![4](https://github.com/user-attachments/assets/a624e623-cc18-42a7-86cf-a88608fe9d04)
Next we have the Network Load Balancer. So this is the connection level Load Balancer. So we say it runs at layer 4 because that's where these port numbers are the TCP ports and the UDP ports, for example, those protocols run up layer 4. So with this type of Load Balancer, it offers extremely high performance and extremely low latency. So watch out for those kind of keywords if you're taking an exam because they often come up. So this is the one for TLS offloading as well at scale. So it's very high performance Load Balancer. One of the other features of the Network Load Balancer, you can have static IPs so those are Elastic IPs in each Availability Zone. So that means you can hard code those, white list them in firewalls, for example. 


Lastly, we have the Gateway Load Balancer. A very different thing here. What this is actually used for, is virtual appliances. So virtual networking appliances like firewalls, intrusion detection systems, intrusion prevention systems. So we're actually able to Load Balance some of the incoming traffic through to those virtual appliances where they can perform some kind of inspection. So it's using the Geneve protocol instead here. So different Load Balancers for different use cases.<br>
![5](https://github.com/user-attachments/assets/936704f0-eab6-4a45-9539-7b0cf22716d7)
 

Let's have a look at what those might be. So for the Application Load Balancer use this one when you have web applications, HTTP and HTTPS and you need that sort of layer 7 routing capability. It's good for micro services architectures like Docker containers, Lambda targets, which are an option with the ALB. 
With the NLB, TCP and UDP based applications extremely low latency, high performance and static IP addresses as well as VPC endpoint services as well.<br>
![6](https://github.com/user-attachments/assets/6a46bcdc-59e7-4dfd-ac70-ca1523fcbfbe)

 
And then lastly for the Gateway Load Balancer. So this is where you want to deploy scale and manage third party virtual network appliances. It gives you centralised inspection and monitoring capabilities. So we're talking about firewalls, intrusion detection, intrusion prevention systems, deep packet inspection systems and other similar virtual network appliances.<br>
![7](https://github.com/user-attachments/assets/4df83259-901c-4f36-9861-2c3fe4a1484b)
 
<br>
<br>



## Create an Application Load Balancer

I'm going to create an Application Load Balancer. Now we've already got an Auto Scaling Group running. So you should have 2 instances across two Availability Zones. We're going to put an ALB in front of our Auto Scaling Group and we're going to direct traffic to the Application Load Balancer. Then in another lesson, we will adjust the Auto Scaling configuration to add a Scaling Policy. And we're going to test actually adding some load to our Auto Scaling Group. 

Back in the EC2 management console.<br>
![8](https://github.com/user-attachments/assets/b9c396b0-74b6-4d0b-a54b-4600da4ef808)<br>
If I just give this a refresh, I should have my 2 instances running and they're running through my Auto Scaling Group. Now, the first thing I need to do for Load Balancing is go ahead and create what's called a Target Group.<br> 
![9](https://github.com/user-attachments/assets/11f40860-60a2-4933-a3ff-dd06872c0f23)


So the Target Group is going to contain the instances. <br>
![1](https://github.com/user-attachments/assets/b79eac92-0762-4835-a9c2-60b2d12ccddc)
 

Let's click on Create target group here. We need to choose what the target type is. We've got Instances, IP addresses, Lambda functions and Application Load Balancer. <br>
![1](https://github.com/user-attachments/assets/6cda43a6-e870-419a-8881-5ad31128bf48)

 
Depending on our scenario and the Load Balancer we're using, some of these options might not work. But in this case, Instances is compatible with our Application Load Balancer. We can see for example that Lambda functions are a possible target as well, but only for Application Load Balancers. <br>
![2](https://github.com/user-attachments/assets/88a22bef-792d-47b2-b34e-7d687a8eac17)

 
And you can even use an Application Load Balancer as a target for a Network Load Balancer. <br>
![3](https://github.com/user-attachments/assets/c78e3635-6095-44ba-a731-7af3fb788f4e)
 

So let's make sure that Instances is selected.<br> 
![4](https://github.com/user-attachments/assets/c76549d8-f3e2-4731-85ca-99d62b733220)
 

We'll give it a name. I'll call it TG1. <br>
![5](https://github.com/user-attachments/assets/4e2af302-fc0d-43c2-9c30-5f755d886c2c)


Here we need to choose the protocol. <br>
![6](https://github.com/user-attachments/assets/ca7c4dd6-3f5e-42d5-9672-9512ecbaa487)<br>
![7](https://github.com/user-attachments/assets/b0c89313-ef6e-48b4-aa33-74bc0f62a817)<br>
In this case, it's going to be HTTP because we're going to create an Application Load Balancer. So HTTP port 80 that's where my web server is running. 
So I want traffic to come in on HTTP port 80. 

Next for IP address type. I'll leave it on IPv4. <br>
![8](https://github.com/user-attachments/assets/da979d86-eb58-4e60-a2c6-c25562ccdc1e)<br>
HTTP1. 


The health check protocol is going to stay on HTTP and /.<br>
![9](https://github.com/user-attachments/assets/0aaf6369-b69c-4b4a-a2bf-1be52d3040ea)<br>
Health checks are performed by the Load Balancer, to check that the instances are actually healthy, they're operational. In this case, it's going to check on the default port for HTTP which is port 80 and it's just going to check the route of the website. You can add a path on here, if you want to, to check a certain path or a document.  

You can also set advanced settings, like the thresholds for unhealthy instances before they're taken out of operation, for example. <br>
![1](https://github.com/user-attachments/assets/c1ec4dcd-613d-4271-a63f-e28507b15d4c)<br>
So I'm going to leave those as default settings. 

Let's click on next. <br>
![2](https://github.com/user-attachments/assets/09bbd0c8-93a8-4a5f-afd1-dcbbaf75959b)

 
Here you can add your instances by including them as pending below, but we don't want to do that. <br>
![3](https://github.com/user-attachments/assets/5b35ede0-296e-4767-841e-b059b4d68ed6)
 

I'll show you why in a moment, what we want is, we want a dynamic assignment so that every time the Auto Scaling Group launches new instances in response to changes in demand or a failed instance for example. It's going to automatically add them into the target group. If we do it here, we're statically defining which instances should be in the target group. So don't do that. Let's just create the target group.<br> 
![4](https://github.com/user-attachments/assets/b1c8c96c-a213-4f03-a2ad-ca7b190c30e2)
 
![5](https://github.com/user-attachments/assets/d92aaa5e-507e-4852-b127-afa68419770b)

 

Now, we can create our Load Balancer. <br>
![6](https://github.com/user-attachments/assets/0a7ba227-5690-4df4-929b-9176b68a5548)
 

So let's choose Load Balancers on the left, create Load Balancer. <br>
![7](https://github.com/user-attachments/assets/56da4284-0a7f-40e2-95ea-3caf99a9eae5)
 

We need to choose which Load Balancer we want. <br>
![8](https://github.com/user-attachments/assets/c6924488-861a-456a-b137-4f08cd161aae)<br>
We want an Application Load Balancer for this use case. 


So let's click on create.<br> 
![9](https://github.com/user-attachments/assets/bbe450ca-c6d1-4dbe-98fe-52db096f52ad)

 
I will call it simply ALB1.<br>
![1](https://github.com/user-attachments/assets/d08fca78-4cbb-4922-89c0-77e3c135a6b9)<br>
It's going to be Internet-facing, Internal would be for, for example, maybe your application logic layer or your back end is in a different private subnet and you are load balancing traffic internally. In this case, it will have a public DNS name that we can connect to from the Internet. 


IP address type will be IPv4.<br>
![2](https://github.com/user-attachments/assets/01c63e58-97d3-4693-8653-9ee856eeba3a)
 

VPC is the default.<br>
![3](https://github.com/user-attachments/assets/637623f2-0919-4039-a4b8-1d9ae5d3209b)

 

I'm going to select the us-east-1a and 1b subnets and Availability Zones because that's where my instances are actually deployed. <br> 
![4](https://github.com/user-attachments/assets/78e29650-aee2-423a-81cc-74318f8e8164)
 

For security groups, I deselect this option and then add in the WebAccess security group. <br>
![5](https://github.com/user-attachments/assets/8a4c361c-2a87-496d-992a-d7fbd46ce493)
 
![6](https://github.com/user-attachments/assets/285ebd78-25ab-4751-8e08-60f1ae89e430)

![7](https://github.com/user-attachments/assets/63e733c0-340c-432c-a93f-9300456afe8c)<br>
So not the default, change it to your WebAccess security group. 

Then we have to define the listeners and the routing. <br>
![1](https://github.com/user-attachments/assets/8e90d3d6-bd5a-491d-8565-519d7f3fa52f)
![2](https://github.com/user-attachments/assets/7c961a74-2266-41e3-89b3-ca7e82a175c3)<br>
So the Load Balancer is going to listen on a certain port and protocol. It's listening for connections using the HTTP protocol on port 80.When it receives those connections on the listener, it's going to route the connections through to the target. The target is TG1, the target group. In other words, the instances that are attached to that target group. 
So that's all we have to do here. We can come down and create the Load Balancer.<br>
![3](https://github.com/user-attachments/assets/90fec91d-fb78-4c80-b655-fb5a0dc76e13)


Load Balancers take a few minutes to become operational.<br> 
![4](https://github.com/user-attachments/assets/79cad3dc-6a52-48e6-b248-06f7b2d7fe74)
 

So on this page here, we can see some of the details of the Load Balancer. 

If you come up a level.<br>
![5](https://github.com/user-attachments/assets/ccfca728-ef39-4937-bd18-fb6ecc97d8f1)
  

You just see the basics and you can see that it's provisioning. <br> 
![6](https://github.com/user-attachments/assets/23448c31-5923-4b01-bd5e-5d5b0d5399f6)<br>
Here we have the DNS name. So the DNS name is what we're going to connect, to the Load Balancer with, once it's up and running. It should become active after a few minutes. 

Another place you can go to see what's going on is the Target Group.<br>
![7](https://github.com/user-attachments/assets/ff9d8b8d-11c5-40d0-b3b5-64d9a23570b2)


So we come back to Target groups. Click on TG1. <br>
![8](https://github.com/user-attachments/assets/f7145761-7b08-49a6-8210-163c178b0659)

![1](https://github.com/user-attachments/assets/3c83b22e-61a0-432c-858e-2e778f04456f)

![2](https://github.com/user-attachments/assets/b64d5514-5f1e-48f8-923e-01ad0e7ca62e)

![3](https://github.com/user-attachments/assets/cf4f419b-e63a-4cc8-8b64-7446ec576d39)
 

Here we've got targets. If I click on refresh, there's no targets. <br>
![4](https://github.com/user-attachments/assets/c391c808-834e-42f1-bf1b-301e2ae30310)

 
Now, remember I said that I wanted to set this up. So the Auto Scaling Group was going to automatically register the targets. So let's go and do that. We're going to come back to Auto Scaling, click on the Auto Scaling Group. <br>
![5](https://github.com/user-attachments/assets/e4cc35e9-8fef-4095-9276-6ea11d2d3106)
 
![6](https://github.com/user-attachments/assets/e93658db-41e4-48f4-9ba1-890b040b7c7c)

![7](https://github.com/user-attachments/assets/27258cb8-86dc-4997-b1b4-17b04ee56ca2)

 
I'm going to scroll down the page a little bit to where it says Load Balancing, click on edit, select Application Network or Gateway Load Balancer target groups and then select TG1 and click on update. <br>
![1](https://github.com/user-attachments/assets/718c9dd9-6233-48bb-a663-672e4eda4ae2)
 
![2](https://github.com/user-attachments/assets/72740e29-e303-44b1-a7c4-3fed7b590781)

![3](https://github.com/user-attachments/assets/9b579f8b-48e0-49f6-ab1a-3fdb874d6737)
 
![4](https://github.com/user-attachments/assets/59a7150e-d4ab-44ff-b807-adb2b0b84860)

![5](https://github.com/user-attachments/assets/3b6bed50-3d07-48ea-b61c-d0d81941fc46)



Now, let's go back to Target groups. <br>
![6](https://github.com/user-attachments/assets/6bc429a3-c4e2-4987-a1e3-5e705a21dfb5)

 

And on my Target group page, I'm going to give this a refresh and let's see if we've got any targets.<br> 
![7](https://github.com/user-attachments/assets/8acf9ee5-323a-4c61-8b32-95df0e32c67b)
 


We go to targets refresh here and there we go. <br>
![8](https://github.com/user-attachments/assets/14e53dbe-4837-4a0b-b817-a1e5beddcc1c)

![1](https://github.com/user-attachments/assets/ae8d6bfa-721e-4630-977e-633eca68320c)

 
We've got 2 targets that have now been registered in the target group.<br>
![2](https://github.com/user-attachments/assets/32bfb70b-ae2c-46d9-b87e-05932122538b)<br>
Now, the health status is Initial, the Load Balancer is not ready yet. So the health check hasn't even begun once the Load Balancer is ready, which should be quite soon, then the health status should change to healthy as long as that website is up and running and the security groups are set up correctly. Because remember we need to be able to perform this health check on the health check port and protocol that is HTTP and the path is a /, HTTP uses port 80 by default. <br>
![3](https://github.com/user-attachments/assets/cd49383e-9af3-487c-9c2d-baecf12aab1f)<br>
So the Security Group for the Instances must allow connections on the HTTP port. 

Let's come back, give it a refresh.<br>
![4](https://github.com/user-attachments/assets/4c525b14-a87a-4601-ae0f-a4bd34f30c37)

 
It's not going to be long now and very soon we will see this change to healthy. Once we see healthy instances.<br>
![5](https://github.com/user-attachments/assets/0c73da53-2cea-499b-9b3d-e98478c2b356)


It happened dynamically, we are now ready to actually test the Load Balancer. <br>
![6](https://github.com/user-attachments/assets/10bf313a-33aa-43a8-8c43-8d3489994a85)
 


So I'm going to come back to the Load Balancers here, select my Load Balancer, click on DNS name to copy the DNS name to my clipboard.<br> 
![7](https://github.com/user-attachments/assets/1090964b-c8d0-43cc-b6b0-072d2d01ec76)<br>
And then let's go over to new browser window, hit enter and we can see that we've hit the webpage of one of our instances in us-east-1a.<br> 
![8](https://github.com/user-attachments/assets/9895d7a4-0e57-4279-8764-938721f390b5)

![9](https://github.com/user-attachments/assets/f0cea2dc-ff3e-4558-b146-251e8b809357)


If I refresh the page, it changes to us-east-1b.<br>
![1](https://github.com/user-attachments/assets/babe1ce9-bd3a-45ca-af49-4e33e51b9352)

 
So I've been Load Balanced between two different Availability Zones, essentially 2 different data centres.  
If I just keep refreshing, we can see I keep getting Low Balanced across those 2 Availability Zones. So we have the traffic being spread equally between those 2 AZs. So that's it for this lesson, the Load Balancer is up and running, Auto Scaling is working.  In the next lesson, what we're going to do is create a Scaling Policy. We're going to add a whole bunch of load to our front end so that we then generate more back end load which will cause the Auto Scaling Group to react and scale.

<br>
<br>



## Create a Scaling Policy

In this lesson, we're going to create a Scaling Policy so that we can set some dynamic scaling on our Auto Scaling Group and then generate some load and cause it to scale. I'm back in EC2. <br>
![2](https://github.com/user-attachments/assets/5fab74a0-529b-4cbf-af4b-711893eecf8f)

 
I have my Auto Scaling Group with 2 instances running. I have a Load Balancer in front that's running as well, so we can generate connections to the Load Balancer and it's currently Load Balancing us between 2 instances. So what we want to do is adjust the Auto Scaling Group and create a Scaling Policy. <br>
![3](https://github.com/user-attachments/assets/b17dac11-f1a2-4151-b712-e3938623bd5a)
 
![4](https://github.com/user-attachments/assets/b08a0165-b680-4aec-a6ec-8de8c937aedb)

 

Now there's no point creating a Scaling Policy when our desired Min and Max are all the same. 



Firstly, we have to change that. <br>
![5](https://github.com/user-attachments/assets/a02a73f3-7bcd-4755-b958-0731bf14c6d0)

 

So in the Auto Scaling Group, I'm going to click on Edit. <br>
![6](https://github.com/user-attachments/assets/95f572ef-b643-4b94-93d9-bbab323a7375)
 

And I'm going to increase my Max desire capacity to 4. <br>
![7](https://github.com/user-attachments/assets/e4ce4ccb-33f5-4076-ac8e-2ed5cb13be76)
 


So now I have 4 instances, I'm going to potentially run and let's click on update. <br>
![8](https://github.com/user-attachments/assets/ccaa0d24-8718-4bb9-acfc-9b6ac307eac6)
 
![9](https://github.com/user-attachments/assets/6da0be67-25e6-4ea2-9d1a-7b112c50e201)

 
Also, I want to make sure that we spread these across more Availability Zones. <br>
![1](https://github.com/user-attachments/assets/69e2be34-1861-4033-9a94-39f9909768b9)<br>
So I'm going to Edit network here and I'm going to add in us- east- 1c and us-east- 1d.<br>
![2](https://github.com/user-attachments/assets/849fb3dc-71df-400a-be5a-87aba72c43c6)

 
So now I've got even better high availability, but I also have to do the same thing for the Load Balancer. <br>
![3](https://github.com/user-attachments/assets/792d0d4c-7612-489a-9b31-9756409a9a22)
 

So on the Load Balancer front end, we can select the Load Balancer.<br>
![4](https://github.com/user-attachments/assets/d1d6cd68-c663-486b-a3c0-ac0806ca3f47)
  

Click on Network mapping and then click on Edit subnets on the right hand side.<br>
![5](https://github.com/user-attachments/assets/378b28cb-e34d-445e-91f1-330524d18a3c)
 

So we need to make sure that the Load Balancer is also going to distribute connections to us-east-1c and us-east-1d and then we can save changes. <br>
![6](https://github.com/user-attachments/assets/d6ad45e1-9ce6-4ba3-bb35-e1f627ff4e8c)

![7](https://github.com/user-attachments/assets/8c4a943f-a8fc-4fb8-bd38-1fa710ccef90)
 

So now we've adjusted the Load Balancer and the Auto Scaling Groups networks. <br>
![8](https://github.com/user-attachments/assets/5c446b17-21cd-421f-b6dc-d4339a5235f1)

![9](https://github.com/user-attachments/assets/bbc6a8ff-f30a-445c-9c5d-8185985dbe83)
 


The next thing to do is go to Automatic Scaling.<br>
![1](https://github.com/user-attachments/assets/5cdb7d31-603d-45f2-823c-350d0cbb35b3)
 

And we're going to create a dynamic scaling policy. So I'm going to click Create dynamic scaling policy.<br>
![2](https://github.com/user-attachments/assets/d1ec74ce-d74c-4112-8cea-99837e8b101b)
 


Here we have some different options, target tracking, step and simple.<br>
![3](https://github.com/user-attachments/assets/e8a32ea0-d940-4857-b59b-33156baa2852)

 

I'm going to leave the Target tracking scaling selection and rather than CPU utilisation, I'm going to change it to Application Load Balancer request count per target.<br> 
![4](https://github.com/user-attachments/assets/3a30327f-633c-40f0-8371-dc8e90604700)



That's the number of connections that are reaching the targets. I'm going to select my target group. <br>
![5](https://github.com/user-attachments/assets/048613a8-5b55-414e-9281-877cfd43402d)
 

I'm going to leave this value at 50, so 50 connections per target. <br>
![6](https://github.com/user-attachments/assets/d14a69be-8ec7-4582-b6af-68cf26e9c061)<br>
If we exceed that number, it's going to start scaling. Then I'm simply going to create this Scaling Policy. <br>
![7](https://github.com/user-attachments/assets/7b428064-ed25-4481-8801-1b8488f96bd5)
 
![8](https://github.com/user-attachments/assets/ed0c2e3e-87a9-45b2-989c-41b9dae0f2b8)<br>
So that's ready once we created the Scaling Policy.



If we head across to the CloudWatch service.<br>
![9](https://github.com/user-attachments/assets/c4949d67-1093-4892-918a-1557d59340c8)

 

We will see that it's created some alarms for us.<br>
![1](https://github.com/user-attachments/assets/2d808c83-8bfd-4a0e-bf5e-83f6edaadf1e)

 

So we head into CloudWatch. This is a performance monitoring service. Under alarms, if we go to All alarms, we can now see these 2 target tracking alarms have been set. <br>
![2](https://github.com/user-attachments/assets/6f3a3a8f-922e-4726-bb91-1d4f6dacbc28)
 

It says insufficient data, it hasn't really received enough information yet to have an opinion. AlarmHigh is going to be triggered when the Request count per target is greater than 50 for 3 data points in 3 minutes. <br>
![3](https://github.com/user-attachments/assets/7c34fb1d-fe03-4dbc-97f3-0b616fca6a1d)
 

That means it's going to scale out. But we got to wait, we got to generate load and then we've got to wait for a few minutes so that those data points come in. Then after a while as the request count per target gets lower under 45 for 15 data points within 15 minutes, then it will scale back in again. <br>
![4](https://github.com/user-attachments/assets/c5f5bc49-0c51-4afe-bf48-19814ef5bd16)
 

Scaling in is a bit slower than scaling out. We want to make sure that we have enough load and we make sure that maybe that spike in demand is actually over before we scale back in again. So this is all set up correctly. We're ready to scale. 



What we need to do is come back to our Load Balancer. <br>
![5](https://github.com/user-attachments/assets/7b66889b-1251-4aba-9f81-654f674caf7c)

 

And what I'm going to do is just bring this down.<br> 
![6](https://github.com/user-attachments/assets/18cde04a-3804-4b48-9431-95c84c3b916a)<br>
I'm going to copy the DNS name for the Load Balancer. <br>
![7](https://github.com/user-attachments/assets/2e03403c-da06-4842-a925-39fbd2b1ee55)

 <br>
 <br>

### In the course download in the Amazon EC2 directory.<br> 
#### Command to generate load on the ALB.

**replace with your alb dns name** <br> 
*for i in {1..200}; do curl http://your-alb-address.com & done; wait*

<br>

We've got this generate-load-on-alb.md file. In here, we have this command. What we need to do, is we need to replace this address here. <br>
![1](https://github.com/user-attachments/assets/2f5e11c6-b9da-47aa-9f68-60848fa68235)<br>
So I'm just going to paste this in. <br>
![2](https://github.com/user-attachments/assets/d8f68bcd-57c7-422d-a3d8-5e52865ffcb1)<br>
And in fact, I do need to keep the http://<br>
So just pop the DNS name in here and this is a *for loop*, what it's going to do, it's going to create 200 connections to the Load Balancer. So using the curl command and we'll just run this several times. I'm going to copy this whole command, not using these little dashes on either side.<br> 
![3](https://github.com/user-attachments/assets/0e0c97d4-273c-48a3-9b2a-92e71b969171)<br>
So just copy that command. 

We're going to come back to the console and I'm going to use CloudShell.<br>
![4](https://github.com/user-attachments/assets/2bf1e9fc-8bf5-4e3b-8634-b9a337a4c8cf)<br>
You can just do this from your computer as well on the command line if you wish to. 
But I'm just going to use CloudShell. So let's open up CloudShell. 

I'm going to adjust the font size here, make it a bit bigger. <br>
![5](https://github.com/user-attachments/assets/7c102e33-e361-4447-9bcb-2d848cc4de84)
![6](https://github.com/user-attachments/assets/50c4b41e-c9db-4296-958a-8e0d6a0eb1fe)<br>
So you can see what's going on.  


And I'm going to paste in this command. <br>
![1](https://github.com/user-attachments/assets/4b3c4369-05db-4dbf-be91-76ceb21d3896)<br>
Then press enter. 

So what's happening is it's actually bringing back the web pages very quickly. <br>
![2](https://github.com/user-attachments/assets/bb2d948e-cb61-492f-8db2-22eb4604bb7a)
 
![3](https://github.com/user-attachments/assets/66c0fe67-4e8b-4f72-b7b2-96365652b7d2)

 
It's actually pulling back everything that's on that web page. So we can see lots of connections are being made to the Load Balancer. So I'm just going to run this command several times. We're generating load. Remember, we have to generate quite a bit of data. More than 50 connections per target. We've got 2 targets. So running this a few times is easily going to exceed that amount. But we have to wait for the collection of 3 data points in 3 minutes. So it's still going to take several minutes. I just keep running this a few times and then eventually we'll see what's happened and we should find that we have an alarm that has changed into an alarm state. <br>
![4](https://github.com/user-attachments/assets/02835d52-6eb1-4e65-a23b-779ccef25cea)<br>
So at the moment we see we've got ok for the high that will change to alarm state at some point soon. So run that a few times, just make sure you keep running that command several times and then we're going to have a look in a few minutes time and see what's happened. 



Back on the Load Balancers page.<br>
![5](https://github.com/user-attachments/assets/42a54b03-327f-4dd9-86db-547e243be4bc)<br>
I've headed over to the monitoring tab and you can see this massive spike in requests. <br>
![6](https://github.com/user-attachments/assets/a70f470e-3432-4783-a4d9-66ff1d0a3909)
 
So we know that this information is coming in. 

You can also go to instances as well and you can have a look at the monitoring tabs for the instances. <br> 
![7](https://github.com/user-attachments/assets/797f5109-a5bd-4a47-ab8a-349f58a1a05c)
 
![8](https://github.com/user-attachments/assets/609f3a7c-4f67-47d7-8278-9d9e43f0e68e)



You can see CPU utilization, Network in, Network out.<br>
![9](https://github.com/user-attachments/assets/a32d4621-1484-4408-95ed-5de4fd7a2a6b)<br>
So we're not exactly seeing requests here, but you can see some of the load that's being generated. Not a huge amount of load because it's very simple to serve that web page. 

Now, if we go back to the CloudWatch console.<br>
![1](https://github.com/user-attachments/assets/405104f0-a6c1-4709-9507-a40a8497b9f7)
  

It's still not quite there yet. So I'm going to run this a couple more times and then we should find that it changes to an alarm state fairly quickly. <br>
![2](https://github.com/user-attachments/assets/658f2db1-53b9-46e6-8781-5e97e1a048ff)
 
![3](https://github.com/user-attachments/assets/a2ce1af4-59b6-4b1d-a5af-02a3d97507a7)

 

Here we go. The CloudWatch alarm is now in the in alarm state.<br>
![4](https://github.com/user-attachments/assets/67374fd4-4f5a-47ff-847a-1fe6f9d6fcb7)<br>
If I click on the alarm, we can see some monitoring information here as well. 

So we can see this massive spike in requests that came in.<br> 
![5](https://github.com/user-attachments/assets/e08cf5e5-f732-4522-b005-1692f917ad94)



So let's head back and have a look at the Auto Scaling Group and see what's going on there. <br>
![6](https://github.com/user-attachments/assets/b67204a1-834c-4b6a-b709-438937b3d4a5)<br>
So we come back down to Auto Scaling, click on the Auto Scaling Group go to activity and we can see that it's already picked up that change. <br>
![7](https://github.com/user-attachments/assets/211e8b92-9117-4910-9d76-0a2c122100b3)

![8](https://github.com/user-attachments/assets/2ad6d883-6b1a-4e28-9691-3625c9c7051a)
 

 
There are a couple of different entries here about launching a new instance and that's based on the target tracking alarm, high alarm, which has been triggered. <br>
![1](https://github.com/user-attachments/assets/a037d0c3-0e28-4cb8-b832-f39410f5c116)

 

And we can see if we go back to the details here, that now the Desired has been changed to 4.<br> 
![2](https://github.com/user-attachments/assets/94455e86-d04d-4531-a94a-08c2ce81899b)<br>
So that's automatically been adjusted for us. 

If we head up to Instances, we can see here, let's refresh.<br> 
![3](https://github.com/user-attachments/assets/52c55b5d-818a-450f-8c65-676c8315331a)
 
![4](https://github.com/user-attachments/assets/431e0a49-3c93-4de3-ac22-776dbca6b8eb)

 
We should see that we now have 4 running Instances.<br> 
![5](https://github.com/user-attachments/assets/40639d4f-6427-4a11-ba4e-60b00e85b809)

We can also go and check if they've been registered into the target group. <br>
![6](https://github.com/user-attachments/assets/d749ab67-47a2-4685-89c3-31ab41ccf8d6)<br>
So Target Groups, let's refresh here and they're not quite there yet. <br>
![7](https://github.com/user-attachments/assets/dc6ec98c-2bab-402d-ba23-551fa8f9bdb7)
 

So we've got 1, 2, 3, 4.  We have four healthy instances.<br> 
![8](https://github.com/user-attachments/assets/adb8fbfd-b399-4cba-99b1-9df31b4ec198)
 

So let's go back to Load Balancers. <br>
![9](https://github.com/user-attachments/assets/33596137-f483-4796-b53c-72a4ab6b8bd0)
 

Copy the DNS name again, put this into a browser window.<br>
![1](https://github.com/user-attachments/assets/5ecc945a-0589-44c8-a426-5a5088ed5339)

![2](https://github.com/user-attachments/assets/6b02e6d3-01e3-4cf2-a50f-adaa5503d544)<br>
Hit enter. 

Now we should cycle between A, B, C and D. Not necessarily in that order, but we're certainly being cycled and Load Balanced across 4 different Availability Zones now. <br>
![1](https://github.com/user-attachments/assets/a1532649-96ae-4864-a889-729507ca788c)

 
So essentially 4 different data centres.  
There we go 4 different instances. So that is Auto Scaling and Load Balancing. We can see that now our application is going to dynamically adjust to demand. It's going to launch and terminate instances through Auto Scaling to make sure we have the right amount of capacity and then the Load Balancer is automatically going to pick that up and send traffic to those various targets. 

So I have finished with this lab. All I need to do is just go and terminate a few resources. So what we're going to do is come back to Auto Scaling Groups. <br>
![2](https://github.com/user-attachments/assets/e11edfbe-9fa6-4e6c-8f10-8f7952cfc4ea)<br>
And I'm going to delete the Auto Scaling Group. <br> 
![3](https://github.com/user-attachments/assets/5483b92e-fd44-4da5-802e-3bf9c93d44bf)
 
![4](https://github.com/user-attachments/assets/562b7951-1c99-4033-81cb-6c98c8b958b3)


Now, what you'll find is that because these instances are associated with a Load Balancer, it's not going to terminate them immediately. <br>
![1](https://github.com/user-attachments/assets/24b46989-eefe-414d-b512-1e0c707d1196)
 
![2](https://github.com/user-attachments/assets/aeba0ec2-383d-4b8f-9944-4b32606c8a9b)

![3](https://github.com/user-attachments/assets/d6ef9223-a3c2-4ba2-9281-42465dabf0e5)
 
![4](https://github.com/user-attachments/assets/932b095d-fb39-4304-8fde-6a5072ef9abc)



In fact, if we go here, it's going to tell you that connection draining is in progress. So what that does is it just waits to make sure just in case there's still some open connections to the instances. So that will take a couple of minutes. Don't worry if they don't get terminated immediately, that is normal, but they will be automatically terminated by Auto Scaling. 
If you want to hurry things up, you can go and just terminate them through the Instances page as well. <br>
![5](https://github.com/user-attachments/assets/e208b90d-74d3-4508-b19c-666e9fcf106e)
 

The other thing I need to get rid of is the Load Balancer. <br>
![6](https://github.com/user-attachments/assets/54dd0dd7-9b32-4aae-9e30-4fc32a118ab3)
 
![7](https://github.com/user-attachments/assets/5d29a07b-a9f6-4b9f-a79b-74c51e1c0617)



These are the two things that could end up costing us money if we leave them running for too long. <br>
![1](https://github.com/user-attachments/assets/168b78c4-5591-4edf-9a27-4ccf158b90ff)<br>
So that's gone. 

The target group doesn't cost us anything. <br>
![2](https://github.com/user-attachments/assets/65d14653-2a67-4506-adc7-682f9e8efb59)
 


The instances will be automatically terminate when they're out of service. <br>
![3](https://github.com/user-attachments/assets/62a8c690-70f0-4c66-b242-d2e7f77a3186)<br>
We see they're in a draining status at the moment. 

Likewise, the launch template doesn't cost us anything. It's there if we need to use it again at some point in the future.





Command to generate load on the ALB <br>
**replace with your alb dns name** <br>
*for i in {1..200}; do curl http://your-alb-address.com & done; wait*

**user-data-web-server.sh**

#!/bin/bash

**#Update the system and install necessary packages**<br>
*yum update -y*<br>
*yum install -y httpd*<br>

**#Start the Apache server**<br>
*systemctl start httpd*<br>
*systemctl enable httpd*<br>

**#Fetch the Availability Zone information using IMDSv2**<br>
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
AZ=`curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone`

**#Create the index.html file**<br>
![Capture](https://github.com/user-attachments/assets/add80717-33ad-47a5-a5d6-f3fcab84cbf3)

**#Ensure the httpd service is correctly set up to start on boot**<br>
*chkconfig httpd on*
