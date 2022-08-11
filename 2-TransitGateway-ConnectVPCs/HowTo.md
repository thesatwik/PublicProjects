# Use Transit Gateway to connect multiple VPCs

## Preparing Basic Environment. 

I have used below cloudformation templates to setup necessary enviroment. 
Below cloudformation scripts creates three VPCs, public & private subnets, EC2 instance in private subnet. 
Cloud formation templates also deploys necessary SSM roles and endpoints so that private EC2 instance can be connected using system manager (Best Practise!!!!!)


### Codes for this demo
All clodes saved in below location. 
https://github.com/thesatwik/PublicProjects/tree/main/2-TransitGateway-ConnectVPCs/01%20Code 

1. **VPC-AwithPrivateEC2A.yaml**    
    A. Deploys VPC with CIDR range 10.20.0.0/16  
    B. creates 12 subnets - 3 Web Public Subnet, 3 DB private subnet, 3 APP  private subnet, and 3 reserved subnet for future requirements   
    C. Creates EC2 instance in Private subnet with necessary SSM role.     
