---
title: WinSxS Space Issues, Fix?
summary: "Investigación sobre el consumo de la carpeta WinSxS..."
date: 2024-08-15
categories:
  - server
tags: ["Windows","WinSxS"]
---

`NOTA:` Este post se estará actualizando con nuevos pasos e información.  


## Intro

Hace poco hice una investigación ya que había varios servidores en los que estaba trabajando que ocupaban un espacio descomunal en la ruta **C:\WinSxS** entre **65GB** a **80GB**. Al tener discos de 120GB las VMs se quedaban sin espacio facilmente. 

Lo que causaba alertas de espacio en disco, pérdida de acceso por RDP, problemas para aplicar parches de seguridad, etc. 

![win!](/images/winsxs/win-s.png)

**Ampliar el disco no era una opción ?** 

Con el volumen de servidores que se maneja y por un tema de costos, no. Por eso se le pidió al soporte de MS, una recomendación por escrito de cuánto espacio debía tener disponible y así de alguna forma ya solucionar algunos problemas de alertas pidiendo una ampliación, ya con una recomendación oficial de parte de MS. La cual le da más peso a nuestro pedido de ampliación (el cuál había sido rechazado anteriormente). 

### Recomendación del soporte de MS

1. **Windows Updates:** Para updates regulares tener **20GB** libres.

2. **Feature Updates:** For larger updates, such as upgrading to a new version of Windows, se recomienda al menos **30 - 40GB** de espacio libre.

3. **Driver Updates:** Al menos **5-10GB**.



### Análisis y problemas encontrados

#### StartComponentCleanup

- **StartComponentCleanup** no limpiaba absolutamente nada al ejecutarlo.
  Comando:

  `Dism.exe /online /Cleanup-Image /StartComponentCleanup`

- Al no limpiar ni comprimir nada en tanto tiempo, **StartComponentCleanup** demoraba muchísimas horas en finalizar (casi 7 hs en un servidor). 


- Dentro de los temporales de **WinSxS**, hay una carpeta llamada **PendingDeletes** que tenía entre 6 y 7GB en varios servidores y **PendingRenames** que era para archivos a renombrar, de menor tamaño(130 - 500MB aprox). Las cuales no se eliminaban con **StartComponentCleanup**, siendo que estaban pendientes a eliminarse.


  ```
  Ejemplo de un servidor:

  ├── WinSxS 76GB
  │   ├── Temp 7.2GB
  │   │   ├── PendingDeletes 7.1GB
  │   │   ├── PendingRenames 138MB
  │   │
  │   ├── ManifestCache 2.5GB
  ```


#### AnalyzeComponentStore

  ` Dism.exe /Online /Cleanup-Image /AnalyzeComponentStore `

  Al ejecutar este comando el output muestra que la última limpieza fue a principios de 2020, mas de 4 años sin limpiarse ! (Ni que reconozca el comando **StartComponentCleanup**)

  <iframe src="https://giphy.com/embed/TGvHZanK0Y8poe2lnA" width="480" height="250" style="" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p</p>


