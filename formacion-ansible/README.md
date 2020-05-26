---
title: Usando Kubernetes
description: Aprendemos a trabajar usando kubernetes.
author: Manuel Torres
tags: Docker, kubernetes 
date_published: 2020-03-16
---

# Curso de iniciación a Ansible para la automatización de operaciones

Documentación para formarse en kubernetes en español.

CC  https://ualmtorres.github.io/CursoAnsible/tutorial/

## [Introducción](./introduccion.md)

La configuración y el aprovisionamiento de sistemas es un proceso que conviene automatizar, especialmente cuando nos enfrentamos a la  administración de una gran cantidad de equipos. Además, normalmente se  trata de procesos repetitivos y farragosos en los que es muy posible la introducción de errores. En este sentido, las herramientas de  automatización de sistemas y gestión de la configuración nos asisten en  estas tareas.

Ansible es una herramienta de automatización y gestión de la  configuración. Con Ansible podremos gestionar decenas, cientos o miles  de sistemas de forma sencilla, y además desde cualquier lugar. Su  arquitectura basada en módulos permite extender sus capacidades casi de  forma indefinida. Encontramos módulos para cloud (Amazon, Azure, Google, OpenStack, …), virtualización (VMware, oVirt), bases de datos,  archivos, monitorización, red, almancenamiento, control de versiones,  Windows, …). Además, su configuración es sencilla, lo que permite tener todo preparado para un proyecto de Ansible con rapidez. La  configuración de Ansible sólo exige la instalación de Python en cada uno de los equipos a administrar. El equipo que orquesta o gestiona la  configuración tendrá instalado Python y Ansible.

En este seminario haremos una introducción al uso de Ansible mediante ejemplos cotidianos, trabajando con algunos de los módulos más  habituales y presentando Ansible Galaxy para aumentar la productividad  en nuestros proyectos.



## Ventajas de Ansible

- No necesita agentes
- Sólo requiere que esté instalado Python en las máquinas a administrar
- Uso de módulos para enriquecer su funcionalidad y facilitar su uso



## Requisitos de Ansible

De forma predeterminada, Ansible administra máquinas mediante el  protocolo SSH. Sólo es necesario tener instalado Ansible en una máquina  (la máquina de control), que es la que puede administrar baterías de  máquinas remotas de forma centralizada. En las máquinas remotas sólo  hace falta que esté instalado Python.

### Requisitos para la máquina de control

Actualmente Ansible puede ejecutarse desde cualquier máquina con  Python 2 (versión 2.7) o Python 3 (versiones 3.5 y posteriores). La  máquina de control no puede ser una máquina Windows.

### Requisitos de los nodos administrados

Necesitamos una forma de comunicarnos con los nodos gestionados, y se suele hacer mediante SSH. También se necesita Python 2 (versión 2.7) o  Python 3 (versiones 3.5 y posteriores)

- De forma predeterminada, Ansible usa el intérprete Python localizado en  `/usr/bin/python` para ejecutar sus módulos. Sin embargo, algunas distribuciones de Linux sólo tienen un intérprete de Python 3 de forma predeterminada (`/usr/bin/python3`). En esos sistemas puede producirse un error como este:
-  `"module_stdout": "/bin/sh: /usr/bin/python: No such file or directory\r\n" you can either set the ansible_python_interpreter inventory variable (see Working with Inventory) to point at your interpreter or you can install a Python 2 interpreter for modules to use. You will still need to set ansible_python_interpreter if the Python 2 interpreter is not installed to /usr/bin/python.`



## Índice



- Instalacion  [01-instalacion.md](01-instalacion.md) 
- Configuración [02-config.md](02-config.md) 
- Playbooks  [03-playbooks.md](03-playbooks.md) 
- Organizaciones de un Playbook  [04-organizacion.md](04-organizacion.md) 
- Playbook galaxy  [05-galaxy.md](05-galaxy.md) 
- Ansible-AWX   [ansible-awx.md](ansible-awx.md) 
- Básicos:
  - YAML [basicos-yaml.md](basicos-yaml.md) 
  - Variables en Ansible [basicos-variables.md](basicos-variables.md) 
  - Ansible Setup [basicos-ansible-setup.md](basicos-ansible-setup.md) 



- ## Anexo F: Enlaces de interés

  ### F.1. Módulos interesantes:

  - [Docker](https://docs.ansible.com/ansible/2.4/list_of_cloud_modules.html#docker)
  - [VMware](https://docs.ansible.com/ansible/2.4/list_of_cloud_modules.html#vmware)
  - [expect](https://docs.ansible.com/ansible/2.4/expect_module.html): Ejecuta un comando y proporciona una respuesta
  - [Certificados y pares de claves](https://docs.ansible.com/ansible/2.4/list_of_crypto_modules.html)
  - [LDAP](https://docs.ansible.com/ansible/2.4/list_of_net_tools_modules.html#ldap)
  - [Notificaciones](https://docs.ansible.com/ansible/2.4/list_of_notification_modules.html)
  - [Sistemas de control de versiones](https://docs.ansible.com/ansible/2.4/list_of_source_control_modules.html)
  - [Sistema](https://docs.ansible.com/ansible/2.4/list_of_system_modules.html)
  - [Windows](https://docs.ansible.com/ansible/2.4/list_of_windows_modules.html)

  ### F.2. Ansible AWX

  [Ansible AWX](https://github.com/ansible/awx) es una interfaz web para la administración de playbooks Ansible. Se trata de la versión open source de [Ansible Tower](https://www.ansible.com/products/tower).

  Para una prueba, [descarga Ansible AWX](https://www.jeffgeerling.com/blog/2017/get-started-using-ansible-awx-open-source-tower-version-one-minute)