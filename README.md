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

2. Create the EKS cluster:

```bash
cd eksctl
eksctl create cluster -f cluster.yaml
```

3. Apply the storage class:

```bash
kubectl apply -f ebs/gp3.yaml
```

4. Apply the custom node pool:

```bash
kubectl apply -f karpenter/custom-nodepool.yaml
```

5. Deploy the stateful Nginx service:

```bash
kubectl apply -f sts.yaml
```

6. Deploy the 2048 game application:

```bash
kubectl apply -f lb-app/manifest.yaml
```


### with Terraform
You can also use Terraform to provision the cluster.

```bash
cd terraform
terraform init
terraform plan -out=planfile
terraform apply planfile
```

### Configuration

- Cluster configuration: Edit `cluster.yaml` to modify EKS cluster settings. Note that Auto Mode is enabled, which includes built-in Karpenter managed node pools.
- Storage classes: Auto Mode includes the gp2 storage class by default. Adjust `ebs/gp3.yaml` for additional storage requirements
- Node pools: Auto Mode includes built-in managed node pools. Modify `karpenter/custom-nodepool.yaml` for additional node configurations
- Application deployment: Update `lb-app/manifest.yaml` for the 2048 game application settings

### Troubleshooting

1. EKS cluster creation fails:
   - Check AWS credentials and permissions
   - Ensure eksctl version is compatible with EKS 1.29
   - Review CloudFormation logs in AWS console

2. Karpenter node provisioning issues:
   - Verify IAM roles and policies for Karpenter
   - Check Karpenter logs: `kubectl logs -n karpenter -l app.kubernetes.io/name=karpenter -c controller`

3. ALB Ingress not working:
   - Ensure ALB Ingress Controller is installed and running
   - Check ALB Ingress Controller logs: `kubectl logs -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller`

### Performance Optimization

- Monitor EKS control plane metrics using CloudWatch
- Use `kubectl top nodes` and `kubectl top pods` to check resource utilization
- Adjust Karpenter node pool configurations based on workload requirements
- Consider using gp3 EBS volumes for improved performance and cost-effectiveness

## Data Flow

The request data flow through the application involves several components:

1. User request → ALB Ingress
2. ALB Ingress → Kubernetes Service (service-2048)
3. Kubernetes Service → 2048 game Pods
4. 2048 game Pods process the request and send the response back
5. Response travels back through the Service and ALB to the user

```
[User] <--> [ALB Ingress] <--> [K8s Service] <--> [2048 Game Pods]
```

Notes:
- The ALB Ingress uses IP-based target type for routing traffic to the Pods
- The 2048 game application is deployed with 5 replicas for high availability
- Karpenter manages the underlying EC2 instances based on the defined node pools

## Infrastructure

The project defines the following key infrastructure resources:

### EKS Cluster
- Type: Amazon EKS Cluster
- Name: auto-mode-cluster
- Region: ap-southeast-1
- Version: 1.29
- Auto Mode: Enabled

### Storage Classes
1. GP2 StorageClass
   - Name: gp2
   - Provisioner: kubernetes.io/aws-ebs
   - Volume Type: gp2

2. GP3 StorageClass (Default)
   - Name: gp3
   - Provisioner: ebs.csi.eks.amazonaws.com
   - Volume Type: gp3

### Karpenter Node Pools
1. Custom Node Pool
   - Resource Limits: 1 vCPU
   - Instance Types: c, m, or r series (4th generation or newer)
   - Architecture: amd64
   - Capacity Type: Spot or On-Demand

2. General-Purpose Node Pool
   - Instance Types: c, m, or r series (4th generation or newer)
   - Architecture: amd64
   - Capacity Type: On-Demand

3. System Node Pool
   - Instance Types: c, m, or r series (4th generation or newer)
   - Architecture: amd64 or arm64
   - Capacity Type: On-Demand
   - Taint: CriticalAddonsOnly

### Application Resources
1. Namespace: game-2048

2. Deployment: deployment-2048
   - Replicas: 5
   - Container Image: public.ecr.aws/l6m2t8p7/docker-2048:latest

3. Service: service-2048
   - Type: NodePort

4. IngressClass: alb
   - Controller: eks.amazonaws.com/alb

5. Ingress: ingress-2048
   - Class: alb
   - Scheme: internet-facing
   - Target Type: IP