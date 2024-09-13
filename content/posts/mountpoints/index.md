---
title: How to create,delete and remap mountpoints in Windows Server
description: "Configuración de mountpoints en Windows server más configuración de VMware/Nutanix."
date: 2024-03-15 
categories:
  - server
tags: ["windows","mountpoints","vmware","nutanix","cluster"]
---





## Agregar discos en VMware o Nutanix

Como se ve en las imágenes a continuación, debemos seleccionar:

`ADD NEW DEVICE -> Hard Drive`

![adddisk1!](/images/mountpoints/AddDisk_VMware.png)

![adddisk2!](images/mountpoints/AddDisk_VMware_2.png)

Si lo necesitamos podemos agregar varios discos al mismo tiempo, luego seleccionar su tamaño y guardar los cambios todos juntos. Nos va ahorrar tiempo en vez de hacer uno a la vez.

Ahora configuramos el espacio necesario y damos OK para aplicar los cambios.

![adddisk3!](images/mountpoints/AddDisk_VMware_3.png)


## Preparar los discos en el OS

1. Levantar e inizializar los discos
2. Configurar el disco
3. Crear el mountpoint


### 1. Inicializar los discos

![online!](images/mountpoints/online.png)

![initializeDisk!](images/mountpoints/initializeDisk.png)

![initializeDisk2!](images/mountpoints/InitializeDisk_2.png)

### 2. Configurar el disco

![volume!](images/mountpoints/simple-volume.png)

![volume!](images/mountpoints/simple-volume2.png)

![volume!](images/mountpoints/simple-volume3.png)

Es importante no asignar una letra al disco

![volume!](images/mountpoints/simple-volume4.png)

En este caso vamos usar
`Allocation Unit Size: 64K`
![volume!](images/mountpoints/simple-volume5.png)

![volume!](images/mountpoints/simple-volume6.png)



### 3. Crear Mountpoint

`Disk Management -> click derecho -> Change Drive Letter and Paths -> Add `

![mountpoint!](images/mountpoints/mountpoint1.png)

`Mount in the ... NTFS folder -> Browse`

![mountpoint!](images/mountpoints/mountpoint2.png)

Ahora hay que seleccionar el disco donde queremos agregarlo y asegurarnos que se haya seleccionado bien.

Luego de eso `New folder -> mountpoint name -> OK`

![mountpoint!](images/mountpoints/mountpoint3.png)

Por último damos OK y nuestro mountpoint quedo configurado.

![mountpoint!](images/mountpoints/mountpoint4.png)





## Reconfigurar / Renombrar mountpoints

En que casos nos puede servir ? 

- Nos equivocamos al crearlo.
- Ya no sirve al disco que apunta.
- Necesitamos reducir el tamaño en disco. AL no poder reducirse, debemos:
  1. montar un disco nuevo (VMware/Nutanix)
  2. Pasar el backup al disco nuevo
  3. Borrar el disco/mountpoint viejo y renombrar el mountpoint nuevo (con el nombre del viejo).

### Reconfigurar/Remap mountpoint

`NOTA:`

En el caso de tener que reducir el disco y renombrar el mountpoint nuevo con el nombre del que vamos a eliminar. Nos vamos a encontrar con un error al querer renombrar el mountpoint.

El problema se debe a que en la carpeta raiz, ya existe una carpeta con el mismo nombre, la cual persiste a pesar de eliminar el mountpoint.

En el ejemplo a continuación, **SQLLOG_12** es el mountpoint nuevo que debe reemplazar al disco/mountpoint viejo. Por lo que necesitamos renombrar **SQLLOG_12** a **SQLLOG_1**.

![remap!](images/mountpoints/remap.png)


Al ya existir una caperta con el mismo nombre, no nos va permitir crear el mountpoint. Por lo que vamos a tener que eliminarla.


Para eliminar el mountpoint viejo, vamos a `Disk Management -> click derecho -> Change Drive Letter and Paths -> seleccionamos el mountpoint creado -> elimilar`. Luego el proceso es exactamente el mismo que al crear un mountpoint, como se mostró anteriormente.



![mountpoint!](images/mountpoints/mountpoint1.png)



### Renombrar el disco desde Disk Management

Cuando hagamos el cambio de configuración, va seguir figurando con el nombre viejo(únicamente desde Disk Management). Por lo que vamos a tener que cambiarlo desde propiedades.


## Remover disco/mountpoint viejo

En VMware a la derecha nos va aparecer una cruz **(x)**, al seleccionarla nos va figurar el mensaje de la imagen.

Allí podremos seleccionar múltiples discos si lo deseamos. Luego al dar **OK** se aplicaran los cambios y lo veremos reflejado en el OS.

![remove!](images/mountpoints/deleteDisk-VMware.png)


**Nota:**

Si son muchos discos los que hay que remover y ya estan identificados desde VMware,Nutanix o del hipervisor que estén usando. Recomiendo empezar desde atras hacia delante o sacarlos todos juntos al mismo tiempo. 

Cual es el motivo ?

Cada vez que sacamos un disco se modifica el orden de los demás, si sacamos el disco 2, 5, 7, 15 y luego debemos sacar el disco 44, 38 y 22 puede que preste a la confución. Ya que los discos que necesitábamos sacar ahora van a figurar con otro orden.

En el caso de VMware desde el portal se pueden seleccionar varios al mismo tiempo, en Nutanix por otro lado solo se puede sacar de a 1 disco. Desconozco cómo se puede hacer desde la consola para quitarlos todos al mismo tiempo.