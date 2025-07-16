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
module "wrapper_rds_aurora" {
  source = "../../"
  # ...
}
```


## 🔧 Additional Features Usage

### Database Management
Deploys a Lambda function that manages the creation and modification of *Users*, *Databases* and their access permissions.
Access credentials are stored in **Parameter Store**.
Sends notifications of completed operations.
**Important**: Does not perform deletion of databases or users - users will remain without permissions on resources.


<details><summary>MySQL / MariaDB Code</summary>

```hcl
rds_aurora_parameters = {
  "mysql-00" = {
    enable_db_management                    = true
    enable_db_management_logs_notifications = true
    db_management_parameters = {
      databases = [
        # ...
      ],
      users = [
        # ...
      ],
      excluded_users = ["rdsadmin", "root", "mariadb.sys", "healthcheck"]
    }
  }
}
```


</details>

<details><summary>PostgreSQL Code</summary>

```hcl
rds_aurora_parameters = {
  "postgresql-00" = {
    enable_db_management                    = true
    enable_db_management_logs_notifications = true
    db_management_parameters = {
      databases = [
        # ...
      ],
      users = [
        # ...
      ],
      excluded_users = ["rdsadmin", "root", "healthcheck"]
    }
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