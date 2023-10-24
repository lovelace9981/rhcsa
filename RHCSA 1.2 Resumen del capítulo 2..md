Lo primero es notar en qué modo se ejecuta la terminal:

- El usuario normal (*regular user*): ejecuta la terminal con el carácter $. Esto nos indica que solo puede ejecutar binarios que están alojados en:
	- **/bin
	- **/usr/bin
	- Otros binarios propios que con permiso de ejecución del usuario.

Suele ser fácilmente identificable cuando ejcutamos los comandos id o whoami.

```bash
usuario@usuario-Standard-PC-Q35-ICH9-2009:~$ whoami
usuario
usuario@usuario-Standard-PC-Q35-ICH9-2009:~$ id
uid=1000(usuario) gid=1000(usuario) groups=1000(usuario),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),122(lpadmin),135(lxd),136(sambashare)

```

En cambio el superusuario, o usuario root, tiene en todas las terminales el carácter #
```bash
root@usuario-Standard-PC-Q35-ICH9-2009:/home/usuario# whoami
root
root@usuario-Standard-PC-Q35-ICH9-2009:/home/usuario# id
uid=0(root) gid=0(root) groups=0(root)
root@usuario-Standard-PC-Q35-ICH9-2009:/home/usuario# 

```

### Composición de un comando
- command
- options: Ajustan el comportamiento del comando.
- arguments: Al igual que en las funciones, se reciben "strings", como argumento, que dependiendo del comando en cuestión se ejecute en un modo no interactivo gracias a recibir los datos que requiere desde aquí.

### Tipos de terminal
- terminal: interfaz basada en texto que permite la introducci'ón de comandos y imprime una salida estándar.
- physical console: Interfaz física llamada **TTY** que es accesible co pulsando la combinación de teclas CTRL + ALT + FN donde N <= 6. Cada consola física es una sesión completamente diferente a la otra, esto se puede ver en el futuro con ps aux, donde a pesar de tener varias veces el mismo usuario autenticado (login), no podemos ver los procesos hijos (o comandos), que se ejecutan en otras sesiones de manera directa. Aquí es donde suel eun administrador de sistemas autenticarse y trabajar. Salvo que lo hagamos con SSH, que en este caso es una virtual console.
- shell promp o virtual console: Aquí es donde solemos trabajar es una con una términal gráfica. No es del mismo estilo que la TTY es decir tienen limitaciones, no pueden ejecutar una interfaz gráfica completa o DE (Desktop Environment) y además al ser un "proceso hijo del TTY", no suelen pedir una autenticación (Salvo con SSH). Su nomenclatura es /dev/pts/N donde N es un nº entre 1 a N. Son pseudo terminales.

Ejemplo de identificación de TTY.

![[Pasted image 20230308131029.png]]
Ejemplo con una terminal gráfica o con ssh.

```bash
root@usuario-Standard-PC-Q35-ICH9-2009:/home/usuario# tty
/dev/pts/1
```

```bash
# Creación de usuarios
useradd

adduser



# passwd modifica la contraseña del usuario actual si no se indica ningún argumento de usuario.
passwd
Changing password for user `user`.
```
```bash
# passwd gdguser
Changin password for user `gdguser`
```
Este comando, suele escribir sobre los siguientes ficheros:
- /etc/shadow: Es un fichero donde se almacenan las contraseñas y los usuarios bajo un algoritmo hash, suele ser MD5, Blowfish, SHA-256, SHA-512, yescrypt. [Ref](https://www.cyberciti.biz/faq/understanding-etcshadow-file/), [Ref 2](https://www.baeldung.com/linux/shadow-passwords)

En el caso de esta contraseña, suele ser \$y\$ que es yescript
```bash
usuario:$y$j9T$WMXExd91lx0Qq14.DwW7W/$4Me8M[redacted]:19424:0:99999:7:::

```


Metacarácteres:
- ;  Permite la ejecución de dos comandos en la misma línea.
- & Permite la ejecución de dos comandos si el primero ha funcionado correctamente. Es decir que devuelve un 0, en caso contrario la ejecución se para y no pasa al comando a la derecha del ampersand.
- $? Comando que indica el estado de ejecución del comando o programa o proceso anterior.


```bash
peter@mark-5:~$ whoami; id
peter
uid=1000(peter) gid=1000(peter) groups=1000(peter),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),108(netdev),113(bluetooth),118(lpadmin),121(scanner),130(libvirt),133(ubridge),998(docker),1001(wireshark)
```

- ! metacarácter que expande el comando previo sin volver a reescribirlo. Es decir es similar a pulsar la flecha de arriba para volver a ejecutar un comando previo. Si se combina con history podemos usar !N donde N es el comando que está en el registro de history.

```bash
root@usuario-Standard-PC-Q35-ICH9-2009:/home/usuario# history
    1  whoami
    2  id
    3  tty
    4  !
    5  history
root@usuario-Standard-PC-Q35-ICH9-2009:/home/usuario# !3
tty
/dev/pts/1
root@usuario-Standard-PC-Q35-ICH9-2009:/home/usuario# !id
id
uid=0(root) gid=0(root) groups=0(root)
```


``` bash
# Muestra la fecha, lo mejor es usar siempre el manpage que es completo para consultar las combinaciones que tiene.
date
```

```bash
file archivo
file folder
file symlink
file bin
```

```bash
[user@host ~]$ **`date`**
Sun Feb 27 08:32:42 PM EST 2022
[user@host ~]$ **`date +%R`**
20:33
[user@host ~]$ **`date +%x`**
02/27/2022 
```

```bash
# Apertura de ficheros en stdout y obtención de resultados distintos
cat file1 file2
head -n 3 file
tail -n 3 file

¿Qué haría el siguiente comando?
cat file1 | head -n 2 | tail -n 1

wc - word count, cuenta las palabras que hay
wc -l fichero1 líneas 
wc -c fichero carácteres
```

```bash
# Comando que muestra la estructura de ficheros que hay
ls 
# Comando que muestra la ruta actual
pwd
```

Hacer laboratorio n 2