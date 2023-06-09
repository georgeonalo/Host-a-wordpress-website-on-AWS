# Host a wordpress website on AWS

In this project, i showed how to deploy a dynamic website on aws using a lamp stack project reference architecture. AWS services used includes; VPC, Security group, Ec2, Nat gateways, RDS, Application Loadbalancer, Route 53, Autoscaling Group, Certificate Manager, EFS and more

# Lamp stack project reference architecture
![1 _LAMP_Stack_Project_Reference_Architecture](https://user-images.githubusercontent.com/115881685/225284346-905bb80b-ad99-4b5c-9f28-832aa455dde3.jpg)

# Create VPC with Public and Private Subnets
To kick off the project, we will build a custom VPC, see vpc reference architecture below which is three(3) tiered enabled.

In a 3 tier vpc reference architecture, the infrastructure is divided into 3 tiers, the first tier contains a public subnet which holds a nat gateway, bastion host and loadbalancer. In the second tier, we have a private subnet which hold the webservers(ec2 instances), the third tier contains a private subnet, which holds the data base. All this subnets are replicated accross multiple availability zones to provide high avalaibilty and fault tolerence. Then finally we will create an internet gateway and route table, this will provide resourses in the vpc with internet access.


### VPC Reference Architecture

![2 _VPC_Reference_Architecture](https://user-images.githubusercontent.com/115881685/225285355-25409fca-777c-4784-b8de-23e4dcb70191.jpg)

To create the VPC, go to the AWS console a type vpc in the search box and select vpc. In the VPC dashboard, select VPC, then click create VPC.
Name the VPC "Dev VPC". In the IPv4 cidr block space, type "10.0.0.0/16". Leave everything as default and scroll down and click create vpc.

![image](https://user-images.githubusercontent.com/115881685/225288822-88cf185b-e21f-495c-b72d-84fd55696d3d.png)
![image](https://user-images.githubusercontent.com/115881685/225289117-2781efec-f4eb-42b6-b70b-faac8863321d.png)
![image](https://user-images.githubusercontent.com/115881685/225289229-2cb26b84-533e-49b6-acf3-fbe68687aad5.png)

![image](https://user-images.githubusercontent.com/115881685/225289405-5f49df39-ef2e-4fbc-b1bc-1f88f8385f59.png)

#### Next step is to eneble DNS hostname in the vpc
To do that, select "action" and then edit "vpc setting". In the next page that opens, check the enable "DNS hostname" box and scroll down and hit save button.

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


### Next step Add public route to the route table
The route aalows the public route table to route traffic to the internet.

To do this, make sure you are on the route tab, then click "edit routes", then click "add route", under "destination", type in "0.0.0.0/0" in the box. After that, under "target", select "internet gateway", then click "save changes"

![image](https://user-images.githubusercontent.com/115881685/225329518-05d1b16d-fd84-4578-be76-6189b6b54ef5.png)
![image](https://user-images.githubusercontent.com/115881685/225329742-4ea562f6-2661-4ccc-be49-e861979caa8a.png)
![image](https://user-images.githubusercontent.com/115881685/225330142-26489a07-0cb6-491e-9e42-773197630259.png)

### Next step, associate the two public subnet to the route table

To this, select the "subnet association" tab, then click "edit subnet association", once in the next page, select the two subnet that you see and hit "save aasociation".

![image](https://user-images.githubusercontent.com/115881685/225331798-87967bff-4cd3-4cb9-8fb7-e47fa36a7d62.png)
![image](https://user-images.githubusercontent.com/115881685/225332075-38c30c18-7c55-4f46-8135-003cacf7df4f.png)
![image](https://user-images.githubusercontent.com/115881685/225332570-0c542af2-1657-46e4-abc7-30d7d674852e.png)


### Create private subnet.
Following our vpc refference architecture, we will create 4 private subnets. to this select "subnets" in the left hand side of your vpc dashboard, click "create subnets". make sure you filter by the Dev vpc created earlier, so that you are only seeing the two public subnets that was previously created.

under "VPC", select our Dev vpc, under "name", type "private app subnet AZ1" select us-east-1 under "availability zone" under "cidr block", type, "10.0.2.0/24", then scroll down and hit create subnet.

![image](https://user-images.githubusercontent.com/115881685/225336899-87c94012-6e9c-495b-9186-e2b8c13b4705.png)

Do the same for the remaining three(3) subnets by following the VPC reference architecture, see screenshots below.

![image](https://user-images.githubusercontent.com/115881685/225337804-b2d5a862-93cf-4564-bec7-2be55bf6586b.png)
![image](https://user-images.githubusercontent.com/115881685/225338006-eb5f4d33-bc77-4f40-9fe4-47b30b0dc6e7.png)
![image](https://user-images.githubusercontent.com/115881685/225338298-617b20a0-ff8f-428b-bfed-924548624210.png)

TO see all the subnets(6), filter by the dev vpc.

![image](https://user-images.githubusercontent.com/115881685/225338851-abcf4ab3-9eab-44a0-87e3-dbeb3a2ead74.png)

Following our overall lamp stack project reference architecture, next step is to create nate gateways.


## Create NAT Gateways in the Public Subnets

### Nat Gateway reference architecture.
![3 _Nat_Gateway_Reference_Architecture](https://user-images.githubusercontent.com/115881685/225374326-b7a41ca3-8712-451a-bc46-1c26d1d66e7a.jpg)


Following the architecture, we are creating two nat gateaways, one in public subnet az1 and the second in public subnet az2.
To do this, select "natgateway" in the vpc dashboard and click "create nat gateways", give it the name "nat gatway az1", select "public subnet az1" and scroll down, under "elastic ip allocation id", click "allocate elastic ip", scroll down and hit "create nat gateway".

![image](https://user-images.githubusercontent.com/115881685/225372601-b6a479c1-6d66-421a-a35e-1a1e6b4b784e.png)
![image](https://user-images.githubusercontent.com/115881685/225372921-d2b2c6e9-0596-47c1-9b2d-e1d0e079fb96.png)
![image](https://user-images.githubusercontent.com/115881685/225373593-2af0442a-c9ae-45f1-bc35-0ede9efc593d.png)



### Next step create a route table.
Like before simply select "route table" in the left side of your vpc dashboard and hit "create route table". Give the route table the name, "private route table az1" and then select our "Dev vpc", scroll down and click "create route table".

![image](https://user-images.githubusercontent.com/115881685/225377151-ad9a36dc-45c8-4c5f-98a0-e97f94ecd803.png)
![image](https://user-images.githubusercontent.com/115881685/225377295-f636c8fb-a960-4da6-bf3c-181e9673a505.png)


Next we add route to the the private route table az1 to route traffic to the internet through the nat gateway in the public subnet az1

To this, like before, select the "route" tab and click "edit route", click "add route", under "destination", type "0.0.0.0/0 and click it. under "local" select our nat gateway az1 in the public subnet and hit "save changes".

![image](https://user-images.githubusercontent.com/115881685/225380230-059c6834-010a-4f62-9ccc-81b0cce57714.png)
![image](https://user-images.githubusercontent.com/115881685/225380436-af00a519-5a53-4be9-a92a-78ff2dd2c774.png)


Next associte this route table with "private app subnet az1" and "private data subnet az1" following the nat gateway reference architecture.

Like before select the "subnet associations" tab and then click "edit subnet associations". Under "available subnets", select private app subnet az1 and private data subnet az1 respectively and click "save associations".


![image](https://user-images.githubusercontent.com/115881685/225382241-b3f03d95-1928-41aa-aa15-49f54f509916.png)
![image](https://user-images.githubusercontent.com/115881685/225382429-9e6f4e36-8478-48a1-912f-a152930057f7.png)
![image](https://user-images.githubusercontent.com/115881685/225382633-47769ae7-b642-4324-bd30-c07cf479f93f.png)


Next we create the second nat gateway in the public subnet az2. see screenshots below

![image](https://user-images.githubusercontent.com/115881685/225389066-a8e358d0-270a-4927-9cc1-b4a78015bbbc.png)
![image](https://user-images.githubusercontent.com/115881685/225390059-71b9088b-51db-47d0-9f39-22c1666696ab.png)

Create another route table and call it private route table az2, see screenshots below.

![image](https://user-images.githubusercontent.com/115881685/225391210-0825109a-9d26-4038-a346-83734b5e80f3.png)
![image](https://user-images.githubusercontent.com/115881685/225391399-8ba84f7f-6cdb-4b76-b516-0333014baf6e.png)


Next step add a route to the private route table az2 to route traffic to the internet through the nat gateway in the public subnet az2.
Follow the steps we use in creating the previous one.

![image](https://user-images.githubusercontent.com/115881685/225393218-27bf887d-1dc0-4cf4-b01c-0517f05eb33d.png)
![image](https://user-images.githubusercontent.com/115881685/225393596-38c303e7-29be-4c11-b73f-be64b1de114c.png)


Next step associate this route table with private app subnet az2 and private data subnet az2, like before, follow the previous steps.

![image](https://user-images.githubusercontent.com/115881685/225395523-7a667ab4-6a5d-479a-ade1-feaf2e5c08a4.png)
![image](https://user-images.githubusercontent.com/115881685/225396128-642a2d75-e721-428e-933f-14c7fa96ddd4.png)


The next major block of this project is security group.

## Create Security Group

#### security group reference architecture for our project

![4 WordPress_SG](https://user-images.githubusercontent.com/115881685/225399771-2094091e-5512-497b-8193-10771573cd0c.jpg)


To create security group, select "security group" in the vpc dashboard, filter by the dev vpc, in there you will see a default security group, click "create security group", name it "alb security group", select our dev vpc under vpc. in "inbound rules", click "add rule". The first rule will be under port 80 which is http, search for it and select it. "source" will be 0.0.0.0/0 which means traffic from anywhere, select it.  The second rule will be https which is on port 443, search for it and select it. Also the "source" will be still be "0.0.0.0/0". scroll down and click "create security group".


![image](https://user-images.githubusercontent.com/115881685/225405267-f7316e3c-db72-440d-8d85-e84352b97bdf.png)
![image](https://user-images.githubusercontent.com/115881685/225405482-f0410487-8a7e-4413-b8e9-73b947dbdea9.png)
![image](https://user-images.githubusercontent.com/115881685/225448688-d5cf17f9-e5f6-43e3-8397-87d1943c4217.png)


Next we create SSH Security group, using the security group architecture by follwing the same steps.

![image](https://user-images.githubusercontent.com/115881685/225449342-07cc831a-9f7a-4228-b220-8dc36d104a2f.png)
![image](https://user-images.githubusercontent.com/115881685/225449697-02b1f566-ec37-4084-9198-21622f9b74fc.png)

Use the same steps in creating the the web server security group, database security group and efs security group using the securerity group reference architecture for guide on the diffrerent ports.

When you are done, filter the security groups by your dev vpc to see the six(6) security groups including the default security group.

![image](https://user-images.githubusercontent.com/115881685/225451348-e9f05f62-f340-4066-9424-443771a02a4b.png)



The next major component in our over all lamp stack project is creating rds instance in the private data subnets.



## Create RDS DATABASE

#### RDS database architecture

![5 WordPress_RDS](https://user-images.githubusercontent.com/115881685/225452479-89a0ecc7-1f12-43f7-8405-3aac322110ff.jpg)



To create rds database in the management console, type "rds" and select rds under services in the search box.

Once in the rds dashboard, lets create "subnet groups" first, so select it and click "create db subnet group". Name it database subnets, use the same name for "description", then select the dev vpc under "vpc", scroll down, under "add subnet", select us-east-1a and us-east-1b, then under "subnets", select the private data subnets with cidr block "10.0.4.0/24 and "10.0.5.0/24 then scroll down and click "create".


![image](https://user-images.githubusercontent.com/115881685/225455872-133fceef-7006-4248-9cb8-77ae7f01c10d.png)
![image](https://user-images.githubusercontent.com/115881685/225456089-4cc6ecec-164f-40a2-a56e-4fa6968aa2b6.png)
![image](https://user-images.githubusercontent.com/115881685/225456354-10c15e06-b8c3-48ab-9308-98f1cdaa5e01.png)
![image](https://user-images.githubusercontent.com/115881685/225456541-37add466-d2f8-4189-9691-1c9d25aab3b7.png)



Next create database, select "database" and click "create database". Under "choose a database creation method", select "standard create", under "engine type" choose "mysql", under "version" select the latest myssql 5.7, scroll down under "template" select "dev/test", scroll down, under "db instance identifier" type in the name "dev-rds-db", under "credential settings", type in your username and password and repete the password. scroll down, under "db instance class", select "bustable classes" and the toggle previous instance classes. scroll down, under connectivity(vpc), select the dev vpc, under "subnet group", select the subnet group we just created. scroll down, under "vpc security group" select "choose existing", in the search box delete the default and select "database security group", under "availability zone" select "us-east-1b". scroll down and expand "additional configuration", under database name, type in "applicationdb", scroll all the way down and hit "create database". It usually takes some minutes before the rds instance is created.


![image](https://user-images.githubusercontent.com/115881685/225460888-592a6bed-ab41-410a-8d0a-1c73a24d59bf.png)
![image](https://user-images.githubusercontent.com/115881685/225460961-d5f40577-e59b-4794-8f6e-b9a30dfd93e1.png)
![image](https://user-images.githubusercontent.com/115881685/225461045-5b8ea733-c8df-456c-b423-6e3980d1b693.png)
![image](https://user-images.githubusercontent.com/115881685/225461166-14821c81-e314-468e-b58f-72918e3e9382.png)
![image](https://user-images.githubusercontent.com/115881685/225461378-508d031f-af0e-4ac1-b851-a7305f9ad0a3.png)
![image](https://user-images.githubusercontent.com/115881685/225461557-508fd323-a466-44ba-b350-1b13cf72c52e.png)
![image](https://user-images.githubusercontent.com/115881685/225461683-b09ec678-4208-480f-a289-3cc173bc36b1.png)
![image](https://user-images.githubusercontent.com/115881685/225461752-58c61dbf-a0b0-4d8f-a9ea-a9a39ca24284.png)
![image](https://user-images.githubusercontent.com/115881685/225461877-0e2b9ec4-c004-40f3-acbf-06530334a511.png)





## Create EFS
Next piece in our puzzle is to create an Elastic file system(EFS) with mount target in the private data saubnet in each availability zone.
The EFS will allow the webservers in the private app subnet to pull the application code and configuration files from the same location.

Type EFS in the search box in your console and select EFS under services, click "create file system", then click "customize". Name it "Def-Efs" and scroll down, uncheck the "encryption" box and leave every other thing as default, name the tag, "dev-efs" then click next, under vpc in network, select the "dev vpc", under "availability zone" in mount target, select us-east-1a and us-east-1b respectively, then move to the left, under "security", delete the default security and select the "EFS security group" for both respectively, then click "next" till you get to the create page, click it.


![image](https://user-images.githubusercontent.com/115881685/225547984-fb6c22d5-4a3f-44b8-ba81-77d7ab2a72ec.png)
![image](https://user-images.githubusercontent.com/115881685/225548242-b6552757-891f-413f-9f58-00a696d9ac2c.png)
![image](https://user-images.githubusercontent.com/115881685/225548351-575256f3-451b-49af-be6a-5bde94ebb851.png)
![image](https://user-images.githubusercontent.com/115881685/225548454-a3d64638-2412-4112-bfbc-901395748db3.png)
![image](https://user-images.githubusercontent.com/115881685/225548736-ebe540eb-938a-42be-bfe4-ac994b1ee7d3.png)
![image](https://user-images.githubusercontent.com/115881685/225548941-10b7e292-bac6-4fa1-bfde-68a1149e6cab.png)





## Launch the setup server (Ec2 instance)
Next step is to launch the ec2 instance that will be used to install wordpress in the public subnet az1. To do that search ec2 in the console and navigate yourself to the ec2 dashboard. select "instances running" and click "launch instance", under "name and tag", call it "setup server", scroll down, select "quick start" tab and choose "amazon linux", scroll down, "under keypair", select your keypair, under "network setting", click "edit" and select our "dev vpc", select "public subnet az1", then under "firewall", select "existing security group" and select the drop down and choose the ssh, alb and webserver security groups. click "launch instance".



![image](https://user-images.githubusercontent.com/115881685/225553445-c8465eca-482d-4961-9e23-3b22c9d59259.png)
![image](https://user-images.githubusercontent.com/115881685/225553562-700ea842-c89b-4c18-9629-ca9db31931c7.png)
![image](https://user-images.githubusercontent.com/115881685/225553694-d049cc90-d92f-425f-8d5d-318b0edf6aca.png)
![image](https://user-images.githubusercontent.com/115881685/225554107-4c97de97-c32c-44b3-9787-fa3436524ec8.png)
![image](https://user-images.githubusercontent.com/115881685/225554370-64159941-7d7b-43f2-93d7-a21a17e8320d.png)



### SSH INTO THE SERVER
Next step is to ssh into the server created.


![image](https://user-images.githubusercontent.com/115881685/225555796-24b39a9d-4285-4e78-a88c-55b599ca2098.png)




once done with sshing into the server, use the commands below to install our wordpress site and move the files to efs. What the commands do is explained at the of every command.

Note before running the commands, update the mount information in the first command, to do that go back to your efs created and copy the mount information and come back to the code to update it.



```
#1. create the html directory and mount the efs to it
sudo su
yum update -y
mkdir -p /var/www/html
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-03c9b3354880b36a6.efs.us-east-1.amazonaws.com:/ /var/www/html


#2. install apache 
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd


#3. install php 7.4
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y


#4. install mysql5.7
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld


#5. set permissions
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html 


#6. download wordpress files
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp -r wordpress/* /var/www/html/


#7. create the wp-config.php file
cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php


#8. edit the wp-config.php file
nano /var/www/html/wp-config.php


#9. restart the webserver
service httpd restart
```



When you are done running all the commands, go and copy the ipv4 public address of the server(ec2) and paste it your browser. Enter it, this will open up the wordpress site in your browser.



![image](https://user-images.githubusercontent.com/115881685/225560963-79be5c01-8c0a-42a2-9dcf-c0af4ff7cecc.png)



Enter your information and click "install wordpress".



![image](https://user-images.githubusercontent.com/115881685/225561308-8a5a5162-244e-4b13-9d42-e6f4b4ee9822.png)






![image](https://user-images.githubusercontent.com/115881685/225561539-43edd910-055e-4a04-92aa-ddbf25c43030.png)



Click login.


![image](https://user-images.githubusercontent.com/115881685/225561827-99973089-597d-42d9-8af6-98bb7c2a1c3a.png)



Enter your login details.




![image](https://user-images.githubusercontent.com/115881685/225562489-8aac755b-0444-46cc-b870-a4259b08922a.png)





There you have it, we have successfully install the wordpress site and moved the files to efs.


## Create Application Loadbalancer
Next step is to create application load balancer to route traffic to the ec2 instances in the private app subnets following our reference architecture.

Before creating the application loadbalancer, first launch ec2 instances in each of the private app subnets. Follow the steps as before in creating instance.



![image](https://user-images.githubusercontent.com/115881685/225566552-16a3a9f1-b4b8-4277-aa9d-0a44f1c1e899.png)
![image](https://user-images.githubusercontent.com/115881685/225566771-5199f205-8507-46af-95ad-84ee74449221.png)
![image](https://user-images.githubusercontent.com/115881685/225566895-9a7d0476-339d-4314-a8a2-fa8fac1638fd.png)



Scroll down and expand "advance details", then scroll down to "user data", in the box, paste the command below, this is the same command used in installing wordpress, ensure you update the efs mount information.



```
#!/bin/bash
yum update -y
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld
echo "fs-03c9b3354880b36a6.efs.us-east-1.amazonaws.com:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a
chown apache:apache -R /var/www/html
sudo service httpd restart
```



![image](https://user-images.githubusercontent.com/115881685/225567740-49db62f5-961a-4eaa-a733-b3e9b30db526.png)



this commands helps the ec2 to automatically install all the dependencies it needs without you having to do it manually. once done, "click launch intance".



![image](https://user-images.githubusercontent.com/115881685/225568313-431ab382-58d6-4663-9b4e-84dfba8efcb1.png)



Follow the same step to launch the second ec2 instance in the private app subnet az2.

Once done go to your ec2 dashboard to see your instances



![image](https://user-images.githubusercontent.com/115881685/225570488-9a55e742-b4eb-475f-8bd9-ee3d7c41147a.png)




### Create target group
Next step is to create target group and place the two instances in it, this will allow the application loadbalancer to route traffic to the intances.

Select target groups in the left side of the ec2 management console and click create "target group". see screenshots below for instructions.


![image](https://user-images.githubusercontent.com/115881685/225576333-7743d14e-b60b-45a0-ab2e-180f0f3bdde0.png)
![image](https://user-images.githubusercontent.com/115881685/225575828-df05340b-efae-4b9d-9bd0-230b129734d2.png)
![image](https://user-images.githubusercontent.com/115881685/225576061-5a34adcf-2872-45e7-b045-6de783858209.png)
![image](https://user-images.githubusercontent.com/115881685/225576714-edea1191-d52d-4b3b-a2bb-58aed88c652c.png)




We can now go on and create the application loadbalancer, see screenshots below for instructions.



![image](https://user-images.githubusercontent.com/115881685/225577953-3448d42c-efd9-4b55-8452-331e052a63a3.png)
![image](https://user-images.githubusercontent.com/115881685/225578179-ac11782e-63fe-4ecc-a34a-71a718a718a8.png)
![image](https://user-images.githubusercontent.com/115881685/225578484-081ec3f2-aaef-4efc-a90f-28a58ef5af62.png)
![image](https://user-images.githubusercontent.com/115881685/225578915-fb862d6f-a469-49d3-98c2-91a9c1e2bce4.png)
![image](https://user-images.githubusercontent.com/115881685/225579182-528e7d85-53b4-40e0-812a-d9f0465a47b4.png)
![image](https://user-images.githubusercontent.com/115881685/225579387-b8db40a4-11ed-4329-8fe4-577e58d05b15.png)
 
 
Then click create loadbalancer.



![image](https://user-images.githubusercontent.com/115881685/225580854-e6973063-7da4-472a-9ed2-be336b3cd260.png)



We have successfully created the loadbalancer, wait for the status to change to active, then copy the the dns of the loadbancer and paste it in your browser, press enter.




![image](https://user-images.githubusercontent.com/115881685/225580987-9ce6e6f9-f6c8-4c56-b6c2-e3ea65a558b6.png)



We can now acceass our webiste using the dns name of the application loadbalancer.

Any time you change your domain address, you need to go into your wordpress domain settings and do likewise. To do this first copy your domain name(the wordpress address)


![image](https://user-images.githubusercontent.com/115881685/225583320-2ef82208-9cf2-4083-83e3-c413b803b382.png)



Then add "/wp-admin" to the end of it, press enter, this will prompt you to login, enter your details and you will be taking to the wordpress dashboard.



![image](https://user-images.githubusercontent.com/115881685/225584137-ee6037b0-7c2c-457a-9276-dec723958357.png)



Once in, click "settings" and the "general", then update the "wordpress address" and "site address" by pasting in the url you copied earlier, ensure to remove the forward slash at the end. Then scroll down and click "save changes".


![image](https://user-images.githubusercontent.com/115881685/225585399-501d2e5d-5c29-4696-b250-e6d884d7064e.png)
![image](https://user-images.githubusercontent.com/115881685/225587025-241d31f8-7266-4185-86f6-d30220944fcc.png)



Once you hit "save changes, you will be prompted to log back in, do that,


![image](https://user-images.githubusercontent.com/115881685/225587862-1f752f7b-818f-4777-954f-dfcbe87be0da.png)


So we have successfully updated the wordpress domain url.


Since we have launched two instances in the private app subnets and can now access the wordpress site with our application balancer dns, We can now go into the console and terminate the initial setup server.



![image](https://user-images.githubusercontent.com/115881685/225589273-7f8dfc0b-5470-408e-b497-73e34dfa1ccc.png)






## Create a Record Set in Route 53
Next step is to create a record set in route 53 to access the website with our domain.

Go to the console and search route 53, select it under services, once in the route 53 dashboard, click on "hosted zone" and select your domain, on next page click "create record", under "record name", type in "www" then toggle on the "alias, scroll down and select the drop down, select "alias to application and classic loadbalancer", then select the "us-east-1", next select the drop down and select the application loadbalancer that was created. Then click "create record".




![image](https://user-images.githubusercontent.com/115881685/225618256-e6816b45-2ed8-4990-b4a2-fa3e10f51f86.png)
![image](https://user-images.githubusercontent.com/115881685/225618394-0ad70fc4-557a-4adb-a806-d81b06d594bc.png)
![image](https://user-images.githubusercontent.com/115881685/225618618-3c94e027-1d20-4c05-b1d2-b3f5c2ce237c.png)


Now the record set has been created, to access our website, copy the record name and paste it in your browser press enter.


![image](https://user-images.githubusercontent.com/115881685/225621393-19310c2d-0348-4cc4-90a7-cc06989afc4f.png)
![image](https://user-images.githubusercontent.com/115881685/225620075-819977ac-dcca-4a40-89af-f096f89b42c1.png)


We can now access the wordpress site using our domain name.

Remember any time our domain name changes, we need to go and update the wordpress domain name settings as well, so follow the exact same process as before, if you that, you should have what is shown in the screenshot below, meaning our wordpress website url is now our domain name.



![image](https://user-images.githubusercontent.com/115881685/225622607-9aa92d56-f191-4611-b8e2-d23a44445944.png)









## Register for an SSL Certificate in AWS Certificate Manager
The certificate manager encrypts all communication between the web browser and our web servers.

To create a free certificate, go to the certificate manager dashboard in the console, and follow the steps in the screenshots below.



![image](https://user-images.githubusercontent.com/115881685/225624926-a8d7dcc0-ddc3-46f1-ad79-bf67022a1160.png)
![image](https://user-images.githubusercontent.com/115881685/225625050-ec772b81-046b-4dc6-b128-305b4ed946c3.png)
![image](https://user-images.githubusercontent.com/115881685/225625247-4664ac7f-8eef-4b08-8179-8fc02062fb62.png)
![image](https://user-images.githubusercontent.com/115881685/225625381-6ed19d1a-97bd-4cc7-a81c-02f43fd2431c.png)


![image](https://user-images.githubusercontent.com/115881685/225627483-e9ea68cd-2229-4683-a73b-7006608cc87d.png)



We have successfully requested for a certificate, the status is pending because it needs to be validated in route 53. To do that click "create a record" in the page and follow the instructions in the screenshots.




![image](https://user-images.githubusercontent.com/115881685/225627634-44d5198c-13d5-4b20-9014-961d668ee4af.png)
![image](https://user-images.githubusercontent.com/115881685/225627831-5bce3113-e095-4ef6-bfb2-270ff830e4ad.png)
![image](https://user-images.githubusercontent.com/115881685/225630927-a8737485-0f82-4c1d-8cc7-283215b394f2.png)



If you now check the status, it has been "issued", and the status of our two name name is now "success"



## Create a HTTPS Listener
Next we will use the ssl certificate to secure all communication with our website by creating https listener in loadbalancer dashboard.

Follow the instructions in the screenshots.



![image](https://user-images.githubusercontent.com/115881685/226197176-b284a3c0-ae59-4406-9c0e-09a49e07dccc.png)

![image](https://user-images.githubusercontent.com/115881685/226197301-315f31f3-e5dc-457d-a34c-50fb9ccfc560.png)

![image](https://user-images.githubusercontent.com/115881685/226197376-bdf4ee55-3030-4105-abdc-7e5261732dfa.png)

![image](https://user-images.githubusercontent.com/115881685/226197705-0b2fb689-0f90-46f7-bf0f-ab94da3a732d.png)



The HTTPS listener has been created, next thing is to modify our HTTP Listener to redirect traffic to HTTPS. See screenshots below for instruction.


![image](https://user-images.githubusercontent.com/115881685/226197947-5794ff97-7bbc-485b-adca-3103ac22165f.png)

![image](https://user-images.githubusercontent.com/115881685/226198051-62a8e5b9-7b15-42b4-b37e-2d323e998574.png)

![image](https://user-images.githubusercontent.com/115881685/226198081-d6da80d9-1b16-4e28-8672-1dd605a16c3b.png)





Now you can check to see if you can access the website. Go to your browser and type in your domain name starting with "https//www.domain name".




![image](https://user-images.githubusercontent.com/115881685/226214476-791b8d08-0403-443e-9d4b-daab4a357a7d.png)




Yes, not only can we access the website, it now has the lock symbol which means all communication between our website and web browser is now secured. What we just did is also called "security in transit".



## Launch Bastion Host

Next step is to ssh into the the instance in the private subnet, but to that we first need to launch an instance in the public subnet, this instance is called bastion host, we will ssh into it, and from there we can now ssh into the instance in the private subnet.

Follow the exact same step as before in launching instance in the public subnet. see screenshots below.





![image](https://user-images.githubusercontent.com/115881685/225631151-391a2c83-dabf-4d9e-9ea5-2eb8fe7c46a9.png)
![image](https://user-images.githubusercontent.com/115881685/225631308-5c117833-7820-45bf-ae7f-68e76611cbca.png)
![image](https://user-images.githubusercontent.com/115881685/225631849-1166e86a-d939-4ba5-bf3f-fda1bf5686ab.png)



Then go to your ec2 dashboard to see all instances.



![image](https://user-images.githubusercontent.com/115881685/225632801-4dc2147f-3804-40cf-b8c8-064c5d610ec8.png)



Copy the ipv4 public address of the bastion host and ssh into it, once that is done, type the command "ssh ec2-user@private ip address of any of the webserver"



![image](https://user-images.githubusercontent.com/115881685/225641188-443fbc16-2a43-4b7a-883f-e979c74298fb.png)
![image](https://user-images.githubusercontent.com/115881685/225635644-e21f9514-4133-4bf8-a326-34dd55a59c40.png)

 
 
 
 If you look closely the Ip in our machine corresponds to the private ip of our webserver instance, this shows we have successfully sshed into the instance in the private subnet.
 
 
 
 
 
 ## Create an Auto Scaling Group
 
 We need to create an autoscaling group to dynamically create and scale the webservers in the private app subnets.
 
 Before creating the autoscaling groups first terminate the two web servers that where manually created.
 
 
 
 
 
Now go ahead, Follow the steps in the screenshots below, first we will create launch template.
The launch template contains configuration about our instance that autoscaling group will use to launch new instances.






![image](https://user-images.githubusercontent.com/115881685/225643311-54ddb472-556e-4391-bb06-2033d37a81c0.png)
![image](https://user-images.githubusercontent.com/115881685/225644299-6577d092-e95e-4acc-846e-e9fca6a9c622.png)![image](https://user-images.githubusercontent.com/115881685/225644525-3b22df67-c652-4265-bb65-42206942f76c.png)
![image](https://user-images.githubusercontent.com/115881685/225644799-d547e310-be92-439d-aa23-512123c5a65f.png)




Expand the advance details tab and scroll down to the user data box, paste in the earlier command we use in configuring our ec2, ensure you update the efs mount information, then click "create launch template".



![image](https://user-images.githubusercontent.com/115881685/225646308-3d0ea517-897a-4fab-91d6-2ab235d89de7.png)


Now go ahead and create our autoscaling group, follow the screenshots below.




![image](https://user-images.githubusercontent.com/115881685/225646762-c799ddd5-5e2c-4f79-93cc-01ecb897f33e.png)
![image](https://user-images.githubusercontent.com/115881685/225646962-554c6591-9901-4025-adbc-bdace4164a2a.png)
![image](https://user-images.githubusercontent.com/115881685/225647149-fc377b11-eb47-4736-87af-5355595b3646.png)
![image](https://user-images.githubusercontent.com/115881685/225647941-6cdc619b-fbdb-4c9e-9568-30e764850679.png)
![image](https://user-images.githubusercontent.com/115881685/225648143-eaa00795-4a6d-4215-af0c-070a82a14bd5.png)
![image](https://user-images.githubusercontent.com/115881685/225648301-9e4334e4-b9c7-4d3f-a36d-31d6d3d8234e.png)
![image](https://user-images.githubusercontent.com/115881685/225648588-2f364f9e-77cf-4ae0-be43-00198dc3e31d.png)
![image](https://user-images.githubusercontent.com/115881685/225648873-f5d665fd-9240-40ee-a112-1d32f9fee0e4.png)
![image](https://user-images.githubusercontent.com/115881685/225649073-c6babd13-c36e-481c-842d-03b074253bb5.png)
![image](https://user-images.githubusercontent.com/115881685/225649326-172d9019-0ac9-4ac7-9d0f-d6cd36b6e79d.png)





Now go to the ec2 dashboard to see the two new instances created by our autoscaling group.



![image](https://user-images.githubusercontent.com/115881685/225651438-3af01ce0-5b86-4ded-86a0-a4211600d99b.png)



To still access our website, type in your domain name in a browser and enter




![image](https://user-images.githubusercontent.com/115881685/225651656-698a78bc-56b8-42f7-beca-6d0e68c7084e.png)




There you go, we can now access our website. This is how you create an autoscaling group to dynamically create and scale ec2 instances in the private app subnets.


 
 
 
 
## Install WordPress Theme and Template
Finally to complete the project, we are going to install free theme and template to make our website professional.

Login into your website as an admin by appending "/wp-admin to the end of your website url, press enter. Once in, select "apperance" and click on "add new".



![image](https://user-images.githubusercontent.com/115881685/225699444-24035e8f-db13-404e-b80d-8f3511cff631.png)




We are installing the "Astra theme, Click "install" and then "activate"


![image](https://user-images.githubusercontent.com/115881685/225700492-fc9f471e-3bcd-448c-97ec-65e97fe0b56e.png)
![image](https://user-images.githubusercontent.com/115881685/225700976-0063cb56-0f5e-416f-9053-7d130015fd40.png)



Wait for to finish activating and on the page that displays, click, "build your website now" and select "elementor"




![image](https://user-images.githubusercontent.com/115881685/225702212-1247cc66-b71c-4a61-832a-66688fc8c8da.png)
![image](https://user-images.githubusercontent.com/115881685/225702381-1bad53bc-f1eb-41fb-9ca6-27602575ae32.png)
![image](https://user-images.githubusercontent.com/115881685/225702503-e413082a-7c84-4d5e-9665-909842cdd7b4.png)



In the page that opens, select local business and underneath it, search "love nature" this is where the template i want to install is located, select it.



![image](https://user-images.githubusercontent.com/115881685/225704151-b90d9be3-2cf1-493a-9db8-e348d02b0e99.png)



Click "skip and continue" scroll down and do the same again. In the page that follows, enter your information and click "submit and build my website".



![image](https://user-images.githubusercontent.com/115881685/225704868-a415bad1-b01e-4772-a5e2-3a1425c75b11.png)



Give it some time to finish building the website, once its done, click "view your website" and you will taken to your website.




![image](https://user-images.githubusercontent.com/115881685/225705479-0d47b19a-c57f-424b-8030-6e2141de9ad2.png)

![image](https://user-images.githubusercontent.com/115881685/225705615-cf68d629-7184-43d4-9c70-cd3cc4bf80b0.png)




Scroll down to see more of the website.





![image](https://user-images.githubusercontent.com/115881685/225705951-c9532cc7-76da-4170-9106-84bd552e3715.png)
![image](https://user-images.githubusercontent.com/115881685/225706042-5b81fde8-e81b-47a1-8abe-57214447ddde.png)
![image](https://user-images.githubusercontent.com/115881685/225706092-9466a3ae-0159-42c5-b0bb-3ac6a3313b89.png)
![image](https://user-images.githubusercontent.com/115881685/225706214-f502799b-c445-4243-bdf1-21ab2eb952e1.png)





Finally log out as the admin and try accessing as how users would. Open a new tab, type in your domain name. and press enter.





![image](https://user-images.githubusercontent.com/115881685/225707875-cdcb81d7-1a2d-46b7-9181-4ed99f574d83.png)






And here you have it guys, we are done.


Please note, all the resourses created in the course of doing this project has to be deleted in other to avoid been charged.


































