## Archivos de configuración y de inventario

En la instalación de Ansible se crea un archivo de configuración global (`/etc/ansible/ansible.cfg`) y un archivo de inventario global (`/etc/ansible/hosts`). Sin embargo, preferimos usar archivos de configuración y de inventario a nivel de cada proyecto Ansible. Esto permite usar diferentes  configuraciones e inventarios en función del proyecto.

El archivo de inventario contiene la lista de máquinas a administrar. Cada máquina aparecerá en una línea y es posible crear grupos de  máquinas para lanzar posteriormente scripts Ansible a grupos de  máquinas.

Ejemplo 1. Ejemplo de archivo de inventario `hosts.cfg` usando grupos

```bash
# Inventory hosts.cfg file

[controller]
10.0.0.51

[network]
10.0.0.52

[compute]
10.0.0.53
10.0.0.54
10.0.0.55
10.0.0.56

[block]
10.0.0.51

[shared]
10.0.0.63

[object]
10.0.0.61
10.0.0.62
```

A modo de ejemplo podemos crear una carpeta de trabajo (p.e. `cursostic`). En esa carpeta guardaremos todos nuestros archivos. Comenzaremos guardando el archivo de configuración (`ansible.cfg`) y el de inventario (`hosts.cfg`). En el archivo de inventario colocaremos las máquinas a administrar

Ejemplo 2. Archivo de configuración local `ansible.cfg`

```bash
[defaults]

inventory      = ./hosts.cfg 
```

|      | Usar el archivo de inventario situado en la misma carpeta |
| ---- | --------------------------------------------------------- |
|      |                                                           |

Ejemplo 3. Archivo de inventario `hosts.cfg`

```bash
# Archivo hosts.cfg de inventario
20.0.1.11
20.0.1.4
```

Prueba de funcionamiento

```bash
$ ansible all -m ping

20.0.1.11 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
20.0.1.4 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```



##  Comandos directos

Ejemplo 4. Conocer el uso de disco de las máquinas del inventario

```bash
$ ansible all -a "df -h" 

20.0.1.11 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
udev            991M     0  991M   0% /dev
tmpfs           201M  3.1M  197M   2% /run
/dev/vda1        20G  2.0G   18G  10% /
tmpfs          1001M     0 1001M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs          1001M     0 1001M   0% /sys/fs/cgroup
tmpfs           201M     0  201M   0% /run/user/1000

20.0.1.4 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
udev            991M     0  991M   0% /dev
tmpfs           201M  3.1M  197M   2% /run
/dev/vda1        20G  2.0G   18G  10% /
tmpfs          1001M     0 1001M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs          1001M     0 1001M   0% /sys/fs/cgroup
tmpfs           201M     0  201M   0% /run/user/1000
```

|      | `all` hace referencia a que se ejecute en todos los equipos del inventario |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

### - Ejecución de comandos como `root`

El argumento `--become` permite ejecutar comandos como `root`.

Ejemplo 5. Realizar operaciones como `root` en todos los equipos administrados

```
ansible all -a "apt update" --become 
```

|      | `--become` ejecuta las operaciones como `root` en los equipos administrados |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Ejemplo 6. Reiniciar todos los equipos del inventario

```bash
$ ansible all -a "reboot" --become
```

Ejemplo 7. Realizar operaciones en un grupo de equipos administrados

```bash
ansible webserver -a "apt install apache2" --become 
```

|      | `webserver` indica el grupo de servidores del inventario  sobre el que realizar operaciones. Se puede indicar una lista de grupos  separados por comas. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Ansible permite el uso de módulos que amplían la funcionalidad básica proporcionada por Ansible. La [página de módulos de Ansible](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html) ofrece un acceso al listado de módulos agrupados por categorías.

Por ejemplo, el módulo [`copy`](https://docs.ansible.com/ansible/latest/modules/copy_module.html#copy-module) copia un archivo del sistema de archivos local al lugar indicado en las máquinas remotas.

Ejemplo 8. Copiar un archivo a las máquinas administradas

```bash
ansible all -m copy -a "src=sample.txt dest=/home/ubuntu/sample.txt"
```