# Per environment

## VPC configuration

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/rds/home?region=us-east-1#launch-dbinstance:gdb=false;s3-import=false)

**Resources to create:** VPC and more

**Name tag auto-generation:** dev

**IPv4 CIDR block:** 170.100.0.0/22

**IPv6 CIDR block:** No IPv6 CIDR block

**Tenancy:** Default

**Number of Availability Zones (AZs):** 2

**Number of public subnets:** 2

**Number of private subnets:** 2

**Public subnet CIDR block in us-east-1a:** 170.100.0.0/24

**Public subnet CIDR block in us-east-1b:** 170.100.1.0/24

**Private subnet CIDR block in us-east-1a:** 170.100.2.0/24

**Private subnet CIDR block in us-east-1b:** 170.100.3.0/24

**NAT gateways ($):** In 1 AZ

**VPC endpoints:** None

**Enable DNS hostnames:** true

**Enable DNS resolution:** true

**Additional tags:** ENVIRONMENT = dev

## RDS Security Group

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#SecurityGroups:sort=desc:group-name)

**Security group name:** dev-rds

**Description:** Allow access to PostgreSQL

**VPC:** dev-vpc

**Inbound rules -> Port range:** 5432

**Inbound rules -> Source:** 0.0.0.0/0

**Tags:** ENVIRONMENT = dev

## Hasura Security Group

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#SecurityGroups:sort=desc:group-name)

**Security group name:** dev-hasura

**Description:** Allow access to Hasura

**VPC:** dev-vpc

**Inbound rules -> Port range:** 8080

**Inbound rules -> Source:** 0.0.0.0/0

**Tags:** ENVIRONMENT = dev

## HTTP Security Group

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#SecurityGroups:sort=desc:group-name)

**Security group name:** dev-http

**Description:** Allow access from HTTP and HTTPS requests

**VPC:** dev-vpc

**Inbound rules -> Port range:** 80

**Inbound rules -> Source:** 0.0.0.0/0

**Inbound rules -> Port range:** 443

**Inbound rules -> Source:** 0.0.0.0/0

**Tags:** ENVIRONMENT = dev

## RDS configuration

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/rds/home?region=us-east-1#launch-dbinstance:gdb=false;s3-import=false)

**Creation method:** Standard create

**Engine type:** PostgreSQL

**Engine Version:** PostgreSQL 14.5-R1

**Templates:** Free tier

**Availability and durability:** None

**DB instance identifier:** dev

**Master username:** postgres

**Auto generate a password:** false

**Master password:** a secure one

**Confirm master password:** a secure one

**Instance configuration:** db.t3.micro

**Allocated storage:** 20gb

**Enable storage autoscaling:** true

**Maximum storage threshold:** 100

**Virtual private cloud (VPC):** The previously created VPC

**DB Subnet group:** default from VPC

**Public access:** No

**VPC security group (firewall):** dev-rds

**Availability Zone:** No preference

**Database authentication:** Password authentication

**Turn on Performance Insights:** false

**Tags:** ENVIRONMENT = dev

## ACM configuration

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/acm/home?region=us-east-1#/certificates/request)

**Request a public certificate:** true

**Fully qualified domain name:** *.dev.adsa.co

**Fully qualified domain name:** *.graphql.dev.adsa.co

**Fully qualified domain name:** *.api.dev.adsa.co

**DNS validation - recommended:** true

**RSA 2048:** true

**Tags:** ENVIRONMENT = dev

**Create records in Route 53:** true

## ECS cluster configuration

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#/clusters/create/new)

**Select cluster template:** Networking only

**Cluster name:** dev

**Tags:** ENVIRONMENT = dev

# Per project

## Target Group configuration

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#CreateTargetGroup:protocol=HTTP)

**Choose a target type:** IP addresses

**Target group name:** boilerplate-dev-hasura

**Protocol:** HTTP

**Port:** 8080

**IPv4:** true

**VPC:** dev-vpc

**HTTP1:** true

**Health check protocol:** HTTP

**Health check path:** /healthz

Tags: PROJECT = boilerplate-dev

**Specify IPs and define ports:** Remove

## Load Balancer configuration

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#SelectCreateELBWizard:)

