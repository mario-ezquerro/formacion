## Ansible Galaxy

Los roles son un concepto básico en Ansible. Con objeto de poder  reutilizar roles en diferentes playbooks es interesante organizar los  roles en carpetas independientes y tener un repositorio para cada uno de ellos.

Dada la posibilidad entonces de organizar así los roles se ha  organizado una comunidad para la publicación e intercambio de roles  denominada [Ansible Galaxy](https://galaxy.ansible.com/). Cada rol en Ansible Galaxy está enlazado a su código fuente.

### 1. Instalación de un rol de Ansible Galaxy

Es conveniente disponer entonces de una carpeta donde tengamos almacenados todos los roles (p.e. `roles`). Después, en un nivel superior tendremos los playbooks y los archivos de inventario correspondientes a cada proyecto. Pero quizá sería mejor  tener todos los playbooks y archivos de inventario en una carpeta al  mismo nivel que los roles. En este caso los playbooks subirían un nivel y luego bajarían por la carpeta `roles` para usar los roles correspondientes.

Ejemplo 32. Organización de playbooks y roles

```bash
.
├── playbooks
│   ├── nginx-hosts.cfg
│   ├── nginx-playbook.yml
│   ├── php-hosts.cfg
│   ├── php-playbook.yml
│   ├── phpwebserver-hosts.cfg
│   └── phpwebserver-playbook.yml
└── roles
    ├── geerlingguy.git
    │   ├── ...
    ├── geerlingguy.php
    │   ├── ...
    ├── ualmtorres.apache
    │   ├── ...
    └── ualmtorres.apache2
        ├── ...
```

Sea `roles` la carpeta donde guardamos todos nuestros roles y sea `geerlingguy.php` el rol que queremos instalar, disponible en Ansible Galaxy. Para descargar e instalar el rol localmente escribiríamos:

```bash
$ ansible-galaxy install geerlingguy.php –p roles
```

Luego, en nuestra carpeta de playbooks, crearíamos el archivo de inventario de hosts para nuestro proyecto y el del playbook.

Ejemplo 33. El archivo `php-hosts.cfg`

```bash
20.0.1.11
20.0.1.4
```

Ejemplo 34. El archivo `php-playbook.yml`

```
---
- hosts: all
  become: true
  roles:
    - ../roles/geerlingguy.php
```

Para ejecutar este playbook desde la carpeta de playbooks basta con:

```bash
$ ansible-playbook -i nginx-hosts.cfg nginx-playbook.yml
```

### 2. Creación de un rol con Ansible Galaxy

Ansible Galaxy también permite la creación de roles. Esto tiene como  ventaja la inicialización de una serie de carpetas y archivos que hará  que nuestros roles sigan los estándares establecidos para el desarrollo  en Ansible y seguidos por la comunidad de Ansible.

Para crear un rol, sobre la carpeta `roles` ejecutaremos el comando siguiente para crear un rol denominado `ualmtorres.apache`. Seguiremos como regla de nomenclatura un nombre de usuario (p.e. el nombre de usuario en Ansible Galaxy) seguido de punto (`.`) y el nombre del rol. Así, podríamos tener varios roles similares, pero  de usuarios diferentes y usar cada uno de ellos según corresponda.

```bash
$ ansible-galaxy init ualmtorres.apache
```

Esto creará la estructura siguiente:

```bash
ualmtorres.apache
├── defaults 
│   └── main.yml
├── files 
├── handlers 
│   └── main.yml
├── meta 
│   └── main.yml
├── README.md 
├── tasks 
│   └── main.yml
├── templates 
├── tests 
│   ├── inventory
│   └── test.yml
└── vars 
    └── main.yml
```

|      | Valores por defecto para variables usadas en el rol. Serán sobrescritas por las definidas en `vars` |
| ---- | ------------------------------------------------------------ |
|      | Archivos requeridos para la ejecución del rol. Estos archivos, a diferencia de los situados en `templates` no pueden ser mmanipulados. |
|      | Carpeta de *handlers* con las tareas pendientes de ejecución generadas por `notify` en tareas ya ejecutadas (p.e. reiniciar servicios tras una modificación de la configuración) |
|      | Metadatos que usar Ansible Galaxy para publicar el rol (p.e. versión mínima de Ansible, plataformas soportadas, dependencias, …) |
|      | Información descriptiva y de uso del rol                     |
|      | Tareas del rol                                               |
|      | Archivos para procesar en el proceso de despliegue y que se modificarán de acuerdo a las variables que usen |
|      | Casos de prueba para soporte a sistemas de integración continua como Jenkins o Travis |
|      | Variables usadas en el rol. Sobrescriben a las que aparezcan en `defaults` |

Por ejemplo, podemos incluir la tarea siguiente en el archivo `tasks/main.yml` para asegurar que Apache queda instalado.

```bash
---
# tasks file for ualmtorres.apache
- name: Install Apache
  apt: name=apache2 state=present
```

### 3. Organización de playbooks y roles

Con el paso del tiempo, la carpeta `roles` irá creciendo  con los roles usados y desarrollados. Todos ellos serán reutilizados en  los distintos proyectos en lo que sean útiles. A continuación se  muestra un ejemplo de la organización propuesta para playbooks y roles.

```bash
.
├── playbooks 
│   ├── nginx-hosts.cfg
│   ├── nginx-playbook.yml
│   ├── php-hosts.cfg
│   ├── php-playbook.yml
│   ├── phpwebserver-hosts.cfg
│   └── phpwebserver-playbook.yml
└── roles 
    ├── geerlingguy.git 
    │   ├── ...
    ├── geerlingguy.php 
    │   ├── ...
    └── ualmtorres.apache 
        ├── ...
```

|      | Carpeta para playbooks y arhivos de inventario |
| ---- | ---------------------------------------------- |
|      | Carpeta para roles                             |
|      | Rol de instalación de Git                      |
|      | Rol de instalación de PHP                      |
|      | Rol propio de instalación de Apache            |

Si ahora queremos desarrollar un playbook con Apache y PHP que use los roles `ualmtorres.apache` y `geerlingguy.php`, bastaría con crear un nuevo playbook como el siguiente

Ejemplo 35. Playbook `phpwebserver-playbook.yml` para la instalación de un servidor web Apache y PHP

```
---
- hosts: all
  become: true
  roles:
    - ../roles/ualmtorres.apache
    - ../roles/geerlingguy.php
```

Para ejecutarlo, desde la carpeta de playbooks escribiríamos:

```bash
$ ansible-playbook -i phpwebserver-hosts.cfg phpwebserver-playbook.yml 
```

|      | El archivo `phpwebserver-hosts.cfg` contendría la lista de hosts en la que se desea ejecutar el playbook |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

|      | También se podrían sacar los archivos de inventario de la carpeta de playbooks y colocarlos en una carpeta aparte (p.e. `inventory`). |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

##  Playbook para inicializar una base de datos MySQL

En este ejemplo veremos cómo inicializar un servidor con MySQL con  una base de datos precargada. El servidor MySQL lo instalaremos con un  rol de Ansible Galaxy. El script de la base de datos lo descargaremos  con una tarea Ansible para la descarga de archivos. La carga la haremos  con una tarea Ansible del módulo MySQL para la carga de datos.

### 1. Instalación de MySQL

Un nivel por encima de nuestra carpeta de roles instalaremos el rol de MySQL de `geerlingguy`.

```bash
$ ansible-galaxy install geerlingguy.mysql -p roles
```

El archivo `geerlingguy.mysq/defaults/main.yml` contiene  variables para la personalización de la instalación de MySQL.  Cambiaremos los valores de las dos variables que establecen la  contraseña del usuarios `root`

```bash
...
mysql_user_password: changeme
...
mysql_root_password: changeme
...
```

Crearemos un playbook (`mysql.yml`) para la instalación del rol

```bash
---

- name: MySQL Playbook
  hosts: dbserver
  roles:
    - geerlingguy.mysql
```

Ejecutaremos el playbook

```bash
$ ansible-playbook mysql.yml --become
```

Esto habrá instalado MySQL en el host `dbserver`. La contraseña del usuario `root` será `changeme`.

### 2. Creación de la BD

La creación de la base de datos la haremos en dos pasos. Primero  descargaremos a la máquina administrada el script que contiene el código de inicialización de la base de datos. Después, importaremos el script  descargado a la base de datos.

Ejemplo 36. Rol (`crearbdSG`) para descargar el script SQL e importarlo a la base de datos

```bash
---

- name: Download SG.sql
  get_url: 
    url: https://raw.githubusercontent.com/ualmtorres/docker_customer_catalog/master/init.sql
    dest: /home/ubuntu/SG.sql

- name: Import SG database
  mysql_db: 
    name: SG
    state: import
    target: /home/ubuntu/SG.sql 
```

|      | Descargar el archivo indicado en la ruta especificada en `dest` |
| ---- | ------------------------------------------------------------ |
|      | El módulo `mysql_db` permite la creación y eliminación de bases de datos, así como operaciones de importación y exportación |
|      | Ruta de la máquina remota en la que se encuentra el archivo a importar |

A continuación, modificaremos el playbook anterior (`mysql.yml`) para añadir el nuevo rol

```bash
---

- name: MySQL Playbook
  hosts: dbserver
  roles:
    - geerlingguy.mysql
    - crearbdSG 
```

|      | Nuevo rol añadido para la carga de datos |
| ---- | ---------------------------------------- |
|      |                                          |

|      | No es necesario ejecutar el playbook completo desde el principio.  Podemos indicar que se comience a ejecutar a partir de una tarea  determinada con el parámetro `start-at-task`  `$ ansible-playbook mysql.yml --become --start-at-task "Download SG.sql"` |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

### 3. Añadir una aplicación PHP a la base de datos SG

A continuacion podríamos crear otro playbook para añadir al host  anterior un servidor Apache y un intérprete PHP. Como ejemplo, podríamos descargar un [script PHP que muestra el listado de clientes de la base de datos SG](https://raw.githubusercontent.com/ualmtorres/CustomerCatalog/master/index.php).

|      | El script está configurado sólo para una prueba de concepto y usa la cuenta de `root` y la contraseña en el mismo código. La base de datos se denomina `SG`, y se accede a través de la cuenta `root` y con la contraseña `changeme`. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

```bash
---

- name: Update package cache
  apt:
    update_cache: yes

- name: Install Apache and PHP
  apt:
    name: ['apache2', 'php', 'libapache2-mod-php', 'php-mysql']

- name: Restart Apache
  service:
    name: apache2
    state: restarted

- name: Download customer_catalog
  get_url:
    url: https://raw.githubusercontent.com/ualmtorres/CustomerCatalog/master/index.php
    dest: /var/www/html/index.php
```