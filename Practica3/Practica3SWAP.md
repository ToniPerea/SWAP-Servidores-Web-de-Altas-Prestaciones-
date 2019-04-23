# Práctica 3 SWAP 
#### Realizado por Antonio Carlos Perea Parras 
#### DNI:45944310Q

## * Contenidos de la Práctica
En esta práctica configuraremos una red entre varias máquinas de forma que
tengamos un **balanceador** que reparta la carga entre varios servidores finales.
Para ello configuraremos nuestros servidores creados anteriormente.

Para la configuración de nuestro servidor se creará una tercera máquina virtual donde instalaremos nuestros softwares de balanceo.
En mi caso instalaré el **nginx**,  **haproxy** y **pound**.

___

## * NGINX
Para instalar el nginx en nuestra tercera máquina virtual lo único que tendremos que hacer será ejecutar sus comandos correspondientes de instalación en nuestra terminal.

**sudo apt-get install nginx**

Tras ello lo único que hará falta será iniciar nuestro **nginx**

**sudo systemctl start nginx**

## Configuración de Nginx
La configuración básica de nginx no nos vale tal cual está, ya que corresponde a una
funcionalidad de servidor web, así es que tenemos que modificar el fichero de
configuración **/etc/nginx/conf.d/default.conf**

* Primero definimos qué máquinas formarán el cluster web, esto se haría en la parte de **upstream**(y en qué puertos están a la
escucha el servidor web correspondiente, en nuestro caso, los servidores Apache). En esta primera sección es donde
vamos a indicar las IP de todos los servidores finales de nuestra granja web.

* Segundo, tras esa sección debemos indicarle al nginx que use ese grupo definido
antes **upstream** son las máquinas a las que debe repartir el tráfico. Se trata ahora
de la sección **server**. Es importante definir el **upstream** al principio del fichero de
configuración, fuera de sección **server**. 

* También es importante para que el
**proxy_pass** funcione correctamente que indiquemos que la conexión entre **nginx** y los
servidores finales sea **HTTP 1.1** así como especificarle que debe eliminar la cabecera
**Connection** (hacerla vacía) para evitar que se pase al servidor final la cabecera que
indica el usuario. Así pues, el fichero de configuración debe quedar con el contenido
siguiente:

<img width="912" alt="Captura de pantalla 2019-04-23 a las 16 08 21" src="https://user-images.githubusercontent.com/23367091/56587572-10c6f780-65e2-11e9-9de0-2c6f0ccdc98f.png">

Ahora nos faltaría simplemente arrancar **nginx** y comprobar su estado.

<img width="360" alt="Captura de pantalla 2019-04-23 a las 16 24 50" src="https://user-images.githubusercontent.com/23367091/56589205-0eb26800-65e5-11e9-8c48-1e86cf397a1d.png">

Para la comprobación del funcionamiento del software con enviar solicitudes desde otro terminal bastaria, como por ejemplo el comando **curl**. El problema es que para hacer capturas de esto no se apreciaría su funcionamiento de balanceo, asi que lo que haré es mandar muchas peticiones a la vez con el comando **ab**, este comnado viene con la instalación de Apache y sirve para comprobar le funcionamiento de los servidores.

<img width="1552" alt="Captura de pantalla 2019-04-23 a las 16 28 44" src="https://user-images.githubusercontent.com/23367091/56589114-e9bdf500-65e4-11e9-9d1e-0d1510084b12.png">

**En esta captura podemos observar por orden de izquierda a derecha:**

* **Un top sobre la máquina 1 para ver sus tareas.**
* **Un top sobre la máquina 2 para ver sus tareas.**
* **El estado de la máquina 3 (balanceador).**
* **La terminal de mi ordenador desde donde mando las tareas a la máquina 3.**



___

## * HAPROXY
**Haproxy** es un balanceador de carga y también proxy, de forma que puede balancear
cualquier tipo de tráfico.

