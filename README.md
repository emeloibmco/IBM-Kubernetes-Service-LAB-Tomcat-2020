# Kubernetes Hands-On ☁️

## Contenido

*   [Despliegue de Tomcat a partir de una imagen Docker.](#despliegue-de-tomcat-a-partir-de-una-imagen-docker)
*   Despliegue de Tomcat a partir de un Chart de Helm.

## Pre-requisitos

*   Si aún no tiene una cuenta de IBM Cloud, [regístrese](https://cloud.ibm.com/registration) para obtener una e inicie sesión.
*   Creación de un clúster de [IBM Cloud Kubernetes Service.](https://cloud.ibm.com/docs/containers?topic=containers-getting-started#clusters_gs)
*   [Enviar](https://docs.docker.com/engine/reference/commandline/push/) la [imagen Tomcat](https://hub.docker.com/_/tomcat)  a [IBM Cloud Container Registry.](https://cloud.ibm.com/docs/container-registry-cli-plugin?topic=container-registry-cli-plugin-containerregcli#bx_cr_image_list)

## Despliegue de Tomcat a partir de una imagen Docker 

En esta sección encontrará el paso a paso para desplegar la imagen más reciente de Tomcat disponible en Docker, en un clúster de Kubernetes, utilizando IBM Cloud Shell.

### **Ingresar a IBM Cloud Shell**

Una vez haya [iniciado sesión](https://cloud.ibm.com/login) con su cuenta de IBM Cloud, seleccione la cuenta **"Edgar Melo´s Account"** en la barra superior, e ingrese a [IBM Cloud Shell](https://cloud.ibm.com/shell) (también puede ingresar dando clic en el cuarto icono de derecha a izquierda en la barra superior dentro de la página de IBM Cloud).

![](https://user-images.githubusercontent.com/60897075/93522677-78789380-f8f7-11ea-94d7-ed27530b9210.gif)

### Crear el despliegue de la imagen de Tomcat

1.  Desde **IBM Cloud Shell** descargue la configuración del clúster mediante el siguiente comando (en el comando se usa el ID del clúster, que puede obtener mediante: `ibmcloud ks cluster ls`). Recuerde que puede hacer uso de **Ctrl + Shift + V** para pegar dentro de IBM Cloud Shell.

```
ibmcloud ks cluster config --cluster btgjo8sw0613303fjbf0
```

   2. Cree un espacio de nombres mediante el siguiente comando, modifique el valor \<namespace> por un nombre único.

```
kubectl create namespace <namespace>
 Ejemplo: kubectl create namespace julianaleonns
```

   3. Cree el servicio de despliegue en Kubernetes, para esto, ejecute los comandos a continuación. Recuerde modificar el valor **\<deployment>** por un nombre único para su despliegue y el **\<namespace>** creado en el paso anterior.

```
kubectl create deployment <deployment> --image=us.icr.io/tomcatns/tomcat2020 -n <namespace>
Ejemplo: kubectl create deployment julianaleondeployment --image=us.icr.io/tomcatns/tomcat2020 -n julianaleonns
```

   4. Luego debe exponer la imagen docker en el puerto por defecto de Tomcat. Nuevamente recuerde modificar los valores **\<deployment>** y **\<namespace>.**
>**NOTA 1**: Si esta trabajando con infraestructura clásica ejecute el siguiente comando:

```
kubectl expose deployment/<deployment> --type=NodePort --port=8080 -n <namespace>
Ejemplo: kubectl expose deployment/julianaleondeployment --type=NodePort --port=8080 -n julianaleonns
```

>**NOTA 2**: Si esta trabajando con VPC (Load Balancer) ejecute el siguiente comando:
```
kubectl expose deployment/<deployment> --type=LoadBalancer --name=<service>  --port=8080 --target-port=8080
```
En la etiqueta **\<service>** indique un nombre para su servicio.

   5. Para el correcto funcionamiento de la imagen de Tomcat es necesario cambiar el nombre de webapps.dist a webapps. Para esto corra el siguiente comando, el cual  listará los pods presentes en su clúster; identifique aquel que inicia por el nombre de su despliegue **\<deployment>** y copie el nombre completo para el siguiente paso. Modifique el valor **\<namespace>.**

```
kubectl get pods -n <namespace>
kubectl get pods -n julianaleonns
```

   6. Para el siguiente comando, reemplace el valor **\<pod>** por el nombre copiado anteriormente.

```
kubectl exec --stdin --tty <pod> -- /bin/bash 
Ejemplo: kubectl exec --stdin --tty julianaleondeployment-845f6d5db5-bzfkz -- /bin/bash
```

    7. El comando anterior obtiene un shell en el pod especificado, allí corra los comandos siguientes para modificar webapps.dist.

```
mv webapps webapps2
mv webapps.dist/ webapps
exit
```

![](https://user-images.githubusercontent.com/60897075/93119599-b53e5380-f687-11ea-9b6c-4623d5f447d8.png)

### Conocer la IP y el puerto de despliegue

1.  Con el siguiente comando podrá ver la información general del clúster, identifique la **IP pública del clúster** (cluster-prueba).

```
ibmcloud cs workers --cluster cluster-prueba -n <namespace>
Ejemplo: ibmcloud cs workers --cluster cluster-prueba -n julianaleonns
```

    2. Con el siguiente comando podrá ver los servicios creados en el clúster, identifique su servicio cuyo nombre debe ser **\<deployment>** y copie el puerto que aparece al lado del puerto 8080.

```
kubectl get services -n  <namespace>
Ejemplo: kubectl get services -n julianaleonns
```

![](https://user-images.githubusercontent.com/60897075/93119916-3a296d00-f688-11ea-8594-feed4169e1ee.png)

### **Resultados**

Ingrese en su navegador  \<IP\_pública\_del\_clúster>:\<Node\_port>, por ejemplo **169.47.168.99:30469.** Y finalmente verifique en el panel de control de Kubernetes que su despliegue y servicio no tenga ningún problema

![](https://user-images.githubusercontent.com/60897075/93120026-5af1c280-f688-11ea-90b8-c73e58f3bc99.gif)

## **Referencias** :mag\_right:

Encuentre más información sobre [IBM Kubernetes Service](https://cloud.ibm.com/docs/containers?topic=containers-getting-started)

## **Autores** :black\_nib:

Equipo IBM Cloud Tech Sales Colombia.
