# Домашнее задание "Введение в Terraform"
### Задание 1
```
root@uchaev-netology3:/opt/ter-homeworks/01/src# terraform init

Initializing the backend...

Initializing provider plugins...
- Finding kreuzwerker/docker versions matching "~> 3.0.1"...
- Finding latest version of hashicorp/random...
- Installing kreuzwerker/docker v3.0.2...
- Installed kreuzwerker/docker v3.0.2 (verified checksum)
- Installing hashicorp/random v3.6.0...
- Installed hashicorp/random v3.6.0 (verified checksum)

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
```
### Задание 2
Допустимо хранить секретную информацию в файле personal.auto.tfvars. 

### Задание 3
"result": "KiI2VJbV6CZ0Mg2U"
### Задание 4
1) Отсутствие имени ресурса "docker image" - добавил имя ресурса "nginx"
2) Неправильное имя ресурса docker-container "nginx" - поправил на nginx
3) В файле root не было задекларировано имя "random_string_FAKE" - исправил на "random_string"
В итоге файл выглядит так:
```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
  required_version = ">=0.13" /*Многострочный комментарий.
 Требуемая версия terraform */
}
provider "docker" {}

#однострочный комментарий

resource "random_password" "random_string" {
  length      = 16
  special     = false
  min_upper   = 1
  min_lower   = 1
  min_numeric = 1
}


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
### Задание 5
Выполнил код, исправленный конфиг в задании 4. 
Вывод docker ps
```
root@uchaev-netology3:/opt/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
135b952ff363   247f7abff9f7   "/docker-entrypoint.…"   8 seconds ago   Up 3 seconds   0.0.0.0:9090->80/tcp   example_KiI2VJbV6CZ0Mg2U
```

### Задание 6
Поменял имя контейнера на hello_world, не перепутал:)
Данный ключ применяется для того, чтобы не подтверждать свои действия. К примеру как в команде rm используется ключ -f (--force). 
Возможно данный ключ применяют для каких-либо скриптов или деплоя, так как скрипты не могут в интерактив. Ну или дикие профи:)
```
root@uchaev-netology3:/opt/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
75d38d3c49b8   247f7abff9f7   "/docker-entrypoint.…"   3 seconds ago   Up 3 seconds   0.0.0.0:9090->80/tcp   hello_world
```
### Задание 7
Выполнил terraform destroy
```
Destroy complete! Resources: 3 destroyed.
-----------------------------------------
root@uchaev-netology3:/opt/ter-homeworks/01/src# cat terraform.tfstate
{
  "version": 4,
  "terraform_version": "1.5.0",
  "serial": 11,
  "lineage": "4ea264a1-aabb-d49d-e76a-70ceb9535393",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

### Задание 8
Образ не удалился, потому что в коде ресурса указана директива keep_locally=true, что означает держать образ локально и не удалять его при операции destroy.
```
keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation.
```