Para instalar este software en nuestra máquina virtual tendremos que realizar la misma acción que con el **nginx** pero con el comando respectivo a este software.

**sudo apt-get install haproxy**

## Configuración de Haproxy

Una vez instalado, debemos modificar el archivo **/etc/haproxy/haproxy.cfg** ya que
la configuración que trae por defecto no nos vale. Así pues, tras consultar cuál es la IP
de nuestra máquina balanceadora, y anotar las IP de nuestras máquinas servidoras, editamos el
fichero de configuración de haproxy, de tal forma que quedaría así en mi caso: 

<img width="912" alt="Captura de pantalla 2019-04-23 a las 16 34 56" src="https://user-images.githubusercontent.com/23367091/56589543-c0ea2f80-65e5-11e9-8225-d72404afaa41.png">


Tras haber configurado nuestro servirdor **Haproxy** tenemos primero que parar nuestro servidor **nginx** usado en la prueba anterior con el comando:

**sudo systemctl stop nginx**


Y luego debemos arrancar nuestro software **haproxy** y comprobar su estado: 

<img width="360" alt="Captura de pantalla 2019-04-23 a las 16 36 23" src="https://user-images.githubusercontent.com/23367091/56589652-f68f1880-65e5-11e9-8681-bd375e33cddc.png">

Y para comprobar su funcionamiento realizaré la misma ejecución realizada con el **nginx**.

<img width="1552" alt="Captura de pantalla 2019-04-23 a las 16 40 37" src="https://user-images.githubusercontent.com/23367091/56589997-8e8d0200-65e6-11e9-9737-369a59837aed.png">

**En esta captura podemos observar por orden de izquierda a derecha:**

* **Un top sobre la máquina 1 para ver sus tareas.**
* **Un top sobre la máquina 2 para ver sus tareas.**
* **El estado de la máquina 3 (balanceador).**
* **La terminal de mi ordenador desde donde mando las tareas a la máquina 3.**


___

## * Para opcional: Pound
En mi caso he configurado el software **Pound** que es otra de las alternativas posibles para esta práctica.

## Configuración del Pound 
La configuración que se tiene que aplicar al **pound** se tiene que realizar en:

**/etc/pound/pound.cfg**

Y la configuración sería la siguiente:

<img width="798" alt="Captura de pantalla 2019-04-23 a las 16 47 11" src="https://user-images.githubusercontent.com/23367091/56590474-78cc0c80-65e7-11e9-9738-ca10d40d2120.png">
<img width="799" alt="Captura de pantalla 2019-04-23 a las 16 46 34" src="https://user-images.githubusercontent.com/23367091/56590512-87b2bf00-65e7-11e9-80e9-99add170b96b.png">
<img width="799" alt="Captura de pantalla 2019-04-23 a las 16 46 48" src="https://user-images.githubusercontent.com/23367091/56590513-897c8280-65e7-11e9-9388-745af14cba04.png">


Tras su instalación lo que quedaría sería arrancar el **pound** y comprobar su estado.

<img width="360" alt="Captura de pantalla 2019-04-23 a las 16 49 11" src="https://user-images.githubusercontent.com/23367091/56590647-baf54e00-65e7-11e9-94bc-17d5d3da9dc4.png">


Y por último ver su ejecución, en mi caso volvere a usar el comando **ab**.

<img width="1552" alt="Captura de pantalla 2019-04-23 a las 16 50 59" src="https://user-images.githubusercontent.com/23367091/56590831-00b21680-65e8-11e9-8b82-72999eb61606.png">

**En esta captura podemos observar por orden de izquierda a derecha:**

* **Un top sobre la máquina 1 para ver sus tareas.**
* **Un top sobre la máquina 2 para ver sus tareas.**
* **El estado de la máquina 3 (balanceador).**
* **La terminal de mi ordenador desde donde mando las tareas a la máquina 3.**

