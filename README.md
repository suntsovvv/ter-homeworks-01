# Домашнее задание к занятию «Введение в Terraform»
### 1.
```
root@study:/terraform# terraform version

Terraform v1.5.0
on linux_amd64

Your version of Terraform is out of date! The latest version
is 1.7.1. You can update by downloading from https://www.terraform.io/downloads.htmls-01
```
```
root@study:/home/user/home_work/ter-homeworks/01/src# terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/random...
- Finding kreuzwerker/docker versions matching "~> 3.0.1"...
- Installing hashicorp/random v3.6.0...
- Installed hashicorp/random v3.6.0 (unauthenticated)
- Installing kreuzwerker/docker v3.0.2...
- Installed kreuzwerker/docker v3.0.2 (unauthenticated)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

╷
│ Warning: Incomplete lock file information for providers
│ 
│ Due to your customized provider installation methods, Terraform was forced to calculate lock file checksums locally for the following providers:
│   - hashicorp/random
│   - kreuzwerker/docker
│ 
│ The current .terraform.lock.hcl file only includes checksums for linux_amd64, so Terraform running on another platform will fail to install these providers.
│ 
│ To calculate additional checksums for another platform, run:
│   terraform providers lock -platform=linux_amd64
│ (where linux_amd64 is the platform to generate)
╵

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
root@study:/home/user/home_work/ter-homeworks/01/src# 
```
### 2.  
 В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?  
 .tfstate
### 3.
o6PchJA3Vv0oDEZY  
### 4.  
**resource "docker_image" {**  - Не задано уникальное имя для ресурса.  
**resource "docker_container" "1nginx" {**  - Ошибка в уникальном имени ресурса, оно должно начинаться с буквы или подчеркивания, а не с цыфры как в нашем случае.  
**name  = "example_${random_password.random_string_FAKE.resulT}"** - Неверная ссылка на ресурс, такого  не существует, так же верно указан параметр resulT,такой не существует.
### 5.
```
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
root@study:/home/user/home_work/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
3c5e2ba22ff6   a8758716bb6a   "/docker-entrypoint.…"   6 minutes ago   Up 6 minutes   0.0.0.0:9090->80/tcp   example_o6PchJA3Vv0oDEZY
root@study:/home/user/home_work/ter-homeworks/01/src# 
```