#### CBS Logs
 
  `C:\Windows\Logs\CBS\`

  Analizando los logs (CBS) se detectó que un paquete **.Net** del 2019 aparecía solo **supreseded** por 14 días. Recién estaba en proceso de reemplazarse ya que no era necesario. 
  
  Algo no cuadraba, estamos a 2024 y esta analizando archivos de cuando se buildeo el servidor.

  `supreseded = reemplazado ` 

  **supreseded** quiere decir que está en un período de gracia por 14 días, esto lo hace en caso que necesitemos volver todo a como estaba antes. Una forma de resguardo de parte de MS, luego de ese período se elimina.

  Lo raro es que con el comando DISM debería saltearse el período de gracia y eliminar el archivo.



- Desde que se buildearon los servidores no se corria el job del sistema **StartComponentCleanup** (estaba deshabilitado), que lo hace de manera automática. Lo que hace es analizar que se puede borrar y que comprimirse (muy similar al comando que ejecutamos con DISM). Por lo que entiendo este Job corre de manera mensual con un límite de 1 hora, por lo que si se excede de esa hora continuara en otro momento. Caso contrario DISM no tiene límite de tiempo



#### C:\Windows\Installer

- Otro problema, en `C:\Windows\Installer` habia archivos de años anteriores al build que todavia seguían ahi. 

- En estos casos no hay que borrar nada de manera manual tanto WinSxS como Installer, más si ocupa esta cantidad de espacio. Tiene tantos hardlinks apuntando a diferentes paths que seria muy dificil saber el impacto que podría tener en un futuro (errores al actualizar por ej). 

- ResetBase command:
Por lo que menciona en la doc oficial de MS, al ejecutarlo resetea la base y no permite borrar archivos viejos. Lo que si permite es borrar archivos nuevos. 




#### Restart Base command

`Nota:` MS recomiendo NUNCA hacer un Reset Base y si se hace es bajo responsabilidad del cliente


Reset base quita los componentes que no necesita al momento de ejecutar el comando.

Problemas con los que nos podemos encontrar:

- El problema es si necesitamos borrar un update en particular y pasar a la anterior, no vamos a poder porque ese update ya queda fijo en el sistema, como algo inamovible (y el anterior ya lo eliminó). El sistema va decir este file lo necesito y no puedo borrarlo.

- No lo recomiendan nunca correrlo ya que va eliminar los archivos que no necesite ahora, pero va dejar como inamobibles los actuales y no los vamos a poder sacar NUNCA. 

  La prox vez que deba hacer el proceso de reemplazar archivos viejos por los nuevos para luego ser eliminados. No va pasar nada, al menos con los archivos que estaban cuando se corrió el comando **reset base**.




**La solucion ? No usar Windows**

<iframe src="https://giphy.com/embed/mq5y2jHRCAqMo" width="480" height="480" style="" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p</p>





### Pasos aplicados

**HKEY_LOCAL_MACHINE = HKLM**

`Note:` 

Remember to backup any registry key before deletion.

- We clear the maintenance flags by deleting the **MaintenanceFlags** value-name under

`HKML\SOFTWARE\Microsoft\CurrentVersion\SideBySide`

  

- Revisar si este entry existe **DeepCleanControl(Dword)**. La idea es que no exista esa KEY, ya que puede afectar a que el sistema no desinstale lo que no ocupa. <

  `HKML\SOFTWARE\Microsoft\CurrentVersion\Configuration`

- "We recomend to increase of the timeout for the **Trusted installer**, to avoid time outs during clean process":
 
   Se incrementa el **timeout** de **Trust Installer**, para evitar tiempos de espera largos a la hora del proceso de limpieza. 


```
Locate the following subkey:
HKML\System\CurrentControlSet\Services\TrustInstaller

Right-click the TrustInstaller KEY, and then click Permissions.
Grant the Full Control user right to the Administrators group.
Change the BlockTimIncrement value to 2a30 (Hexadecimal)
```



- Verificar que **TrustInstaller** este corriendo en el sistema
```
Service name: TrustInstaller
Display name: Windows Modules Installer
```

#### Comandos usados:

 

#### AnalyzeComponentStore

Sirve para determinar el tamaño real de la carpeta WinSxS.

` Dism.exe /Online /Cleanup-Image /AnalyzeComponentStore `

Nos vamos a encontrar con lo siguiente:


| Output Items                 |  Descripción  |
|------------------------------|:-------------:|
| Fecha de la última limpieza  | "Esta es la fecha de la limpieza del almacén de componentes completada más recientemente." |
| Número de paquetes reclamable|   "Este es el número de paquetes reemplazados en el sistema que la limpieza de componentes puede quitar."   |

```
$ Dism.exe /Online /Cleanup-Image /AnalyzeComponentStore
...
...
...
Date of Last Cleanup: 2020-03-11 22:00:27
Numbers of Reclaimable Packages: 85
```

Hay más items que los pueden buscar en la Doc. oficial de MS si les interesa.


#### StartComponentCleanup Job

Debemos activar el Job **StartComponentCleanup** que sirve para limpiar y comprimir componentes.

Podemos correr el siguiente comando como admin en CMD/PS o hacerlo de manera gráfica:

` schtasks.exe /Run /TN "\Microsoft\Windows\Servicing\StartComponentCleanup `



