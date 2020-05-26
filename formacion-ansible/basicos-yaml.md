##  YAML

YAML es un lenguaje de serialización de datos legible. Permite  definir tipos de datos comunes, como listas, mapas y valores escalares.

YAML es sensible a los espacios en blanco y usa indentación para el anidado de datos.

### 1. Inicio de un documento YAML

Es posible añadir dos directivas al inicio de los documentos YAML (`%YAML` y `%TAG`), aunque en la práctica no se suelen usar.

- `%YAML`: Especifica la versión YAML del documento
- `%TAG`: Define un *tag*. Los *tags* se usan para definir tipos de datos en documentos YAML.

Después de las dos directivas se añade una línea con tres guiones (`---`) para marcar el inicio del documento YAML. La mayoría de los documentos YAML comienzan directamente con los tres guiones (`---`) ignorando el uso de las directivas `%YAML` y `%TAG`.

### 2. Escalares

Usaremos valores escalares para cadenas y números

```
---
name: Michael
power_level: 9001
```

### 3. Listas

Podemos definir listas de dos formas: En un listado por líneas en el  que cada ítem aparecerá indentado y con un guion, o bien de forma  compacta separando los ítems por comas y encerrando los elementos de la  lista entre corchetes

```
---
ansible_statements:
- Easy to learn
- Powerful
- Extensive module support
---
ansible_statements: [Easy to learn, Powerful, Extensive module support]
```

### 4. Mapas

Un mapa permite definir pares clave→valor. También son conocidos como arrays asociativos o `hashmaps`. Anteriormente ya usamos un mapa

```
---
name: Michael
power_level: 9001
```

El mapa es el todo, que está formado por pares clave→valor. De forma compacta, podemos expresar el mapa anterior como:

```
---
{ name: Michael, power_level: 9001 }
```

Pero podemos definir estructuras más complejas:

```
---
person:
    first_name: Michael
    last_name: Heap
    skills: 
        - Ansible
        - Golang
        - Python
        - PHP
    likes: [dogs, walking, programming] 
    favorites: 
        drink: Pepsi Max
        color: Red
    other: 
        - key: value 
          another: val
        - key: foo
          another: bar 
```

|      | Lista          |
| ---- | -------------- |
|      | Lista compacta |
|      | Mapa           |
|      | Mapa de listas |
|      | Mapa           |
|      | Mapa           |

### 5. Literales

Permiten definir cadenas largas

```
message: >
    This is a message that is
    going to span several lines
    but is going to be placed on
    a single line when evaluated
```

Si usamos el operador *pipe* (`|`) respetará los saltos de línea definidos en el literal

```
message: |
    This is a message that is
    going to span several lines
    whilst keeping whitespace
    intact
```