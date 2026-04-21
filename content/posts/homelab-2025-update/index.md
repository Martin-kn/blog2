---
layout: single
title: HomeLab 2025 Update
excerpt: "Homelab update."
date: 2025-03-15 
featured_image: '/assets/images/homelab/lab2.png'
image: '/homelab/lab2.png'
classes: wide
header:
  teaser: /assets/images/homelab/lab2.png
  teaser_home_page: true
  icon: /assets/images/homelab/server.ico
categories:
  - server
tags: ["linux","homelab"]
---

Este es mi servidor personal, funciona tanto para dar servicios a usuarios y conexión a internet con un router/firewall virtual conectado a mi red así como también para implementar y probar nuevas tecnologias.


<!--Es también mi laboratorio para poder practicar redes, seguridad e implementar diferentes servicios para uso personal. -->


![Lab!](/images/homelab/lab2.png)

Si bien algunas VM se utilizan para hacer pruebas, el servidor es estable ya que al dar internet tanto a VM's como equipos físicos en la red no debe dejar de funcionar.



### Diagrama 
![Lab!](/images/homelab2025/homelab2025-4.png)

`NOTA:` El tercer Nic es un usb con chipset ASIX, ASX88179. Con buen soporte para BSD y Linux

Como se ve en el diagrama el NIC1 pasa directo a la VM "OPNsense" que es quién maneja la red, actua como WAN. Gestiona la red interna al igual que se conecta con otro switch físico del diagrama.

Si bien esta configuración tiene sus ventajas, como por ejemplo no desperdiciar recursos, al reiniciar Proxmox se pierde toda conectividad. Y también se agrega complejidad al implementarlo.




### Uptime
![Lab!](/images/homelab2025/mordor.png)
Este fué mi medidor de cortes de luz, hasta que alguien tiró un cable al agua e hizo corto un fin de semana.
![Lab!](/images/homelab2025/looz.jpg)
<!--
No llegué a sacar un screenshoot a tiempo pero estuvo activa mas de 210 días, 7 meses activo.
-->





### Modificaciones
Desde el primer post del homelab pasaron varios años y se hicieron muchos cambios. Voy a mencionar lo que se hizo/modificó hasta la fecha desde el post pasado:

Cabe mencionar que el último update fué hace 4 años 

#### Modificaiones generales
- Se configuró Tailscale para acceder remotamente junto con ACL para limitar los accesos a cada usuario.
- Se cambió PFsense por OPNsense
- Cambié el dashboard donde figuran los accesos a las apps (No figuran todas). Lo uso de homepage en el navegador, también tiene agregados marcadores divididos por columnas con un buscador integrado:
![Lab!](/images/homelab2025/homepage.png)

- Migré la mayor parte de VMs y LXC a Debian. 
- Se eliminaron varias VM y servicios que no se utilizaban.
- Traté de simplificar lo máximo posible ya que luego se hace más complejo y demandante tener que dar soporte. <!--a más VMs.-->
- WIKI: Se migró a una wiki sin base de datos para mayor simplicidad al hacer backups o levantar nuevamente el servicio en otra VM/Docker.

<!-- ![Proxmox!](/images/homelab2025/proxmox-vms.png)  -->

```
  ├── Proxmox server
  │   ├── Monitoring
  │   ├── GitlabCI
  │   ├── NPM
  │   ├── IAM
  │   ├── DockerVM
  │   ├── Nessus
  │   ├── OPNsense
  │   ├── NAS
  │   ├── Testing VM1
  │   ├── Testing VM2
  │   ├── ...
  │   ├── ...
  ├── Proxmox node2
  │   ├── ...
  │   ├── ...
  ```

#### NAS
- Cambié una VM con OMV como NAS por una instalación limpia de Debian y configuré SMB allí. En lo personal me resulta más cómodo.
- Los contenedores multimedia tienen un volumen montado al NAS para facilitar la gestión de archivos.
- Hace unos años usaba una Raspberry Pi b3+ como NAS con un disco externo. Eso se migró a Proxmox.

