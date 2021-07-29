# Steps
## 1. Create VPC, routes tables, subnet tables and export outputs 
- [vpc.yaml](./Templates/vpc.yaml)

```bash
aws cloudformation create-stack --stack-name sgpvpc --template-body file://vpc.yaml --parameters ParameterKey='VPCCIDR',ParameterValue='192.168.0.0/16' ParameterKey='PublicSubnet1CIDR',ParameterValue='192.168.1.0/24' ParameterKey='PublicSubnet2CIDR',ParameterValue='192.168.2.0/24' ParameterKey='PublicSubnet3CIDR',ParameterValue='192.168.3.0/24' ParameterKey='RegionCode',ParameterValue='sgp' ParameterKey='AZ1Code',ParameterValue='sgpaz1' ParameterKey='AZ2Code',ParameterValue='sgpaz2' ParameterKey='AZ3Code',ParameterValue='sgpaz3'
```
## 2. Create security group with necesory permissions ( ssh, https , http )
- [vpc-securitygroup.yaml](./Templates/vpc-securitygroup.yaml)

```bash
aws cloudformation create-stack --stack-name sgvpc-securitygroup --template-body file://vpc-securitygroup.yaml --parameters ParameterKey='vpcStackName',ParameterValue='sgpvpc' 
```

## 3.Create web server 1 with http service
- [public-instance-v1.yaml](./Templates/public-instance-v1.yaml)

```bash
aws cloudformation create-stack --stack-name instancev1 --template-body file://public-instance-v1.yaml --parameters ParameterKey='vpcStackName',ParameterValue='sgpvpc' ParameterKey='vpcSecurityGroupStackName',ParameterValue='sgvpc-securitygroup' ParameterKey='appVersion',ParameterValue='v1'
```

## 4.Create web server 2 with http service
- [public-instance-v2.yaml](./Templates/public-instance-v2.yaml)

```bash
aws cloudformation create-stack --stack-name instancev2 --template-body file://public-instance-v2.yaml --parameters ParameterKey='vpcStackName',ParameterValue='sgpvpc' ParameterKey='vpcSecurityGroupStackName',ParameterValue='sgvpc-securitygroup' ParameterKey='appVersion',ParameterValue='v2'
```


## 5. Create ALB
- [alb.yaml](./Templates/alb.yaml)
```
aws cloudformation create-stack --stack-name sgpalb --template-body file://alb.yaml 
```
Test Result: 
```
 while sleep 0.9; do curl -k "Application-Load-Balancer-392717069.ap-southeast-1.elb.amazonaws.com"; done
<html><h1 align='center'><p style='color:blue'>This Is kumoribay - app v1</p></h1></html>
<html><h1 align='center'><p style='color:green'>This Is kumoribay - app v2</p></h1></html>
<html><h1 align='center'><p style='color:green'>This Is kumoribay - app v2</p></h1></html>
<html><h1 align='center'><p style='color:blue'>This Is kumoribay - app v1</p></h1></html>
<html><h1 align='center'><p style='color:green'>This Is kumoribay - app v2</p></h1></html>
<html><h1 align='center'><p style='color:blue'>This Is kumoribay - app v1</p></h1></html>
<html><h1 align='center'><p style='color:blue'>This Is kumoribay - app v1</p></h1></html>
<html><h1 align='center'><p style='color:green'>This Is kumoribay - app v2</p></h1></html>
<html><h1 align='center'><p style='color:green'>This Is kumoribay - app v2</p></h1></html>
<html><h1 align='center'><p style='color:blue'>This Is kumoribay - app v1</p></h1></html>
<html><h1 align='center'><p style='color:green'>This Is kumoribay - app v2</p></h1></html>
```

