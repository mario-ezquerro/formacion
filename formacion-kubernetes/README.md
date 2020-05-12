---
title: Usando Kubernetes
description: Aprendemos a trabajar usando kubernetes.
author: Manuel Torres
tags: Docker, kubernetes 
date_published: 2020-03-16
---

# Orquestando Docker con Kuberenteres en Español

Documentación para formarse en kubernetes en español.

CC  https://ualmtorres.github.io/SeminarioKubernetes/#trueintroducci-n

## [Introducción](./introduccion.md)

La adopción de Docker sigue creciendo de forma imparable y cada vez  más organizaciones lo usan en producción. Por tanto, se hace necesario  contar con una plataforma de orquestación que permita administrar y  escalar los contenedores.

Supongamos que hemos comenzado a usar Docker y hemos hecho un  despliegue de un par de servidores. De pronto, la aplicación comienza a  tener un gran tráfico de entrada y hay que escalar a una gran cantidad  de servidores para atender la demanda. Aquí es donde entra Kubernetes  para hacer tareas del tipo *dónde debe ir un contenedor, cómo se  monitorizan esos contenedores, cómo se reinician cuando tengan un  problema, quiero escalar de forma automática de 1 a 20 réplicas en  función de la carga*. Incluso, con componentes como Istio, podemos *derivar porcentajes de tráfico a distintas versiones desplegadas de una  aplicación o servicio, derivar tráfico a las versiones en función de la  red o IP desde la que se reciben las peticiones* o *en función del usuario que realiza la petición*.

Por funcionalidades como estas y otras más sorprendentes aún son por  la que posiblemente te planteará que debes poner en tu vida un  orquestador como Kubernetes ;-)



## Índice



- [Introduccion ](introduccion.md)
- [Instalación Kubernetes](./instalacion-kubernetes.md)
- Instalación kubernetes en un portátil (dos maneras)
  1. [Usando Microk8s](3-microk8s.md)
  2. [Usando Minikube](3-minikube.md)
- [kubectl](4-kubectl.md)
- [Componentes de kubernetes](5-Componentes.md)
- [Escalado kubernetes](6-escalado.md)
- [Despliege de aplicaciones](8-despliege-aplicaciones.md)
- [Escalado Horizontal](9-horizontal-pod-autoscaler.md)
- [Almacenamiento](10-almacenamiento.md)
- [Init Containers](11-init.md)
- [Istio](13-istio.md)
- [Apendices](12-apendices.md)





##  Referencias

- [Kubernetes Documentation](https://kubernetes.io/docs/home/) Web oficial.
- [Kubernetes under the hood](https://github.com/mvallim/kubernetes-under-the-hood/blob/master/README.md). Marcos Vallim.
- [Getting Started with Kubernetes](https://theithollow.com/2019/01/26/getting-started-with-kubernetes/). The IT Hollow.
- [Kubernetes: part 1 — architecture and main components overview](https://itnext.io/kubernetes-part-1-architecture-and-main-components-overview-a9ce97264a74). Arseny Zinchenko.
- [Kubernetes Services simply visually explained](https://medium.com/swlh/kubernetes-services-simply-visually-explained-2d84e58d70e5). Kim Wuestkamp.
- [Using environment files over injected environment variables in Kubernetes](https://glamanate.com/blog/using-environment-files-over-injected-environment-variables-kubernetes?utm_sq=g7lkc35pm4). Matt Glaman.
- [Almacenamiento en Kubernetes. PersistentVolumen. PersistentVolumenClaims](https://www.josedomingo.org/pledin/2019/03/almacenamiento-kubernetes/). José Domingo Muñoz.
- [Learn about the basics of Kubernetes Persistence (Part 1)](https://itnext.io/learn-about-the-basics-of-kubernetes-persistence-part-1-b1fa2847768f). Abhishek Gupta.
- [Tutorial: Basics of Kubernetes Volumes (Part 2)](https://itnext.io/tutorial-basics-of-kubernetes-volumes-part-2-b2ea6f397402). Abhishek Gupta.
- [4 command-line tools for Kubernetes: Linux edition](https://developers.redhat.com/blog/2019/08/08/4-command-line-tools-for-kubernetes-linux-edition/?utm_sq=g7nv5eimcv). Don Schenck.