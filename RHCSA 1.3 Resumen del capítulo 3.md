### Jerarquía de directorios.

En WIndows la jerarquía de directorios es totalmente diferente a la de Linux, en concreto se usan las letras A: B: C: D: E:, como índices de volúmenes de los sistemas de ficheros.

En concreto, en los sistemas Windows, el disco principal es C:, los sistemas alternativos serán indexados con otra etiqueta que no sea la C:

Los separadores de los niveles de Linux, es Backslash, es decir para ir a tu directorio personal se necesitan 3 subniveles. 

/home/tunombre/

Cada backslash, es un subnivel, parece que son dos, pero si hacemos lo siguiente:

```bash
# Aquí se vería los primeros subdirectorios.
[root@redhatpruebas ~]# ls /
afs  bin  boot  dev  efi  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

Podemos ver que / es un directorio que contiene todos los directorios.  O sea la raíz. Luego cuando navegamos a otro directorio, poniendo un backslash, podemos ver que al ponerle el backslash, se convierte en la raíz de esos archivos. Son como separadores para navegar.

En el tutorial se indica que /etc/passwd, es un fichero, si bien es cierto que es un fichero passwd, no necesariamente son todos un fichero típico, podemos poner /home/pepe/algo, ¿Es una subcarpeta?, ¿es un fichero?, ¿es un binario? En Linux, se usan las extensiones de fichero como algo previamente acordado para distinguir entre las decenas de formatos binarios que hay. Pero en Linux, no es obligatorio como en Windows, que si en Windows suprimimos la extensión no sabe que hacer con ese "binario".

```bash
[root@redhatpruebas ~]# ls -la /etc/passwd
-rw-r--r--. 1 root root 1462 Feb 26 07:07 /etc/passwd
[root@redhatpruebas ~]# ls -la /etc
total 1084
drwxr-xr-x. 91 root root     8192 Apr 30 04:18 .

# Limitaciones de ls, no sabe si un fichero es un binario o no.
[root@redhatpruebas ~]# ls -l /bin/bash
-rwxr-xr-x. 1 root root 1388904 Nov 24 09:29 /bin/bash

