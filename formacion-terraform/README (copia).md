---
title: Usando Kubernetes
description: Aprendemos a trabajar usando kubernetes.
author: Manuel Torres
tags: Docker, kubernetes 
date_published: 2020-03-16
---

## Crear una base de datos de MySQL con Terraform



![img](/home/mario/Documentos/repositorios/docker-es/formacion-terraform/imagenes/mysql_PNG9.png)



Como ejemplo en este tutorial crearemos una base de datos de MySQL. El código del ejemplo está en el siguiente repositorio de Github: https://github.com/galvarado/terraform-examples

**1. Crear archivo de configuración**

El arhivo de configuración define el proveedor, en este caso MySQL y contiene los datos para poder conectar al motor de base de datos y crear los recursos. También se definen los recursos a crear que serían una base de datos llamada “some_db_name” y un usuario para la misma con su password.

mysql-example.tf:

```
provider "mysql" {
  endpoint = "localhost:3306"  # MySQL Endpoint
  username = "root"   # Some existing ser with privileges
  password = "Pb5c2.<:-Gf7vc4M"  # User pass
}

# Crear base de datos
resource "mysql_database" "some_db" {
  name = "some_db_name"  #Database to be created
}

# Crear usuario
resource "mysql_user" "demo" {
  user     = "demo"  # User to be created
  host     = "localhost"  #host where database will be created
  password = "Xd5c2.<:-Gf7vc5M"    # Some strong pass for demo user
}
```

**2. Inicializar el proveedor**

Dentro del directorio que contiene el archivo mysql-example.tf:

```
[root@zenbook mysql]# terraform init
```

![img](/home/mario/Documentos/repositorios/docker-es/formacion-terraform/imagenes/terraform_init.png)

**3. Ejecutar el plan**

```
[root@zenbook mysql]# terraform plan
```

![img](/home/mario/Documentos/repositorios/docker-es/formacion-terraform/imagenes/terraform_plan.png)

Con esto nos aseguramos que configuraciones se vana realizar antes de verdaderamente aplicarlas, es un double check para asegurarnos.

**4. Aplicar los cambios**

```
[root@zenbook mysql]# terraform aply
```

![img](/home/mario/Documentos/repositorios/docker-es/formacion-terraform/imagenes/terraform_aply.png)

Después de aplicar los cambios podemos ver un resumen de los recursos se agregaron o cambiaron.



##  Actualizar la configuración de la base de datos

**5. Hacer una actualización**

En un escenario real quizá necesitemos actualizar el password constantemente, por tanto para realizar esta actualización cambié el password del usuario demo, para aplicar este cambio en la base de datos realizaré los comandos plan y aply:

![img](/home/mario/Documentos/repositorios/docker-es/formacion-terraform/imagenes/terraform_plan2.png)

Como podemos ver terraform conoce que la acción es un cambio de password. Ahora aplicamos el cambio:

![img](/home/mario/Documentos/repositorios/docker-es/formacion-terraform/imagenes/terraform_aply2.png)

Con esto conocemos ahora como interactuar con Terraform para realizar despliegue recursos y actualizaciones sobre recursos existentes.

Si te pareció útil, por favor comparte =)

[PARTE II: Automatizar todo el despliegue de una aplicación con terraform y automatizar la configuración de la aplicación con Ansible](https://galvarado.com.mx/post/terraform-ansible-automatizar-el-despliegue-de-wordpress-en-digitalocean/)

Referencias:

- https://terraform-infraestructura.readthedocs.io/es/latest/caracteristicas/index.html
- https://www.terraform.io/intro/index.html
- https://learn.hashicorp.com/terraform/getting-started/install