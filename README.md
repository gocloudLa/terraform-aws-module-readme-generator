# Standard Platform - Terraform Module

<div align="center">

[![Standard Platform](https://img.shields.io/badge/Standard-Platform-blue?style=for-the-badge&logoColor=white)](https://gocloud.la)
[![AWS Partner](https://img.shields.io/badge/AWS%20Partner-Advanced-orange?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/partners/find/)
[![Terraform](https://img.shields.io/badge/Terraform-Module-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io/)
[![GitHub](https://img.shields.io/badge/GitHub-Repository-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/gocloudLa)
[![License](https://img.shields.io/badge/License-Apache%202.0-green?style=for-the-badge&logo=apache&logoColor=white)](LICENSE)

</div>

Welcome to the Standard Platform — a suite of reusable and production-ready Terraform modules purpose-built for AWS environments.
Each module encapsulates best practices, security configurations, and sensible defaults to simplify and standardize infrastructure provisioning across projects.

## 📦 Module: Wrapper RDS Aurora
A comprehensive Terraform module for provisioning and managing Amazon RDS Aurora clusters with advanced features including database management, automated backups, DNS integration, and security configurations.

### ✨ Features

- 🗄️ [Database Management](#database-management) - Automated database and user management with MySQL and PostgreSQL support, credentials stored in Parameter Store



### 🔗 External Modules

- [terraform-aws-modules/rds-aurora/aws](https://github.com/terraform-aws-modules/terraform-aws-rds-aurora) version 9.9.1

- [terraform-aws-modules/security-group/aws](https://github.com/terraform-aws-modules/terraform-aws-security-group) version ~> 4.0

- [terraform-aws-modules/lambda/aws](https://github.com/terraform-aws-modules/terraform-aws-lambda) version 4.7.1



## 🚀 Quick Start
```hcl
rds_parameters = {
  ## Nombre y definición de una instancia RDS
  "00" = {
    
    ## Definiciones para la creación del motor
    engine                 = "mariadb"
    engine_version         = "10.6.14"
    instance_class         = "db.t3.micro"
    deletion_protection    = true
    publicly_accessible    = false
    
    ## Definición de subnets sobre las cuales se desplegará y accederá al motor
    ## Definido por ids
    subnet_ids             = data.aws_subnets.public.ids
    ## Definido por nombre
    # subnet_name = "${local.common_name_prefix}-db*" # Default: "${local.common_name_prefix}-private*"
    
    ## Reglas de acceso para el recurso
    ingress_with_cidr_blocks = [
      {
        rule        = "mysql-tcp"
        cidr_blocks = "172.1.0.0/16"
      }
    ]
    
    ## Definición de tamaño y tipo de storage para el motor
    allocated_storage     = 5
    max_allocated_storage = 10
    storage_type          = null
    
    ## Parametros que utilizara el motor
    parameters = [
      {
        name  = "max_connections"
        value = "150"
      }
    ]
    ## Definiciones de ventanas de mantenimiento y backup
    maintenance_window      = "Sun:04:00-Sun:06:00"
    backup_window           = "03:00-03:30"
    backup_retention_period = "7"
    apply_immediately       = false
    
    ## Habilitación y retención de servicio de performance insights
    #performance_insights_enabled           = false
    #performance_insights_retention_period  = 7
  }
}

rds_defaults = var.rds_defaults
```


## 🔧 Additional Features Usage

### Database Management
Deploys a Lambda function that manages the creation and modification of *Users*, *Databases* and their access permissions.
Access credentials are stored in **Parameter Store**.
Sends notifications of completed operations.
**Important**: Does not perform deletion of databases or users - users will remain without permissions on resources.


<details><summary>MySQL / MariaDB Code</summary>

```hcl
rds_parameters = {
  "mysql" = {
    ...
    enable_db_management                    = true
    enable_db_management_logs_notifications = true
    db_management_parameters = {
      databases = [
        {
          name    = "mydb1"
          charset = "utf8mb4"
          collate = "utf8mb4_general_ci"
        },
        {
          name    = "mydb2"
          charset = "utf8mb4"
          collate = "utf8mb4_general_ci"
        }
      ],
      users = [
        {
          username = "user1"
          host     = "%"
          password = "password1"
          grants = [
            {
              database   = "mydb1"
              table      = "*"
              privileges = "ALL"
            },
            {
              database   = "mydb2"
              table      = "*"
              privileges = "SELECT, UPDATE"
            }
          ]
        },
        {
          username = "user2"
          host     = "%"
          password = "password2"
          grants = [
            {
              database   = "mydb2"
              table      = "*"
              privileges = "ALL"
            }
          ]
        }
      ],
      excluded_users = ["rdsadmin", "root", "mariadb.sys", "healthcheck", "rds_superuser_role", "mysql.infoschema", "mysql.session", "mysql.sys"]
    }
    ...
  }
}
```


</details>

<details><summary>PostgreSQL Code</summary>

```hcl
rds_parameters = {
  "postgresql" = {
    ...
    enable_db_management                    = true
    enable_db_management_logs_notifications = true
    db_management_parameters = {
      databases = [
        {
          "name" : "db1",
          "owner" : "root",
          "schemas" : [
            {
              "name" : "public",
              "owner" : "root"
            },
            {
              "name" : "schema1",
              "owner" : "usr1"
            }
          ]
        },
        {
          "name" : "db2",
          "owner" : "usr2",
        },
        {
          "name" : "db3",
          "owner" : "usr3",
        }
      ],
      roles = [
        { "rolename" : "example_role_1" },
        { "rolename" : "example_role_2" }
      ],
      users = [
        {
          "username" : "usr1",
          "password" : "passwd1",
          "grants" : [
            {
              "database" : "db1",
              "schema" : "public",
              "privileges" : "ALL PRIVILEGES",
              "table" : "*"
            }
          ]
        },
        {
          "username" : "usr2",
          "password" : "passwd2",
          "grants" : [
            {
              "privileges" : "example_role_1",
              "options" : "WITH SET TRUE"
            },
            {
              "privileges" : "example_role_2",
              "options" : "WITH SET TRUE"
            }
          ]
        },
        {
          "username" : "usr3",
          "password" : "passwd3",
          "grants" : []
        }
      ],
      excluded_users = ["rdsadmin", "root", "healthcheck"]
    }
    ...
  }
}
```


</details>








## 📚 Examples
- **Complete Example**: Full implementation with MySQL and PostgreSQL clusters, including database management, backups, DNS records, and security configurations




## ⚠️ Important Warnings
- **🚨 Restart Engine**: Restart the engine during parameter group changes - set `apply_immediately = true`
- **🌐 Public Access**: Exposes the resource to the internet - use `publicly_accessible = true` with caution 


---

## 🤝 Contributing
We welcome contributions! Please see our contributing guidelines for more details.

## 🆘 Support
- 📧 **Email**: info@gocloud.la
- 🐛 **Issues**: [GitHub Issues](https://github.com/gocloudLa/issues)

## 🧑‍💻 About
We are focused on Cloud Engineering, DevOps, and Infrastructure as Code.
We specialize in helping companies design, implement, and operate secure and scalable cloud-native platforms.
- 🌎 [www.gocloud.la](https://www.gocloud.la)
- ☁️ AWS Advanced Partner (Terraform, DevOps, GenAI)
- 📫 Contact: info@gocloud.la

## 📄 License
This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details. 