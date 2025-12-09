![1735216850313](https://github.com/user-attachments/assets/78e13bba-2b51-4d78-9299-b6322f765628)
![1735216850298](https://github.com/user-attachments/assets/3dd03b6d-41fa-4fbe-9dc6-ece58d7e8956)
![1735216850297](https://github.com/user-attachments/assets/b4548019-af13-4f1b-bc1a-a2f89e611dca)
![1735216850242](https://github.com/user-attachments/assets/12fbebeb-d9e5-4a37-9a34-67dbb6486610)
![1735216850215](https://github.com/user-attachments/assets/a1e2e0ec-36eb-44e1-bc32-4fd7cc944014)
![1735216850112](https://github.com/user-attachments/assets/4c2f4c10-1a39-46ca-9a3f-ddfb2af2be44)
![1735216850030](https://github.com/user-attachments/assets/291fa99b-f2b5-4dfe-a459-c2c1bb33dd3b)
![1735216849999](https://github.com/user-attachments/assets/5c197863-afd4-4a87-8308-18912bfb2659)
![1735216849994](https://github.com/user-attachments/assets/38e4f942-d4e8-480f-9fa3-cc2de0c1909a)
![1735216849796](https://github.com/user-attachments/assets/d8d11574-380d-4037-be53-713a26c83153)
![1735216849521](https://github.com/user-attachments/assets/e6c7a92d-6c77-47cd-87da-5a9aa2ada4dc)
![1735216849244](https://github.com/user-attachments/assets/7d887527-98d4-4d74-b745-9c9c87245da1)
![1735216848703](https://github.com/user-attachments/assets/d67ee201-f446-4c6c-90ef-1a9667c5ea7d)
![1735216848217](https://github.com/user-attachments/assets/1d4b4eec-15af-44e8-971f-1c4419ea36e3)
![1735216848019](https://github.com/user-attachments/assets/95bcf195-b42a-47dd-8b7c-55216c6b29e7)
# FitWeb - ECS Fargate Deployment

## Overview
Complete infrastructure-as-code deployment of a containerized fitness web application on AWS ECS Fargate. This project demonstrates serverless container orchestration, secure networking, and automated infrastructure provisioning using Terraform.

## Architecture
```
Internet → ALB → ECS Service (Fargate) → Docker Container (Nginx)
           ↓
    Target Group
           ↓
      VPC (Public Subnets)
```

**Infrastructure Components:**
- Custom VPC with multi-AZ public subnets
- ECS Cluster with Fargate launch type
- Application Load Balancer for traffic distribution
- IAM roles for task execution
- Security groups for controlled access

## Technologies Used
- **AWS Services:** ECS Fargate, VPC, ALB, IAM, CloudWatch
- **IaC:** Terraform (v1.5+)
- **Container:** Docker
- **Web Server:** Nginx
- **Languages:** HCL, HTML, CSS

## Features
- ✅ Serverless container deployment (no EC2 management)
- ✅ Automatic load balancing across availability zones
- ✅ Infrastructure as Code for reproducible deployments
- ✅ Secure networking with isolated VPC
- ✅ Zero-downtime deployments

## Project Structure
```
fitweb-ecs-project/
├── main.tf              # VPC, ECS cluster, services
├── variables.tf         # Input variables
├── outputs.tf           # ECS cluster ID, public IPs
├── provider.tf          # AWS provider configuration
├── Dockerfile           # Container image definition
├── index.html           # Application frontend
├── styles.css           # Application styling
└── .gitignore           # Terraform state files
```

## Prerequisites
- AWS Account with ECS and VPC permissions
- Terraform v1.5 or later
- Docker installed locally
- AWS CLI configured
- Git for version control

## Setup Instructions

### 1. Clone Repository
```bash
git clone https://github.com/DrewTheeFourth/fitness-webapp-deployment.git
cd fitness-webapp-deployment
```

### 2. Review Configuration
Edit `variables.tf` to customize:
- VPC CIDR block
- Subnet CIDR blocks
- AWS region
- Availability zones

### 3. Initialize Terraform
```bash
terraform init
```

### 4. Validate Configuration
```bash
terraform validate
terraform fmt
```

### 5. Plan Deployment
```bash
terraform plan -out=tfplan
```

### 6. Apply Infrastructure
```bash
terraform apply tfplan
```

### 7. Retrieve Application URL
```bash
terraform output alb_dns_name
# Visit: http://<alb-dns-name> in your browser
```

## Terraform Resources Created

| Resource | Purpose |
|----------|---------|
| aws_vpc | Isolated network environment |
| aws_subnet | Public subnets across 2 AZs |
| aws_internet_gateway | Internet connectivity |
| aws_security_group | Firewall rules (port 80) |
| aws_ecs_cluster | Container orchestration |
| aws_ecs_task_definition | Container specifications |
| aws_ecs_service | Service management |
| aws_iam_role | Task execution permissions |

## Application Details

**FitWeb Features:**
- Custom workout timers
- Meal plan recommendations
- Progress tracking interface
- Responsive design for mobile/desktop

**Container Configuration:**
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/
COPY styles.css /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Verification & Testing

### Check ECS Service Status
```bash
aws ecs describe-services \
  --cluster fitweb-ecs-cluster \
  --services fitweb-service \
  --query 'services[0].{Status:status,Running:runningCount,Desired:desiredCount}'
```

### View Task Logs
```bash
aws logs tail /ecs/fitweb-task --follow
```

### Test Application
```bash
# Get ALB DNS name
ALB_DNS=$(terraform output -raw alb_dns_name)

# Test HTTP response
curl -I http://$ALB_DNS
```

## Cost Optimization
- Fargate Spot for non-production (up to 70% savings)
- Right-sized task CPU/memory allocation
- Auto-scaling policies for dynamic workloads

## Security Considerations
- Security groups restrict traffic to port 80 only
- IAM roles follow least privilege principle
- VPC provides network isolation
- CloudWatch logs enabled for audit trails

## Troubleshooting

**Common Issues:**

1. **Task won't start:**
```bash
   aws ecs describe-tasks --cluster fitweb-ecs-cluster --tasks <task-id>
   # Check "stoppedReason" field
```

2. **Can't access application:**
   - Verify security group allows port 80 from 0.0.0.0/0
   - Check target group health status
   - Ensure task is in RUNNING state

3. **Terraform errors:**
```bash
   terraform refresh
   terraform plan
```

## What I Learned
- Implementing serverless container orchestration with ECS Fargate
- Designing multi-AZ architectures for high availability
- Using Terraform for reproducible infrastructure deployments
- Troubleshooting ECS task launch failures and networking issues

## Future Enhancements
- [ ] Add HTTPS with ACM certificate
- [ ] Implement CI/CD pipeline with GitHub Actions
- [ ] Add RDS database for persistent storage
- [ ] Configure auto-scaling based on CPU/memory metrics
- [ ] Add CloudFront CDN for global content delivery
- [ ] Implement blue/green deployment strategy

## Cleanup
```bash
terraform destroy
# Confirm with 'yes' when prompted
```

---

**AWS Certified Solutions Architect**
