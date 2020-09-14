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

### **Abrir el panel de control de kubernetes** y **IBM Cloud Shell**

1.  Una vez haya iniciado sesión con su cuenta de IBM Cloud, ingrese a la [lista de recursos](https://cloud.ibm.com/resources).
2.  En la pestaña **Clústeres** de clic en el nombre **cluster-prueba,** que corresponde al nombre del clúster que se usará en la guía.
3.  Pulse el botón **Panel de control de kubernetes,** esto abrirá una nueva pestaña con la información del clúster que se usará a lo largo del Hands-On.
4.  Regrese a la página de IBM Cloud y de clic en el icono de **IBM Cloud Shell** (el cuarto botón de derecha a izquierda en la barra superior). Esto abrirá una nueva pestaña en su navegador.

![](https://user-images.githubusercontent.com/60897075/93118767-6348fe00-f686-11ea-935b-d632e7cacdc4.gif)

### Crear el despliegue de la imagen de Tomcat

1.  Desde **IBM Cloud Shell** descargue la configuración del clúster mediante el siguiente comando (en el comando se usa el ID del clúster, que puede obtener mediante: `ibmcloud ks cluster ls`).

```
ibmcloud ks cluster config --cluster br8o9fsw08ha12p3ahk0
```

   2. Cree el servicio de despliegue en Kubernetes, para esto, ejecute los comandos a continuación. Recuerde modificar el valor **\<nombreapellidodeployment>** por sus datos, para dar un nombre único a su despliegue.

```
kubectl create deployment <nombreapellidodeployment> --image=us.icr.io/tomcatns/tomcat2020
Ejemplo: kubectl create deployment julianaleondeployment --image=us.icr.io/tomcatns/tomcat2020
```

   3. Luego debe exponer la imagen docker en el puerto por defecto de Tomcat. Nuevamente recuerde modificar el valor **\<nombreapellidodeployment>** por el nombre de su despliegue, que fue configurado en el paso anterior.

```
kubectl expose deployment/<nombreapellidodeployment> --type=NodePort --port=8080
Ejemplo: kubectl expose deployment/julianaleondeployment --type=NodePort --port=8080
```

   4. Para el correcto funcionamiento de la imagen de Tomcat es necesario cambiar el nombre de webapps.dist a webapps. Para esto corra el siguiente comando, el cual  listará los pods presentes en su clúster; identifique aquel que inicia por el nombre de su despliegue **\<nombreapellidodeployment>** y copie el nombre completo para el siguiente paso.

```
kubectl get pods 
```

   5. Para el siguiente comando, reemplace el valor **\<nombre\_pod>** por el nombre copiado anteriormente.

```
kubectl exec --stdin --tty <nombre_pod> -- /bin/bash
Ejemplo: kubectl exec --stdin --tty julianaleondeployment-845f6d5db5-bzfkz -- /bin/bash
```

    6. El comando anterior obtiene un shell en el pod especificado, allí corra los comandos siguientes para modificar webapps.dist.

```
mv webapps webapps2
mv webapps.dist/ webapps
exit
```

![](https://user-images.githubusercontent.com/60897075/93119599-b53e5380-f687-11ea-9b6c-4623d5f447d8.png)

### Conocer la IP y el puerto de despliegue

1.  Con el siguiente comando podrá ver la información general del clúster, identifique la **IP pública del clúster** (cluster-prueba).

```
ibmcloud cs workers --cluster cluster-prueba
```

    2. Con el siguiente comando podrá ver los servicios creados en el clúster, identifique su servicio cuyo nombre debe ser **\<nombreapellidodeployment>** y copie el puerto que aparece al lado del puerto 8080.

```
kubectl get services
```

![](https://user-images.githubusercontent.com/60897075/93119916-3a296d00-f688-11ea-8594-feed4169e1ee.png)

### **Resultados**

Ingrese en su navegador  \<IP\_pública\_del\_clúster>:\<Node\_port>, por ejemplo **169.47.168.99:30469.** Y finalmente verifique en el panel de control de Kubernetes que su despliegue y servicio no tenga ningún problema

![](https://user-images.githubusercontent.com/60897075/93120026-5af1c280-f688-11ea-90b8-c73e58f3bc99.gif)
