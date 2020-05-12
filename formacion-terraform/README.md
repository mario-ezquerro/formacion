---
title: Usando Kubernetes
description: Aprendemos a trabajar usando kubernetes.
author: Manuel Torres https://galvarado.com.mx/
tags: Docker, kubernetes 
date_published: 2020-03-16
---

# Tutorial: Infraestructura como código con Terraform

Documentación para formarse en kubernetes en español.

CC  https://ualmtorres.github.io/CursoAnsible/tutorial/

## [Introducción a Terraform](./introduccion.md)

“Terraform es una herramienta para construir, combinar y poner en marcha de manera segura y eficiente la infraestructura. Desde servidores físicos a contenedores hasta productos SaaS (Software como un Servicio), Terraform es capaz de crear y componer todos los componentes necesarios para ejecutar cualquier servicio o aplicación.”

Se define la infraestructura completa como código, incluso si se extiende a múltiples proveedores de servicios. Por ejemplo los servidores pueden estar de Openstack, el DNS en AWS, terraform construirá todos los recursos a través de estos proveedores en paralelo, con esto se proporciona un flujo de trabajo y se usa como herramientas para cambiar y actualizar la infraestructura con seguridad.

Los archivos de configuración describen en Terraform los componentes necesarios para ejecutar una sola aplicación o el centro de datos completo. Terraform genera un plan de ejecución que describe lo que hará para alcanzar el estado deseado y luego lo ejecuta para construir la infraestructura descrita. A medida que cambia la configuración, Terraform puede determinar qué ha cambiado y crear planes de ejecución incrementales que se pueden aplicar.

La infraestructura que Terraform puede administrar incluye componentes de bajo nivel, como instancias de cómputo, almacenamiento y redes, así como componentes de alto nivel como entradas de DNS, características de SaaS, etc.

Las principales características de Terraform son:

**Infraestructura como código:** La infraestructura se describe utilizando una sintaxis de alto nivel. Esto permite que un blueprint sea versionado y tratado como lo haría con cualquier otro código. Estos archivos que describen la infraestructura pueden ser compartidos y reutilizados.

**Planes de Ejecución:** Terraform tiene un paso de “planificación” donde genera un plan de ejecución. El plan de ejecución muestra lo que hará Terraform cuando se ejecute. Esto permite evitar sorpresas.

**Gráfico de recursos:** Terraform crea un gráfico de todos los recursos y paraleliza la creación y modificación de cualquier recurso. Con esto los operadores obtienen información sobre las dependencias en la infraestructura.

**Automatización de cambios:** Los conjuntos de cambios complejos se pueden aplicar a su infraestructura con una mínima interacción humana. Con el plan de ejecución y el gráfico de recursos mencionados anteriormente, se sabe exactamente qué cambiará Terraform y en qué orden, evitando muchos posibles errores humanos.



## Instalación de Terraform

Este tutorial sigue los pasos para de instalación para Linux - Fedora 29.

**1. Descargar el paquete**

Descargar el paquete según la plataforma. El paquete debe descargarse desde https://www.terraform.io/downloads.html.

Sistemas operativos compatibles con Terraform:

- Linux: 32­bit | 64­bit | Arm
- Windows: 32­bit | 64­bit
- Mac OS X: 64­bit
- FreeBSD: 32­bit | 64­bit | Arm
- OpenBSD: 32­bit | 64­bit
- Solaris: 64­bit

Seleccionamos el sistema operativo y la arquitectura, en mi caso elegiremos Linux 64­bit puesto que lo instalaremos en una maquina con Fedora 29.

**2. Instalación**

**2.1 Crear un directorio para los binarios de Terraform:**

```
[root@zenbook Descargas]# mkdir /opt/terraform
```

**2.2 Mover el archivo descargado anteriormente al interior del directorio:**

```
[root@zenbook Descargas]# mv terraform_0.11.13_linux_amd64.zip /opt/terraform/
```

**2.3 Nos situamos en el directorio terraformy descomprimimos los binarios:**

```
[root@zenbook Descargas]# cd /opt/terraform/
[root@zenbook terraform]# unzip terraform_0.11.13_linux_amd64.zip 
Archive:  terraform_0.11.13_linux_amd64.zip
  inflating: terraform
```

**2.4 Exportamos las variables de entorno para añadir el directorio de Terraform al PATH del sistema (variable $PATH):**

```
[root@zenbook terraform]# export PATH="$PATH:/opt/terraform"
```

Para hacer persistente el cambio y que el binario de terraform sea reconocido después de esta sesión la terminar debemos agregarlo en ~/.profile o ~/.bashrc:

```
[root@zenbook ~]# echo PATH="$PATH:/opt/terraform" >>  ~/.bashrc
[root@zenbook ~]# source .bashrc
```

**2.5 Por último comprobamos que se ha instalado bien ejecutando el comando siguiente:**

```
[root@zenbook terraform]# terraform --version
Terraform v0.11.13
```



## Primeros pasos con Terraform

Antes de ejecutar un ejemplo es necesario conocer algunos conceptos de terraform:

**1. Archivos de configuración**

