# Host-a-wordpress-website-on-AWS

In this project, i showed how to deploy a dynamic website on aws using a lamp stack project reference architecture. AWS services used includes; VPC, Security group, Ec2, Natgateways, RDS, Application Loadbalancer, Route 53, Autoscaling Group, Certificate Manager, EFS and more

# LAMP STACK PROJECT REFERENCE ARCHITECTURE
![1 _LAMP_Stack_Project_Reference_Architecture](https://user-images.githubusercontent.com/115881685/225284346-905bb80b-ad99-4b5c-9f28-832aa455dde3.jpg)

# Create CUSTOM VPC
To kick off the project, i built a custom VPC, see reference architecture below
![2 _VPC_Reference_Architecture](https://user-images.githubusercontent.com/115881685/225285355-25409fca-777c-4784-b8de-23e4dcb70191.jpg)

To create the VPC, go to the AWS console a type vpc in the search box and select vpc. In the VPC dashboard, select VPC, then click create VPC.
Name the VPC "Dev VPC". In the IPv4 cidr block space, type "10.0.0.0/16". Leave everything as default and scroll down and click create vpc.

![image](https://user-images.githubusercontent.com/115881685/225288822-88cf185b-e21f-495c-b72d-84fd55696d3d.png)
![image](https://user-images.githubusercontent.com/115881685/225289117-2781efec-f4eb-42b6-b70b-faac8863321d.png)
![image](https://user-images.githubusercontent.com/115881685/225289229-2cb26b84-533e-49b6-acf3-fbe68687aad5.png)

![image](https://user-images.githubusercontent.com/115881685/225289405-5f49df39-ef2e-4fbc-b1bc-1f88f8385f59.png)

Next step is to eneble DNS hostname in the vpc, to do that, select "action" and then edit "vpc setting". In the next page that opens, check the enable "DNS hostname" box and scroll down and hit save button.

![image](https://user-images.githubusercontent.com/115881685/225291345-c31135a1-fa3a-4846-9bac-3a3e72cf1ff7.png)
![image](https://user-images.githubusercontent.com/115881685/225291408-914debf2-26c2-4844-b03a-d971f6255a57.png)


### create Internet Gateway

On the vpc dashboard, click on internet gateway and hit create internet gateway. Name it "Dev Internet Gateway", then scroll down and hit create internet gateway.

![image](https://user-images.githubusercontent.com/115881685/225293323-2a957e58-e968-4d79-ae90-c4aa55883d03.png)
![image](https://user-images.githubusercontent.com/115881685/225293451-87a74411-1ddd-4cc6-b793-d6b1a23f1f51.png)

Next step is to attach the internet gateway to the vpc that was created, to do that, click on "attach to a vpc" or  "action".
Under "available vpcs", select the Dev VPC that was created, and the scroll down and click "attach internet gateway".

![image](https://user-images.githubusercontent.com/115881685/225295224-23de14b1-fbe9-4e38-9a31-386cfd680595.png)
![image](https://user-images.githubusercontent.com/115881685/225294860-ccab48fd-65a2-45d0-b749-0eb458b2ec7f.png)
![image](https://user-images.githubusercontent.com/115881685/225295395-8e663aa2-607e-4d47-a3f3-c472ce689cc1.png)


### Create Subnets.
Next step is to create a subnets in the first and second availability zones according to the vpc refference architecture. To do this, select "subnet" in the vpc dashboard and click "create subnets". Under "vpc id", select our "Dev VPC" and the scroll down to "subnet name", according to our reference architecture, it should be called "public subnet AZ1". Under "availability zone", select "us-east-1a". Under IPv4 Cidr block, type in "10.0.0.0/24". scroll down and click "create subnets"

![image](https://user-images.githubusercontent.com/115881685/225298356-e7b1d44f-c588-4aa2-bdb7-778ef1f33487.png)
![image](https://user-images.githubusercontent.com/115881685/225298547-6dd77db0-f912-458d-b68a-78a1f043fbaa.png)
![image](https://user-images.githubusercontent.com/115881685/225298768-26513a48-edc6-4206-89a9-b9fa9bf3bc96.png)

For our second subnet, follow the same steps as the first one, under "subnet name", call it "public subnet AZ2", for availability zones, select "us-east-1b", Under IPv4 Cidr bock, type, "10.0.1.0/24 and then scroll down and hit create subnets.
To see the two subnets, filter by the vpc created.

![image](https://user-images.githubusercontent.com/115881685/225300423-b79e295f-951a-4718-94b2-99b14af0aec3.png)
![image](https://user-images.githubusercontent.com/115881685/225300692-99f0dfa5-06fb-413b-9638-dcb44e307e12.png)
![image](https://user-images.githubusercontent.com/115881685/225303279-3a1661a4-9914-4dd0-b20f-242012fffa96.png)


#### enable auto-assign ip for the subnet
To do this, select the first subnet and click "action", click "edit subnets settings", under "auto-assign ip setting", enable "auto-assign public ipv4 address", then scroll down and click save.

![image](https://user-images.githubusercontent.com/115881685/225302758-e31a1a10-5c75-4230-8eed-78cf372e3e8c.png)
![image](https://user-images.githubusercontent.com/115881685/225303472-a8dd6b08-754d-477c-867e-11634ddb6e32.png)
![image](https://user-images.githubusercontent.com/115881685/225303577-5a4327c8-3303-4aac-b542-284dda1f7538.png)

Repeat the same step for the second subnet.

### Create route table.
To create route table, select routetable on the left hand side of the vpc dashboard and click "create route table".
Note there is always one main route table created along with the vpc creation process.
Under "name", call it "public-route-table", under vpc, select our "Dev-vpc", then scroll down and hit "create route table"

![image](https://user-images.githubusercontent.com/115881685/225306384-ebaf6e45-99bd-4c06-accd-874b7b500bee.png)
![image](https://user-images.githubusercontent.com/115881685/225306515-d4eb6358-d01e-4ede-94b0-5b5e8dba17ea.png)
![image](https://user-images.githubusercontent.com/115881685/225306742-0b7be1cc-7606-4d66-91a3-c5b25f779c11.png)








