# Laboratorio 5.-Docker
## 1. Preparativos
## 2. Docker Engine
En esta parte del laboratorio se trabaja con la herramienta CLI de Docker para manipular contenedores en un entorno local.  
- Ejecutar ‘docker --help' en el terminal y revisar las opciones disponibles para manipular contenedores.  
  ```.bash
    docker --help

- Utilizando la imagen de busybox, obtener la siguiente información. Para ello, sobre-escribe el comando de arranque en cada ocasión:  
  - Mostrar los sistemas de ficheros que están montados.  
    ```.bash
    $ sudo docker run busybox df -h  
      
  - Mostrar el contenido de la carpeta /etc. ¿Hay más o menos elementos que en la carpeta /etc de un Ubuntu Server?
    ```.bash
    docker run busybox ls /etc
    group
    hostname
    hosts
    localtime
    mtab
    network
    nsswitch.conf
    passwd
    resolv.conf
    shadow
    
    Muchas menos cosas que en un ubuntu server
        
  - Mostrar el cuántos binarios ejecutables hay en la carpeta /bin. ¿Hay más o menos elementos que en la carpeta /bin de un Ubuntu Server?
    ```.bash
    $ sudo docker run busybox ls /etc | wc -l
      405
    $ ls /etc | wc -l	# (En tu instancia)  
      1050
    Hay bastante menos archivos en la carpeta /bin de busybox que en la de ubuntu server
    
  - Mostrar cuantas interfaces de red tiene y qué IPs tienen asignadas.
    ```.bash
    
    $ docker run busybox ifconfig
    
    eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
              inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:3 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:286 (286.0 B)  TX bytes:0 (0.0 B)
    
    lo        Link encap:Local Loopback  
              inet addr:127.0.0.1  Mask:255.0.0.0
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

- Lanzar un contenedor de busybox con el comando “ping www.ehu.eus”. Sin interrumpir su ejecución, parar el contenedor desde otro terminal con el comando “docker stop”. ¿Se realiza al instante?
  ```.bash
  #en terminal A
  $ docker run busybox ping www.ehu.eus
  #en terminal B
  $ docker ps 
    CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS     NAMES
    19dcc8939b27   busybox   "ping www.ehu.eus"   42 seconds ago   Up 41 seconds             crazy_hoover
  $ docker stop 19dcc8939b27

  El contenedor tarde en pararse un rato. 

