#rhcsa #labs
Los laboratorios son herramientas que nos provee Red Hat online.

Los vemos en nuestro curso arriba a la derecha.

![[Pasted image 20230220180314.png]]

Los creamos de la siguiente manera después de pulsar en el icono anterior:

![[Pasted image 20230220180405.png]]

Los laboratorios creados son, cada uno tiene una particularidad que irá apareciendo a lo largo del curso:

*  Bastion
* Classroom
* Server A
* Server B
* Utility
* Workstation - *Máquina con una GUI* 
	* pass: students 

El punto más importante del laboratorio es la máquina de workstation, es útil como punto de partida. El laboratorio entero tiene una duración máxima de 80 horas de uso.

![[Pasted image 20230220180639.png]]

La creación de las máquinas virtuales suele tardar alrededor de 10 minutos. No se podrá abrir una consola hasta que no se indique como active.

**Nota 1:** Si la resolución de la máquina virtual es muy pequeña, se recomienda cambiarla a la máxima en su configuración.

**Nota 2:** Las máquinas tienen un periodo de autodestrucción automático, se puede aumentar, pero se recomienda no dejar ningún dato personal, o de gran importancia. Se pueden recrear a los 7 días.

**Nota 3:** Las máquinas tienen una parada automática en alrededor de una hora, tenedlo en cuenta si estamos trabajando en ellas. También en el uso en horas de las máquinas virtuales, si las usamos excesivamente o las dejamos encendidas sin parada automática sigue contabilizando. Se recomienda parar las máquinas con el botón de STOP.

**Nota 4:** Los usuarios de Firefox, debemos permitir las ventanas emergentes (pop-ups), de la red hat para poder visualizar la máquina virtual.

![[Pasted image 20230220181205.png]]

Visualización de la máquina virtual en el portal de la Red Hat.

## Creación de nuestro propio laboratorio

**Límite de horas:** Infinito.

Esto es como consejo personal, el curso inicialmente, va a ir sobre una terminal básica BASH para practicar comandos hasta determinado punto, se pueden ejecutar sobre un sistema Red Hat con GUI si os parece más cómodo tenerlo de manera visual, o con el CLI en un TTY como debería ser en un Servidor. 

Hay dos formas de obtener un entorno de Red Hat GUI o CLI:

*El ID de la Red Hat nos provee de manera gratuita una clave de licencia de Red Hat Enterprise Linux RHEL que podemos usar en un nº limitado de máquinas (físicas o virtuales).* 

[El portal](https://developers.redhat.com/products/rhel/download) que se ve en la siguiente imagen, nos ofrece una variedad de opciones de medios de instalación, que son: 
- ISOs: Imágenes como medio de instalación. Para máquinas virituales, máquinas físicas (bare metal). Nos meteremos aparte en un tutorial. [[RHCSA 1.2 - Creando una máquina virtual ISO.]]
- Imágenes de cloud público y privados: Aquí es donde voy a meterme, ya que tiene particularidades que me gustan, ya que aprenderemos sobre una herramienta muy útil para desplegar máquinas virtuales con un comando preparadas sin tener que meternos en la interfaz gráfica. [[RHCSA 1.1 - Creando imágenes cloud.]]

¿Qué pasa si no queremos descargarnos RHEL?

Bueno existen clones 100%, llamados RHEL-like, pueden considerarse los siguientes:
- Fedora Workstation o Server, esta es una distribución de donde RHEL, obtiene los paquetes probados y refinados. Recordemos que RHEL es una distribución que tiene paquetes de hace año y medio. Es decir no va a la última (bleeding edge es el término utilizado en las distribuciones de este estilo), Fedora en su caso va introduciendo esos paquetes y software nuevo en el presente para que en el futuro en un RHEL v10 (actualmente la v9), obtenga un paquete de Gnome 4.
- Rocky Linux: Es una distribución de Linux que ha aparecido recientemente, como sustitución a CentOS, es una distribución que es un clon o copia carbón (CC), de RHEL, su propósito es el mismo. Solo que no tiene claves de licencias.
- Alma Linux: Idem que Rocky.
- CentOS: Esta distribución ha pasado de ser una CC de RHEL, a ser una distribución que esta en el medio entre Fedora y Red Hat, prueba paquetes ya más refinados pero aún siguen estando con una idea de ir terminando de testearlos, comprobar la interoperabilidad de estos y de encontrar comportamientos indefinidos. Su nombre actual es CentOS Stream.

