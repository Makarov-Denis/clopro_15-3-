# «Безопасность в облачных провайдерах» - Макаров Денис

Используя конфигурации, выполненные в рамках предыдущих домашних заданий, нужно добавить возможность шифрования бакета.

---
## Задание 1. Yandex Cloud   

1. С помощью ключа в KMS необходимо зашифровать содержимое бакета:

 - создать ключ в KMS;
 - с помощью ключа зашифровать содержимое бакета, созданного ранее.

 Взял за основу наработки из прошлого задания и применил модифицированый манифест:


 ```
admden@admden-VirtualBox:~/terraform-yandex-oblako/clopro_15-3./src$ terraform apply
var.cloud_id
  ID облака

  Enter a value: b1gd691r29c5ghf8ujpd

var.folder_id
  ID папки

  Enter a value: b1gusa0rlmql2290uftn


Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # yandex_iam_service_account.service will be created
  + resource "yandex_iam_service_account" "service" {
      + created_at = (known after apply)
      + folder_id  = "b1gusa0rlmql2290uftn"
      + id         = (known after apply)
      + name       = "bucket-dm"
    }

  # yandex_iam_service_account_static_access_key.dm-static-key will be created
  + resource "yandex_iam_service_account_static_access_key" "dm-static-key" {
      + access_key                   = (known after apply)
      + created_at                   = (known after apply)
      + description                  = "static access key for object storage"
      + encrypted_secret_key         = (known after apply)
      + id                           = (known after apply)
      + key_fingerprint              = (known after apply)
      + output_to_lockbox_version_id = (known after apply)
      + secret_key                   = (sensitive value)
      + service_account_id           = (known after apply)
    }

  # yandex_kms_symmetric_key.encryptkey will be created
  + resource "yandex_kms_symmetric_key" "encryptkey" {
      + created_at          = (known after apply)
      + default_algorithm   = "AES_256"
      + deletion_protection = false
      + folder_id           = (known after apply)
      + id                  = (known after apply)
      + name                = "encryptkey"
      + rotated_at          = (known after apply)
      + rotation_period     = "8760h"
      + status              = (known after apply)
    }

  # yandex_resourcemanager_folder_iam_member.bucket-editor will be created
  + resource "yandex_resourcemanager_folder_iam_member" "bucket-editor" {
      + folder_id = "b1gusa0rlmql2290uftn"
      + id        = (known after apply)
      + member    = (known after apply)
      + role      = "editor"
    }

  # yandex_storage_bucket.my_bucket will be created
  + resource "yandex_storage_bucket" "my_bucket" {
      + access_key            = (known after apply)
      + acl                   = "public-read"
      + bucket                = "dmakarov-2025-01-18"
      + bucket_domain_name    = (known after apply)
      + default_storage_class = (known after apply)
      + folder_id             = (known after apply)
      + force_destroy         = true
      + id                    = (known after apply)
      + secret_key            = (sensitive value)
      + website_domain        = (known after apply)
      + website_endpoint      = (known after apply)

      + server_side_encryption_configuration {
          + rule {
              + apply_server_side_encryption_by_default {
                  + kms_master_key_id = (known after apply)
                  + sse_algorithm     = "aws:kms"
                }
            }
        }
    }

  # yandex_storage_object.picture will be created
  + resource "yandex_storage_object" "picture" {
      + access_key   = (known after apply)
      + acl          = "public-read"
      + bucket       = (known after apply)
      + content_type = (known after apply)
      + id           = (known after apply)
      + key          = "earth.jpg"
      + secret_key   = (sensitive value)
      + source       = "./earth.jpg"
    }

Plan: 6 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + backet      = (known after apply)
  + picture_key = "earth.jpg"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_iam_service_account.service: Creating...
yandex_kms_symmetric_key.encryptkey: Creating...
yandex_kms_symmetric_key.encryptkey: Creation complete after 0s [id=abj1itsv8pid8491bun7]
yandex_iam_service_account.service: Creation complete after 2s [id=ajeuq97c22947av6r5e8]
yandex_resourcemanager_folder_iam_member.bucket-editor: Creating...
yandex_iam_service_account_static_access_key.dm-static-key: Creating...
yandex_iam_service_account_static_access_key.dm-static-key: Creation complete after 2s [id=aje9bl7qtnlhtvk64nau]
yandex_storage_bucket.my_bucket: Creating...
yandex_resourcemanager_folder_iam_member.bucket-editor: Creation complete after 3s [id=b1gusa0rlmql2290uftn/editor/serviceAccount:ajeuq97c22947av6r5e8]
yandex_storage_bucket.my_bucket: Creation complete after 4s [id=dmakarov-2025-01-18]
yandex_storage_object.picture: Creating...
yandex_storage_object.picture: Creation complete after 1s [id=earth.jpg]

Apply complete! Resources: 6 added, 0 changed, 0 destroyed.

Outputs:

backet = "dmakarov-2025-01-18.storage.yandexcloud.net"
picture_key = "earth.jpg"

```
![Снимок экрана от 2025-01-18 21-58-19](https://github.com/user-attachments/assets/8c1db635-5a23-4a1e-aee8-6de867037d52)


2. (Выполняется не в Terraform)* Создать статический сайт в Object Storage c собственным публичным адресом и сделать доступным по HTTPS:

 - создать сертификат;
 - создать статическую страницу в Object Storage и применить сертификат HTTPS;
 - в качестве результата предоставить скриншот на страницу с сертификатом в заголовке (замочек).

Полезные документы:

- [Настройка HTTPS статичного сайта](https://cloud.yandex.ru/docs/storage/operations/hosting/certificate).
- [Object Storage bucket](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/storage_bucket).
- [KMS key](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/kms_symmetric_key).

--- 
## Задание 2*. AWS (задание со звёздочкой)

Это необязательное задание. Его выполнение не влияет на получение зачёта по домашней работе.

**Что нужно сделать**

1. С помощью роли IAM записать файлы ЕС2 в S3-бакет:
 - создать роль в IAM для возможности записи в S3 бакет;
 - применить роль к ЕС2-инстансу;
 - с помощью bootstrap-скрипта записать в бакет файл веб-страницы.
2. Организация шифрования содержимого S3-бакета:

 - используя конфигурации, выполненные в домашнем задании из предыдущего занятия, добавить к созданному ранее бакету S3 возможность шифрования Server-Side, используя общий ключ;
 - включить шифрование SSE-S3 бакету S3 для шифрования всех вновь добавляемых объектов в этот бакет.

3. *Создание сертификата SSL и применение его к ALB:

 - создать сертификат с подтверждением по email;
 - сделать запись в Route53 на собственный поддомен, указав адрес LB;
 - применить к HTTPS-запросам на LB созданный ранее сертификат.

Resource Terraform:

- [IAM Role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role).
- [AWS KMS](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/kms_key).
- [S3 encrypt with KMS key](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_object#encrypting-with-kms-key).

Пример bootstrap-скрипта:

```
#!/bin/bash
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>My cool web-server</h1></html>" > index.html
aws s3 mb s3://mysuperbacketname2021
aws s3 cp index.html s3://mysuperbacketname2021
```

### Правила приёма работы

Домашняя работа оформляется в своём Git репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
Файл README.md должен содержать скриншоты вывода необходимых команд, а также скриншоты результатов.
Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.