# Usamos la utilidad file, para poder distinguir qué es cada cosa en el sistema de ficheros.
[root@redhatpruebas ~]# file /bin/bash
/bin/bash: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=5d82a44f2a4466ff21f763af86b004d1fcb3a8f1, for GNU/Linux 3.2.0, stripped
[root@redhatpruebas ~]# file /etc/passwd
/etc/passwd: ASCII text
[root@redhatpruebas ~]# file /etc
/etc: directory
```

En toda la estructura de ficheros de cada sistema GNU/Linux, tiene que seguir mas o menos un estándar fijado por la Linux Foundation, que es 
[FHS Filesystem Hierachy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)

Puede existir variaciones en cada sistema, en Red Hat en las últimas versiones cada fichero siguiente es un enlace simbólico a su contraparte en /usr. Antes estaban por separado.

```bash
[root@redhatpruebas ~]# ls -la /bin
lrwxrwxrwx. 1 root root 7 Aug  9  2021 /bin -> usr/bin
[root@redhatpruebas ~]# ls -la /sbin
lrwxrwxrwx. 1 root root 8 Aug  9  2021 /sbin -> usr/sbin
[root@redhatpruebas ~]# ls -la /lib
lrwxrwxrwx. 1 root root 7 Aug  9  2021 /lib -> usr/lib
[root@redhatpruebas ~]# ls -la /lib64
lrwxrwxrwx. 1 root root 9 Aug  9  2021 /lib64 -> usr/lib64
```

En la certificación esos subdirectorios, ya no se hacen distinción por separado como en el FHS, por lo que se aplica a su definición por defecto como /bin, que es el directorio de los binarios ejecutables que contiene los comandos de Bash. 

/usr/sbin: En el FHS, son los comandos "no esenciales", pero esto ha desaparecido, ya que en realidad no hay distinción.


## Rutas absolutas

Las rutas absolutas son las rutas descritas desde la raíz */*. Es de uso común para navegar a directorios que están a niveles diferentes o lejanos.  Como acceder a /etc, cuando estas en tu /home/loquesea.

¿Qué quiere decir esto?

```bash
## Podemos ver que estamos en /root, por lo que si quiero ir a /usr/bin a copiar un nuevo binario para hacerlo comando, o ver el log en /var/log. Tenemos que poner toda la ruta.
[root@redhatpruebas ~]# pwd
/root
## Queremos ver un log
[root@redhatpruebas ~]# cat /var/log/cron 
Feb 26 07:07:11 localhost crond[977]: (CRON) STARTUP (1.5.7)
```

## Rutas relativas

Las rutas relativas trabajan a partir del directorio actual, permitiendo la navegación sin tener que indicar la ruta completa desde la raíz, esto es útil si navegamos a directorios que solo están a un nivel de diferencia, como el directorio padre (parent directory).


Tipos de rutas relativas:
 1. **..** : Directorio padre *parent directory*: Es el directorio que está justo encima del actual. 
 2. **.**: Directorio actual. Puntero al directorio actual.
	 1. No se debe confundir el **.fichero** con **.**, lo primero es un fichero oculto en el sistema GNU/Linux, que no es visible por defecto con ls, si no con ls -la.
 3. **~**: Directorio $HOME, es tu home, se usa como ruta relativa para navegación rápida a los ficheros localizados en tu directorio personal.
 4. **-**: Cambia al directorio anterior, es como una memoria de deshacer el cambio. Te devuelve al directorio anterior en el que estabas trabajando.

En el ejemplo de abajo podemos usar 

```bash
# USO de ..
[root@redhatpruebas ~]# pwd
/root
# Similar a /home/cloud-user/hola
[root@redhatpruebas ~]# touch ../home/cloud-user/hola
[root@redhatpruebas ~]# ls ../home/cloud-user/
.bash_history  .bash_logout   .bash_profile  .bashrc        .ssh/          hola           
[root@redhatpruebas ~]# ls ../home/cloud-user/hola 
../home/cloud-user/hola
# Lo siguiente sube a dos niveles, se puede hacer tantas veces como subdirectorios tengamos.
../../

# Esto tambien vale
../..

# Esto tambien vale, son lo mismo. El backslash es por si necesitas apuntar a un fichero o subdirectorio del directorio padre, es decir a ficheros o directorios hermanos *Están al mismo nivel*
../
..

# Uso de ~ 
[root@redhatpruebas var]# pwd
/var
[root@redhatpruebas var]# cd ~
[root@redhatpruebas ~]# pwd
/root
# Tambien si dejamos el comando cd sin argumentos es similar a cd ~
[root@redhatpruebas var]# cd
[root@redhatpruebas ~]# pwd
/root


# Uso de .
[root@redhatpruebas ~]# mkdir pruebas
[root@redhatpruebas ~]# touch pruebas/hola
[root@redhatpruebas ~]# ls
pruebas
[root@redhatpruebas ~]# ls pruebas/
hola
[root@redhatpruebas ~]# cp ./pruebas/hola .
[root@redhatpruebas ~]# ls
hola  pruebas
[root@redhatpruebas ~]# ls pruebas/
hola

# Uso de -
[root@redhatpruebas var]# cd -
/var
[root@redhatpruebas var]# cd ~
[root@redhatpruebas ~]# cd -
/var

