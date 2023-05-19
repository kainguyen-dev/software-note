# AWS Cloud 

## 1. Cloud overview

- Region:
  - Most of AWS Service is region scope
  - Choice of region:
    - Compilance
    - Proximity
    - Price
- AZ:
    - Cluster of data center in region
- Point of presence (edge location)

- AWS has Global Services:
    - Identity and Access Management (IAM)
    - Route 53 (DNS service)
    - CloudFront (Content Delivery Network)
    - WAF (Web Application Firewall)


- Most AWS services are Region-scoped:
    - Amazon EC2 (Infrastructure as a Service)
    - Elastic Beanstalk (Platform as a Service)
    - Rekognition (Software as a Service)

## 2. IAM
- IAM is a global service:
- Enity of IAM:
    - User
    - Groups
    - User don't have to belong a group or multiple groups
    - Permission is a JSON document call Policy that can assign to user, groups

```json
"Version": "2012-10-17",
"Statement": [
    {
    "Effect": "Allow",
    "Action": "ec2:Describe*",
    "Resource": "*"
    }, {
    "Effect": "Allow",
    "Action": "elasticloadbalancing:Describe*",
    "Resource": "*"
    }, {
    "Effect": "Allow",
    "Action": [
        "cloudwatch:ListMetrics",
        "cloudwatch:GetMetricStatistics",
        "cloudwatch:Describe*"
    ],
    "Resource": "*"
    }
]
```