**Application Load Balancer:** Create

**Load balancer name:** boilerplate-dev-hasura

**Internet-facing:** true

**IPv4:** true

**VPC:** dev-vpc

**us-east-1a:** true

**Subnet:** dev-subnet-public1

**us-east-1b:** true

**Subnet:** dev-subnet-public2

**Security groups:** dev-http

**Listener HTTP:80:** 
- ** Port:** 80
- **Default action:** boilerplate-dev-hasura

**Listener HTTPS:443:** 
- ** Port:** 443
- **Default action:** boilerplate-dev-hasura

**Security policy:** ELBSecurityPolicy-2016-08

**Default SSL/TLS certificate:** From ACM *dev.adsa.co

**AWS Global Accelerato:** false

**Tags:** PROJECT = boilerplate-dev

## ECR hasura repository

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/ecr/create-repository?region=us-east-1)

**Private:** true

**Repository name:** boilerplate/dev/hasura

**Tag immutability:** false

**Scan on push:** false

**KMS encryption:** false

**Tags:** PROJECT = boilerplate-dev

## ECS Task definition hasura

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#/taskDefinitions/create)

**Select launch type compatibility:** FARGATE

**Task definition name:** boilerplate-dev-hasura

**Task role:** None

**Operating system family:** Linux

**Task execution role:** ecsTaskExecutionRole

**Task memory (GB):** 2GB

**Task CPU (vCPU):** 1 vCPU

**Container definitions:** 
- **Container name:** hasura
- **Image:** hasura/graphql-engine:v2.16.1
- **Private repository authentication:** false
- **Memory Limits (MiB):** None
- **Port mappings:** 8080
- **Advanced container configuration:** None

**Enable App Mesh integration:** false

**Enable proxy configuration:** false

**Enable FireLens integration:** false

**Volumes:** None

**Tags:** PROJECT = boilerplate-dev

## ECS Service hasura

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#/clusters/dev/createService)

**Launch type:** FARGATE

**Operating system family** Linux

**Task Definition:** boilerplate-dev-hasura:1

**Platform version:** LATEST

**Cluster:** dev

**Service name:** boilerplate-hasura

**Number of tasks:** 1

**Minimum healthy percent:** 0

**Minimum healthy percent:** 200

**Deployment circuit breaker:** Disabled

**Rolling update:** true

**Enable ECS managed tags:** true

**Tags:** PROJECT = boilerplate-dev

**Cluster VPC:** dev-vpc

**Subnets:** [dev-subnet-public1, dev-subnet-public2]

**Security groups:** dev-hasura

**Auto-assign public IP:** ENABLED

**Load balancer type:** Application Load Balancer

**Health check grace period:** 10

**Load balancer name:** boilerplate-dev-hasura

**Container name : port:** Add to load balancer

**Target group name:** boilerplate-dev-hasura

**Do not adjust the service’s desired count:** true

## API Gateway

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/apigateway/main/publish/domain-names/create?region=us-east-1)

**Domain name:** mizu.api.dev.adsa.co

**TLS 1.2 (recommended):** true

**Endpoint type:** Regional

**ACM certificate:** *.dev.adsa.co

**Tags:** PROJECT = boilerplate-dev

**API mappings:** 
- **API:** dev-boilerplate-api
- **Stage:** dev

## Route 53 configuration for hasura

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones#)

**Record name:** boilerplate.graphql.dev

**Alias:** true

**Route traffic to:** 
- Alias to Application and Classic Load Balancer
- us-east-1
- boilerplate-dev-hasura

**Routing policy:** Simple routing

**Evaluate target health:** true

## Route 53 configuration for API

**On:** [Amazon Console](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones#)

**Record name:** boilerplate.api.dev

**Alias:** true

**Route traffic to:** 
- Alias to API Gateway API
- us-east-1
- boilerplate-dev-api

**Routing policy:** Simple routing

**Evaluate target health:** true

## Dedicated database

```
CREATE DATABASE yourdbname;
CREATE USER youruser WITH PASSWORD 'yourpass';
GRANT ALL PRIVILEGES ON DATABASE yourdbname TO youruser;
```

