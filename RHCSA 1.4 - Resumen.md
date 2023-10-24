## manpages

Tabla de las secciones comunes de los manpages.

| Section | Content type                                  | Description                                      |
|:------- |:--------------------------------------------- |:------------------------------------------------ |
| 1       | User commands                                 | Both executable and shell programs               |
| 2       | System calls                                  | Kernel routines that are invoked from user space |
| 3       | Library functions                             | Provided by program libraries                    |
| 4       | Special files                                 | Such as device files                             |
| 5       | File formats                                  | For many configuration files and structures      |
| 6       | Games and screensavers                        | Historical section for amusing programs          |
| 7       | Conventions, standards, and miscellaneous     | Protocols and file systems                       |
| 8       | System administration and privileged commands | Maintenance tasks                                |
| 9       | Linux kernel API                              | Internal kernel calls                            |


¿Qué significa cada sección? Pues cuando queramos consultar cualquier cosa, de los manpages, por ejemplo si es un comando el comando sería passwd(1), indicando que es un ''user command''. En otros casos, por ejemplo si queremos consultar informaci'on sobre la estructura del fichero  */etc/passwd*, con el comando siguiente podemos realizarlo:

```bash
# Podemos acceder a la seccion 5 del fichero de /etc/passwd relacionada con el comando,
man 5 passwd 

# El comando de arriba es
man section topic

# Busqueda de manpages por palabras clave
man -k passwd
chgpasswd (8)        - update group passwords in batch mode
chpasswd (8)         - update passwords in batch mode
fgetpwent_r (3)      - get passwd file entry reentrantly
getpwent_r (3)       - get passwd file entry reentrantly
...
passwd (1)           - update user's authentication tokens
passwd (1ossl)       - OpenSSL application commands
passwd (5)           - password file
passwd2des (3)       - RFS password encryption
```