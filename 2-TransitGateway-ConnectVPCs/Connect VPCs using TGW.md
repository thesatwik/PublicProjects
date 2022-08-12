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

![TGW attachment]()