Terraform utiliza archivos de texto para describir la infraestructura y establecer variables. El lenguaje de los ficheros de configuración de Terraform se llama HashiCorp Configuration Language (HCL). Los ficheros se deberán crear con la extensión “.tf”.

El formato de los archivos de configuración puede estar en dos formatos: formato Terraform y JSON. El formato de Terraform es más legible, admite comentarios y es el formato generalmente recomendado para la mayoría de los archivos de Terraform. El formato JSON está destinado a las máquinas para crear, modificar y actualizar, pero los operadores de Terraform también pueden hacerlo si lo prefiere. El formato Terraform termina en .tf y el formato JSON termina en .tf.json.

Ejemplo de archivo Terraform, en el cual conectamos a una nube OpenStack para crear una máquina virtual:

```
[root@zenbook terraform-example]# cat openstack-example.tf 

# Configure the OpenStack Provider
provider "openstack" {
  user_name   = "admin"
  tenant_name = "admin"
  password    = "somestrongpass"
  auth_url    = "http://controller01:5000/v3"
  region      = "RegionOne"
}

# Create a RHEL server
resource "openstack_compute_instance_v2" "basic" {
  name            = "vm_from_terraform"
  image_id        = "567887bd-2635-4c2e-9feb-248a1b770745"
  flavor_id       = "i78478b4-2d58-42f6-940e-15bdea5a7849"

  metadata = {
    this = "that"
  }

  network {
    name = "Some_Network"
  }
}
```

**2. Proveedores de Terraform**

Terraform se utiliza para crear, administrar y actualizar recursos de infraestructura como máquinas físicas, máquinas virtuales, routers , contenedores y más. Casi cualquier tipo de infraestructura puede representarse como un recurso en Terraform.

Un proveedor es responsable de comprender las interacciones de API entre terraform y la plataforma proveedora de los recursos y crear los recursos.

Los proveedores generalmente son IaaS, por ejemplo:

- AWS
- GCP
- Microsoft Azure
- OpenStack
- Digital Ocean

Proveedores de PaaS:

- Heroku
- Nutanix
- Rancher
- Kubernetes (No es completamente un PaaS)

Servicios SaaS:

- Terraform Enterprise
- DNSimple
- CloudFlare
- Bitbucket
- Datadog

La lista completa de proveedores se encuentra en la documentación oficial: https://www.terraform.io/docs/providers/index.html

La mayoría de los proveedores requieren algún tipo de configuración para proporcionar información de autenticación, URLs, etc. Cuando se requiere una configuración explícita, se utiliza un bloque de proveedor dentro de la configuración, como se ilustra a continuación:

Configuración para conectar a OpenStack como proveedor:

```
# Configure the OpenStack Provider
provider "openstack" {
  user_name   = "admin"
  tenant_name = "admin"
  password    = "pwd"
  auth_url    = "http://myauthurl:5000/3"
  region      = "RegionOne"
}
```

Configuración para conectar a AWS como proveedor:

```
# Configure the AWS Provider
provider "aws" {
  access_key = "${var.aws_access_key}"
  secret_key = "${var.aws_secret_key}"
  region     = "us-east-1"
}
```

De forma predeterminada, Ansible administra máquinas mediante el  protocolo SSH. Sólo es necesario tener instalado Ansible en una máquina  (la máquina de control), que es la que puede administrar baterías de  máquinas remotas de forma centralizada. En las máquinas remotas sólo  hace falta que esté instalado Python.

### Requisitos para la máquina de control

Actualmente Ansible puede ejecutarse desde cualquier máquina con  Python 2 (versión 2.7) o Python 3 (versiones 3.5 y posteriores). La  máquina de control no puede ser una máquina Windows.

### Requisitos de los nodos administrados

Necesitamos una forma de comunicarnos con los nodos gestionados, y se suele hacer mediante SSH. También se necesita Python 2 (versión 2.7) o  Python 3 (versiones 3.5 y posteriores)

- De forma predeterminada, Ansible usa el intérprete Python localizado en  `/usr/bin/python` para ejecutar sus módulos. Sin embargo, algunas distribuciones de Linux sólo tienen un intérprete de Python 3 de forma predetermianda (`/usr/bin/python3`). En esos sistemas puede producirse un error como este:
-  `"module_stdout": "/bin/sh: /usr/bin/python: No such file or directory\r\n" you can either set the ansible_python_interpreter inventory variable (see Working with Inventory) to point at your interpreter or you can install a Python 2 interpreter for modules to use. You will still need to set ansible_python_interpreter if the Python 2 interpreter is not installed to /usr/bin/python.`



3. Inicialización**

Cada vez que se agrega un nuevo proveedor a la configuración, ya sea explícitamente a través de un bloque de proveedores o agregando un recurso de ese proveedor, es necesario inicializar ese proveedor antes de usarlo. La inicialización descarga e instala el plugin del proveedor y lo prepara para su uso.

La inicialización del proveedor es una de las acciones de **terraform init.** Al ejecutar este comando se descargará e inicializará cualquier proveedor que aún no esté inicializado.

Los proveedores descargados por terraform init solo se instalan para el directorio de trabajo actual, otros directorios de trabajo pueden tener sus propias versiones de proveedor instaladas.