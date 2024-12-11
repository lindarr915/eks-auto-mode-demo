# EKS Auto Mode

## Usage

To provision the provided configurations you need to execute:

```bash
terraform init
terraform plan
terraform apply --auto-approve
```

Once the cluster has finished provisioning, you should be able to see an output which indicate how you can add the kubeconfig to your local machine, or you can use `terraform output` to get the output.

```bash
aws eks --region <region> update-kubeconfig --name <cluster-name> --alias <cluster-alias>
```

Then you can deploy a sample workload. For example, to deploy a sample deployment and see EKS Auto Mode in action, run:

```bash
kubectl apply -f deployment.yaml
```

Note that this example may create resources which cost money. Run `terraform destroy` when you don't need these resources.

<!-- BEGIN_TF_DOCS -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 1.3.2 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 5.79 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | 5.80.0 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_eks"></a> [eks](#module\_eks) | terraform-aws-modules/eks/aws | ~> 20.31.1 |
| <a name="module_vpc"></a> [vpc](#module\_vpc) | terraform-aws-modules/vpc/aws | ~> 5.0 |

## Resources

| Name | Type |
|------|------|
| [aws_availability_zones.available](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/availability_zones) | data source |

## Inputs

No inputs.

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_configure_kubectl"></a> [configure\_kubectl](#output\_configure\_kubectl) | Configure kubectl: make sure you're logged in with the correct AWS profile and run the following command to update your kubeconfig |

<!-- END_TF_DOCS -->