```


**Notas:** En la navegación por consola el uso de pwd, es necesario, porque normalmente las variables de entorno de bash, no van a ser sobrecargadas como en algunos sistemas de GNU/Linux, que te ponen en tu alias el directorio actual en el que estas de  manera absoluta.

## Administración de ficheros.
### Creación de directorios

```bash
# Comando por defecto
mkdir Carpeta
# Comando que crea tanto la carpeta padre como la hija
# Se deben usar con precaución porque no hay control de error.
mkdir -p Padre/Hijo
# Trucos usando extended brace expansion, que crea un rango de echos para generar directorios. Carácteres especiales de BASH
[root@redhatpruebas ~]# mkdir -p padre/hijo{1..3}
[root@redhatpruebas ~]# ls -R padre/
padre/:
hijo1  hijo2  hijo3

padre/hijo1:

padre/hijo2:

padre/hijo3:
# Usando extended brace expansion
[root@redhatpruebas ~]# file padre/hijo{1..3}
padre/hijo1: directory
padre/hijo2: directory
padre/hijo3: directory
# Usando extended brace expansion como lista para otros directorios
[root@redhatpruebas ~]# file padre/{hijo{1..3},primo}
padre/hijo1: directory
padre/hijo2: directory
padre/hijo3: directory
padre/primo: directory


```

[Referencia de los carácteres especiales de bash](https://tldp.org/LDP/abs/html/special-chars.html)

## Copia de ficheros y directorios.

Se usa el comando cp. Este por defecto ignora la copia de directorios, salvo que lo forcemos.[]
```bash
# Generando ficheros para las pruebas
[root@redhatpruebas ~]# touch padre/{hijo{1..3},primo}/nieto{1..3}
[root@redhatpruebas ~]# ls -R padre/
padre/:
hijo1  hijo2  hijo3  primo

padre/hijo1:
nieto1  nieto2  nieto3

padre/hijo2:
nieto1  nieto2  nieto3

padre/hijo3:
nieto1  nieto2  nieto3

padre/primo:
nieto1  nieto2  nieto3
```

Copia de ficheros usando lo aprendido de las rutas relativas.
```bash
[root@redhatpruebas padre]# cp hijo1/nieto1 hijo2/hijo1nieto1
[root@redhatpruebas padre]# ls -R .
.:
hijo1  hijo2  hijo3  primo

./hijo1:
nieto1  nieto2  nieto3

./hijo2:
hijo1nieto1  nieto1  nieto2  nieto3

./hijo3:
nieto1  nieto2  nieto3

./primo:
nieto1  nieto2  nieto3

root@redhatpruebas padre]# cp -R hijo1 hijo1new
[root@redhatpruebas padre]# ls -R
.:
hijo1  hijo1new  hijo2  hijo3  primo

./hijo1:
nieto1  nieto2  nieto3

./hijo1new:
nieto1  nieto2  nieto3

./hijo2:
hijo1nieto1  nieto1  nieto2  nieto3

./hijo3:
nieto1  nieto2  nieto3

./primo:
nieto1  nieto2  nieto3

```


### Mover directorios y ficheros
```bash
[root@redhatpruebas padre]# mv ./hijo2/hijo1nieto1 ./hijo1
[root@redhatpruebas padre]# ls -R
.:
hijo1  hijo1new  hijo2  hijo3  primo

./hijo1:
hijo1nieto1  nieto1  nieto2  nieto3

./hijo1new:
nieto1  nieto2  nieto3

./hijo2:
nieto1  nieto2  nieto3

./hijo3:
nieto1  nieto2  nieto3

./primo:
nieto1  nieto2  nieto3

```

## Borrado de ficheros

```bash
[root@redhatpruebas padre]# rm hijo1/hijo1nieto1 
rm: remove regular empty file 'hijo1/hijo1nieto1'? y
[root@redhatpruebas padre]# rm -r hijo1new/
rm: descend into directory 'hijo1new/'? y
rm: remove regular empty file 'hijo1new/nieto1'? y
rm: remove regular empty file 'hijo1new/nieto2'? y
rm: remove regular empty file 'hijo1new/nieto3'? y
rm: remove directory 'hijo1new/'? y
[root@redhatpruebas padre]# ls -R
.:
hijo1  hijo2  hijo3  primo

