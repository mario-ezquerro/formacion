## Organización básica de proyectos Ansible

En un modo de funcionamiento normal de Ansible las tareas no suelen  estar directamente en los playbooks. En cambio, se suelen organizar las  tareas en roles, y los playbooks incluirán una lista de roles a  ejecutar, junto con los hosts a los que van dirigidos.

Ejemplo 25. Ejemplo de playbook basado en roles

```bash
- hosts: all 
  become: true
  roles: 
    - basic

- hosts: controller
  become: true
  roles:
    - ntp_server

- hosts: all:!controller 
  become: true
  roles:
    - ntp_others

- hosts: all
  become: true
  roles:
    - openstack_packages

- hosts: controller
  become: true
  roles:
    - sql_database
    - rabbitmq
    - memcached
```

|      | Hosts sobre los que se ejecutarán los roles indicados. |
| ---- | ------------------------------------------------------ |
|      | Lista de roles a ejecutar sobre los hosts indicados    |
|      | Ejecutar en todos los hosts excepto `controller`       |

Los roles se definen en carpetas **que le dan nombre al rol**. Además, los roles se crean de acuerdo a una estructura de subcarpetas establecida, que es la siguiente:

- `tasks`: Incluye el archivo `main.yml` con la  lista de tareas a ejecutar. La ejecución de una tarea puede desencadenar la ejecución de acciones (p.e. reiniciar un servicio tras modificar un  archivo de configuración). La tarea *notifica* una acción pendiente. Las acciones notificadas se ejecutarán tras finalizar todas las tareas del rol.
- `handlers`: Incluye el archivo `main.yml` con la lista de acciones paras las notificaciones pendientes.
- `templates`: Incluye las plantillas de archivos que se  desplegarán en las máquinas remotas previa sustitución de variables. Los archivos se colocarán en una estructura de carpetas similar a la que  tendrán en el host de destino tomando como raíz la carpeta `handlers`. Por ejemplo, una plantilla para personalizar los hosts en las máquinas de destino se colocaría en `handlers/etc/hosts`, ya que en las máquinas de destino se coloca en (`/etc/hosts`).

Ejemplo 26. Ejemplo de organización de un rol

```bash
ntp_server/
├── handlers
│   └── main.yml
├── tasks
│   └── main.yml
└── templates
    └── etc
        └── chrony
            └── chrony.conf
```

|      | Cuando vamos a crear un rol, podemos crear la carpeta del rol y la  estructura de subcarpetas con un solo comando. El comando siguiente  crearía la carpeta para el rol `ntp_server` y las subcarpetas para `handlers`, tareas y `templates`.  `$ mkdir -p ntp_server/{handlers,tasks,templates}` |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Un proyecto Ansible se organizaría de esta forma:

```bash
├── ansible.cfg 
├── group_vars 
│   └── all.yml
├── hosts.cfg 
├── playbook-1.yml 
├── playbook-2.yml
├── ...
├── roles 
│   ├── barbican
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── etc
│   │           └── barbican
│   │               ├── barbican-api-paste.ini
│   │               └── barbican.conf
│   ├── ...
│   ├── heat
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── etc
│   │           └── heat
│   │               └── heat.conf
│   ├── ...
└── site.yml 
```

|      | Archivo de configuración del proyecto (p.e. para indicar el archivo de inventario) |
| ---- | ------------------------------------------------------------ |
|      | Variables accesibles a todos los playbooks                   |
|      | Archivo de inventario de hosts                               |
|      | Playbooks del proyecto                                       |
|      | Roles del proyecto                                           |
|      | Playbook opcional que contiene la llamada a todos los playbooks del proyecto |

|      | Si un proyecto Ansible contiene gran cantidad de playbooks, es  conveniente crear un nuevo playbook que se encargue de llamarlos a  todos. Esto se realiza en Ansible mediante `include`  Por ejemplo, `site.yml` contiene la llamada a todos los playbooks que realizan un despliegue complejo:  `- include: playbook-basic.yml - include: playbook-keystone.yml - include: playbook-glance.yml - include: playbook-nova.yml - include: playbook-neutron.yml ...` |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Ejemplo 27. Ejemplo de `tasks/main.yml` con las tareas de un rol

