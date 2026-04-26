Project Title : Creating a EC2 instance with a Security group SSH access 

This is a project to create a stack using aws cloudformation. You can use visual designer, sample template or upload a template file. I usually use a sample template and edit it as i work along but your free to do what you please.

On this particular project we will use aws cloudformation to launce a ec-2-instance with a Security group for SSH access. Importantly we need to create a security group first the attach it to the ec-2-instance.

Secondly, we will add a volume and attach it to our ec-2-instance without changing the cloudformation configuration.

Lasty, we will the add S3 bucket to number 1 and number 2. That is without changing the original values.

Note : I picked the current image id of Amazon Linux { ami-098e39bafa7e7303d }



## Deployment

To deploy this project sign in your aws console, and launch aws cloudformation. You can copy and save this file as aws-cloudformation.yaml or aws-cloudformation.json using vscode and upload it into the console.



```bash
AWSTemplateFormatVersion: '2010-09-09'
Description: Creata a EC2 instance with a Security group SSH access
Resources:
  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH access
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
 MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-098e39bafa7e7303d
      InstanceType: t2.micro
      SecurityGroupIds:
        - !Ref InstanceSecurityGroup

```


## 2.0 Attaching an EBS Volume to the ec-2 Instance

I created a 25 GB Volume using the same ec-2 instance properties and then we wil attach the Volume to the ec-2



```bash
AWSTemplateFormatVersion: '2010-09-09'
Description: Creata a EC2 instance with a Security group SSH access
Resources:
  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable SSH access
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
 MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-098e39bafa7e7303d
      InstanceType: t2.micro
      SecurityGroupIds:
        - !Ref InstanceSecurityGroup
MyVolume:
  Type: AWS::EC2::Volume
  Properties:
    AvailabilityZone: !GetAtt MyInstance.AvailabilityZone
    Size: 25
MyVolumeAttachment:
  Type: AWS::EC2::VolumeAttachment
  Properties:
    AvailabilityZone: !Ref MyInstance
    VolumeId: !Ref MyVolume
    Device: /dev/sdf
    
```

## 2.0.1 Adding S3 Bucket to the Stack 

```bash
AWSTemplateFormatVersion: '2010-09-09'
Description: Creata a EC2 instance with a Security group SSH access
Resources:
  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable SSH access
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
 MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-098e39bafa7e7303d
      InstanceType: t2.micro
      SecurityGroupIds:
        - !Ref InstanceSecurityGroup
MyVolume:
  Type: AWS::EC2::Volume
  Properties:
    AvailabilityZone: !GetAtt MyInstance.AvailabilityZone
    Size: 25
MyVolumeAttachment:
  Type: AWS::EC2::VolumeAttachment
  Properties:
    AvailabilityZone: !Ref MyInstance
    VolumeId: !Ref MyVolume
    Device: /dev/sdf
MyS3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    BucketName: my-unique-bucket-name-001122554  <<< Ensure your bucket name is unique >>>

    ```


