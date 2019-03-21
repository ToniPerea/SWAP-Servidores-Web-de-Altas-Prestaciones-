# Practica 2 SWAP
#### Realizado por Antonio Carlos Perea Parras 
#### DNI:45944310Q

## * Contenidos de la Práctica
En la siguiente práctica nos centraremos en la copia de archivos mediante **ssh**, clonar contenido entre máquinas, configurar el ssh para acceder a la máquina remota sin necesidad de introducir la contraseña y establecer tareas en cron.

----
## * Crear un tar con ficheros locales en un equipo remoto
Primero crearé en la máquina 1 una carpeta **practica2** donde introducire un **documento.txt**. Aquí en la imagen podemos observar el proceso.

<img width="800" alt="Captura de pantalla 2019-03-21 a las 18 48 24" src="https://user-images.githubusercontent.com/23367091/54773467-ed3b1680-4c09-11e9-9d92-5b77a28f8440.png">

<img width="799" alt="Captura de pantalla 2019-03-21 a las 18 44 37" src="https://user-images.githubusercontent.com/23367091/54773242-74d45580-4c09-11e9-807c-659f3fe3fee6.png">

Después lo que haremos será comprimir el directorio entero con el documento y mandarlo mediante **ssh** a la máquina 2. Todo mediante un mismo comando:

**tar czf - directorio | ssh equipodestino 'cat > ~/tar.tgz'**

<img width="798" alt="Captura de pantalla 2019-03-21 a las 18 54 08" src="https://user-images.githubusercontent.com/23367091/54774275-a1896c80-4c0b-11e9-8ff7-ac58f7c78387.png">

<img width="799" alt="Captura de pantalla 2019-03-21 a las 18 54 23" src="https://user-images.githubusercontent.com/23367091/54774280-a2ba9980-4c0b-11e9-94d7-aec4d6e84431.png">


Como hemos comprobado el archivo **tar.tgz** se ha creado en la máquina2, ahora lo descomprimiremos y comprobaremos que es el mismo documento.

<img width="798" alt="Captura de pantalla 2019-03-21 a las 19 00 07" src="https://user-images.githubusercontent.com/23367091/54774282-a3ebc680-4c0b-11e9-8df0-dfae6dd7cfd9.png">

----

## * Instalacion de la herramienta rsync
Primero procederemos a instalar la herramienta **rsync** en nuestro servidor con el comando:

**sudo apt-get install rsync**

En mi caso como ya lo tengo instalado. En este caso, y como detalle previo, es hacer que el usuario sea el dueño de
la carpeta donde residen los archivos que hay en el espacio web (en ambas
máquinas): 

**sudo chown antoniperea:antonioperea -R /var/www**

Para probar su funcionamiento clonaremos la carpeta **/var/www/html** de la máquina 1 a la 2. En esta imagen observamos que no tienen el mismo contenido.

<img width="1440" alt="Captura de pantalla 2019-03-21 a las 19 29 31" src="https://user-images.githubusercontent.com/23367091/54776811-2a56d700-4c11-11e9-8b27-ab29c6393518.png">


Y tras usar el comando **rsync -avz -e ssh ipmaquina1:/var/www/ /var/www/** comprabamos que la copia se realizo con existo.

<img width="798" alt="Captura de pantalla 2019-03-21 a las 19 40 03" src="https://user-images.githubusercontent.com/23367091/54776815-2aef6d80-4c11-11e9-9e73-cac799ccebcb.png">

---

## * Acceso sin contraseña para ssh
Mediante ssh-keygen podemos generar la clave, con la opción -t para el tipo de clave.
Así, en la máquina secundaria ejecutaremos:
**ssh-keygen -b 4096 -t rsa**

<img width="799" alt="Captura de pantalla 2019-03-21 a las 19 49 28" src="https://user-images.githubusercontent.com/23367091/54777832-786cda00-4c13-11e9-8544-7b0acd8476dc.png">



A continuación deberemos copiar la clave pública al equipo remoto (máquina principal)
añadiéndola al fichero **~/.ssh/authorized_keys**, que deberá tener permisos 600 (por
defecto esto estará bien configurado; sólo si nos da algún error debemos hacerlo):
**chmod 600 ~/.ssh/authorized_keys**

La copia del documento lo haremos con el comnado:

**ssh-copy-id maquina1**

Y como podemos ver en la siguiente foto ahora podemos hacer ssh a la otra máquina sin necesidad de poner la contraseña.

<img width="797" alt="Captura de pantalla 2019-03-21 a las 19 56 38" src="https://user-images.githubusercontent.com/23367091/54777833-799e0700-4c13-11e9-8478-8c4c2f16dd2d.png">

----

## * Programar tareas con crontab
Cron es un administrador procesos en segundo plano que ejecuta procesos en el instante indicado en el fichero crontab.

Cron se ejecuta en **background** y revisa cada minuto la tabla del fichero **crontab**.

En mi caso este es mi **crontab** editado.

<img width="800" alt="Captura de pantalla 2019-03-21 a las 20 12 31" src="https://user-images.githubusercontent.com/23367091/54778694-aeab5900-4c15-11e9-9bb0-565d22d6760c.png">