./hijo1:
nieto1  nieto2  nieto3

./hijo2:
nieto1  nieto2  nieto3

./hijo3:
nieto1  nieto2  nieto3

./primo:
nieto1  nieto2  nieto3
## Argumentos que permiten forzar a que rm pregunte o no
[root@redhatpruebas padre]# touch notprompt
[root@redhatpruebas padre]# touch prompted
[root@redhatpruebas padre]# rm -ri prompted 
rm: remove regular empty file 'prompted'? y
[root@redhatpruebas padre]# rm -rf notprompt 
```

### Hard Links

Para entender el uso de los Hard Links, o enlaces duros. Hace falta entender qué son los Inodes (Inodos). 

#### Inodes

Es una acortación de lo que se llama de manera completa, index node. Es un identificador único de una pieza de metadatos alojada en un sistema de ficheros. Cada trozo de metadatos describe lo que pensamos que es un fichero.

Los metadatos incluyen:
- *Tipo de fichero (File type):*
	- Regular:
	- Directorio:
	- Block special:
	- Character Special:
	- Symbolic link:
	- FIFO (PIPEs):
	- Sockets
- *Permisos de acceso (File permission):*
	- Read *R*
	- Write *W*
	- Execution *E*

- *Dueños (Owners):* User y Group
- *Timestamps: *Tiempos de creación, modificación, último acceso, etc..
- *Tamaño: *Tamaño del fichero.
- *Punteros a los bloques de datos:* Es un array de punteros de tamaño 2⁴ = 16 bloques. Si ocurre los siguientes casos:
	- El tamaño del fichero supera los 16 bloques que tiene el inodo original, el inodo usa los 16 bloques originales, como indices a subindices de otros 16 bloques, que puede que sea otro índice, o un bloque indirecto.
	- Si es un bloque indirecto, este tiene otra tabla de bloqus que al igual, puede apuntar al nodo lógico o ser otro array de punteros.

¿Por qué se usan punteros de punteros a bloques de datos?
Esto es porque cada bloque al que apunta el array del inodo, tiene un tamaño lógico. Como se puede visualizar en la tabla de abajo, por cada subnivel indirecto del array, permite indexar mayor número de bloques que permiten un fichero mas grande.

| Logical block size | Direct Blocks | Single indirect blocks | Double indirect Blocks |
| ------------------ | ------------- | ---------------------- | ---------------------- |
| 2048 bytes         | 24 KB         | 1 MB                   | 512 MB                 |
| 4096 bytes         | 48 KB         | 4 MB                   | 4 GB                   |
| 8192 bytes         | 96 KB         | 16 MB                  | 32 GB                  | 

![[Pasted image 20230430160442.png]]

Bueno una vez sabiendo esto, un **hard link**, es un enlace que permite que exista un fichero que no tenga distinción con un fichero original. Ya que el hard link es como otro inodo con una cabecera que contiene exactamente la misma información que el original.

Con el comando ls -li podemos conocer el número de inodo, el argumento -i es que pide el inodo.

```bash
[root@redhatpruebas links]$ ls -i 
25172495 file
[root@redhatpruebas links]$ ln file file_hardlink
[root@redhatpruebas links]$ ls -li
total 0
25172495 -rw-r--r--. 2 root root 0 Apr 30 10:07 file
25172495 -rw-r--r--. 2 root root 0 Apr 30 10:07 file_hardlink
```

Bueno para qué sirve un hard link en concreto, aparte de para que apunte a la misma dirección de los bloques de ficheros en disco, que se puede demostrar variando el contenido en el fichero original o en el hardlink *a efectos prácticos el sistema no distingue entre un hardlink y el fichero original*.

Con el hardlink lo que tenemos es un fichero del mismo inodo de referencia, sobre el cual si por ejemplo borramos uno. El contenido sigue existiendo en el sistema de ficheros. Solo se ha borrado una referencia.

```bash
[root@redhatpruebas links]# cat file && cat file
file           file_hardlink  
[root@redhatpruebas links]# cat file && cat file_hardlink 
Hola
Hola
[root@redhatpruebas links]# rm file
rm: remove regular file 'file'? y
[root@redhatpruebas links]# ls -i
25172495 file_hardlink
[root@redhatpruebas links]# cat file_hardlink 
Hola

