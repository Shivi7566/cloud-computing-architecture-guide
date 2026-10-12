# Module 7: GitOps, CI/CD & Terraform Infrastructure as Code (IaC)

> **Part of the [Ultimate Cloud Computing Handbook](../README.md)** by [CloudKnowledge.in](https://cloudknowledge.in)
> Author: **Shivam Tiwari** (Senior IAM & Cloud Architect)

---

## Module 7: GitOps, CI/CD & Terraform Infrastructure as Code (IaC)

        

          Modern cloud teams treat infrastructure identically to application code: stored in version control, subjected to automated code reviews, linted for security misconfigurations, and deployed automatically via declarative pipelines.
        

        
### 7.1 Terraform & OpenTofu Fundamentals

        

          HashiCorp Terraform (and its open-source fork OpenTofu) is the industry standard declarative IaC tool. Terraform models infrastructure as a state graph, calculating execution diffs between local configuration manifests and live cloud APIs.
        

        

          State Management Best Practices: Never store `terraform.tfstate` locally. In production, configure an encrypted remote state backend using Amazon S3 (with KMS encryption enabled) combined with an Amazon DynamoDB table for distributed state locking to prevent concurrent destructive writes.
        

        
          
            hcl (terraform)
            Copy Code
          
          
```hcl
terraform {
  required_version = ">= 1.7.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.40"
    }
  }

  backend "s3" {
    bucket         = "cloudknowledge-production-tfstate"
    key            = "networking/vpc/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "cloudknowledge-tfstate-locks"
  }
}

provider "aws" {
  region = var.aws_region

  default_tags {
    tags = {
      Environment = "Production"
      ManagedBy   = "Terraform"
      Repository  = "cloudknowledge/infrastructure"
    }
  }
}

# Production VPC Definition
resource "aws_vpc" "main" {
  cidr_block           = "10.100.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "cloudknowledge-prod-vpc"
  }
}
```

        

        
### 7.2 GitOps: The Convergence Loop

        

          GitOps takes declarative IaC to its logical zenith. In a GitOps workflow (powered by tools like ArgoCD or Flux):
        

        
          - Git is the Single Source of Truth: The entire intended state of application manifests, Helm charts, and cluster configurations resides exclusively in a Git repository.

          - Pull-Based Deployment: Instead of external CI servers pushing changes into the cluster (which requires granting broad administrative credentials to external runners), an agent running inside the cluster continuously compares Git state to live cluster state.

          - Automated Self-Healing: If an operator manually modifies a service or an outage mutates configuration, the GitOps controller detects the configuration drift and immediately reconciles the cluster back to the authorized Git state.

        

        
### 7.3 Policy as Code: Guardrails with OPA & Kyverno

        

          To prevent engineers from accidentally provisioning unencrypted S3 buckets, opening security groups to `0.0.0.0/0`, or deploying containers running as `root`, enterprise platforms enforce Policy as Code:
        

        
          - Open Policy Agent (OPA) / Conftest: Written in Rego, OPA evaluates Terraform execution plans during CI/CD pull requests, failing builds before any infrastructure can be provisioned if policies are violated.

          - Kyverno: A Kubernetes-native policy engine that operates as a dynamic admission controller. It validates, mutates, and generates Kubernetes resources in real-time, blocking any pod that lacks mandatory security contexts or resource limits.

---

### 🔗 Essential Resources
- 🌐 **Main Platform:** [CloudKnowledge.in](https://cloudknowledge.in)
- 🖨️ **Print & Desk Cheat Sheets:** [PrintKrlo.in](https://printkrlo.in) (Doorstep delivery for diagrams & notes)
- 📺 **Video Labs & Walkthroughs:** [YouTube: @Cloud-Knowledge](https://youtube.com/@Cloud-Knowledge)
- 🔙 **[Return to Table of Contents](../README.md)**
