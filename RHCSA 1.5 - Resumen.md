
## Creando, viendo y editando ficheros

### Redirigiendo la salida estándar y entrada estándar

Podemos redirigir la salida estándar o de error, para almacenarla en un lugar para luego poder investigar cómo ha ido la ejecución de un programa. Todo ello con tuberías (pipes).

**Normalmente, en la ejecución de un programa, este lee la entrada desde teclado y envía la salida a la términal.**

El proceso usa lo que se conoce como **file descriptor**, para poder obtener la entrada y enviar la salida. Todos los procesos tienen al menos los siguientes file descriptor:
- Standard input (channel 0) - File descriptor que lee la entrada de teclado.
- Standard output (channel 1) - File descriptor que escribe la salida en terminal.
- Standard error (channel 2) - Igual que el 1, pero solo para errores.

Si un programa tiene otras conexiones a otros ficheros, usan otros file descriptor por separado y de mayor numeración.

![[Pasted image 20230709210302.png]]

|Number|Channel name|Description|Default connection|Usage|
|:--|:--|:--|:--|:--|
|0|`stdin`|Standard input|Keyboard|Read only|
|1|`stdout`|Standard output|Terminal|Write only|
|2|`stderr`|Standard error|Terminal|Write only|
|3+|``_`filename`_``|Other files|None|Read, write, or both|4

La tabla anterior, describe de manera general el uso de los file descriptor.

### Redirección de una salida a un fichero.

La redirección de I/O cambia cómo el proceso obtiene la entrada o la salida, en vez de recibir la entrada por el teclado, o enviar la salida a una terminal, el proceso puede leer o escribir desde y hacía los ficheros. Podemos guardar mensajes, en vez de mostrarlos en la terminal. De manera alternativa, podemos usar la redirección para descartar salidas o salidas de errores, pero no son mostrados o guardados.

#### Redirección de la salida

Podemos redirigir la salida hacia un fichero existente o no, en el caso de que no exista el fichero de destino, este es creado.

Sin embargo dependiendo de como se haga la redirección, si el fichero existe, el contenido normalmente es reemplazado por el nuevo si no es usado el modo (append - agregar). 

Para descartar la salida, podemos redirigir hacia */dev/null*. Este es un fichero especial, que borra todo lo que le llege como entrada.

```bash
# Caso 1: Redirige la salida estandar stdout para sobreescribir un fichero
> file
# Caso 2: Redirige la salida estandar stdout para hacer append a un fichero
>> file
# Caso 3: Redirige la salida de error stdeer a un fichero
2> file
# Caso 4: Descarta la salida de error stderr a /dev/null
2> /dev/null
# Caso 5: Envia la salida estandar stdout y stderr al mismo fichero
> file 2>&1
# Caso 5: Hace lo mismo que el anterior
&> file
# Caso 6: Hace append del stdou y del stderr al mismo fichero
>> file 2>&1
```

- Caso 1
![[Pasted image 20230709211907.png]]
- Caso 2
![[Pasted image 20230709211946.png]]
- Caso 3
![[Pasted image 20230709211951.png]]
- Caso 4
![[Pasted image 20230709211958.png]]
- Caso 5
![[Pasted image 20230709212009.png]]
- Caso 6
![[Pasted image 20230709212014.png]]
El orden de las operaciones de redirección importa

The following sequence redirects standard output to the `output.log` file and then redirects standard error messages to the same place as standard output (`output.log`).

```bash
> output.log 2>&1
```

The next sequence redirects in the opposite order. This sequence redirects standard error messages to the default place for standard output (the terminal window, so no change) and _then_ redirects only standard output to the `output.log` file.
```bash
2>&1 > output.log
```

For this reason, some people prefer to use the merging redirection operators:

-  `&> output.log` instead of `> output.log 2>&1`
    
-  `&>> output.log` instead of `>> output.log 2>&1` (in Bash 4 or RHEL 6 and later)

NOTAS: Los operadores de redirección más nuevos no suelen funcionar, no están estandarizados.

```bash
# Ejemplo de comando que usa la redireccion
echo "Bin" > file 2> /dev/null
```

#### Construcción de pipelines (canalizaciones tuberías)

Un pipe o pipeline es una secuencia de uno o más comandos que son separados por el siguiente carácter:

```bash
echo "Hola" | tee -a file
```

Este pipeline conecta la salida estándar del comando de la izquierda a la entrada estándar del comando de la derecha.

![[Pasted image 20230709212611.png]]

El uso común de los pipelines es para formatear la salida de un proceso por otros procesos antes de mostrar la salida por consola. Los datos fluyen desde un proceso a otro, siendo alterados por cada comando (proceso) que pase.

**Notas** Los pipelines y la redirección de I/O manipulan la salida y entrada estándar. Pero los pipelines solo envían la salida estándar de un proceso a otro. La redirección la envía a los ficheros, o la obtiene de ficheros.

#### Uso combinado de pipelines y I/O.

Cuando combinamos la redirección con un pipeline, la consola (shell), configura el pipeline entero primero y luego redirige la I/O. Si usamos la redirección I/O en medio de un pipeline, entonces la salida va al fichero pero no al siguiente comando (proceso) en el pipeline.

El siguiente comando envía la salida al fichero saved, pero no muestra nada con less, porque la salida estándar ha terminado antes y no envía nada al pipeline.

```bash
ls > /tmp/saved-output | less
```

El comando **tee** supera esta limitación, permite la copia hacia un fichero, y luego a continuación escribe en la entrada estándar del siguiente comando (proceso). En el caso de no haber ningún proceso, se escribe en terminal.

Tee es una T join, donde sale por ficheros y luego redirige a un proceso.

![[Pasted image 20230709214526.png]]

Examples:

Redirects the output of the `ls` command to the `/tmp/saved-output` file and passes it to the `less` command, so it is displayed on the terminal one screen at a time.
```bash
ls -l | tee /tmp/saved-output | less
```

If you use the `tee` command at the end of a pipeline, then the terminal shows the output of the commands in the pipeline and saves it to a file at the same time.
```bash
ls -t | head -n 10 | tee /tmp/ten-last-changed-files
```

Use the `tee` command `-a` option to append the content to a file instead of overwriting it.
```bash
ls -l | tee -a /tmp/append-files
```


#### Cambiando el entorno shell *Shell Environment*

Fijar *set* shell variables para ejecutar comandos y editar script de bash de arranque *startup* y las variables de entorno para modificar el comportamiento del shell junto a los programas que se ejecutan en este shell.

https://java2blog.com/bash-echo-to-stderr/

https://web.archive.org/web/20221126221021/https://wiki.bash-hackers.org/howto/redirection_tutorial?do=