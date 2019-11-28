---
title: "Lab 02: Resources"
date: 2019-10-28T14:35:59Z
weight: 110
---

Lets build EC2 template. In this Lab, you will write CloudFormation template and learn more about its sections. 

As you read through different sections, there is a code at the end, which you should copy to your template file.

**Lets go!**

1. Go to `code/40-cloudformation-features/` directory.
1. Open the `01-lab02-Resources.yaml` file.
1. Copy the code as you go through the topics bellow.

#### Format Version
The _AWSTemplateFormatVersion_ section identifies the capabilities of the template. The latest template format
 version is _2010-09-09_ and is currently the only valid value. 
 
```yaml
AWSTemplateFormatVersion: '2010-09-09'
```

#### Description
The _Description_ section enables you to include comments about your template.

```yaml
Description : 'AWS CLoudFormation Workshop template.'
```

#### Metadata
You can use the _Metadata_ section to include arbitrary JSON or YAML objects. For example, group and order the Amazon EC2 Configuration parameters with _AWS::CloudFormation::Interface_.

```yaml
Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups:
      - Label:
          default: 'Amazon EC2 Configuration'
        Parameters:
          - InstanceType

    ParameterLabels:
      InstanceType:
        default: 'Type of EC2 Instance'
```

#### Parameters
_Parameters_ enable you to input custom values to your template each time you create or update a stack.

AWS CloudFormation supports the following parameter types:

|Type|Description|Example|
|----|----|----|
|_String_|A literal string.|"MyUserName"|
|_Number_|An integer or float.|"123"|
|_List\<Number\>_|An array of integers or floats.|"10,20,30"|
|_CommaDelimitedList_|An array of literal strings.|"test,dev,prod"|
|[AWS-Specific Parameter Types](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#aws-specific-parameter-types)|AWS values such as Amazon VPC IDs.|_AWS::EC2::VPC::Id_|
|[SSM Parameter Types](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#aws-ssm-parameter-types)|Parameters that correspond to existing parameters in Systems Manager Parameter Store.|_AWS::SSM::Parameter::Value\<AWS::EC2::Image::Id\>_|
 
```yaml
Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - m1.small
      - m1.large
    Description: 'Enter t2.micro, m1.small, or m1.large. Default is t2.micro.'
``` 

#### Resources

The required _Resources_ section declares the AWS resources that you want to include in the stack. Lets add the EC2 resource to your stack.

```yaml
Resources:
  MyEC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: <replace with AMI ID ami-xxxxx>
      InstanceType: !Ref InstanceType
```

The only required property of EC2 resource type is _ImageId_. There are two ways to find out correct AMI ID for the AWS Region:

##### 1. Via Console
1. open [AWS EC2 console](https://console.aws.amazon.com/ec2)
1. click _Instances_ -> _Launch Instance_
1. Copy the **Amazon Linux 2 AMI** `ami-xxxxxxxxx`

![](/40-cloudformation-features/ami-1.gif)

##### 1. Via terminal using AWS CLI

You can use AWS CLI to query AWS Systems Manager Parameter Store

Bellow is an example, how to get the latest Amazon Linux 2 AMI ID in London Region.

```bash
  aws ssm get-parameters \
  --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 \
  --query Parameters[].Value \
  --region eu-west-2 \
  --output text
```

#### Final Template
Once you have your AMI ID, copy and paste it to _ImageId_ property.

{{% notice info %}} 
You can find working solution for **London Region** in `code/40-cloudformation-features/02-lab02-Resources-Solution.yaml` file.
{{% /notice %}}

Now your EC2 template is ready to be deployed. Go back to AWS console and deploy the stack same way as you did in 
the [Lab 01: Template and Stack](/30-cloudformation-fundamentals/200-lab-01-stack)