#### Seguridad / Vuln
- En 2024 implementé Nessus para análisis de vulnerabilidades en las VM y equipos físicos, para poder remediar las vulnerabilidades con la información de los scan.
- Hago otros scan sobre Docker y Linux. Por ej. análisis SAST para contenedores y scripts de hardening en Linux. 
- Implementé un runner local para CI/CD con Gitlab CI, donde subo imágenes docker al registro y realizo scans SAST.


#### IaaC
- Implementación de Infra as a Code y configuración del entorno:
  - Ansible 
  - Terraform

#### Monitoreo

- Se implementó influxDB2 en Proxmox para almacenar métricas y mostrarlas en grafana.
- Entre otras herramientas de monitoreo para VMs, docker, alertas por errores, logs, etc.
- Grafana lo uso para centralizar toda la información y poder visualizarla usando diferentes dashboards, incluso las alertas de errores.

![Lab!](/images/homelab2025/grafana-server-2.png)

![Lab!](/images/homelab2025/grafana-server-3.png)
![Lab!](/images/homelab2025/grafana-server-4.png)
![Lab!](/images/homelab2025/LXC-grafana.png)


#### Nginx Proxy manager
- Se configuró un reverse proxy para que funcione de intermediario, redirigiendo las consultas al servidor/puerto correspondiente. Y poder configurar certificados TLS/SSL.


### Firewall/Router

Se configuró el firewall/router en una VM dentro de Proxmox con pci-passthrough asignando los puertos físicos. Funcionando tanto para las VM dentro de Proxmox como para por ej. el switch físico donde esta conectada la workstation.

Se migró de PFsense a OPNsense. Estoy utilizando PfBlockerNG junto con unas listas para bloquear ads,tracking y páginas maliciosas. Ntopng para monitorear el tráfico.


### AI - DeepSeek

Estoy corriendo Deepseek de manera local y me idea es poder pasarlo a una VM para que todos en la red tengan acceso y puedan consumirlo como un servicio web. El problema es el consumo de recursos, tengo que liberar un poco de ram o usar otro equipo para poder implementarlo.

### Multimedia
(Contenido libre de copyright)
#### Videos
- A día de hoy el contenedor tiene montado un volumen al NAS, pero en un principio como solución provisoria, me encontré con el problema de que no reconocía los soft links generados hacia la carpeta donde lee docker (lo cuál no me paso con otro contenedor). Esto era una solución temporal "atado con alambre".  
- Una de las razones del cambio de Plex a Jellyfin fué el software libre/open source y que Plex no lo es.

![Lab!](/images/homelab2025/proprietary.jpg)


#### Música
- A diferencia de Jellyfin el servicio de streaming de música que hosteo no tiene ningún tipo de problema con los soft links.
- Posibilidad de crear listas y descargar música.
![Lab!](/images/homelab2025/tech2.png)



### IOT / Domótica

En el post pasado mostré como estaba usando una Raspberry Pi con un relay para poder prender equipos/luces con python.

<!-- ![Relay!](/images/homelab/relay6.gif) -->


<img src="/images/homelab/relay6.gif" width="400" height="auto">

Ahora estoy planificando agregar una VM para controlar todo por el protocolo Zigbee, el cuál es más eficiente energéticamente (lo que beneficia cuando hay baterias de por medio, otro projecto que voy a comentar en otro momento).

#### Sistema de riego

Estoy planificando un sistema de riego por goteo automatizado para mi huerta/frutales que se controle mediante una app y devuelva informacion. 

<!--
<iframe src="https://giphy.com/embed/xT5LMrX45sQcjqNmVy" width="480" height="250" style="" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p</p>
-->

Dejo una foto de mis primeros duraznos:
![Proxmox!](/images/homelab2025/frutal.jpeg)

También tengo plantas de tomate cherry y estoy cultivando algunas variedades raras. Así como verdura de hoja verde, entre ellas variedades orientales.
![cherry!](/images/homelab2025/cherry.png)

