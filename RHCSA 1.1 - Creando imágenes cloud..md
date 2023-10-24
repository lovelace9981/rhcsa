#rhcsa #labs #vm #cloud-init #qemu-kvm
En la página web indicada en el siguiente [enlace](https://developers.redhat.com/products/rhel/download) debemos ver el submenú que se expone a continuación:

![[Pasted image 20230220184944.png]]

Luego a continuación debemos selecciónar de la sección Other (La distribución **Release** debe estar en RHEL 9)): Virtualización y bare metal. ¿Qué implicaciones tiene?
- **El Virtualization:** es una imagen qcow2, que es un fichero que funciona como un disco virtual, que los hypervisores de las máquinas virtuales usan como punto para poder almacenar la información de los Sistemas Operativos invitados (Guest).
- **Bare Metal:** es el clásico fichero de ISO que se indica en la siguiente subsección de instalarlo en una máquina virtual.

![[Pasted image 20230220185217.png]]

En la siguiente parte de registro de la imagen, indicamos que lo registramos luego **Register Later**, ya que no tenemos ninguna licencia activa, sin embargo al instalar la imagen, sí podemos activarlo con nuestro ID de la Red Hat mediante consola o GUI.

En la siguiente parte, la de configuración de sistemas de ficheros, hay dos opciones: 
- **Automático:** Recomendado si no sabemos gestionar particiones o redimensionarlas en este caso ya que son imágenes de disco precargadas.
- **Manual:** Aquí lo explicaré de manera un poco más extensa con comandos para poder , [[RHCSA 1.2 - Particionado Linux.]] De momento debemos quedarnos que el particionado de la web tiene como objetivo el particionado de la imagen QCOW2, es decir una imagen precargada para un despliegue de una máquina ligera para practicar donde se apoya en una única particion de 10 GiB en Root / con un sistema de ficheros XFS.

![[Pasted image 20230220185942.png]]

Luego en la siguiente parte, nos preguntará sobre si queremos incluir algún paquete adicional, esto es útil por ejemplo si en un futuro queremos una imagen de RHEL, precargada con NGINX, PHP, Python 4,... Lo que sea... Nosotros no vamos a necesitar de momento ningún paquete precargado, además en esta certificación vamos a aprender a usar yum o dnf para gestión y instalación de paquetería.

![[Pasted image 20230220190518.png]]

Finalmente nos preguntará por el nombre que le queramos poner a la imagen. Pongo uno por defecto o el que se os ocurra.
![[Pasted image 20230220190618.png]]

Finalmente nos muestra el resumen y finalizamos. En el panel del image builder se empezará a crear las imágenes para la descarga. Esperamos unos minutos.

![[Pasted image 20230220190706.png]]

Una vez creadas las imagenes precocinadas, podemos obtener el JSON de la imagen en los 3 botones, para poder reintroducirlo y que se pueda regenerar una configuración similar. Ya que las imágenes recreadas no duran indefinidamente y expiran al cabo de unas horas.

![[Pasted image 20230220191031.png]]

##  Instalando kcli
Como se ha indicado anteriormente, la imagen es #cloud-init, esto implica ventajas como que podemos inyectar mediante automatizaciones claves de SSH que nos permiten utilizar las máquinas virtuales, sin tener que meternos en una ventana gráfica de algún gestor de máquinas virtuales de Hypervisor tipo 1 (Bare Metal - QEMU-KVM), o un Hypervisor tipo 2 (Capa de aplicación - VirtualBox).

kcli es una herramienta programada en Python que nos permite gestionar desde Hypervirsores de Tipo 1 (QEMU-KVM aka Libvirt, vsphere) o nubes basadas en máquinas virtuales (Openstack, AWS,...)

Nos permite la generación de máquinas virtuales con comandos de manera sencilla, así como poder conectarnos a ellas sin tener que sabernos la dirección IP privada, simplemente le tenemos que decir los nombres de la máquina en cuestión.

Para proceder a instalar kcli, necesitamos qemu-kvm en el sistema. Para ello seguimos los siguientes comandos.

