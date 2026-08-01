# AWS Cost Estimates - Ultimate DevOps Real-World Project Implementation

> **Summary:** Most students complete this course for **$30-50 total**. With smart resource management and Spot instances, you can finish for under $40.

---

## Table of Contents

- [Overview](#overview)
- [Course Structure and Instance Types](#course-structure-and-instance-types)
- [Detailed Cost Breakdown by Section](#detailed-cost-breakdown-by-section)
- [Spot Instance Cost Savings Guide (Section 18-19)](#spot-instance-cost-savings-guide-section-18-19)
- [AWS Service Pricing Reference](#aws-service-pricing-reference)
- [Cost Scenarios](#cost-scenarios)
- [Cost-Saving Tips](#cost-saving-tips)
- [Setting Up AWS Budget Alerts](#setting-up-aws-budget-alerts)
- [FAQ](#faq)

---

## Overview

This document provides transparent cost estimates for completing the **Ultimate DevOps Real-World Project Implementation AWS Cloud** course.

> **Pricing Note:** All prices in this document are approximate values from **AWS us-east-1 (N. Virginia) region** as of **February 2026**. Prices may vary slightly over time.

### Why Real AWS Resources?

This course uses **real AWS infrastructure** because:
- You learn production-grade patterns
- Hands-on experience with actual services
- Skills directly transferable to your job
- No simulations or shortcuts

### The Good News

| Fact | Details |
|------|---------|
| **80% of EKS content** | Uses cost-efficient **t3.small** nodes |
| **Docker sections** | Only need a **single EC2 instance** |
| **Terraform sections** | Build 3-Tier VPC, NAT Gateway runs only during practice, then progress to production-grade EKS clusters |
| **Typical total cost** | **$30-50** for the entire course |
| **Section 18-19** | Spot instance option available - save approximately 70% on autoscaling demos |

---

## Course Structure and Instance Types

### Course Overview

| Metric | Value |
|--------|-------|
| Total Sections | 21 |
| Total Lectures | 235 |
| Total Duration | 38 hours 15 minutes |
| Hands-on Demos | 55+ |

### Instance Types Used Per Section

| Sections | Content | Duration | Instance Type | Notes |
|----------|---------|----------|---------------|-------|
| **2-5** | Docker | ~4 hrs | Single t3.large EC2 | Runs 10 containers (5 microservices + 5 databases) |
| **6** | Terraform Basics | ~5 hrs | 3-Tier AWS VPC with NAT Gateway | Learn Terraform by building production VPC |
| **7-13** | EKS Cluster + K8s Basics | ~12 hrs | 3x t3.small | Sufficient for learning Kubernetes fundamentals |
| **14-17** | Full Stack + Karpenter | ~8 hrs | 3x t3.small + AWS DBs | Production patterns with managed services |
| **18** | HPA Autoscaling | ~1.5 hrs | Karpenter auto-provisions (On-Demand or Spot) | Spot files provided - see guide below |
| **19** | Retail Store Helm + Dataplane | ~1.5 hrs | Karpenter auto-provisions (On-Demand or Spot) | Spot files provided - see guide below |
| **20-21** | OpenTelemetry + CI/CD | ~6-7 hrs | 3x t3.large + AWS DBs | OTEL collectors need more memory |

### Key Insights

1. **32+ hours** of the course (84%) runs on **t3.small nodes** - the most cost-efficient option.

2. Only the final **6-7 hours** (OpenTelemetry and CI/CD sections) require t3.large nodes.

3. **Section 18-19 (HPA and Helm with Autoscaling):** 
   - Karpenter dynamically provisions nodes based on pod demand
   - I demo using **On-Demand** nodes for reliability during recording
   - **You can switch to Spot instances** to save approximately 70% - all files are provided in the course
   - Recommended approach: Watch the section first, understand the concepts, then practice with Spot
   - This teaches you where to modify: nodeSelector, number of pods, PDB rules, topology spread constraints, etc.

---

## Detailed Cost Breakdown by Section

> **Note:** All prices below are approximate values from AWS us-east-1 region as of February 2026.

### Phase 1: Docker Fundamentals (Sections 2-5)

**Duration:** Approximately 4 hours of content

**What you will run:**
- Single t3.large EC2 instance
- 10 Docker containers locally on EC2

**Why t3.large?**
We run the complete Retail Store application with 10 containers:
- 5 Microservices (UI, Catalog, Cart, Checkout, Orders)
- 5 Data stores (MySQL, PostgreSQL, Redis, RabbitMQ, DynamoDB Local)

| Resource | Approx. Hourly Cost | Approx. Daily Cost (4 hrs practice) |
|----------|---------------------|-------------------------------------|
| t3.large EC2 | $0.0832 | $0.33 |
| EBS Storage (20GB) | ~$0.003 | $0.07 |
| **Daily Total** | | **Approx. $0.40** |

**Phase 1 Estimate:** Approx. $2-5 total

---

### Phase 2: Terraform Basics (Section 6)

**Duration:** Approximately 5 hours of content

**What you will build:**
- Production-grade 3-Tier AWS VPC
- Public, Private, and Database subnets across 3 AZs
- 1 NAT Gateway (cost-optimized for learning)
- Internet Gateway, Route Tables

**Why this matters:**
You learn Terraform by building **real infrastructure**, not just reading syntax. The VPC you create here becomes the foundation for your EKS cluster.

| Resource | Approx. Hourly Cost | Notes |
|----------|---------------------|-------|
| NAT Gateway | $0.045/hr | Only runs during practice |
| VPC, Subnets, Routes | Free | No charge for these resources |
| Internet Gateway | Free | No hourly charge |
| **Per Practice Session (2-3 hrs)** | | **Approx. $0.10-0.15** |

**Cost Pattern:** Create VPC, Practice, Destroy with `terraform destroy`

**Phase 2 Estimate:** Approx. $1-3 total

---

### Phase 3: EKS Cluster and Kubernetes Basics (Sections 7-13)

**Duration:** Approximately 12 hours of content

**What you will run:**
- EKS Control Plane
- 3x t3.small worker nodes
- NAT Gateway
- Application Load Balancer
- RDS MySQL (for Section 10)

| Resource | Approx. Hourly Cost | Approx. Daily Cost (4 hrs practice) |
|----------|---------------------|-------------------------------------|
| EKS Control Plane | $0.10 | $0.40 |
| 3x t3.small nodes | $0.0624 | $0.25 |
| NAT Gateway | $0.045 | $0.18 |
| ALB | $0.0225 | $0.09 |
| RDS MySQL (db.t3.micro) | $0.017 | $0.07 |
| **Daily Total** | | **Approx. $1.00** |

**Pro Tip:** Create and destroy EKS cluster per session using Terraform. It takes only 15-20 minutes to create.

**Phase 3 Estimate:** Approx. $5-12 total

---

### Phase 4: Production Stack with Karpenter, HPA and Helm (Sections 14-19)

**Duration:** Approximately 11 hours of content

**What you will run:**
- EKS with 3x t3.small nodes (base configuration)
- Full AWS Persistent Dataplane:
  - RDS MySQL
  - RDS PostgreSQL
  - DynamoDB
  - ElastiCache (Redis)
  - SQS
- Karpenter (auto-provisions nodes as needed)
- External DNS + SSL
- Horizontal Pod Autoscaler (HPA)
- Helm deployments

| Resource | Approx. Hourly Cost | Approx. Daily Cost (4 hrs practice) |
|----------|---------------------|-------------------------------------|
| EKS Control Plane | $0.10 | $0.40 |
| 3x t3.small nodes | $0.0624 | $0.25 |
| NAT Gateway | $0.045 | $0.18 |
| ALB | $0.0225 | $0.09 |
| RDS MySQL (db.t3.micro) | $0.017 | $0.07 |
| RDS PostgreSQL (db.t3.micro) | $0.017 | $0.07 |
| ElastiCache (cache.t3.micro) | $0.017 | $0.07 |
| DynamoDB (on-demand) | ~$0.00 | Pennies |
| SQS | ~$0.00 | Pennies |
| **Daily Total** | | **Approx. $1.20** |

#### Section 18-19 (HPA and Helm Autoscaling) - Additional Notes

During Karpenter autoscaling demos:
- Karpenter dynamically provisions **3-7 additional t3.small nodes** based on HPA scaling
- Each microservice has: minReplicas=3, maxReplicas=12
- Nodes are created only when pods need them
- Demo completes in **approximately 90 minutes**
- Karpenter **automatically terminates** unused nodes after demo

**Cost Comparison: On-Demand vs Spot for Section 18-19**

| Capacity Type | 7 nodes x 1.5 hrs | Approx. Cost |
|---------------|-------------------|--------------|
| **On-Demand** | 7 x $0.0208 x 1.5 | **Approx. $0.22** |
| **Spot** | 7 x $0.007 x 1.5 | **Approx. $0.07** |
| **Savings with Spot** | | **Approximately 70%** |

See the detailed Spot Instance Guide below for exact files to modify.

**Phase 4 Estimate:** Approx. $6-12 total (with On-Demand) or $4-8 (with Spot)

---

### Phase 5: Observability and CI/CD (Sections 20-21)

**Duration:** Approximately 6-7 hours of content

**What you will run:**
- EKS with 3x **t3.large** nodes (OTEL collectors need more memory)
- Full AWS Persistent Dataplane
- ADOT Operator and Collectors
- AWS X-Ray, CloudWatch, Managed Prometheus, Managed Grafana

| Resource | Approx. Hourly Cost | Approx. Daily Cost (4 hrs practice) |
|----------|---------------------|-------------------------------------|
| EKS Control Plane | $0.10 | $0.40 |
| 3x t3.large nodes | $0.2496 | $1.00 |
| NAT Gateway | $0.045 | $0.18 |
| ALB | $0.0225 | $0.09 |
| All managed databases | ~$0.07 | $0.28 |
| CloudWatch/X-Ray | ~$0.05 | $0.20 |
| Managed Prometheus | ~$0.10 | $0.40 |
| **Daily Total** | | **Approx. $2.55** |

**Phase 5 Estimate:** Approx. $8-15 total

---

## Spot Instance Cost Savings Guide (Section 18-19)

This section provides **step-by-step instructions** to switch from On-Demand to Spot instances for Sections 18 and 19, saving approximately **70% on node costs**.

### Overview

| Section | Demo Type | Duration | On-Demand Cost | Spot Cost | Savings |
|---------|-----------|----------|----------------|-----------|---------|
| **18** | K8s YAML Manifests | ~90 min | Approx. $0.22 | Approx. $0.07 | ~70% |
| **19** | Helm Charts | ~90 min | Approx. $0.22 | Approx. $0.07 | ~70% |

### Recommended Approach

1. **First:** Watch the entire section video using the default On-Demand configuration
2. **Understand:** Learn where nodeSelector, replicas, PDB rules, and topology constraints are configured
3. **Then:** Practice yourself using Spot instances (following the guide below)
4. **Benefit:** You save money AND learn how to modify these configurations - a real-world skill

---

### Section 18: HPA Autoscaling with K8s YAML Manifests

#### Files to Modify

There are **10 deployment files** across 2 folders (5 microservices x 2 demo variations):

**Folder 1: `03_RetailStore_k8s_manifests_with_Data_Plane_ScheduleAnyway`**

| File Path | Microservice |
|-----------|--------------|
| `02_RetailStore_Microservices/01_catalog/03_catalog_deployment.yaml` | Catalog |
| `02_RetailStore_Microservices/02_cart/03_cart_deployment.yaml` | Cart |
| `02_RetailStore_Microservices/03_checkout/03_checkout_deployment.yaml` | Checkout |
| `02_RetailStore_Microservices/04_orders/03_orders_deployment.yaml` | Orders |
| `02_RetailStore_Microservices/05_ui/03_ui_deployment.yaml` | UI |

**Folder 2: `04_RetailStore_k8s_manifests_with_Data_Plane_DoNotSchedule`**

| File Path | Microservice |
|-----------|--------------|
| `02_RetailStore_Microservices/01_catalog/03_catalog_deployment.yaml` | Catalog |
| `02_RetailStore_Microservices/02_cart/03_cart_deployment.yaml` | Cart |
| `02_RetailStore_Microservices/03_checkout/03_checkout_deployment.yaml` | Checkout |
| `02_RetailStore_Microservices/04_orders/03_orders_deployment.yaml` | Orders |
| `02_RetailStore_Microservices/05_ui/03_ui_deployment.yaml` | UI |

#### What to Change

In each deployment file, find the `nodeSelector` section and change:

```yaml
# FROM (On-Demand - used in video demo)
      # THIS IS CRITICAL - Forces pods to run ONLY on on-demand nodes
      nodeSelector:
        karpenter.sh/capacity-type: on-demand    

# TO (Spot - for cost savings)
      # THIS IS CRITICAL - Forces pods to run ONLY on Spot nodes
      nodeSelector:
        karpenter.sh/capacity-type: spot   
```

#### Quick Command (Linux/Mac)

```bash
# Navigate to Section 18 folder
cd 18_Autoscaling_HPA

# Replace on-demand with spot in all deployment files
find . -name "*_deployment.yaml" -exec sed -i 's/capacity-type: on-demand/capacity-type: spot/g' {} \;

# Verify the change
grep -r "capacity-type:" --include="*_deployment.yaml"
```

---

### Section 19: Helm Charts with AWS Dataplane

#### Files to Modify

There are **7 Helm values files** in the folder:
`03_RetailStore_Helm_with_Data_Plane/02_retailstore_values_HELM_aws_dataplane/`

| File | Microservice |
|------|--------------|
| `values-catalog.yaml` | Catalog (v1.0.0) |
| `values-catalog-v2.0.0.yaml` | Catalog (v2.0.0) |
| `values-cart.yaml` | Cart |
| `values-checkout.yaml` | Checkout |
| `values-orders.yaml` | Orders (v1.0.0) |
| `values-orders-v2.0.0.yaml` | Orders (v2.0.0) |
| `values-ui.yaml` | UI |

#### What to Change

In each values file, find the `nodeSelector` section and change:

```yaml
# FROM (On-Demand - used in video demo)
# ----------------------------------------------------------------------------
# KARPENTER NODE SELECTION
# ----------------------------------------------------------------------------
# Schedule pods on Karpenter On-Demand nodes for stability
nodeSelector:
  karpenter.sh/capacity-type: on-demand

# TO (Spot - for cost savings)
# ----------------------------------------------------------------------------
# KARPENTER NODE SELECTION
# ----------------------------------------------------------------------------
# Schedule pods on Karpenter Spot nodes for cost savings
nodeSelector:
  karpenter.sh/capacity-type: spot
```

#### Quick Command (Linux/Mac)

```bash
# Navigate to Section 19 Helm values folder
cd 19_Helm_RetailStore_AWS_Dataplane/03_RetailStore_Helm_with_Data_Plane/02_retailstore_values_HELM_aws_dataplane

# Replace on-demand with spot in all values files
sed -i 's/capacity-type: on-demand/capacity-type: spot/g' values-*.yaml

# Verify the change
grep "capacity-type:" values-*.yaml
```

---

### What You Will Learn by Doing This

By manually switching from On-Demand to Spot, you learn:

| Concept | What You Learn |
|---------|----------------|
| **nodeSelector** | How pods target specific node types |
| **Karpenter NodePools** | How Karpenter provisions On-Demand vs Spot nodes |
| **HPA + Karpenter Integration** | How pod scaling triggers node scaling |
| **PDB (Pod Disruption Budget)** | How to maintain availability during Spot interruptions |
| **Topology Spread Constraints** | How to distribute pods across AZs |
| **Cost Optimization** | Real-world skill for production clusters |

---

### Detailed Cost Comparison

#### Section 18-19 Autoscaling Demo (On-Demand)

| Component | Quantity | Duration | Hourly Rate | Cost |
|-----------|----------|----------|-------------|------|
| EKS Control Plane | 1 | 1.5 hrs | $0.10 | $0.15 |
| Base t3.small nodes | 3 | 1.5 hrs | $0.0208 | $0.09 |
| Karpenter-provisioned t3.small | 4-7 | 1.5 hrs | $0.0208 | $0.12-0.22 |
| NAT Gateway | 1 | 1.5 hrs | $0.045 | $0.07 |
| ALB | 1 | 1.5 hrs | $0.0225 | $0.03 |
| **Total (On-Demand)** | | | | **Approx. $0.50-0.60** |

#### Section 18-19 Autoscaling Demo (Spot)

| Component | Quantity | Duration | Hourly Rate | Cost |
|-----------|----------|----------|-------------|------|
| EKS Control Plane | 1 | 1.5 hrs | $0.10 | $0.15 |
| Base t3.small nodes | 3 | 1.5 hrs | $0.0208 | $0.09 |
| Karpenter-provisioned t3.small (Spot) | 4-7 | 1.5 hrs | ~$0.007 | $0.04-0.07 |
| NAT Gateway | 1 | 1.5 hrs | $0.045 | $0.07 |
| ALB | 1 | 1.5 hrs | $0.0225 | $0.03 |
| **Total (Spot)** | | | | **Approx. $0.38-0.45** |

**Savings per demo session:** Approx. $0.10-0.15 (approximately 20-25%)

> **Note:** The base managed node group (3x t3.small) remains On-Demand for Karpenter controller stability. Only the Karpenter-provisioned autoscaling nodes use Spot.

---

## AWS Service Pricing Reference

> **Note:** All prices from AWS us-east-1 (N. Virginia) region as of February 2026. Always verify current pricing in AWS console.

### EC2 Instance Pricing (us-east-1, On-Demand)

| Instance Type | vCPU | Memory | Approx. Hourly Cost | Use Case in Course |
|---------------|------|--------|---------------------|-------------------|
| t3.micro | 2 | 1 GB | $0.0104 | Not sufficient |
| t3.small | 2 | 2 GB | $0.0208 | EKS nodes (Sec 7-19) |
| t3.medium | 2 | 4 GB | $0.0416 | Alternative option |
| t3.large | 2 | 8 GB | $0.0832 | Docker and OTEL sections |

### EC2 Spot Instance Savings (us-east-1)

| Instance Type | On-Demand | Approx. Spot Price | Savings |
|---------------|-----------|--------------------| --------|
| t3.small | $0.0208 | ~$0.006-0.008 | ~65-70% |
| t3.medium | $0.0416 | ~$0.012-0.015 | ~65-70% |
| t3.large | $0.0832 | ~$0.025-0.030 | ~65-70% |

### EKS and Networking

| Service | Approx. Hourly Cost | Notes |
|---------|---------------------|-------|
| EKS Control Plane | $0.10 | Fixed cost per cluster |
| NAT Gateway | $0.045 | + $0.045/GB data processed |
| Application Load Balancer | $0.0225 | + LCU charges (minimal) |

### Managed Databases (Smallest Instances)

| Service | Instance | Approx. Hourly Cost | Notes |
|---------|----------|---------------------|-------|
| RDS MySQL | db.t3.micro | $0.017 | Free tier eligible (750 hrs/month) |
| RDS PostgreSQL | db.t3.micro | $0.017 | Free tier eligible (750 hrs/month) |
| ElastiCache Redis | cache.t3.micro | $0.017 | |
| DynamoDB | On-demand | Pay per request | Pennies for course usage |
| SQS | Standard | $0.40/million | Essentially free for course |

---

## Cost Scenarios

### Scenario 1: Fast and Efficient Learner

**Profile:**
- Completes course in 2-3 weeks
- Practices 3-4 hours daily
- Destroys resources after each session using `terraform destroy`
- Uses Spot instances for Section 18-19

| Phase | Days | Approx. Daily Cost | Total |
|-------|------|------------|-------|
| Docker | 2 | $0.50 | $1 |
| Terraform (VPC) | 2 | $0.50 | $1 |
| EKS Basics | 5 | $1.00 | $5 |
| Production Stack + Karpenter + HPA | 5 | $1.20 | $6 |
| OTEL + CI/CD | 3 | $2.50 | $7.50 |
| **TOTAL** | **17 days** | | **Approx. $20.50** |

**Buffer for mistakes:** +$5

**Total: Approx. $25-30**

---

### Scenario 2: Average Pace Learner

**Profile:**
- Completes course in 4-6 weeks
- Practices 2-3 hours daily
- Destroys resources after each session
- Uses Spot instances for Section 18-19

| Phase | Days | Approx. Daily Cost | Total |
|-------|------|------------|-------|
| Docker | 4 | $0.60 | $2.40 |
| Terraform (VPC) | 3 | $0.50 | $1.50 |
| EKS Basics | 8 | $1.20 | $9.60 |
| Production Stack + Karpenter + HPA | 7 | $1.50 | $10.50 |
| OTEL + CI/CD | 5 | $3.00 | $15 |
| **TOTAL** | **27 days** | | **Approx. $39** |

**Buffer for mistakes:** +$6

**Total: Approx. $40-50**

---

### Scenario 3: If You Forget to Destroy Resources

**What happens if you leave resources running overnight or over a weekend?**

| Scenario | Additional Cost |
|----------|-----------------|
| EKS cluster running overnight (8 hrs) | Approx. $2.00 |
| EKS cluster running over weekend (48 hrs) | Approx. $12.00 |
| RDS instance running 1 week unused | Approx. $3.00 |

**Important:** Always run `terraform destroy` when you finish practicing for the day. This single habit can save you $20-50 over the course.

---

### Summary Comparison

| Scenario | Duration | Estimated Cost |
|----------|----------|----------------|
| Fast and Efficient | 2-3 weeks | **Approx. $25-30** |
| Average Pace | 4-6 weeks | **Approx. $40-50** |
| Forgot to destroy resources | - | **Add $20-50 extra** |

---

## Cost-Saving Tips

### Critical: Resource Management

#### 1. STOP vs TERMINATE - Know the Difference

| Action | What Happens | When to Use |
|--------|--------------|-------------|
| **STOP** | Instance paused, EBS charges continue (~$0.10/GB/month) | Taking a break for a few hours |
| **TERMINATE** | Instance deleted, no charges | Done for the day or section |

#### 2. Use Terraform for Infrastructure

```bash
# Create infrastructure when starting practice
terraform apply

# Destroy when done for the day
terraform destroy
```

**Time investment:** 15-20 minutes to create EKS cluster

**Savings:** Potentially **50-70%** of your total cost

#### 3. The Golden Rule Checklist

Before closing your laptop, verify:

- EC2 instances stopped/terminated
- EKS cluster destroyed (if done for the day)
- RDS instances stopped (or deleted if section complete)
- Load Balancers deleted
- NAT Gateways deleted (part of VPC destroy)
- Check AWS Cost Explorer

### Smart Practices

#### 4. Use us-east-1 Region

North Virginia (us-east-1) has the **lowest prices** for most AWS services. All course demos use this region.

#### 5. Leverage AWS Free Tier

| Service | Free Tier Allowance | Notes |
|---------|---------------------|-------|
| EC2 | 750 hrs/month t2.micro | Not used in course (too small) |
| RDS | 750 hrs/month db.t3.micro | Use this |
| S3 | 5 GB storage | Terraform state |
| CloudWatch | 10 metrics, 5 GB logs | Basic monitoring |

#### 6. Use Spot Instances for Section 18-19

See the **Spot Instance Cost Savings Guide** above for detailed instructions.

**Summary:**
- Watch the demo video first (I use On-Demand)
- Understand nodeSelector, HPA, PDB, topology constraints
- Practice using Spot instances (files provided)
- Save approximately 70% on autoscaling node costs

#### 7. Practice in Focused Sessions

**Less effective:**
- Leave cluster running while watching videos
- Create resources, take a 2-hour break, come back

**More effective:**
- Watch video first, understand the concept
- Create resources, practice, destroy
- Repeat for next demo

### Section-Specific Tips

| Section | Tip |
|---------|-----|
| Docker (2-5) | Stop EC2 instance during breaks |
| Terraform (6) | VPC + NAT Gateway - destroy after each practice |
| EKS (7-13) | Destroy cluster daily, recreate tomorrow (15-20 mins) |
| Databases (14) | Stop RDS when not in use |
| Karpenter (17) | NodePools auto-terminate unused nodes |
| HPA (18) | Use Spot instances - see guide above |
| Helm (19) | Use Spot instances - see guide above |
| OTEL (20) | This section costs more - plan focused sessions |

---

## Setting Up AWS Budget Alerts

### Step 1: Go to AWS Budgets

1. Open AWS Console
2. Search for "Budgets"
3. Click "Create budget"

### Step 2: Create Cost Budget

1. Select "Cost budget - Recommended"
2. Set budget name: `DevOps-Course-Budget`
3. Set amount: `$50` (adjust based on your scenario)
4. Set to "Monthly" recurring

### Step 3: Configure Alerts

Create alerts at these thresholds:

| Threshold | Alert Type | Action |
|-----------|------------|--------|
| 50% ($25) | Email | Check resource usage |
| 80% ($40) | Email | Review and cleanup unused resources |
| 100% ($50) | Email | Immediate review required |

### Step 4: Add Your Email

Enter your email address to receive alerts.

**Tip:** Check AWS Cost Explorer weekly to track spending patterns.

---

## FAQ

### Q: Can I use t2.micro (Free Tier) for this course?

**A:** Unfortunately, no. The Docker sections require running 10 containers simultaneously (5 microservices + 5 databases). t2.micro has only 1GB RAM - Java applications alone need 512MB-1GB each. t3.large (8GB) is the minimum for Docker sections.

### Q: What if I cannot afford the full cost right now?

**A:** Consider these strategies:

1. **Watch videos first:** You can watch all videos without creating any AWS resources. Watch each section twice to understand the approach, architecture, and commands before you start practicing. This way, when you do practice, you will be faster and more efficient.

2. **Complete in phases:** Start with Docker sections (approximately $2-5), then take a break if needed. Complete remaining sections when you are ready.

3. **Use Spot instances:** For Sections 18-19, switch to Spot instances to save approximately 70% on node costs (see the guide above).

4. **Lifetime access:** Your course access never expires. There is no rush - complete it at your own pace over weeks or months.

### Q: Can I use Spot instances throughout the course?

**A:** For EKS node groups provisioned by Karpenter, yes. You can modify the Terraform variables or Kubernetes manifests as shown in the Spot Instance Guide above.

This saves approximately 70% on EC2 costs. However, Spot instances can be interrupted, so use On-Demand for critical learning sessions where you do not want disruptions.

### Q: What is the most expensive part?

**A:** Sections 20-21 (OpenTelemetry and CI/CD) cost more because:
- t3.large nodes are required (OTEL collectors need more memory)
- Multiple observability services running
- Complete application stack with all databases

**Tip:** Plan focused 2-3 day sessions for these sections.

### Q: Will Karpenter autoscaling create unexpected costs?

**A:** Karpenter is designed to be cost-efficient:
- It provisions nodes **only when needed**
- It **automatically terminates** nodes when workload decreases
- In Section 17-19 demos, nodes are created temporarily (demo completes in approximately 90 minutes)
- You can use **Spot instances** for additional savings (see guide above)

The autoscaling demos may create 3-7 extra t3.small nodes temporarily, costing approximately $0.10-0.20 for the demo duration with On-Demand, or approximately $0.04-0.07 with Spot.

### Q: How do I know if I left something running?

**A:** Check these places in AWS Console:

1. **EC2 Dashboard** - Running instances
2. **EKS Dashboard** - Clusters
3. **RDS Dashboard** - Databases
4. **VPC Dashboard** - NAT Gateways
5. **EC2 Dashboard** - Load Balancers
6. **Cost Explorer** - Daily costs

### Q: Is this cost worth it?

**A:** Consider the return on investment:

| Investment | Return |
|------------|--------|
| $30-50 AWS costs | Production-grade DevOps skills |
| 38 hours of learning | Resume-worthy project experience |
| Real hands-on practice | Confidence in interviews |
| | Potential salary increase: $10K-30K+ |

The skills you learn are exactly what companies pay $150K+ for in senior DevOps roles.

---

## Need Help?

If you have questions about costs or need guidance, ask in the **Udemy Q&A Section**. I respond to all questions personally.

---

## Summary

| Key Point | Details |
|-----------|---------|
| **Typical Total Cost** | Approx. $30-50 |
| **Most Expensive Phase** | OTEL + CI/CD (Sec 20-21) |
| **Biggest Cost Saver** | `terraform destroy` after each session |
| **Spot Instance Option** | Available for Sec 18-19, saves approximately 70% on autoscaling nodes |
| **Recommended Budget Alert** | $50 |
| **Best Region** | us-east-1 (all demos use this) |

---

**Remember:** This is an investment in your career. The cost of this course (content + AWS) is a fraction of what you will earn with these skills.

Happy Learning!

---

*Last Updated: February 2026*

*All prices are approximate values from AWS us-east-1 (N. Virginia) region as of February 2026. Actual costs may vary slightly. Always monitor your AWS billing dashboard and verify current pricing in AWS console.*
