# Домашнее задание к занятию "7.3. Основы и принцип работы Терраформ"

## Задача 1. Создадим бэкэнд в S3 (необязательно, но крайне желательно).

Если в рамках предыдущего задания у вас уже есть аккаунт AWS, то давайте продолжим знакомство со взаимодействием
терраформа и aws. 

1. Создайте s3 бакет, iam роль и пользователя от которого будет работать терраформ. Можно создать отдельного пользователя,
а можно использовать созданного в рамках предыдущего задания, просто добавьте ему необходимы права, как описано 
[здесь](https://www.terraform.io/docs/backends/types/s3.html).
1. Зарегистрируйте бэкэнд в терраформ проекте как описано по ссылке выше. 


## Задача 2. Инициализируем проект и создаем воркспейсы. 

1. Выполните `terraform init`:
    * если был создан бэкэнд в S3, то терраформ создат файл стейтов в S3 и запись в таблице 
dynamodb.
    * иначе будет создан локальный файл со стейтами.  
1. Создайте два воркспейса `stage` и `prod`.
1. В уже созданный `aws_instance` добавьте зависимость типа инстанса от вокспейса, что бы в разных ворскспейсах 
использовались разные `instance_type`.
1. Добавим `count`. Для `stage` должен создаться один экземпляр `ec2`, а для `prod` два. 
1. Создайте рядом еще один `aws_instance`, но теперь определите их количество при помощи `for_each`, а не `count`.
1. Что бы при изменении типа инстанса не возникло ситуации, когда не будет ни одного инстанса добавьте параметр
жизненного цикла `create_before_destroy = true` в один из рессурсов `aws_instance`.
1. При желании поэкспериментируйте с другими параметрами и рессурсами.

В виде результата работы пришлите:
* Вывод команды `terraform workspace list`.
* Вывод команды `terraform plan` для воркспейса `prod`.  

---

### Ответы:

``` Создал бакет и s3 хранилище ```

![000](https://user-images.githubusercontent.com/106814458/201439209-7874bc0f-49df-4dc9-ba7e-586e4401bd3f.jpg)
![001](https://user-images.githubusercontent.com/106814458/201438827-c1b6fcfa-d39f-4a2d-86ff-853d97ab8079.jpg)
![002](https://user-images.githubusercontent.com/106814458/201438845-d74debbf-1382-4743-933c-70884eaf2511.jpg)

``` Вывод команды terraform workspace list```

![00](https://user-images.githubusercontent.com/106814458/201438916-14685a84-c2ce-46ef-8db8-4c49c02d23e5.jpg)

``` У каждого workspace свои значения по параметрам VM ```

![image](https://user-images.githubusercontent.com/106814458/201438993-3316f438-b8e6-4b06-bf85-bb158a86d10f.png)

``` Правда значения были не приняты ЯО поэтому пришлось редактировать на подходящие )) ```

![01](https://user-images.githubusercontent.com/106814458/201439053-09e9ce04-1db8-4f18-a010-b6af09dbfc3d.jpg)
![02](https://user-images.githubusercontent.com/106814458/201439055-b65dc7f9-b188-4965-abbb-e7e149b62cfa.jpg)

### Доработка
```
Вроде бы разобрался , есть вот такой блок с циклом:

lifecycle {
    create_before_destroy = true
  }
  
```

### Вывод команды terraform plan
<details>

```bash

root@UbuntaTEST /h/f/P/-/terraform (main)# terraform plan
data.yandex_compute_image.ubuntu: Reading...
data.yandex_compute_image.ubuntu: Read complete after 0s [id=fd8kdq6d0p8sij7h5qe3]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # yandex_compute_instance.vm[0] will be created
  + resource "yandex_compute_instance" "vm" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                ubuntu:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsDvOAAPCDODDtYLZfKcH5ZGLKqh9MBa0v1CjsXnyPwwxCIHhougyWkcSSxH7mRj2plPd+CrxUsSXwRncgomkOR3l6TbCESgWXtUvtqadEbKgJbESVgFOX6ICph4XZFN90J/G89+S04YNu+DmEIpcJ+Et6nU3JfDGIW1uBzHya637DLbzdDTUlP8DM+uK4LeTDulHORkmzIm8u7zrG0hYQRUG9n6dtUN9M88oPnvWB/l+P8frJryA8xGMzjdev8R5uuWa3SHUQ0AkbnQwPMlIzrKJnBEClylZkzgLHnkG0hrouaN1aHiS51TSW5g31LcLRvdEvtVZzyzLgats+hRan root@UbuntaTEST
            EOT
        }
      + name                      = "netology-terraform-prod-0"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8kdq6d0p8sij7h5qe3"
              + name        = (known after apply)
              + size        = 20
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = false
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.vm[1] will be created
  + resource "yandex_compute_instance" "vm" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                ubuntu:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsDvOAAPCDODDtYLZfKcH5ZGLKqh9MBa0v1CjsXnyPwwxCIHhougyWkcSSxH7mRj2plPd+CrxUsSXwRncgomkOR3l6TbCESgWXtUvtqadEbKgJbESVgFOX6ICph4XZFN90J/G89+S04YNu+DmEIpcJ+Et6nU3JfDGIW1uBzHya637DLbzdDTUlP8DM+uK4LeTDulHORkmzIm8u7zrG0hYQRUG9n6dtUN9M88oPnvWB/l+P8frJryA8xGMzjdev8R5uuWa3SHUQ0AkbnQwPMlIzrKJnBEClylZkzgLHnkG0hrouaN1aHiS51TSW5g31LcLRvdEvtVZzyzLgats+hRan root@UbuntaTEST
            EOT
        }
      + name                      = "netology-terraform-prod-1"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8kdq6d0p8sij7h5qe3"
              + name        = (known after apply)
              + size        = 20
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = false
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.vm[2] will be created
  + resource "yandex_compute_instance" "vm" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                ubuntu:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsDvOAAPCDODDtYLZfKcH5ZGLKqh9MBa0v1CjsXnyPwwxCIHhougyWkcSSxH7mRj2plPd+CrxUsSXwRncgomkOR3l6TbCESgWXtUvtqadEbKgJbESVgFOX6ICph4XZFN90J/G89+S04YNu+DmEIpcJ+Et6nU3JfDGIW1uBzHya637DLbzdDTUlP8DM+uK4LeTDulHORkmzIm8u7zrG0hYQRUG9n6dtUN9M88oPnvWB/l+P8frJryA8xGMzjdev8R5uuWa3SHUQ0AkbnQwPMlIzrKJnBEClylZkzgLHnkG0hrouaN1aHiS51TSW5g31LcLRvdEvtVZzyzLgats+hRan root@UbuntaTEST
            EOT
        }
      + name                      = "netology-terraform-prod-2"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8kdq6d0p8sij7h5qe3"
              + name        = (known after apply)
              + size        = 20
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = false
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_storage_bucket.test will be created
  + resource "yandex_storage_bucket" "test" {
      + access_key            = "YCAJEV8VI1mVQrLczFLsVFufI"
      + acl                   = "private"
      + bucket                = "myfirstbucket"
      + bucket_domain_name    = (known after apply)
      + default_storage_class = (known after apply)
      + folder_id             = (known after apply)
      + force_destroy         = false
      + id                    = (known after apply)
      + secret_key            = (sensitive value)
      + website_domain        = (known after apply)
      + website_endpoint      = (known after apply)

      + anonymous_access_flags {
          + list = (known after apply)
          + read = (known after apply)
        }

      + versioning {
          + enabled = (known after apply)
        }
    }

  # yandex_vpc_network.net will be created
  + resource "yandex_vpc_network" "net" {
      + created_at                = (known after apply)
      + default_security_group_id = (known after apply)
      + folder_id                 = (known after apply)
      + id                        = (known after apply)
      + labels                    = (known after apply)
      + name                      = "Netolgynet"
      + subnet_ids                = (known after apply)
    }

  # yandex_vpc_subnet.subnet will be created
  + resource "yandex_vpc_subnet" "subnet" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "Netologysubnet"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "192.168.101.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-a"
    }

Plan: 6 to add, 0 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
root@UbuntaTEST /h/f/P/-/terraform (main)# terraform plan
data.yandex_compute_image.ubuntu: Reading...
data.yandex_compute_image.ubuntu: Read complete after 0s [id=fd8kdq6d0p8sij7h5qe3]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # yandex_compute_instance.vm[0] will be created
  + resource "yandex_compute_instance" "vm" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                ubuntu:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsDvOAAPCDODDtYLZfKcH5ZGLKqh9MBa0v1CjsXnyPwwxCIHhougyWkcSSxH7mRj2plPd+CrxUsSXwRncgomkOR3l6TbCESgWXtUvtqadEbKgJbESVgFOX6ICph4XZFN90J/G89+S04YNu+DmEIpcJ+Et6nU3JfDGIW1uBzHya637DLbzdDTUlP8DM+uK4LeTDulHORkmzIm8u7zrG0hYQRUG9n6dtUN9M88oPnvWB/l+P8frJryA8xGMzjdev8R5uuWa3SHUQ0AkbnQwPMlIzrKJnBEClylZkzgLHnkG0hrouaN1aHiS51TSW5g31LcLRvdEvtVZzyzLgats+hRan root@UbuntaTEST
            EOT
        }
      + name                      = "netology-terraform-prod-0"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8kdq6d0p8sij7h5qe3"
              + name        = (known after apply)
              + size        = 20
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = false
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.vm[1] will be created
  + resource "yandex_compute_instance" "vm" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                ubuntu:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsDvOAAPCDODDtYLZfKcH5ZGLKqh9MBa0v1CjsXnyPwwxCIHhougyWkcSSxH7mRj2plPd+CrxUsSXwRncgomkOR3l6TbCESgWXtUvtqadEbKgJbESVgFOX6ICph4XZFN90J/G89+S04YNu+DmEIpcJ+Et6nU3JfDGIW1uBzHya637DLbzdDTUlP8DM+uK4LeTDulHORkmzIm8u7zrG0hYQRUG9n6dtUN9M88oPnvWB/l+P8frJryA8xGMzjdev8R5uuWa3SHUQ0AkbnQwPMlIzrKJnBEClylZkzgLHnkG0hrouaN1aHiS51TSW5g31LcLRvdEvtVZzyzLgats+hRan root@UbuntaTEST
            EOT
        }
      + name                      = "netology-terraform-prod-1"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8kdq6d0p8sij7h5qe3"
              + name        = (known after apply)
              + size        = 20
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = false
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.vm[2] will be created
  + resource "yandex_compute_instance" "vm" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                ubuntu:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsDvOAAPCDODDtYLZfKcH5ZGLKqh9MBa0v1CjsXnyPwwxCIHhougyWkcSSxH7mRj2plPd+CrxUsSXwRncgomkOR3l6TbCESgWXtUvtqadEbKgJbESVgFOX6ICph4XZFN90J/G89+S04YNu+DmEIpcJ+Et6nU3JfDGIW1uBzHya637DLbzdDTUlP8DM+uK4LeTDulHORkmzIm8u7zrG0hYQRUG9n6dtUN9M88oPnvWB/l+P8frJryA8xGMzjdev8R5uuWa3SHUQ0AkbnQwPMlIzrKJnBEClylZkzgLHnkG0hrouaN1aHiS51TSW5g31LcLRvdEvtVZzyzLgats+hRan root@UbuntaTEST
            EOT
        }
      + name                      = "netology-terraform-prod-2"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8kdq6d0p8sij7h5qe3"
              + name        = (known after apply)
              + size        = 20
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = false
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_storage_bucket.test will be created
  + resource "yandex_storage_bucket" "test" {
      + access_key            = "YCAJEV8VI1mVQrLczFLsVFufI"
      + acl                   = "private"
      + bucket                = "myfirstbucket"
      + bucket_domain_name    = (known after apply)
      + default_storage_class = (known after apply)
      + folder_id             = (known after apply)
      + force_destroy         = false
      + id                    = (known after apply)
      + secret_key            = (sensitive value)
      + website_domain        = (known after apply)
      + website_endpoint      = (known after apply)

      + anonymous_access_flags {
          + list = (known after apply)
          + read = (known after apply)
        }

      + versioning {
          + enabled = (known after apply)
        }
    }

  # yandex_vpc_network.net will be created
  + resource "yandex_vpc_network" "net" {
      + created_at                = (known after apply)
      + default_security_group_id = (known after apply)
      + folder_id                 = (known after apply)
      + id                        = (known after apply)
      + labels                    = (known after apply)
      + name                      = "Netolgynet"
      + subnet_ids                = (known after apply)
    }

  # yandex_vpc_subnet.subnet will be created
  + resource "yandex_vpc_subnet" "subnet" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "Netologysubnet"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "192.168.101.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-a"
    }

Plan: 6 to add, 0 to change, 0 to destroy.

```
</details>