```bash
# RedHat-Like systems
sudo yum -y install libvirt libvirt-daemon-driver-qemu qemu-kvm
# or Debian-Like systems
sudo apt-get install libvirt libvirt-daemon-system qemu-kvm

sudo newgrp libvirt

# Añadimos a continuación a nuestro usuario en el grupo de libvirt.
sudo usermod -aG qemu,libvirt $(id -un)
# or Debian-like systems
sudo usermod -aG libvirt-qemu $(id -un)

# activamos el demonio de libvirt
sudo systemctl enable --now libvirtd

# Recomendado, se necesita hacer un logout o reiniciar el sistema para que se recargue la configuración de permisos de grupo en el usuario actual.

# Añadimos los repositorios
sudo dnf -y copr enable karmab/kcli ; sudo dnf -y install kcli

# Debian-like 
curl -1sLf https://dl.cloudsmith.io/public/karmab/kcli/cfg/setup/bash.deb.sh | sudo -E bash
sudo apt-get update
sudo apt-get -y install python3-kcli

```

A continuación, comprobamos que funcione kcli con el siguiente comando:

```bash
kcli list vm

# Output 
peter@mark-6:~$ kcli list vm
+----------------+--------+----+--------+------+---------+
|      Name      | Status | Ip | Source | Plan | Profile |
+----------------+--------+----+--------+------+---------+
|  Windows_2000  |  down  |    |        |      |         |
| debian11server |  down  |    |        |      |         |
|   debian11xl   |  down  |    |        |      |         |

```

### Creando perfil *profile* de rhel9.0

Primeramente, tenemos que tener la imagen disponible y descargada desde la web anterior. Luego tenemos que crear un perfil en kcli para que prepare el modo cloud-init, donde pondrá nuestras claves de SSH para autenticarnos de manera automática. 

**Si no tenemos claves de SSH usamos el siguiente comando**

```bash
ssh-keygen

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in prueba
Your public key has been saved in prueba.pub
The key fingerprint is:
SHA256:JI2eTX25RPQfFI2NftlkmygPlMtz0ZDlt53axFRZR74 peter@mark-6
The key's randomart image is:
+---[RSA 3072]----+
|           .+.=OX|
|       o . + ==+B|
|      o + + =.+*B|
|     . *   O ++=X|
|      o S   B  Eo|
|             .+  |
|             . . |
|                 |
|                 |
+----[SHA256]-----+
```


Para ello tenemos que crear una carpeta en tu $HOME o ~, llamada .kcli. 

```bash
mkdir ~/.kcli
nano ~/.kcli/profiles.yml
# or
vi ~/.kcli/profiles.yml
```

Luego en profiles.yml, ponemos lo siguiente y se puede alterar.

```yaml
redhat9:
 image: rhcsa9.qcow2
 numcpus: 2
 memory: 2048
 disks:
  - size: 10
 reservedns: true
```

Finalmente, se tiene que comprobar que kcli lea el profile correctamente.
```bash
peter@mark-6:~$ kcli list profiles
+---------+------------------+---------+-------+--------------+---------+-----------+--------+------------+-------------+
| Profile |      Flavor      |   Pool  | Disks |    Image     |   Nets  | Cloudinit | Nested | Reservedns | Reservehost |
+---------+------------------+---------+-------+--------------+---------+-----------+--------+------------+-------------+
| redhat9 | 2cpus 2048Mb ram | default |   10  | rhcsa9.qcow2 | default |    True   |  True  |    True    |    False    |
+---------+------------------+---------+-------+--------------+---------+-----------+--------+------------+-------------+
```
Luego para crear una máquina virtual con kcli, usamos el siguiente comando:

```bash
peter@mark-6:~$ kcli create vm redhatpruebas -p redhat9
Deploying vm redhatpruebas from profile redhat9...
Creating dns entry for redhatpruebas in network default
Waiting 5 seconds to grab ip...
Waiting 5 seconds to grab ip...
Waiting 5 seconds to grab ip...
Waiting 5 seconds to grab ip...
redhatpruebas created on local
peter@mark-6:~$ kcli list vm
+----------------+--------+-----------------+--------------+-------+---------+
|      Name      | Status |        Ip       |    Source    |  Plan | Profile |
+----------------+--------+-----------------+--------------+-------+---------+
|  Windows_2000  |  down  |                 |              |       |         |
| debian11server |  down  |                 |              |       |         |
|   debian11xl   |  down  |                 |              |       |         |
| debiandesktop  |  down  |                 |              |       |         |
|    gemini0     |  down  |                 |              |       |         |
|    gemini1     |  down  |                 |              |       |         |
|    gemini2     |  down  |                 |              |       |         |
|     gentoo     |  down  |                 |              |       |         |
| redhatpruebas  |   up   | 192.168.122.221 | rhcsa9.qcow2 | kvirt | redhat9 |
|  rockydesktop  |  down  |                 |              |       |         |
|     win11      |  down  |                 |              |       |         |
+----------------+--------+-----------------+--------------+-------+---------+

```

