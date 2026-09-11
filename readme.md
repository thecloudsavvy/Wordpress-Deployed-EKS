# WordPress on Amazon EKS

Terraform provisions a VPC and an EKS cluster; Helm deploys WordPress onto that cluster.

## What it deploys

| Path | What it is |
| --- | --- |
| `terrform/` | VPC (public/private subnets, NAT) and EKS via `terraform-aws-modules`, plus an IRSA role for the EBS CSI driver |
| `wordpress/` | Helm chart for WordPress (`wordpress:php7.4-apache`) with MySQL, PersistentVolumeClaim, Secret, and ConfigMap templates |
| `.github/workflows/wordpress.yml` | Helm upgrade job (needs AWS credentials in repo secrets; not a plan/apply pipeline) |

Ingress is **disabled** in `wordpress/values.yaml`. The Terraform cluster name is generated as `education-eks-*`. Remote state is an S3 backend in `terrform/provide.tf`.

## Terraform layout

- `terrform/main.tf` — VPC + EKS + EBS CSI IRSA
- `terrform/provide.tf` — AWS provider and S3 backend
- `terrform/variable.tf` — region (default `us-east-1`)
- `terrform/output.tf` — cluster name, endpoint, security group

## Helm chart

```bash
helm upgrade --install wordpress wordpress -f wordpress/values.yaml
```

The GitHub Actions workflow runs the same Helm upgrade after `aws eks update-kubeconfig`.
