## Uso de variables

Las variables en Ansible son gestionadas por el motor de plantillas [Jinja2](http://jinja.pocoo.org/). Jinja2 proporciona sustitución de variables usando la sintaxis de doble llave `{{ variable }}`.

En Ansible se pueden definir variables a varios niveles, cada uno con un nivel de prioridad. Las variables definidas en variables con mayor  nivel de prioridad sobrescriben los valores definidos en lugares con  mayor nivel de prioridad.

Niveles de prioridad crecientes de variables en Ansible

- command line values (eg “-u user”)
- role defaults
- inventory file or script group vars
- inventory group_vars/all
- playbook group_vars/all
- inventory group_vars/*
- playbook group_vars/*
- inventory file or script host vars
- inventory host_vars/*
- playbook host_vars/*
- host facts / cached set_facts
- play vars
- play vars_prompt
- play vars_files
- role vars (defined in role/vars/main.yml)
- block vars (only for tasks in block)
- task vars (only for the task)
- include_vars
- set_facts / registered vars
- role (and include_role) params
- include params
- extra vars (always win precedence)

### C.1. Definición de variables para playbooks

En `group_vars/all.yml` estableceremos las variables que queremos que sean comunes a todos los playbooks.

```
nodes_by_name:
    controller: {name: testcontroller, type: controller, management_ip: 10.0.0.51, tunnel_ip: 10.0.1.51, provider_ip: 192.168.64.18}
    network: {name: testnetwork, type: network, management_ip: 10.0.0.52, tunnel_ip: 10.0.1.52, provider_ip: 192.168.64.19}
    compute01: {name: testcompute01, type: compute, management_ip: 10.0.0.53, tunnel_ip: 10.0.1.53}
    compute02: {name: testcompute02, type: compute, management_ip: 10.0.0.54, tunnel_ip: 10.0.1.54}
    compute03: {name: testcompute03, type: compute, management_ip: 10.0.0.55, tunnel_ip: 10.0.1.55}
    compute04: {name: testcompute04, type: compute, management_ip: 10.0.0.56, tunnel_ip: 10.0.1.56}
    block: {name: testcontroller, type: block, management_ip: 10.0.0.51, tunnel_ip: 10.0.1.51}
    object01: {name: testobject01, type: object, management_ip: 10.0.0.61, tunnel_ip: 10.0.1.61}
    object02: {name: testobject02, type: object, management_ip: 10.0.0.62, tunnel_ip: 10.0.1.62}
    shared: {name: testshared, type: shared, management_ip: 10.0.0.63, tunnel_ip: 10.0.1.63, provider_ip: 10.0.0.63}
```

En las tareas o en las plantillas de archivos podremos acceder a estos valores posteriormente con la notación punto (`.`). Como las variables están definidas en `group_vars/all.yml` no tendremos que indicar nada para poder acceder a sus valores.

```
{{ nodes_by_name.controller.management_ip }}
```

### C.2. Definición de variables en archivos

Ejemplo 37. Archivo `variables.yml`

```
---
username: johndoe
fullname: John Doe
```

Ejemplo 38. Archivo `playbook-variables-en-archivo.yml`

```
---
- hosts: all
  vars_files:
    - variables.yml
  tasks:
    - debug: msg="Username {{ username }}"
    - debug: msg="Nombre completo {{ fullname }}"
```

### C.3. Definición local de variables

```
---
- hosts: all
  vars:
    username: johndoe
    fullname: John Doe
  tasks:
    - debug: msg="Username {{ username }}
    - debug: msg="Nombre completo {{ fullname }}
```

### C.4. Paso de variables en la línea de comandos

Usaremos el parámetro `--extra-vars` o `-e`  para pasar la lista de pares variable valor la línea de comandos. Esta  opción sobrescribirá cualquier valor asignado previamente

```bash
$ ansible-playbook playbook-variables-en-archivo.yml -e 'username=mtorres fullname="Manuel Torres"'
```

|      | De forma predeterminada los valores son pasados como cadenas. Si  necesitamos pasar valores númericos, booleanos, listas u otro tipo, las  variables se deben pasar como JSON  `$  ansible-playbook playbook-variables-en-archivo.yml -e '{"username":"mtorres", "fullname":"Manuel Torres"}'` |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

### C.5. Petición de variables en la ejecución

Ejemplo 39. Ejemplo de petición de petición de variables en la ejecución

```
---
- hosts: all
  vars_prompt:
    - name: your_name
      prompt: "What is your name?"
  tasks:
    - debug: msg="Hello {{your_name}}"
```

### C.6. Iteración sobre variables

```
---
- hosts: all
  become: true
  vars_files:
    - utilities.yml 
  tasks:
    - name: Instalar utilidades
      apt:
        name: "{{ utilities }}" 
        state: present
```

|      | Archivo que contiene la lista de paquetes a instalar |
| ---- | ---------------------------------------------------- |
|      | Variable con la lista de paquetes a instalar         |

Iteración en versiones anteriores de Ansible

En versiones anteriores de Ansible, el recorrido de la lista de  paquetes anterior se habría hecho iterando sobre la lista con una  construcción `with_items_.

```
---
- hosts: all
  become: true
  vars_files:
    - utilities.yml
  tasks:
    - name: Instalar utilidades
      apt:
        name: "{{ item }}" 
        state: present
      with_items: "{{ utilities }}" 
```

|      | `{{ item }} es la forma usada para iterar sobre la variable indicada en `with_items` |
| ---- | ------------------------------------------------------------ |
|      | `with_items` indica la variable sobre la que iterar          |