---
title: Resize Kali linux VM in Virtualbox and VMware (or any other distro)
summary: "How Add disk space to a linux vm for eg: root partition"
date: 2025-01-02
categories:
  - linux
tags: ["kali","disk","virtualbox","vmware"]
---

En esta guia voy a explicar como expandir la partición root de Kali Linux (aplica a caulquier otra distribución de Linux).

Se puede usar tanto en Virtualbox, VMware como cualquiér otro programa, simplemente cambia el paso de agregar espacio a la VM.

## Virtualbox

En el caso de Virtualbox, vamos abrir **Virtualbox media manager** 

`File -> Tools -> Virtualbox media manager` o podemos usar `Ctrl+D`

 Primero debemos agregar el espacio a la VM, en esta caso seleccionamos Kali. Si la parte de abajo figura en gris pueden dar un refresh o apagar la VM.

![image!](/images/kali-vm/1-1.png)

`En este ejemplo agregué solamente 1GB`

## VMware 

En el caso de usar **VMware**:

<!-- En virtual machine settings --> 

Vamos a nustra VM, seleccionamos `"edit virtual machine settings" -> En "virtual machine settings" -> Go to Hard Disk -> Expand`

![image!](/images/kali-vm/vmware.png)

Seleccionamos el espacio que deseamos agregar y damos a "Expand"

![image!](/images/kali-vm/vmware2.png)


## Expansión disco

Aquí podemos ver el GB agregado "Unallocated", como se ve en la imagen figura atrás de todo del lado derecho (bloque de lineas punteadas).

![image!](/images/kali-vm/1b.png)

Lo que debemos hacer es ir moviendo el bloque para que quede posicionado al lado de /dev/sda1 para poder expandir esa partición. De lo contrario no vamos a poder hacerlo.

`NOTA:` Todos estos cambios no se van aplicar de manera inmediata por lo que si se equivocan, lo pueden revertir. Para aplicar los cambios de forma definitiva lo explico al final de la guia.

Seguir los pasos que se ven a continuación:

1) Primer paso mover el bloque unallocated a /dev/sda2


Click derecho sobre /dev/sda2 -> Resize/Move 

![image!](/images/kali-vm/2.png)

Seleccionar la flecha negra y arrastrarla hacia la derecha

![image!](/images/kali-vm/3.png)

Quedaría así:

![image!](/images/kali-vm/4.png)

Aplicamos los cambios con el botón  [Resize/Move]

Ahora vemos en la siguiente imagen que el bloque "unallocated" forma parte de `/dev/sda2`

![image!](/images/kali-vm/5.png)

3) Ahora debemos seleccionar Swapoff en nuestra partición swap para poder trabajar con ella.

![image!](/images/kali-vm/6.png)

Ahora se habilita la opción para poder incrementar/mover, damos click allí.

![image!](/images/kali-vm/7.png)

`NOTA:`

En este paso no hay que aumentar el espacio sino desplazar el bloque hacia el lado derecho. Lo que va permitir dejar el unallocated space listo para poder expandir nuestra partición Root

Mantenemos el click en la parte blanca y arrastramos el bloque hacia la derecha:

![image!](/images/kali-vm/8.png)  


4) A continuación dejo un video de como se debería mover el bloque en /dev/sda2 para poder dejar el espacio libre listo para poder usarse en la partición Root

<video src="/images/kali3.mp4" width="720" height="540" controls></video>


5) Una vez hecho eso nos dirigimos a la particion root (/dev/sda1)

Click derecho -> Resize/Move 

![image!](/images/kali-vm/10.png)

Y se debe mover la flecha negra hacia el final del bloque.

![image!](/images/kali-vm/11.png)


6) Aplicar los cambios de manera definitiva:

![image!](/images/kali-vm/12.png)


At your own risk :D

![image!](/images/kali-vm/13.png)

7) Por último debemos activar la swap nuevamente:

![image!](/images/kali-vm/14.png)


Listo, expandimos correctamente la partición y ya podemos usarla.












