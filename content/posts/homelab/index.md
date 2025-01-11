---
layout: single
title: Home Lab
excerpt: "Proyecto personal en el que fui aprendiendo bastante y de a poco voy agregando hardware, modificando o instalando nuevos servicios. La idea es también poder hostear las aplicaciones web que vaya creando a medida que voy aprendiendo mejor los frameworks e incorporando nuevas tecnologías."
date: 2021-03-15
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

`NOTA:` Se realizaron muchos cambios con el paso de los años, los voy actualizar en un nuevo post.

Laboratorio en proceso, para poder practicar redes, seguridad e implementar diferentes servicios para uso personal.

![Lab!](/images/homelab/lab2.png?w=200)


<!-- <img src = "/homelab/lab2.png"> -->

### Firewall/Router

Tiene instalado Pfsense, cuenta con un i3 6100U, 8gb de ram y dos interfaces de red. La idea es poder utilizarlo también como IDS (sistema de detección de intrusos) con Suritcata , para poder hacerlo me hace falta seguir investigando y saber como utilizar sus reglas correctamente. Estoy utilizando PfBlockerNG junto con unas listas para bloquear ads,tracking y páginas maliciosas. Ntopng para monitorear el tráfico.

### Switch

Cisco sf-300-24 managed, lo cambié hace poco para poder crear Vlans anteriormente estaba utilizando un Netgear gs108.

### Otros equipos

- Amd Ryzen 5 2600: workstation, que también uso para virtualizar con kvm/qemu.
- Amd Fx 6300 con Proxmox: Open Media Vault ,Portainer, Ansible, Jellyfin, Nextcloud, algunas Wiki, entre otros. Cuenta con una tarjeta de red adicional que utilizo para conectar algunas vm’s con otra red.


![Proxmox!](/images/homelab/proxmox-vm.png?w=200)

Update: Imagen actual de Proxmox (2024)

![Lab!](/images/homelab/proxmox.png)


- Raspberry Pi b3+: samba, nginx, wiki. 
- Raspberry Pi b3+: motionEyeOS, sistema de vigilancia.
- También tengo un mother itx 1151 con algunos de los pines del socket dobaldos que me regalo un amigo, solo faltaría terminar de repararlo y conseguirle un procesador. Sería el reemplazo perfecto del Amd Fx 6300 (atx), más que nada por el consumo eléctrico y el espacio físico que ocupa ya que por el momento no necesito tantos puertos PCIe.



### Domótica

Otra parte del proyecto es domótica, la cual quedó un poco en el olvido, lo que se ve a continuación es una raspberry conectada a un relay el cual se activa con un script en python. 

![Relay!](/images/homelab/relay6.gif)

La idea es poder ejecutar desde la terminal un comando y prender las luces de la habitación. Lo más práctico sería tener algún asistente open source y colocar varios dispositivos wifi conectados al mismo, de esta forma se puede controlar por ejemplo del teléfono con una app sin estar escribiendo comandos.

En estos momentos prefiero usar el tiempo libre en aprender ciberseguridad , programación o temas relacionados a gnu/linux, en un futuro me gustaría retomarlo y poder llevar a cabo varias ideas que tengo dando vueltas en la cabeza.

### Mejoras a futuro: 

- Rack a medida, la idea es hacerlo en madera, colocarle unas ruedas para poder movilizarlo sin problemas y que sea lo más compacto posible.
- Patch panel.
