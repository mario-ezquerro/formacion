##  Playbooks para operaciones sencillas

Antes de pasar a crear proyectos más completos que incluyan varias  operaciones agrupadas en roles comencemos por la creación de playbooks  con operaciones sencillas. Esto nos permitirá familiarizarnos con las  tareas de Ansible.

Los playbooks y los roles, que veremos más adelante, se escriben en sintaxis YAML, descrita en el Apéndice [YAML](https://ualmtorres.github.io/CursoAnsible/tutorial/#trueyaml).

En los playbooks seguiremos la estructura siguiente:

- Nombre del Playbook
- Indicar si queremos recuperar información de los hosts administrados
- Hosts sobre los que aplicar el playbook
- Lista de tareas a realizar

Ejemplo 9. Playbook de ejemplo `local.yml` para mostrar información local y un mensaje por pantalla

```bash
---

- name: Basic playbook run locally 
  gather_facts: true 
  hosts: localhost 
  tasks: 
    - name: Doing a ping
      ping:

    - name: Show info
      debug:
        msg: "Machine name: {{ ansible_hostname }}"
```

|      | Nombre del playbook                                          |
| ---- | ------------------------------------------------------------ |
|      | Obtener información de los hosts de destino. No sería necesario porque es la opción predeterminada |
|      | Hosts sobre los que aplicar las tareas siguientes            |
|      | Lista de tareas a ejecutar                                   |

Los playbooks se ejecutan con `ansible-playbook`, que en su sintaxis más básica ejecuta el playbook que le pasemos como argumento.

Para ejecutar el playbook anterior escribiríamos el comando siguiente:

```bash
$ ansible-playbook local.yml
```

A continuación se muestra el resultado de ejecución del playbook

```bash
PLAY [Basic playbook run locally] **********************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Doing a ping] ************************************************************
ok: [localhost]

TASK [Show info] ***************************************************************
ok: [localhost] => {
    "msg": "Machine name: ansible-control"
}

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0
```

|      | En la ejecución de un playbook es posible obtener información de los hosts administrados. Este proceso se conoce como *gather facts* y de forma predeterminada se obtiene dicha información. El apéndice [Obtener información de los nodos administrados](https://ualmtorres.github.io/CursoAnsible/tutorial/#trueobtener-informaci-n-de-los-nodos-administrados) ofrece información sobre esta funcionalidad. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

### 1. Parámetros de interés para ejecución de playbooks

- `-i archivo_de_inventario`: Permite usar un archivo de inventario específico
- `--start-at-task=tarea_de_inicio`: Indica la tarea por la que comenzar a ejecutar el playbook
- `--step`: Permite ejecutar el playbook paso a paso
- `--become`: Ejecuta operaciones como `root`

El comando siguiente ejecuta paso a paso el playbook `mysql.yml` como `root` comenzando por la tarea `Update package cache`

```bash
$ ansible-playbook mysql.yml --become --start-at-task "Update package cache" --step
```

### 2. Modificación de archivos con `blockinfile`

El módulo [`blockinfile`](https://docs.ansible.com/ansible/latest/modules/blockinfile_module.html?highlight=blockinfile) inserta, actualiza o elimina un bloque de líneas en un archivo. El  texto modificado queda delimitado por líneas que actúan como marcador.

Ejemplo 10. Playbook `blockinfile.yml`

```bash
---

- name: Blockinfile to edit files
  gather_facts: false
  hosts: all
  tasks:
    - name: "Adding Ansible manager and managed nodes to /etc/hosts"
      blockinfile:
        name: /etc/hosts 
        block: | 
          # Manager
          20.0.1.7 manager

          # Managed-1
          20.0.1.11 managed-1

          # Managed-2
          20.0.1.4 managed-2
        marker: "# {mark} ANSIBLE MANAGED BLOCK manager and managed nodes" 
```

|      | Archivo a modificar                             |
| ---- | ----------------------------------------------- |
|      | Bloque de texto a incluir                       |
|      | Texto para delimitar el bloque de texto añadido |

La ejecución la haremos con `ansible-playbook`

```bash
$ ansible-playbook blockinfile.yml --become

PLAY [Blockinfile to edit files] ***********************************************

TASK [Adding Ansible manager and managed nodes to /etc/hosts] ******************
changed: [20.0.1.4]
changed: [20.0.1.11]

PLAY RECAP *********************************************************************
20.0.1.11                  : ok=1    changed=1    unreachable=0    failed=0
20.0.1.4                   : ok=1    changed=1    unreachable=0    failed=0
```

|      | Dado que Ansible es idempotente, la ejecución repetida del playbook  no añadirá nuevos bloques en cada ejecución. La ejecución de un playbook de Ansible debe entenderse como *este el estado deseado para las máquinas administradas*. Una modificación sobre los valores del playbook supondría un cambio y  al volver a ejecutar el playbook se trasladaría la modificación a las  máquinas gestionadas. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

A continuación se muestra un extracto del archivo `/etc/hosts` en las máquinas administradas como resultado de ejecutar el playbook anterior.

```bash
127.0.0.1 localhost

....

# BEGIN ANSIBLE MANAGED BLOCK manager and managed nodes 
# Manager 
20.0.1.7 manager

# Managed-1
20.0.1.11 managed-1

# Managed-2
20.0.1.4 managed-2
# END ANSIBLE MANAGED BLOCK manager and managed nodes 
```

|      | Inicio del texto delimitador del bloque |
| ---- | --------------------------------------- |
|      | Texto introducido                       |
|      | Fin del texto delimitador del bloque    |

#### 2.1. Uso de un archivo de variables para los playbooks

Las variables definidas en `group_vars/all.yml` serán visibles para todos los playbooks del mismo directorio sin necesidad de indicar o incluir nada.

Como ejemplo, vamos a definir un archivo de variables `group_vars/all.yml` con el nombre y la dirección IP de un conjuntos de máquinas.

Ejemplo 11. El archivo `group_vars/all.yml`

```bash
manager: { name: manager, ip: 20.0.1.7 }
managed_1: { name: managed-1, ip: 20.0.1.11 }
managed_2: { name: managed-2, ip: 20.0.1.4 }
```

Veamos ahora una revisión del ejemplo del playbook anterior usando variables.

Ejemplo 12. Playbook de `blockinfile` usando variables

```bash
---

- name: Blockinfile to edit files
  gather_facts: false
  hosts: all
  tasks:
    - name: "Adding Ansible manager and managed nodes to /etc/hosts"
      blockinfile:
        name: /etc/hosts
        block: |
          # Manager
          {{ manager.ip }} {{ manager.name }} 

          # Managed-1
          {{ managed_1.ip }} {{ managed_1.name }} 

          # Managed-2
          {{ managed_2.ip }} {{ managed_2.name }} 
        marker: "# {mark} ANSIBLE MANAGED BLOCK manager and managed nodes"
```

|      | Variables para la IP y nombre del nodo `manager`   |
| ---- | -------------------------------------------------- |
|      | Variables para la IP y nombre del nodo `managed-1` |
|      | Variables para la IP y nombre del nodo `managed-2` |

|      | El apéndice [Uso de variables](https://ualmtorres.github.io/CursoAnsible/tutorial/#trueuso-de-variables) contiene información sobre la forma y los distintos lugares donde se  pueden definir variables en Ansible. También se muestra cómo pedir  variables en el momento de la ejecución y como iterar sobre ellas. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

### 3. Uso de *templates* para creación de archivos personalizados

Con [`template`](https://docs.ansible.com/ansible/latest/modules/template_module.html?highlight=template) podemos incluir archivos en los nodos administrados sustituyendo  previamente las variables que incluyan por sus valores correspondientes.

Ejemplo 13. El archivo *template* de base `sample-template.txt`

```bash
Ejemplo de archivo personsalizado usando templates:

El nodo {{ manager.name }} tiene la IP: {{ manager.ip }}.
El nodo {{ managed_1.name }} tiene la IP: {{ managed_1.ip }}.
El nodo {{ managed_2.name }} tiene la IP: {{ managed_2.ip }}.
```

Ejemplo 14. Playbook `template.yml`

```bash
---

- name: Template to customize files
  gather_facts: false
  hosts: all
  tasks:
    - name: "Creating customized sample-template.txt in /home/ubuntu/sample-template.txt"
      template: >
        src=/home/ubuntu/cursostic/sample-template.txt
        dest=/home/ubuntu/sample-template.txt
        owner=ubuntu
        group=ubuntu
        mode=0644
```

El resultado en los nodos administrados:

```bash
Ejemplo de archivo personsalizado usando templates:

El nodo manager tiene la IP: 20.0.1.7.
El nodo managed-1 tiene la IP: 20.0.1.11.
El nodo managed-2 tiene la IP: 20.0.1.4.
```

### 4. Modificación de archivos con `lineinfile`

El módulo [`lineinfile`](https://docs.ansible.com/ansible/latest/modules/lineinfile_module.html?highlight=lineinfile) asegura que exista una línea con un texto concreto en un archivo. Para la búsqueda se usan [expresiones regulares](https://docs.python.org/2/library/re.html).

Por ejemplo, cuando el nombre de la máquina no está en el archivo `/etc/hosts/` aparece un mensaje molesto como el siguiente en la línea de comandos cuando cambiamos al modo superusuario con `sudo su -`

```bash
sudo: unable to resolve host ...
```

Para solucionarlo basta con añadir `127.0.0.1` seguido del nombre de la máquina al archivo `/etc/hosts`. A continuación veremos cómo localizar la entrada `127.0.0.1 localhost` en el archivo e introducir una línea a continuación para solucionar el molesto mensaje.

Ejemplo 15. El playbook `lineinfile.yml`

```bash
---

- name: Lineinfile to edit files
  hosts: all
  tasks:
    - name: "Adding hostname to /etc/hosts"
      lineinfile:
        path: /etc/hosts 
        insertafter: '^127\.0\.0\.1' 
        line: 127.0.0.1 {{ ansible_hostname }} 
```

|      | Archivo a modificar                                          |
| ---- | ------------------------------------------------------------ |
|      | Buscar la última línea que comienza por `127.0.0.1` para insertar una línea a continuación (`insertafter`) |
|      | Insertar la linea con `127.0.0.1` y el nombre de la máquina, obtenido en el proceso de `Gathering facts` y disponible en la variable `ansible_hostname` |

### 5. Gestión de repositorios `APT`

El módulo [`apt_repository`](https://docs.ansible.com/ansible/latest/modules/apt_repository_module.html?highlight=apt_repository) permite añadir o eliminar repositorios `APT` en distribuciones Ubuntu y Debian.

Ejemplo 16. El playbook `apt_repository.yml`

```bash
---

- name: apt_repository to manage APT repositories
  gather_facts: false
  hosts: all
  tasks:
    - name: "Add APT OpenStack repository for Ubuntu Xenial"
      apt_repository:
        repo: "deb http://ubuntu-cloud.archive.canonical.com/ubuntu xenial-updates/ocata main"
```

Tras ejecutar el playbook podemos comprobar que las máquinas de  destino tienen el repositorio disponible mostrando el contenido del  archivo

```bash
/etc/apt/sources.list.d/ubuntu_cloud_archive_canonical_com_ubuntu.list
```

El resultado será:

```bash
deb http://ubuntu-cloud.archive.canonical.com/ubuntu xenial-updates/ocata main
```

Para eliminar un repositorio se usaría el parámetro `state: absent` de `apt_repository`

Ejemplo 17. El playbook `remove-apt_repository.yml`

```bash
---

- name: apt_repository to manage APT repositories
  gather_facts: false
  hosts: all
  tasks:
    - name: "Add APT OpenStack repository for Ubuntu Xenial"
      apt_repository:
        repo: "deb http://ubuntu-cloud.archive.canonical.com/ubuntu xenial-updates/ocata main"
      state: absent
```

Tras ejecutar el playbook podemos comprobar que las máquinas de  destino ya no tienen el repositorio disponible y que no existe el  archivo

```bash
/etc/apt/sources.list.d/ubuntu_cloud_archive_canonical_com_ubuntu.list
```

### 6. Instalación de paquetes

El módulo [`apt`](https://docs.ansible.com/ansible/latest/modules/apt_module.html?highlight=apt) se encarga de la gestión de paquetes en Ubuntu y Debian. Cuando  queremos instalar una lista de paquetes definiremos la lista de paquetes y normalmente lo haremos con una variable

Ejemplo 18. El playbook `apt.yml`

```bash
---

- name: Blockinfile to edit files
  gather_facts: false
  hosts: all
  vars: 
    packages:
      - mysql-server
      - phpmyadmin

  tasks:
    - name: Install packages old style with explicit list
      apt:
        name: "{{ item }}" 
      with_items: 
        - mysql-server
        - phpmyadmin

    - name: Install packages old style using variables
      apt:
        name: "{{ item }}"
      with_items:
        - "{{ packages }}" 


    - name: Install packages new style with explicit list
      apt:
        name: ['mysql-server', 'phpmyadmin'] 

    - name: Install packages new style using variables
      apt:
        name: "{{ packages }}" 
```

|      | Definición de variables en el propio playbook                |
| ---- | ------------------------------------------------------------ |
|      | `{{ item }}` representa la variable de iteración de un bucle `with_items` |
|      | Especificación de un bucle                                   |
|      | Uso de una variable para suministrar los valores sobre los que iterar |
|      | Sintaxis compacta especificando una lista en lugar de usar un bucle |
|      | Sintaxis compacta usando una variable que proporciona los elementos de iteración |

Para eliminar paquetes usamos el parámetro `state: absent` en `apt`.

Ejemplo 19. Playbook para eliminar un paquete (`remove-apt.yml`)

```bash
---

- name: Remove apt packages
  gather_facts: false
  hosts: all

  tasks:
    - name: Removing phpmyadmin
      apt:
        name: phpmyadmin
        state: absent
```

### 7. Ejecución de comandos en máquinas administradas

El módulo [`shell`](https://docs.ansible.com/ansible/latest/modules/shell_module.html?highlight=shell) toma un comando como argumento y lo ejecuta en la máquina remota.

Ejemplo 20. Playbook `shell.yml` para copia de un archivo

```bash
---

- name: Run commands with shell
  hosts: all

  tasks:
    - name: Copy sample-template.txt to sample-template.bak
      shell: 'cp sample-template.txt sample-template.bak' 
      args:
        chdir: /home/ubuntu 
```

|      | Comando a ejecutar                          |
| ---- | ------------------------------------------- |
|      | Directorio sobre el que ejecutar el comando |

### 8. Manejo de archivos

El módulo [`file`](https://docs.ansible.com/ansible/latest/modules/file_module.html?highlight=file) permite configurar atributos de archivos y directorios. También permite la creación y eliminación de archivos.

Ejemplo 21. Playbook para gestión de archivos `file.yml`

```bash
---

- name: Run file commands
  hosts: all
  gather_facts: false

  tasks:
    - name: Create a directory
      file: 
        path: /home/ubuntu/myfolder
        state: directory
        owner: ubuntu
        group: ubuntu

    - name: Delete sample-template.bak file
      file:
        path: /home/ubuntu/sample-template.bak
        state: absent 
```

|      | Creación de un directorio y modificación del propietario |
| ---- | -------------------------------------------------------- |
|      | Eliminar el archivo                                      |

### 9. Reiniciar servicios

El módulo [`service`](https://docs.ansible.com/ansible/latest/modules/service_module.html?highlight=service) permite la administración de servicios en nodos remotos.

Ejemplo 22. Playbook para el reinicio de servicios `services.yml`

```bash
---

- name: Restart services
  hosts: all
  gather_facts: false

  tasks:
    - name: Restart MySQL and Apache
      service:
        name: "{{ item }}" 
        state: restarted
      with_items: 
        - mysql
        - apache2
```

|      | Elemento del bucle sobre el que se está iterando |
| ---- | ------------------------------------------------ |
|      | Lista de servicios sobre los que iterar          |

### 10. Reiniciar una batería de servidores

Podemos usar el módulo `shell` para lanzar un `reboot` sobre los nodos administrados. Además, podemos combinar esta operación con el módulo [wait_for_connection](https://docs.ansible.com/ansible/latest/modules/wait_for_connection_module.html?highlight=wait_for_connection) que espera la cantidad de segundos que le indiquemos. Una vez  recuperada la conexión dentro de ese periodo, continúa la ejecución del  playbook.

Ejemplo 23. Playbook `reboot-and-wait.yml`

```bash
---

- name: Reboot and wait
  hosts: all

  tasks:
    - name: Rebooting
      shell: sleep 2 && reboot
      async: 1
      poll: 0

    - name: Waiting for rebooting
      wait_for_connection:
        delay: 15
        sleep: 10
        timeout: 300

    - debug:
        msg: "{{ inventory_hostname }} is up and running"
```

### 11. Descarga condicional de archivos

El módulo [`fetch`](https://docs.ansible.com/ansible/latest/modules/fetch_module.html) permite la descarga de archivos de las máquinas gestionadas al nodo manager.

Podemos combinar este módulo con la ejecución condicional que permite por ejemplo descargar el archivo sólo si la máquina remota tiene cierto nombre. La cláusula [`when`](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html?highlight=when#the-when-statement) permite la evaluación de expresiones.

A modo de ejemplo, usaremos los hechos (*facts*) recuperados  de las máquinas remotas para obtener su nombre y ejecutar la tarea de  descarga de archivos sólo si el nombre coincide con el que buscamos.

Otro uso podría ser la instalación de paquetes con `yum` o `apt` en función de si la distribución es de la familia (`ansible_facts['os_family']`) Red Hat o Debian, respectivamente.

Ejemplo 24. Playbook para descarga condicional de archivos `conditions.yml`

```bash
---

- name: Get remote files
  hosts: all

  tasks:
    - name: Get remote file checking conditions
      fetch:
        src: /etc/hosts
        dest: /home/ubuntu/hosts-from-managed-1
        flat: yes
      when:
        ansible_facts['hostname'] == "ansible-managed-1" 
```

|      | Descarga el archivo sin añadir el nombre de la máquina y la ruta  completa del archivo. El comportamiento predeterminado descargaría el  archivo en `/home/ubuntu/hosts-from-managed-1/20.0.1.11/etc/hosts` |
| ---- | ------------------------------------------------------------ |
|      | La tarea sólo se ejecuta en aquellos hosts cuyo `hostname` sea el indicado |