En uno de los servidores logramos reducir **15GB**, que para el poco espacio de disco C: que tiene libre fué un logro.



#### Limpiar temporales en safe mode
` C:\Windows\WinSxS\Temp\ `

Puede que como hace tanto tiempo que no se limpia WinSxS, el OS no esté borrando los componentes.
Por lo que deberíamos hacerlo de manera manual, por recomendación de MS, ésto debe realizarse en modo seguro y no hacerlo sin tomar precauciones. Ya que si hay algún componente usando/leyendo alguno de esos archivos podría llegar a corromperse.

**Próximamente haré esta parte de la guia**



### Rebuild & Upgrade


Siempre es recomendado es hacer un clean install, si en esa VM se daña algo posiblemente no se pueda reparar (o al menos fácilmente). Ya que estamos arrastrando problemas viejos. De MS recomiendan hacer un clean install.

`In place upgrade` hacerlo lo único que va provocar es que arrastremos posibles errores y no nos va solucionar el problema de espacio ocupado por WinSxS ya que como mucho se van a reemplazar algunos DLL y tal vez otros archivos pero el espacio que va tener luego del upgrade de versión va ser muy similar.

`Rebuild` Es la opción más sana y la que recomienda MS. Pero siempre está el factor realidad que nos enfrentamos, tener que bajar la aplicación, decisiones que debe tomar el owner que exceden lo que nosotros querramos o pensemos que es lo mejor.



### Documentación Oficial


 [Documentación de MS para limpiar WinSxS](https://learn.microsoft.com/es-es/windows-hardware/manufacture/desktop/clean-up-the-winsxs-folder?view=windows-11) 


### Conclusión
- Este tipo de análisis lleva tiempo, no es solo verificar que la solución funcione sino que sea igual para todos los servidores impactados.
- Lo mejor es hacer un rebuild si la expansión de disco tiene un impacto grande en los costos. 10 VM no va ser un costo grande pero si hablamos de más de 200 o 1.000 servidores, los números son otros.
- Otras veces escapa de toda lógica y nos toca vivir un infierno.

  <iframe src="https://giphy.com/embed/9M5jK4GXmD5o1irGrF" width="417" height="480" style="" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>






### Update: borrar folder de forma segura

NOTA: Se recomienda hacer un backup antes de realizar los siguientes pasos.

Las siguientes instrucciones me las dió el soporte de MS para borrar de manera segura el contenido de WinSxS sin dañar nada en el proceso.

#### Pasos a seguir
Para chequear el estado de WinRE, ejecute 

`reagentc /info`

Para habilitar WinRE, ejecute 

`reagentc /enable`


**Luego:**

1. En CMD: `REAgentC /bittore`
2. Reboot server/vm
3. Click en troutbleshoot

[image]

4. Click en Advanced option

[image]

5. Ahora tenemos todas las opciones de recovery listadas en la pantalla. Abrimos Command Prompt

[image]

6. Escriba los siguientes comandos en CMD para identificar el disco del OS.

`Diskpart`

`list disk`

7. Encuentre el disco con el label "WIndows" o "OSDisk" 
8. En CMD, ejecute los comandos Notepad
9. Esto va abrir una aplicación de Notepad, vaya a file open y busque en el disco hasta encontrar la carpeta Windows 
10. Luego de confirmar el disco donde Windows esta instalado, continue con los siguientes pasos:
(Los pasos siguientes asumen que el disco del OS es D:, si el OS se encunetra en otro disco modifique los comandos acorde)

`rmdir d:\Windows\WinSxS\Temp\InFlight\ /s /q`

`rmdir d:\Windows\WinSxS\Temp\PendingDeletes\ /s /q`

`rmdir d:\Windows\WinSxS\Temp\PendingRenames\ /s /q`