- Repetir la tarea anterior, pero utilizando “docker kill”. ¿Qué diferencia hay con utilizar “docker stop”?
  ```.bash
  #en terminal A
  $ docker run busybox ping www.ehu.eus
  #en terminal B
  $ docker ps 
     CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS     NAMES
    0e47e26d17b6   busybox   "ping www.ehu.eus"   6 seconds ago   Up 5 seconds             amazing_darwin
  $ docker kill 0e47e26d17b6
  El contenedor se detiene al instante.
  La diferencia es que ambos paran el contendor pero:
  - stop-> permite a los procesos en ejecucion detenerse y borra lo que fuera necesario
  - kill-> para el contenedor de golpe

- Abrir una Shell dentro de un contenedor busybox utilizando “docker run” y realizar lo siguiente:
  ```.bash
  $ docker run -it --rm busybox
  ```
  -  Mostrar el número de procesos en ejecución
    ```.bash
    / # ps | wc -l
      4
    ```
  -  Crear un fichero llamado miFichero dentro de /home.
    ```.bash
    / # touch /home/miFichero
   ```
  -  Cerrar la sesión.
    ```.bash
    /# exit
    ```
- Abrir una Shell de nuevo en un contenedor busybox. ¿El fichero en /home sigue estando? ¿Por qué?
  ```
  No esta ya que al cerrar la sesion el contenedor se ha parado tambien y todo el contenido que no sea propio de la imagen no se guardan
  ```
  
A partir de aquí se trabaja con Redis. La forma de uso más básica de Redis es utilizar los siguientes comandos desde
su consola:
  - set clave valor  -> Guardar un par clave-valor, por ejemplo: set miNumero 5
  -  get clave ->  Recuperar el valor de una clave, por ejemplo: get miNumero
    
Más adelante se dan instrucciones de cómo acceder a la consola Redis.

Realizar las siguientes tareas:  
- Lanzar un contenedor con el servidor redis. Para ello, buscar en DockerHub el nombre de la imagen oficial de Redis. Si se ha lanzado correctamente, debería mostrarse “Ready to accept connections” en la salida.
  ```.bash
  $ docker run redis
  o
  $ docker exec -it [id obtenido haciendo ps] /bin/bash
  ```
- Con el contenedor en ejecución, abrir una Shell y obtener la siguiente información:
  - Mostrar el contenido de la carpeta /etc. ¿Hay más o menos elementos que en la carpeta /etc de la imagen busybox?
    ```.bash
    $ sudo docker run redis ls /etc | wc -l
    71

    ```
    ```
    Hay más elementos en la carpeta /etc del contenedor redis que del contenedor busybox
    ```
  - Mostrar el cuántos binarios ejecutables hay en la carpeta /bin. ¿Hay más o menos elementos que en la carpeta /bin de busybox?
    ```.bash
    root@12db775f1f92:/data# ls /bin | wc -l
    277
    Si, hay menos elementos que en /bin de busybox
    ```
- Con el contenedor en ejecución, ejecutar el comando “redis-cli” dentro de él para acceder a una consola de Redis. Añadir una variable llamada miVar con valor 7. Salir de la consola Redis.
  ```.bash
  root@12db775f1f92:/data# redis-cli
  127.0.0.1:6379> set miVar 7
  OK
  127.0.0.1:6379> exit
  ```
- Sin parar el contenedor, volver a entrar en la consola Redis. Leer el contenido de la variable miVar.
  ```.bash
  root@12db775f1f92:/data# redis-cli
  127.0.0.1:6379> get miVar
  "7"
  ```
- Reiniciar el contenedor Redis y entrar a la consola. ¿Es posible leer el contenido de la variable miVar?
  ```
  No es posible, si reinicio no guarda el valor  Devuelve nil
  ```
- Para acceder al contenedor mientras estaba en marcha habrás utilizado el comando “docker exec”. ¿Qué diferencia hay al utilizar los siguientes parámetros?: -i, -t, -it, ninguno de ellos.
  ```
  -i (Interactivo)
    Permite interactuar con la terminal
  -t (Terminal)
      Muestra la terminal de forma legible
  -it (Interactivo y Terminal)
      Permite interactuar con la terminal y la muestra de forma legible
  ```
## 3. Imágenes Docker propias
En esta parte del laboratorio se plantean 3 tareas sobre crear imágenes propias utilizando ficheros Dockerfile.  

La primera tarea es crear una imagen propia que ejecute un servidor Redis. El resultado será una imagen equivalentea la imagen oficial de Redis utilizada en la sección anterior del laboratorio, pero hecha por nosotros.   

Seguir estos pasos:   
- Crear un directorio nuevo ennuestro sistema y crear un fichero Dockerfile dentro. El Dockerfile deberá tener las siguientes instrucciones:
  - Utilizar como imagen base “ubuntu”.
  - Instalar el paquete “redis” con apt.
  - Ejecutar como comando de arranque “redis-server”
- Utilizar los comandos de Docker para crear una imagen a partir del Dockerfile con el nombre usuario/redis-ubuntu donde usuario es un nombre que vosotros elijáis, p.e. ulopez para Unai Lopez.
- En el proceso de creación de la imagen, ¿cuántos contenedores intermedios se han creado? ¿Por qué?
- Lanzar un contenedor con la imagen recién creada y abrir una Shell mientras esté en ejecución. Utilizar los comandos vistos en la sección anterior para crear una variable “miOtraVar” con valor 8. Salir de la consola y de la Shell.
- Abrir una Shell de nuevo en el contenedor y verificar que la variable “miOtraVar” mantiene su valor.
La segunda tarea es crear una imagen de las mismas características que la anterior, pero utilizando “alpine” como imagen base en lugar de “ubuntu”:
- Crear un directorio nuevo en el sistema y crear un fichero Dockerfile dentro. El Dockerfile deberá tener las siguientes instrucciones:
  -  Utilizar como imagen base “alpine”.
  -  Instalar el paquete “redis” con el sistema de paquetes de Alpine (Alpine no utiliza apt).
  -  Ejecutar como comando de arranque “redis-server”.
- Utilizar los comandos de Docker para crear una imagen a partir del Dockerfile con el nombre usuario/redis-alpine donde usuario es el nombre elegido en la tarea anterior.
