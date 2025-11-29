# 🌐 Azure Container Apps Terraform Module

Terraform module for deploying an **Azure Container App** — developed as part of the *School of Software Engineering (Endava, 2025)* training program.

> ⚙️ **Note:**  
> This module was originally used within a corporate training environment on Azure DevOps to automate application deployments in a shared Azure Container Apps environment.  
> That infrastructure is no longer active, but the module remains a useful reference for learning Terraform modularization, Azure resource provisioning, and container app deployment.

<br>

## 🚀 What does this module do?

- Deploys an **Azure Container App** with basic configuration  
- Creates a **Blob Storage Container** in an existing Storage Account  
- Automatically configures **health probes**  
- Enables **external ingress** for public access  
- Designed to integrate with a shared **Container Apps Environment**
- It's consumed by the infrastructure repository: [**Infrastructure Repository - GitHub**](https://github.com/codenamecoffee/azure-container-apps-terraform-infrastructure)

<br>

## 🧩 Requirements

| Name | Version |
|------|----------|
| terraform | >= 1.6.0 |
| azurerm | ~> 4.0 |

<br>

## Module Structure

```
mg_fg_terraform_module/
├── main.tf            # Main Terraform configuration for resources
├── variables.tf       # Input variables for the module
├── outputs.tf         # Output values exported by the module
├── versions.tf        # Required Terraform and provider versions
├── README.md          # This file.
```

### Description:

- main.tf: Contains the resource definitions (Container App, Blob Storage Container, etc.).
- variables.tf: Declares all input variables required to use the module.
- outputs.tf: Defines outputs that expose resource information after deployment.
- versions.tf: Specifies the minimum Terraform and provider versions for compatibility.
- README.md: Provides documentation, usage examples, and workflow instructions.

<br>

## 🧰 Use in the infrastructure repository

Below is an example of how the module was used during the lab.  
> 🧭 If you clone this repository today, remember that the referenced Azure resources no longer exist, so deployment commands will not succeed without recreating those dependencies.

<br>

### Basic Example

```hcl
# In your branch feature/<equipo>
module "my_team_app" {
  source = "git@ssh.dev.azure.com:v3/EndavaMVD/SchoolOf2025/mg_fg_terraform_module.git?ref=main"
  
  # Variables requeridas
  app_name                     = "mi-equipo-app"  # Nombre único para tu equipo
  container_image              = "tu-acr.azurecr.io/tu-app:latest"
  resource_group_name          = var.resource_group_name  # Usar variable del lab
  container_app_environment_id = var.container_app_environment_id  # Environment compartido
}

# Obtener la URL de tu app desplegada
output "app_url" {
  description = "URL para acceder a la aplicación"
  value       = module.my_team_app.app_url
}
```

<br>

### Example with Custom Configuration

```hcl
module "my_team_app" {
  source = "git@ssh.dev.azure.com:v3/EndavaMVD/SchoolOf2025/mg_fg_terraform_module.git?ref=main"
  
  # Variables requeridas
  app_name                     = "mi-equipo-api"
  container_image              = "tu-acr.azurecr.io/tu-api:v1.0.0"
  resource_group_name          = var.resource_group_name
  container_app_environment_id = var.container_app_environment_id
  
  # Configuración opcional
  container_port    = 8080           # Puerto de tu aplicación
  health_check_path = "/api/health"  # Endpoint de health check
  cpu              = 0.5             # CPU asignada
  memory           = "1Gi"           # Memoria asignada
}
```

```hcl
module "my_team_app" {
  source = "git@ssh.dev.azure.com:v3/EndavaMVD/SchoolOf2025/mg_fg_terraform_module?ref=v1.1.1"

  app_name             = "mi-equipo-app"
  container_image      = "tu-acr.azurecr.io/tu-app:latest"
  resource_group_name  = var.resource_group_name
  container_app_environment_id = var.container_app_environment_id

  # Blob Storage Container
  container_name       = "myteamcontainer"
  storage_account_id   = var.storage_account_id
}
```

<br>

## ⚙️ Variables

| Name                           | Description                                 | Type     | Default     | Required |
| ------------------------------ | ------------------------------------------- | -------- | ----------- | :------: |
| `app_name`                     | Unique name for the Container App           | `string` | -           |     ✅    |
| `container_image`              | Full URL of the Docker image in ACR         | `string` | -           |     ✅    |
| `resource_group_name`          | Name of the Resource Group                  | `string` | -           |     ✅    |
| `container_app_environment_id` | ID of the shared Container Apps Environment | `string` | -           |     ✅    |
| `container_port`               | Port exposed by the application             | `number` | `8000`      |     ❌    |
| `health_check_path`            | Endpoint for the health check               | `string` | `"/health"` |     ❌    |
| `cpu`                          | CPU allocated to the container              | `number` | `0.25`      |     ❌    |
| `memory`                       | Memory allocated to the container           | `string` | `"0.5Gi"`   |     ❌    |
| `container_name`               | Name for the Blob Storage container         | `string` | -           |     ✅    |
| `storage_account_id`           | ID of the existing Storage Account          | `string` | -           |     ✅    |

<br>

## 📤 Outputs

| Name                  | Description                                      |
| --------------------- | ------------------------------------------------ |
| `app_url`             | Public URL of the deployed app                   |
| `app_id`              | ID of the created Container App                  |
| `app_name`            | Name of the Container App                        |
| `app_fqdn`            | Fully qualified domain name of the Container App |
| `blob_container_name` | Name of the created Blob Storage Container       |


<br>

## 🩺 Health Check

The module automatically configures **liveness** and **readiness** probes.

Your application must respond with HTTP 200 on the health_check_path.

### Example (FastAPI):

```python
# FastAPI
@app.get("/health")
def health():
    return {"status": "ok"}
```

<br>

## 🧪 Training Workflow (Original Lab Setup)

1. Create a branch – feature/<team-name>

2. Reference this module in your .tf files using the repository URL.

3. Set all required variables (environment IDs were shared by the instructor).

4. Open a Pull Request and request review from Gonzalo Rodríguez (Endava).

5. Pipeline behavior:
   - ```terraform plan``` runs automatically on PR.
   - ```terraform apply``` runs after merge to main.

<br>

## 👥 Authors

- **Federico González** ([codenamecoffee](https://github.com/codenamecoffee))
- **Mariana Guerra** ([MarianaGuerraC](https://github.com/MarianaGuerraC))

<br>

## License

This project is licensed under the MIT License.

<br>

## 🎓 Educational Context

This module was developed as part of the School of Software Engineering – Endava (2025) to practice Terraform module design, Azure resource provisioning, and CI/CD workflows for Infrastructure as Code.

It is intended for educational purposes and reference only.