Finalmente para conectarnos podemos usar el siguiente comando:

```bash
peter@mark-6:~$ kcli ssh redhatpruebas
Activate the web console with: systemctl enable --now cockpit.socket

Register this system with Red Hat Insights: insights-client --register
Create an account or view all your systems at https://red.ht/insights-dashboard
[root@redhatpruebas ~]# uname -a
Linux redhatpruebas 5.14.0-162.12.1.el9_1.x86_64 #1 SMP PREEMPT_DYNAMIC Tue Dec 20 06:06:30 EST 2022 x86_64 x86_64 x86_64 GNU/Linux
```

O también podemos hacerlo mediante el clásico ssh.

```bash
peter@mark-6:~$ ssh root@192.168.122.221
The authenticity of host '192.168.122.221 (192.168.122.221)' can't be established.
ED25519 key fingerprint is SHA256:<redacted>.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.122.221' (ED25519) to the list of known hosts.
Activate the web console with: systemctl enable --now cockpit.socket

Register this system with Red Hat Insights: insights-client --register
Create an account or view all your systems at https://red.ht/insights-dashboard
Last login: Sun Feb 26 07:08:45 2023 from 192.168.122.1
[root@redhatpruebas ~]# 

```

Finalmente registramos nuestro sistema Red Hat en la web indicada con nuestro ID en el siguiente [enlace](https://red.ht/insights-dashboard)
![[Pasted image 20230226131347.png]]
Luego en la página de insights, debemos indicarle que usamos una versión superior ala 8.4, que es cloud públic, nos dará las instrucciones del fichero de configuración que debemos usar y sus parámetros. Junto al comando que debemos ejecutar.

![[Pasted image 20230226132531.png]]

Para editar el fichero:

```bash
vi /etc/insights-client/insights-client.conf
```

**Nota: recuerden quitar las almohadillas del fichero de configuración, # son comentarios. 

```conf
# Change authentication method, valid options BASIC, CERT. Default BASIC
authmethod=BASIC

# username to use when authmethod is BASIC
username=<redacted>

# password to use when authmethod is BASIC
password=<redacted>
```

Finalmente ejecutamos el comando de registro.
```bash
insights-client --register

[root@redhatpruebas ~]# insights-client --register
Successfully registered host redhatpruebas
Automatic scheduling for Insights has been enabled.
Starting to collect Insights data for redhatpruebas
Failed to parser yum repolist: Line containing 'repo id' was not found in table
Uploading Insights data.
Successfully uploaded report from redhatpruebas to account <redacted>.
View the Red Hat Insights console at https://console.redhat.com/insights/
```


Luego en el Inventory, deberá aparecer el sistema que acabamos de registrar.
![[Pasted image 20230226132656.png]]

Luego debemos registrarlo en otro sitio, para poder activar los repositorios de Red Hat.
```
[root@redhatpruebas ~]# dnf update
Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered with an entitlement server. You can use subscription-manager to register.

Error: There are no enabled repositories in "/etc/yum.repos.d", "/etc/yum/repos.d", "/etc/distro.repos.d".
```

```bash
[root@redhatpruebas ~]# subscription-manager register
Registering to: subscription.rhsm.redhat.com:443/subscription
Username: <redacted>  
Password: 
The system has been registered with ID: <redacted>
The registered system name is: redhatpruebas
```
Finalmente podemos trabajar con los repositorios DNF
```bash
[root@redhatpruebas ~]# dnf update
Updating Subscription Management repositories.
Red Hat Enterprise Linux 9 for x86_64 - BaseOS (RPMs)                                                                 1.2 MB/s | 7.8 MB     00:06    
Red Hat Enterprise Linux 9 for x86_64 - AppStream (RPMs)                                                              2.9 MB/s |  16 MB     00:05    
Dependencies resolved.
======================================================================================================================================================
 Package                          Architecture            Version                             Repository                                         Size
======================================================================================================================================================
Upgrading:
 webkit2gtk3-jsc                  x86_64                  2.36.7-1.el9_1.2                    rhel-9-for-x86_64-appstream-rpms                  6.2 M

Transaction Summary
======================================================================================================================================================
Upgrade  1 Package

Total download size: 6.2 M
Is this ok [y/N]: 

```

Fuentes adicionales:
https://access.redhat.com/documentation/es-es/red_hat_enterprise_linux/8/html/creating_customized_rhel_images_using_the_image_builder_service/assembly_creating-and-uploading-a-customized-rhel-vmdk-system-image-to-vsphere