```


**Limitaciones de los hardlinks**:
- Solo puede trabajar con ficheros regulares, no se puede crear un hardlink a directorios o ficheros especiales.
- Solo se puede crear hard links si el fichero original y el hard link. Son creados en el mismo sistema de ficheros, esto es importante si la jerarquía de sistema de ficheros está compuesta de distintas particiones o de distintos dispositivos de almacenamiento. Ejemplo, existe el caso habitual donde el /home esté en una partición distinta con el mismo sistema de ficheros o otro, pero que este sistema de ficheros es el que tiene otra tabla de inodos distinta, ya que puede coincidir el número de inodo con otra tabla. 
	- /home sistema ext4 en una partición /dev/sda2 con su propia tabla de inodos.
	- / sistema ext4 en una partición /dev/sda1 con su propia tabla de inodos.
	- Si tratamos de crear un hard link entre /bin/sh y /home/sh_hardlink. No sería posible, las tablas de inodos colisionan.

### Enlaces simbólicos
Son un tipo de enlace, que funcionan como los accesos directos de Windows, es un tipo especial de fichero, que apunta a un fichero que tiene un inodo apropiado o a un directorio.

Pueden:
- Existir enlaces entre distintos sistemas de ficheros.
- Pueden existir enlaces simbólicos que apunten a un directorio, o a otros ficheros especiales. No solo a ficheros regulares.
Como consecuencia:
- Si borramos los ficheros originales, el enlace se queda colgado (dangling), no sirve para nada.

```bash
[root@redhatpruebas links]# ln folder hardlink
ln: folder: hard link not allowed for directory
[root@redhatpruebas links]# ln -s folder symlink
[root@redhatpruebas links]# ls -la
total 8
drwxr-xr-x. 3 root root   56 Apr 30 10:43 .
dr-xr-x---. 8 root root 4096 Apr 30 10:06 ..
-rw-r--r--. 1 root root    5 Apr 30 10:11 file_hardlink
drwxr-xr-x. 2 root root    6 Apr 30 10:42 folder
lrwxrwxrwx. 1 root root    6 Apr 30 10:43 symlink -> folder
[root@redhatpruebas links]# unlink symlink
```


## Match file names with shell expansion

Bash tiene soporte completo de REGEX - metacarácteres, lo que implica que podemos utilizarlo para filtrar ficheros o carpetas o cualquier otra cosa.

| Pattern              | Match                                                                                                 | example                                                                                             |
| -------------------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| *                    | Cualquier string, con cero o mas carácteres                                                           | cp folder/* another_folder/. - Copia todos los ficheros                                             |
| ?                    | Cualquier caracter, solo 1, se pueden usar varias veces para coincidir con cualquier nº de carácteres | ls \| egrep "hijo?"                                                                                 |
| \[abc...\]           | Cualquier caracter dentro de los corchetes conocidos como enclosed class                              | ls \| egrep "\[h\]"                                                                                 |
| \[!abc...\]          | Cualquier caracter que no esté dentro de los corchetes conocidos como enclosed class                  |                                                                                                     |
| \[^abc...\]          | Igual que el de arriba                                                                                |                                                                                                     |
| \[::alpha::\]        | Cualquier carácter alfabético                                                                         | \[root@redhatpruebas padre\]# ls \| egrep "\[\[:alpha:\]\]" **hijo**1 **hijo**2 **hijo**3 **primo** |
| Otros ver referencia | [Ref](https://tldp.org/LDP/abs/html/x17129.html)                                                      |                                                                                                     |

Metacarácteres de bash

## metacaracter ;
Se usa como en C, donde se hace una separación de comandos que se ejecutan en el mismo prompt sin importar lo que le haya pasado al primero.

```bash
[root@redhatpruebas padre]# ls ; echo hola
hijo1  hijo2  hijo3  primo
hola
```

## metacaracter  ?

```bash
**[root@redhatpruebas padre]# ls | egrep "??"
hijo1
hijo2
hijo3
primo
**
```

## ~ tilde expansion
Carácter especial que coincide con el $HOME del usuario.
```bash
echo ~
/root
```

### Brace expansion 
{1,2} Expansión por coma, cada elemento es único 
{1..2} Expansión por rango con la sintaxis de doble punto.
{a{1,2}, b} Expansión anidada.

Util para usarlo con mkdir.
```bash
[root@redhatpruebas padre]#  echo file{a{1,2},b{4..10}}txt
filea1txt filea2txt fileb4txt fileb5txt fileb6txt fileb7txt fileb8txt fileb9txt fileb10txt

