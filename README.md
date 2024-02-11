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
personal.auto.tfvars
### 3.
"result": "o6PchJA3Vv0oDEZY" 
### 4.  
**resource "docker_image" {**  - Не задано уникальное имя для ресурса.  
**resource "docker_container" "1nginx" {**  - Ошибка в уникальном имени ресурса, оно должно начинаться с буквы или подчеркивания, а не с цифры как в нашем случае.  
**name  = "example_${random_password.random_string_FAKE.resulT}"** - Неверная ссылка на ресурс, такого  не существует, так же верно указан параметр resulT,такой не существует.
### 5.
```hcl
resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}"

  ports {
    internal = 80
    external = 9090
  }
}
```
```
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
root@study:/home/user/home_work/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
3c5e2ba22ff6   a8758716bb6a   "/docker-entrypoint.…"   6 minutes ago   Up 6 minutes   0.0.0.0:9090->80/tcp   example_o6PchJA3Vv0oDEZY
root@study:/home/user/home_work/ter-homeworks/01/src# 
```
### 6.  
```
Apply complete! Resources: 1 added, 0 changed, 1 destroyed.
root@study:/home/user/home_work/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
6c1a581480cc   a8758716bb6a   "/docker-entrypoint.…"   7 seconds ago   Up 6 seconds   0.0.0.0:9090->80/tcp   hello_world
root@study:/home/user/home_work/ter-homeworks/01/src# 
```
Ключ -auto-approve применяет новый план(изменение в иннфаструктуре) без запроса подтверждения.
Опасность применения ключа -auto-approve, если с проектом работает еще кто-то, мы можем не знать об изменениях внесенных не нами.
Предполагаю, что использоваться может для автоматизации развертывания, например через скрипт. Для ускорения процедуры развертывания, разумеется не для критичной инфраструктуры.

### 7. 
```
root@study:/home/user/home_work/ter-homeworks/01/src# terraform destroy

Destroy complete! Resources: 3 destroyed.
```
Файл terraform.tfstate :
```hcl
{
  "version": 4,
  "terraform_version": "1.5.0",
  "serial": 14,
  "lineage": "93f52775-e00b-a601-82fc-361e9df98be1",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```
### 8.
Контейнер не удалился потому, чтов аргументах для создания ресурса "docker_image" был использован параметр keep_locally = true.  

**keep_locally** (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation.

#Задание 2*

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
        
          }
  }
  required_version = ">=0.13" 
}

provider "docker" {
    host  = "ssh://user@158.160.142.194:22"
    #ssh_opts = ["-o", "StrictHostKeyChecking=no", "-o", "UserKnownHostsFile=/dev/null"]
}

resource "docker_volume" "db_data" {
    name = "db_data"

}
resource "random_password" "mysql_root_password" {
  length = 8
}

resource "random_password" "mysql_password" {
  length = 8
}

resource "docker_image" "mysql" {
  name = "mysql:8"
  keep_locally = true
 }

resource "docker_container" "mysql" {
   name  = "mysql"
   image = docker_image.mysql.image_id
   
    env =  [
         "MYSQL_ROOT_PASSWORD = ${random_password.mysql_root_password.result}",
         "MYSQL_PASSWORD = ${random_password.mysql_password.result}",
         "MYSQL_ROOT_HOST = %" ,
         "MYSQL_DATABASE = wordpress",
         "MYSQL_USER = wordpress"
    ]
    restart = "always"   
   mounts {
    source = "db_data"
    target = "/var/lib/mysql"
    type = "volume" 
  }

  ports {
    internal = 3306
    external = 3306
  }
}
```
контейнер на удаленной машине поднимается, но постоянно в ребуте, как будто не передаются env:
```
Команда  docker logs mysql выдает:
```
2024-02-11 02:05:08+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.3.0-1.el8 started.
2024-02-11 02:05:08+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2024-02-11 02:05:08+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.3.0-1.el8 started.
2024-02-11 02:05:08+00:00 [ERROR] [Entrypoint]: Database is uninitialized and password option is not specified
    You need to specify one of the following as an environment variable:
    - MYSQL_ROOT_PASSWORD
    - MYSQL_ALLOW_EMPTY_PASSWORD
    - MYSQL_RANDOM_ROOT_PASSWORD
```
В файле terraform.tfstate вроде как они есть:

```
     "entrypoint": [
              "docker-entrypoint.sh"
            ],
            "env": [
              "MYSQL_DATABASE = wordpress",
              "MYSQL_PASSWORD = y\u003eLduI!m",
              "MYSQL_ROOT_HOST = %",
              "MYSQL_ROOT_PASSWORD = nRer)vtB",
              "MYSQL_USER = wordpress"
            ],
            "exit_code": null,
```


Не могу понять где ошибка, с локальным докером ситуация аналогичная.