```bash
- name: Install chrony
  apt:
    name: chrony
    state: latest

- name: Setup chrony on controller
  template: > 
    src=etc/chrony/chrony.conf
    dest=/etc/chrony/chrony.conf
    owner=root
    group=root
    mode=0644
  notify: restart chrony 
```

|      | Uso de un archivo *template*                                |
| ---- | ----------------------------------------------------------- |
|      | Notificación de ejecución de una acción al finalizar el rol |

Ejemplo 28. Ejemplo de *template* `templates/etc/chrony/chrony.conf`

```bash
pool 2.debian.pool.ntp.org offline iburst

server {{ntp_server}} iburst 
allow {{management_network}}/24

keyfile /etc/chrony/chrony.keys
commandkey 1
driftfile /var/lib/chrony/chrony.drift
log tracking measurements statistics
logdir /var/log/chrony
maxupdateskew 100.0
dumponexit
dumpdir /var/lib/chrony
logchange 0.5
hwclockfile /etc/adjtime
rtcsync
```

|      | Uso de variables. El archivo se creará en los servidores de destino con los valores asignados a las variables (p.e. `ntp_server: 1.es.pool.ntp.org`) |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Ejemplo 29. Ejemplo de `handlers/main.yml`

```bash
- name: restart chrony 
  service:
    name: chrony
    state: restarted
```

|      | El nombre del *handler* tiene que corresponder con el indicado en la cláusula `notify` de la tarea |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

## Creación de un playbook para el despliegue de una aplicación PHP desde un repositorio GitHub

Ansible dispone de un módulo [`git`](https://docs.ansible.com/ansible/latest/modules/git_module.html?highlight=git) que permite realizar operaciones `git` en los equipos administrados. A continuación se muestra un ejemplo de tarea para clonar un repositorio de GitHub en la carpeta `/var/www/html/diariostic`

```bash
- name: Clone diariostic repository
  git:
    repo: 'https://github.com/ualmtorres/diariostic.git'
    dest: /var/www/html/diariostic
```

Veamos un ejemplo de playbook (`diariostic.yml`) que se ejecutará sobre un equipo al que denominamos `diariostic`, que estará incluido en el archivo de inventario de hosts. El playbook incluye un rol, denominado `diariostic`.

Ejemplo 30. Playbook `diariostic.yml`

```bash
---

- name: Deploy diariostic PHP application from scratch
  hosts: diariostic
  roles:
    - diariostic
```

El rol `diariostic` descarga Apache, PHP y el repositorio  de aplicación. Además, personaliza Apache para que trabaje sobre el  puerto 8080 en lugar de sobre el 80.

Ejemplo 31. Rol `diariostic`

```bash
---

- name: Update package cache
  apt:
    update_cache: yes

- name: Install Apache and PHP
  apt:
    name: ['apache2', 'php']

- name: Clone diariostic repository
  git:
    repo: 'https://github.com/ualmtorres/diariostic.git'
    dest: /var/www/html/diariostic

- name: Change port to 8080 in /etc/apache2/ports.conf
  lineinfile:
    path: /etc/apache2/ports.conf
    regexp: '^Listen 80'
    line: 'Listen 8080'

- name: Change port to 8080 in /etc/apache2/sites-enabled/000-default.conf
  lineinfile:
    path: /etc/apache2/sites-enabled/000-default.conf
    regexp: '^<VirtualHost \*:80>'
    line: '<VirtualHost *:8080>'

- name: Restart Apache
  service:
    name: apache2
    state: restarted
```

Después de ejecutar el playbook con

```bash
$ ansible-playbook diariostic.yml --become
```

la aplicación estará disponible en la carpeta `diariostic` del servidor aprovisionado.

![diariostic.png](/home/mario/Documentos/repositorios/docker-es/formacion-ansible/imagenes/diariostic.png)

1. 