```

### Uso de variables en BASH

Es útil para marcar variables de entorno que tienen la capacidad de ser leídas como variables globales por programas que lo necesiten, como puede ser Java $JAVA_HOME, $HOME,...

Se pueden ver todas las variables usadas por el sistema con echo $ tabulador.
```bash
[root@redhatpruebas padre]# echo $ {TABULADOR}
$BASH                       $EPOCHREALTIME              $MAIL                       $SHELL
$BASHOPTS                   $EPOCHSECONDS               $MAILCHECK                  $SHELLOPTS
$BASHPID                    $EUID                       $MOTD_SHOWN                 $SHLVL
$BASHRCSOURCED              $GROUPS                     $OLDPWD                     $SRANDOM
$BASH_ALIASES               $HISTCMD                    $OPTERR                     $SSH_CLIENT
$BASH_ARGC                  $HISTCONTROL                $OPTIND                     $SSH_CONNECTION
$BASH_ARGV                  $HISTFILE                   $OSTYPE                     $SSH_TTY
$BASH_ARGV0                 $HISTFILESIZE               $PATH                       $TERM
$BASH_CMDS                  $HISTSIZE                   $PIPESTATUS                 $UID
$BASH_COMMAND               $HOME                       $PPID                       $USER
$BASH_LINENO                $HOSTNAME                   $PROMPT_COMMAND             $XDG_RUNTIME_DIR
$BASH_REMATCH               $HOSTTYPE                   $PS1                        $XDG_SESSION_CLASS
$BASH_SOURCE                $IFS                        $PS2                        $XDG_SESSION_ID
$BASH_SUBSHELL              $LANG                       $PS4                        $XDG_SESSION_TYPE
$BASH_VERSINFO              $LESSOPEN                   $PWD                        $_
$BASH_VERSION               $LINENO                     $RANDOM                     $colors
$COLUMNS                    $LINES                      $SECONDS                    $which_declare
$COMP_WORDBREAKS            $LOGNAME                    $SELINUX_LEVEL_REQUESTED    $which_opt
$DBUS_SESSION_BUS_ADDRESS   $LS_COLORS                  $SELINUX_ROLE_REQUESTED     
$DIRSTACK                   $MACHTYPE                   $SELINUX_USE_CURRENT_RANGE  

```

# Referencias
https://web.mit.edu/tytso/www/linux/ext2intro.html
https://docs.oracle.com/cd/E19455-01/805-7228/fsfilesysappx-4/index.html
http://web.cs.ucla.edu/classes/spring14/cs111/scribe/12b/#LoUI
http://uw714doc.xinuos.com/en/FS_admin/_ufs_Inodes.html
https://tldp.org/LDP/abs/html/special-chars.html
https://www.redhat.com/sysadmin/inodes-linux-filesystem
