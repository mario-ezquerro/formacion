## Obtener información de los nodos administrados

Al lanzar la ejecución de un playbook se ejecuta una tarea que  recopila información sobre los hosts sobre los que se lanza el playbook. Entre esta información se encuentra información del procesador, red,  fecha y hora, variables de entorno y gran cantidad de información de los sistemas remotos.

El comando siguiente muestra la información que se recupera de los hosts remotos:

```bash
$ ansible all -m setup
```

Para acceder a la información recopilada usaremos la variable `hostvars`. El ejemplo siguiente muestra la recuperación de la dirección IP de una interfaz de red de un equipo remoto.

```
hostvars['myserver.com']['ansible_ens3']['ipv4']['address']
```

También se puede usar la notación punto (`.`) para navegar por los distintos elementos:

```
hostvars['20.0.1.4'].ansible_ens3.ipv4.address
```

También podemos usar `ansible_facts` para acceder a información de los hosts remotos.

```
---
- hosts: all
  tasks:
    - debug: msg="Nombre {{ ansible_facts.nodename }} Procesador {{ ansible_facts.processor }}"
```

Para desactivar la recopilación de información de los sistemas remotos añadiremos `gather_facts: false` al playbook. Esto hará que la ejecución sea más rápida en aquellos  casos en que no necesitemos obtener información sobre los sistemas  remotos.

```
---
- hosts: all
  gather_facts: false # Desactivación de recopilación de información de hosts remotos
  vars_prompt:
    - name: your_name
      prompt: "What is your name?"
  tasks:
    - debug: msg="Hello {{your_name}}"
```

