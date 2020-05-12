## 4. kubectl (el CLI de Kubernetes)

Para la interacción con un cluster local o remoto de Kubernetes mediante comandos se usa `kubectl`, un CLI sencillo que nos permitirá realizar tareas habituales como despliegues, escalar el cluster u obtener información sobre los servicios en ejecución. `kubectl` es el CLI para interactuar con el servidor de la API de Kubernetes.

Para más información, consultar la [página oficial de instalación y configuración de `kubectl`](https://kubernetes.io/es/docs/tasks/tools/install-kubectl/#instalar-kubectl)



Para interactuar con unos ejemplos sencillo con `kubectl` podemos

- Obtener información de la versión

  ```bash
  $ kubectl version
  Client Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.3", GitCommit:"b3cbbae08ec52a7fc73d334838e18d17e8512749", GitTreeState:"clean", BuildDate:"2019-11-14T04:24:34Z", GoVersion:"go1.12.13", Compiler:"gc", Platform:"darwin/amd64"}
  Server Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.2", GitCommit:"c97fe5036ef3df2967d086711e6c0c405941e14b", GitTreeState:"clean", BuildDate:"2019-10-15T19:09:08Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"linux/amd64"}
  ```

- Obtener información del cluster

  ```bash
  $ kubectl cluster-info
  Kubernetes master is running at https://192.168.99.100:8443
  KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
  ```

- Obtener los nodos que forman el cluster

  ```bash
  $ kubectl get nodes
  NAME       STATUS   ROLES    AGE     VERSION
  minikube   Ready    master   3d23h   v1.15.0
  ```

- Otras operaciones de interés son:

  - `kubectl get pods` para listar todos los pods desplegados.
  - `kubectl get all` para listar todos los objetos desplegados.
  - `kubectl describe ` para obtener información detallada sobre un recurso.
  - `kubectl logs ` para mostrar los logs de un contenedor en un pod.
  - `kubectl exec  ` para ejecutar un comando en un contenedor de un pod.