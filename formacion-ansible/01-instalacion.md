##  Instalación de Ansible

### 1. Configuración de la máquina de control

Instalación de Python

Comenzaremos instalando Python. En nuestro caso instalaremos Python 2.7.

```bash
$ sudo apt-get update
$ sudo apt-get install -y python-minimal
```

Instalación de Ansible

```bash
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt-get install ansible
```

- Si estamos usando OpenStack, podemos pasar en el proceso de creación  de la instancia que actúa como máquina de control de Ansible el script  de instalación de Python y Ansible. De esta forma, una vez creada la  instancia, ya estará preparada para para actuar como máquina de control  Ansible.  `#!/bin/bash echo "Instalando Python" apt-get update apt-get install -y python-minimal echo "Instalando Ansible" apt-get install -y software-properties-common apt-add-repository --yes --update ppa:ansible/ansible apt-get install -y ansible`



Tras la instalación podemos probar que Python y Ansible están funcionando correctamente

```bash
$ python --version
Python 2.7.12

$ ansible --version
ansible 2.7.5
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/ubuntu/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.12 (default, Nov 12 2018, 14:36:49) [GCC 5.4.0 20160609]
```

### 2. Configuración de las máquinas administradas

En las máquinas administradas basta con instalar Python.

```bash
$ sudo apt-get update
$ sudo apt-get install -y python-minimal
```

|      | Si estamos usando OpenStack, podemos pasar en el proceso de creación  de las instancias que actúan como máquinas administradas por Ansible el  script de instalación de Python. De esta forma, una vez creadas las  instancias, ya estarán preparadas para para actuar como máquinas  administradas por Ansible.  `#!/bin/bash echo "Instalando Python" apt-get update apt-get install -y python-minimal` |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

### 3. Copia de claves SSH a máquinas administradas

La comunicación entre la máquina de control y las administradas es  vía SSH. Por tanto, la máquina de control deberá tener la clave privada y las máquinas administradas la clave pública (examinar el archivo `~/.ssh/authorized_keys` de las máquinas administradas para ver las claves públicas autorizadas).

Para ello, copiaremos la clave desde la máquina de control hasta las máquinas administradas con `ssh-copy-id`.

Por ejemplo:

```bash
ssh-copy-id -i ~/.ssh/id_rsa 20.0.0.27
ssh-copy-id -i ~/.ssh/id_rsa 20.0.0.22
```

|      | Si hemos creado las instancias de Ansible en OpenStack, dichas  instancias ya se habrán creado con una clave pública inyectada. Sólo los clientes en los que esté su pareja de clave privada podrán iniciar  sesión en dichas instancias.  Podemos crear un par de claves para la ocasión y distribuirla desde  la máquina de control de Ansible a las máquinas remotas. Otra opción es  copiar a la máquina de control Ansible la clave privada que empareja con la clave pública que ya tienen inyectada las instancias |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

### 4. Herramientas de línea de comandos instaladas con Ansible

Tras la instalación de Ansible podemos comprobar que hay varias herramientas de línea de comandos instaladas:

- `ansible`: Permite la ejecución directa de comandos sobre un conjunto de hosts.
- `ansible-playbook`: Ejecuta playbooks sobre un conjunto de hosts.
- `ansible-vault`: Cifra el contenido de archivos con datos sensibles, como los que contienen contraseñas.
- `ansible-galaxy`: Instala roles de [Ansible Galaxy](https://galaxy.ansible.com), una plataforma para el intercambio de roles (recetas) Ansible.
- `ansible-console`: Consola de ejecución de comandos.
- `ansible-config`: Gestiona la configuración de Ansible.
- `ansible-doc`: Muestra documentación sobre los módulos instalados.
- `ansible-inventory`: Muestra información sobre el inventario de hosts.
- `ansible-pull`: Descarga playbooks desde un sistema de control de versiones y lo ejecuta en el sistema local.