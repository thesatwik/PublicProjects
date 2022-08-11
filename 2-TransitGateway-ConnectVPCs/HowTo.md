# Use Transit Gateway to connect multiple VPCs

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

[This oneclick deployment](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://thesatwiklab.s3.amazonaws.com/Codes/2-TransitGateway-ConnectVPCs/VPC-AwithPrivateEC2A.yaml&stackName=VPC-AwithPrivateEC2 ) can be used which will fetch necessary code from my public S3 bucket.  

2. **VPC-BwithPrivateEC2B.yaml**    Deployed in Account-B  

    A. Deploys VPC with CIDR range **10.21.0.0/16**  
    B. creates 12 subnets - 3 Web Public Subnet, 3 DB private subnet, 3 APP  private subnet, and 3 reserved subnet for future requirements   
    C. Creates EC2 instance in Private subnet with necessary SSM role.   

[This oneclick deployment](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://thesatwiklab.s3.amazonaws.com/Codes/2-TransitGateway-ConnectVPCs/VPC-BwithPrivateEC2B.yaml&stackName=VPC-BwithPrivateEC2 ) can be used which will fetch necessary code from my public S3 bucket. 



![Architectural setup with 3 VPCs in 3 accounts](https://raw.githubusercontent.com/thesatwik/PublicProjects/2b83b95bb61633aa8d62f18f153d174dd7e96c7c/2-TransitGateway-ConnectVPCs/02%20Diagrams/Trasit%20Gatway_VPC%20Setup.png)