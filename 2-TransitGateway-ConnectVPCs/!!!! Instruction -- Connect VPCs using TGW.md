# Use Transit Gateway to connect multiple VPCs

Below is the architecture diagram on how VPCs are connected to Transit gateway to have inter VPC communication. 

![Architectural setup with 3 VPCs in 3 accounts](https://raw.githubusercontent.com/thesatwik/PublicProjects/main/2-TransitGateway-ConnectVPCs/02%20Diagrams/Trasit%20Gatway_VPC%20Setup.png)

VPC-A, VPC-B and VPC-C are part of an AWS Organisation. 

## Preparing Basic Environment. 

I have used below cloudformation templates to setup necessary enviroment. 
Below cloudformation scripts creates three VPCs, public & private subnets, EC2 instance in private subnet. 
Cloud formation templates also deploys necessary SSM roles and endpoints so that private EC2 instance can be connected using system manager (Best Practise!!!!!)


### Codes for this demo
All clodes saved in [this location](https://github.com/thesatwik/PublicProjects/tree/main/2-TransitGateway-ConnectVPCs/01%20Code). 


1. **VPC-AwithPrivateEC2A.yaml**    -- Deployed in Account-A  

    A. Deploys VPC with CIDR range **10.20.0.0/16**
    B. creates 12 subnets - 3 Web Public Subnet, 3 DB private subnet, 3 APP  private subnet, and 3 reserved subnet for future requirements   
    C. Creates EC2 instance in Private subnet with necessary SSM role.   

[This oneclick deployment](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://thesatwiklab.s3.amazonaws.com/Codes/2-TransitGateway-ConnectVPCs/VPC-AwithPrivateEC2A.yaml&stackName=VPC-AwithPrivateEC2 ) can be used which will fetch necessary code from my public S3 bucket.  Login or switch to related AWS account before launching oneclick deployement, US-east-1 region needs to be selected. 

2. **VPC-BwithPrivateEC2B.yaml**    Deployed in Account-B  

    A. Deploys VPC with CIDR range **10.21.0.0/16**  
    B. creates 12 subnets - 3 Web Public Subnet, 3 DB private subnet, 3 APP  private subnet, and 3 reserved subnet for future requirements   
    C. Creates EC2 instance in Private subnet with necessary SSM role.   

[This oneclick deployment](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://thesatwiklab.s3.amazonaws.com/Codes/2-TransitGateway-ConnectVPCs/VPC-BwithPrivateEC2B.yaml&stackName=VPC-BwithPrivateEC2 ) can be used which will fetch necessary code from my public S3 bucket.  Login or switch to related AWS account before launching oneclick deployement, US-east-1 region needs to be selected. 


2. **VPC-CwithPrivateEC2B.yaml**    Deployed in Account-C  

    A. Deploys VPC with CIDR range **10.22.0.0/16**  
    B. creates 12 subnets - 3 Web Public Subnet, 3 DB private subnet, 3 APP  private subnet, and 3 reserved subnet for future requirements   
    C. Creates EC2 instance in Private subnet with necessary SSM role.   

[This oneclick deployment](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://thesatwiklab.s3.amazonaws.com/Codes/2-TransitGateway-ConnectVPCs/VPC-CwithPrivateEC2C.yaml&stackName=VPC-CwithPrivateEC2 ) can be used which will fetch necessary code from my public S3 bucket.  Login or switch to related AWS account before launching oneclick deployement, US-east-1 region needs to be selected. 

Once Cloudformation completed deploying necessary infrastructure along with EC2 instances,below private IP addresses were assigned in my case (This is expected to be differnet if the same CFN template is used later)

EC2-A(Account-A, VPC-A)  --> 10.20.39.213

EC2-B(Account -B, VPC-B) ---> 10.21.33.187

EC2-C(Account-C, VPC-C) ----> 10.22.47.229


###################################This is the END of VPC & EC2 Setup################################

## Connectivity check before starting TGW related steps 

There is no connectivity b/w VPCs before setting up TGW

![No Connectivity](https://github.com/thesatwik/PublicProjects/blob/main/2-TransitGateway-ConnectVPCs/02%20Diagrams/BeforeTGW-VPCsCantCommunicate.png)


## Create Transit Gateway in Account A  & share with other accounts hosting VPC-B & VPC-C within AWS Organisation 

Transit Gateway is created in VCP A in Account A. Then the same TGW is shared with other accounts (B&C) of the organisation. 


Following sceenshot from AWS console shows how TGW was created and then shared with other accounts. 

![GUI guide on how to create & Share TGW](https://github.com/thesatwik/PublicProjects/blob/10f6f407abfe671fe7f5a0b8365fc17823b767f8/2-TransitGateway-ConnectVPCs/02%20Diagrams/Create%20&%20Share%20TGW.png)

## Create Transit Gateway Attachments   

Below AWS console screen details on how to create TGW attachments and if the TGW is created without "Auto accept Shared Attachment", then owner of TGW (Account-A in this case) must accpet each request. 
Three VPC attachments from created from VPC A/B/C of account A/B/C respecively. 

![TGW attachment](https://github.com/thesatwik/PublicProjects/blob/main/2-TransitGateway-ConnectVPCs/02%20Diagrams/TransitGatewayAttachments.png)


## Verification of Route Table association. 

Notice that during TGW creation "Default route table association" was checked. Therefore, All attached VPCs will propagate its route, and "**TGW Route Table**" will be populated. 


![TGW Default RT](https://github.com/thesatwik/PublicProjects/blob/main/2-TransitGateway-ConnectVPCs/02%20Diagrams/TGWDefaultRT.png)


## Updating VPC Subnet Specific Route Table

In this example, EC2 instances were launced in **PrivateSubnet** Called AppA. For all three subnets under three different account, Route table needs to be updated so that relavant traffic be routed towards Transit Gateway. 
Once trafiic reaches transit gateway, based on transit gateway default route table, it will forward connections to respective VPCs. 




![SubnetRTUpdate](https://github.com/thesatwik/PublicProjects/blob/main/2-TransitGateway-ConnectVPCs/02%20Diagrams/SubnetRTUpdate.png)


## Post Implementation Validation 

Once all Aove setup will complete, All three VPCs should be able to talk to to each other. 
Login to any EC2 instance, and ping other instances. 

![SuccessAtTheEnd](https://github.com/thesatwik/PublicProjects/blob/main/2-TransitGateway-ConnectVPCs/02%20Diagrams/SuccessAtTheEnd.png)


## Troubleshooting 

Things to check if connectivity issue persists after succesful TGW implemenation. 

* Check Security group and NACL to confirm all required source/port are open for communication. 
* Transit Gateway Attachments are in "Avaiable" state. 
* Private Subnet RTs are updated correctly to push traffic towards TGW for interVPC communication. 


#  Coming up


In above example All VPCs can talk to each other. However, in order to maintain segregation of duty, there may be requirements to allow connectivty to only specific VPCs while block other VPCs (i.e. PROD VPC can talk to PROD VPC, but not to DEV VPC or vice versa).
TGW setup for similar requirement will be documented under my [Public Project Github Repo](https://github.com/thesatwik/PublicProjects/) in next mini POC project documentation.