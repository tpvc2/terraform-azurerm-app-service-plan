# Azure App Service Plan

This Terraform module creates an [Azure App Service Plan](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans)
with default SKU capacity sets to "2" for dedicated plans.

## Requirements and limitations

* Azure provider >= 1.20.0
* [Mixing Windows and Linux apps in the same resource group is not supported by Azure](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro#limitations)

## Terraform version compatibility

| Module version | Terraform version |
|----------------|-------------------|
| 2.x.x          | 0.12.x            |
| 1.x.x          | 0.11.x            |

## Usage

You can use this module by including it this way:
```hcl
module "az-region" {
  source = "git::ssh://git@git.fr.clara.net/claranet/cloudnative/projects/cloud/azure/terraform/modules/regions.git?ref=vX.X.X"

  azure_region = var.azure_region
}

module "rg" {
  source = "git::ssh://git@git.fr.clara.net/claranet/cloudnative/projects/cloud/azure/terraform/modules/rg.git?ref=vX.X.X"

  location     = module.az-region.location
  client_name  = var.client_name
  environment  = var.environment
  stack        = var.stack
}

module "app_service_plan" {
  source = "git::ssh://git@git.fr.clara.net/claranet/cloudnative/projects/cloud/azure/terraform/modules/app-service-plan.git?ref=vX.X.X"

  client_name         = var.client_name
  environment         = var.environment
  stack               = var.stack
  resource_group_name = module.rg.resource_group_name
  location            = module.az-region.location
  location_short      = module.az-region.location_short

  sku = {
    tier = "Basic"
    size = "B2"
  }

  kind = "Linux"

  extra_tags = {
    foo = "bar"
  }
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| client\_name |  | string | n/a | yes |
| custom\_name | Name of the App Service Plan, generated if not set. | string | `""` | no |
| environment |  | string | n/a | yes |
| extra\_tags | Extra tags to add | map | `<map>` | no |
| kind | The kind of the App Service Plan to create. See documentation https://www.terraform.io/docs/providers/azurerm/r/app_service_plan.html#kind | string | n/a | yes |
| location | Azure location for App Service Plan. | string | n/a | yes |
| location\_short | Short string for Azure location. | string | n/a | yes |
| name\_prefix | Optional prefix for the generated name | string | `""` | no |
| reserved | Flag indicating if App Service Plan should be reserved. Forced to true if "kind" is "Linux". | string | `"false"` | no |
| resource\_group\_name |  | string | n/a | yes |
| sku | A sku block. See documentation https://www.terraform.io/docs/providers/azurerm/r/app_service_plan.html#sku | map | n/a | yes |
| stack |  | string | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| app\_service\_plan\_id | Id of the created App Service Plan |
| app\_service\_plan\_location | Azure location of the created App Service Plan |
| app\_service\_plan\_max\_workers | Maximum number of workers for the created App Service Plan |
| app\_service\_plan\_name | Name of the created App Service Plan |

## Related documentation

Terraform resource documentation: [https://www.terraform.io/docs/providers/azurerm/r/app_service_plan.html]

Microsoft Azure documentation: [https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans]
