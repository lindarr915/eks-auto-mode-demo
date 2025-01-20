# EKS Auto Mode Demo

This project provides configuration files and manifests for setting up an Amazon EKS (Elastic Kubernetes Service) cluster with custom node pools, storage classes, and a sample 2048 game application deployment.

The repository contains YAML configuration files for creating an EKS cluster with Auto Mode enabled, defining custom and managed node pools using Karpenter, setting up EBS storage classes, and deploying a stateful Nginx service along with a 2048 game application exposed through an Application Load Balancer (ALB).

## Repository Structure

```
.
├── cluster
│   └── cluster.yaml
├── ebs
│   ├── gp2.yaml
│   └── gp3.yaml
├── karpenter
│   ├── custom-nodepool.yaml
│   └── managed-nodepool.yaml
├── lb-app
│   └── manifest.yaml
└── sts.yaml
```

### Key Files:

- `cluster.yaml`: EKS cluster configuration with Auto Mode enabled
- `ebs/`: EBS storage class definitions
- `karpenter/`: Karpenter node pool configurations
- `lb-app/manifest.yaml`: 2048 game application deployment and ALB ingress
- `sts.yaml`: Stateful Nginx service configuration

## Usage Instructions

### Prerequisites

- AWS CLI (version 2.0 or later)
- eksctl (version compatible with EKS 1.29, 0.197.0+)
- kubectl (version 1.29 or compatible)
- Helm (version 3.0 or later)

### Installation

1. Set up your AWS credentials:

```bash
aws configure
```

### with Terraform
You can also use Terraform to provision the cluster.

```bash
cd terraform
terraform init
terraform plan -out=planfile
terraform apply planfile
```
