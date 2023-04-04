# terraform-aws-rds

[![Lint Status](https://github.com/tothenew/terraform-aws-rds/workflows/Lint/badge.svg)](https://github.com/tothenew/terraform-aws-rds/actions)
[![LICENSE](https://img.shields.io/github/license/tothenew/terraform-aws-rds)](https://github.com/tothenew/terraform-aws-rds/blob/master/LICENSE)

This is a template to use for baseline. The default actions will provide updates for section bitween Requirements and Outputs.

The following  content needed to be created and managed:
 - Introduction
     - Explaination of module 
     - Intended users
 - Resource created and managed by this module
 - Example Usages

<!-- BEGIN_TF_DOCS -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 1.3.0 |

## Providers

| Name | Version |
|------|---------|
| aws | n/a |
| template | n/a |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [random_string](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/string) | resource |
| [aws_security_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group) | resource |
| [aws_security_group_rule](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group_rule) | resource |
| [aws_db_subnet_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_subnet_group) | resource |
| [aws_db_instance](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_instance) | resource |
| [aws_rds_cluster](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/rds_cluster) | resource |
| [aws_rds_cluster_instance](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/rds_cluster_instance) | resource |
| [aws_ssm_parameter](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ssm_parameter) | resource |
| [mysql_user](https://registry.terraform.io/providers/petoju/mysql/latest/docs/resources/user) | resource |
| [mysql_grant](https://registry.terraform.io/providers/winebarrel/mysql/latest/docs/resources/grant) | resource |
| [aws_db_parameter_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_parameter_group) | resource |
| [aws_rds_cluster_parameter_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/rds_cluster_parameter_group) | resource |

## Usage
```
module "create_database" {
  source              = "git::https://github.com/tothenew/terraform-aws-rds.git"
  create_rds     = false
  create_aurora = true

  subnet_ids       = ["subnet-999999","subnet-999999"]
  vpc_id           = "vpc-999999"
  vpc_cidr         = ["172.31.0.0/16"]

  publicly_accessible = true
  allocated_storage = 10
  max_allocated_storage = 20
  engine = "aurora-mysql"
  engine_version = "8.0.mysql_aurora.3.02.0"
  instance_class = "db.serverless"
  database_name = "mydb"
  username   = "root"
  identifier = "final-test"
  apply_immediately = false
  storage_encrypted = false
  multi_az = false
  db_subnet_group_id = "subnet-group"
  deletion_protection = false
  auto_minor_version_upgrade = false
  count_aurora_instances = 1
  serverlessv2_scaling_configuration_max = 1.0
  serverlessv2_scaling_configuration_min = 0.5

  environment = "dev"
  project = "project-1"
  create_cluster_parameter_group = true
  family = "aurora-mysql8.0"
  create_mysql_user = true
  mysql_users = ["user1","user2"]
}
```

## Inputs

| Name                                   | Description                                                                                                            | Type           | Default | Required |
|----------------------------------------|------------------------------------------------------------------------------------------------------------------------|----------------|---------|:--------:|
| create_rds                             | If you want to create the AWS RDS (MySQL, PostgreSQL) enable this check                                                | `bool`         | `false` |   yes    |
| create_aurora                          | If you want to create the AWS Aurora (MySQL, PostgreSQL) enable this check                                             | `bool`         | `true`  |   yes    |
| subnet_id                              | The VPC Subnet IDs to launch in                                                                                        | `string`       | `n/a`   |   yes    |
| vpc_id                                 | VPC ID                                                                                                                 | `string`       | `n/a`   |   yes    |
| vpc_cidr                               | VPC cidr for allowing rule in SG                                                                                       | `string`       | `n/a`   |   yes    |
| publicly_accessible                    | Bool to control if instance is publicly accessible                                                                     | `bool`         | `false` |    no    |
| allocated_storage                      | Storage size in GB                                                                                                     | `number`       | `n/a`   |   yes    |
| max_allocated_storage                  | Argument higher than the allocated_storage to enable Storage Autoscaling, size in GB. 0 to disable Storage Autoscaling | `string`       | `n/a`   |    no    |
| engine                                 | Type of broker engine, `mysql`, `postgres`, `aurora-postgresql` or `aurora-mysql`                                      | `sting`        | `n/a`   |   yes    |
| engine_version                         | The engine version to use                                                                                              | `string`       | `n/a`   |   yes    |
| instance_class                         | The instance type of the RDS instance                                                                                  | `list(string)` | `n/a`   |   yes    |
| database_name                          | A string value for Database Name                                                                                       | `string`       | `n/a`   |   yes    |
| username                               | Admin username                                                                                                         | `string`       | `n/a`   |   yes    |
| password                               | Admin password                                                                                                         | `string`       | `n/a`   |   yes    |
| identifier                             | identifier for DB. If not passed, {environment_name}-{name} will be used                                               | `string`       | `n/a`   |    no    |
| apply_immediately                      | Apply changes immediately or wait for the maintainance window                                                          | `bool`         | `false` |    no    |
| storage_encrypted                      | Enables storage encryption                                                                                             | `bool`         | n/a     |    no    |
| kms_key_arn                            | KMS Key ARN to use a CMK instead of default shared key, when storage_encrypted is true                                 | `string`       | `n/a`   |    no    |
| multi_az                               | Deploy multi-az instance database                                                                                      | `bool`         | `false` |    no    |
| deletion_protection                    | The database can't be deleted when this value is set to true                                                           | `bool`         | `false` |    no    |
| auto_minor_version_upgrade             | Indicates that minor engine upgrades will be applied automatically to the DB instance during the maintenance window    | `bool`         | `true`  |    no    |
| count_aurora_instances                 | Number of Aurora Instances                                                                                             | `number`       | `1`     |    no    |
| serverlessv2_scaling_configuration_max | The maximum number of Aurora capacity units (ACUs) for a DB instance in an Aurora Serverless v2 cluster.               | `number`       | `1.0`   |    no    |
| serverlessv2_scaling_configuration_min | The minimum number of Aurora capacity units (ACUs) for a DB instance in an Aurora Serverless v2 cluster.               | `number`       | `0.5`   |    no    |
| create_mysql_user                            | Set create_mysql_user to true, for creating mysql users                                                                          | `bool`  | `false`   |    no    |
| mysql_users                            | list of mysql users                                                                          | `list(string)`  | `n/a`   |    no    |
| common_tags                            | A map to add common tags to all the resources                                                                          | `map(string)`  | `n/a`   |    no    |
| environment                            | Environment                                                                                                            | `string`       | `dev`   |   yes    |
| family                    | Parameter group family                                                                 | `string`       | `n/a`   |   no    |
| parameter_group_name                    | Name of the DB parameter group to associate or create                                                                 | `string`       | `n/a`   |   no    |
| create_db_parameter_group                    | Whether to create a database parameter group                                                                 | `bool`       | `false`   |   no    |
| create_cluster_parameter_group                    | Whether to create a cluster parameter group                                                                 | `bool`       | `false`   |   no    |


## Outputs

| Name | Description |
|------|-------------|
| endpoint | `n/a` |
| identifier | `n/a` |

<!-- END_TF_DOCS -->

## Authors

Module managed by [TO THE NEW Pvt. Ltd.](https://github.com/tothenew)

## License

Apache 2 Licensed. See [LICENSE](https://github.com/tothenew/terraform-aws-rds/blob/main/LICENSE) for full details.
