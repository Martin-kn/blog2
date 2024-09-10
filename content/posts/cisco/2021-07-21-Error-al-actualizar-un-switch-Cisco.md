---
layout: single
title: Illegal copy / Oversize al actualizar firmware - Switch Cisco
excerpt: "Un problema con el que me encontré a la hora de actualizar el firmware cuando compre el switch. Errores como 'illegal copy' o 'oversize', en mi caso el modelo es SF300-24 pero no solo se limita a éste los errores. "
date: 2021-03-10
classes: wide
header:
  teaser: /assets/images/cisco/cisco1.png
  teaser_home_page: true
  icon: 
categories:
  - server
tags:  
  - cisco
---


Un problema con el que me encontré a la hora de actualizar el firmware cuando compré el switch. Errores como 'illegal copy' u 'oversize', en mi caso es el modelo es SF300-24 pero no solo se limita a este los errores.


### Illegal copy

- illegal software format copy al subir un archivo → hay que cargar una versión anterior hasta que deje subir el firmware. Luego se puede cargar otra más nueva de manera escalonada hasta llegar a la version que necesitemos. 

![Cisco1!](/images/cisco/cisco1.png)

### Oversize

- oversize o tamaño muy grande al actualizar → en el foro de cisco dice que esto es porque tenemos que buscar dentro de esa versión la que tiene zip (que dentro de ella viene el bootloader) e instalar el bootloader. Para ello necesitamos si o si hacerlo via TFTP y levantar nuestro propio servidor para enviar el archivo.


![Cisco2!](/images/cisco/cisco2.png)
