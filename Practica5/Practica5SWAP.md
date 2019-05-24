# Práctica 5 SWAP 
#### Realizado por Antonio Carlos Perea Parras 
#### DNI:45944310Q

## * Contenidos de la Práctica
A la hora de hacer copias de seguridad de nuestras bases de datos (BD) MySQL, una opción muy común suele ser la de usar una réplica maestro-esclavo, de manera que nuestro servidor en producción hace de maestro y otro servidor de backup hace de esclavo. Tener una réplica en otro servidor también añade fiabilidad ante fallos totales del
sistema en producción, los cuales, tarde o temprano, ocurrirán. Por ejemplo, podemostener un pequeño servidor actuando como backup en nuestra oficina sincronizado mediante réplicas con nuestro sistema en producción.

Los objetivos concretos de esta práctica son:
* Copiar archivos de copia de seguridad mediante ssh.
* Clonar manualmente BD entre máquinas.
* Configurar la estructura maestro-esclavo entre dos máquinas para realizar el clonado automático de la información.

---

## * Crear un tar con ficheros locales y copiarlos en un equipo remoto
Ya vimos en la práctica 2 cómo crear un tar.gz con un directorio de un equipo y dejarlo en otro mediante ssh.

                tar czf - directorio | ssh equipodestino 'cat > ~/tar.tgz'

De esta forma en el equipo de destino tendremos creado el archivo tar.tgz

---

## * Crear una BD e insertar datos

Para la realización de la práctica debemos crearnos una BD en **MySQL** e insertar algunos datos.

<img width="755" alt="Captura de pantalla 2019-05-24 a las 15 44 04" src="https://user-images.githubusercontent.com/23367091/58332134-e5cb0000-7e3a-11e9-84f4-ca9c93b7ddc8.png">

Ya tenemos datos (un registro) insertados en nuestra BD llamada “datos”.
Ahora veremos como realizar consultas sobre la BD.

<img width="721" alt="Captura de pantalla 2019-05-24 a las 15 44 18" src="https://user-images.githubusercontent.com/23367091/58332261-1f037000-7e3b-11e9-833b-0f28aa7c7f3b.png">
<img width="673" alt="Captura de pantalla 2019-05-24 a las 15 44 24" src="https://user-images.githubusercontent.com/23367091/58332263-20349d00-7e3b-11e9-9d3c-1211d78fb74e.png">


---

## * Replicar una BD MySQL con mysqldump

**MySQL** ofrece la una herramienta para clonar las BD que tenemos en nuestra máquina. Esta herramienta es **mysqldump**; es parte de los programas de cliente de **MySQL**, que puede ser utilizado para generar copias de seguridad de BD.

Esta herramienta soporta una cantidad considerable de opciones. Ejecuta como root el siguiente comando:

                mysqldump --help


La sintaxis de uso es:

                mysqldump ejemplodb -u root -p > /root/ejemplodb.sql


Esto puede ser suficiente, pero tenemos que tener en cuenta que los datos pueden estar actualizándose constantemente en el servidor de BD principal. En este caso, antes de hacer la copia de seguridad en el archivo **.SQL** debemos evitar que se acceda a la BD para cambiar nada.
Así, en el servidor de BD principal (maquina1) hacemos: 

                mysql -u root –p
                mysql> FLUSH TABLES WITH READ LOCK;
                mysql> quit




Ahora ya sí podemos hacer el mysqldump para guardar los datos. En el servidor principal (maquina1) hacemos:

                mysqldump ejemplodb -u root -p > /tmp/ejemplodb.sql 
Como habíamos bloqueado las tablas, debemos desbloquearlas (quitar el “LOCK”):

                mysql -u root –p
                mysql> UNLOCK TABLES;
                mysql> quit
Ya podemos ir a la máquina esclavo (maquina2, secundaria) para copiar el archivo .SQL con todos los datos salvados desde la máquina principal (maquina1):

                scp maquina1:/tmp/ejemplodb.sql /tmp/


y habremos copiado desde la máquina principal (1) a la máquina secundaria (2) los datos que hay almacenados en la BD.

Con el archivo de copia de seguridad en el esclavo ya podemos importar la BD completa en el MySQL. Para ello, en un primer paso creamos la BD:

                mysql -u root –p
                mysql> CREATE DATABASE ‘ejemplodb’;
                mysql> quit
Y en un segundo paso restauramos los datos contenidos en la BD (se crearán las tablas en el proceso):

                mysql -u root -p ejemplodb < /tmp/ejemplodb.sql
Por supuesto, también podemos hacer la orden directamente usando un “pipe”a un ssh para exportar los datos al mismo tiempo (siempre y cuando en la máquina secundaria ya hubiéramos creado la BD):

                mysqldump ejemplodb -u root -p | ssh equipodestino mysql


## * Replicación de BD mediante una configuración maestro-esclavo

La opción anterior funciona perfectamente, pero es algo que realiza un operador a mano. Sin embargo, **MySQL** tiene la opción de configurar el **demonio** para hacer replicación de las BD sobre un esclavo a partir de los datos que almacena el maestro. Se trata de un **proceso automático** que resulta muy adecuado en un entorno de producción real. Implica realizar algunas configuraciones, tanto en el servidor principal como en el secundario.

A continuación se muestra el proceso de desarrollo de las **dos máquinas**:

* Lo primero que debemos hacer es la configuración de mysql del maestro. Para ello editamos, como root, el **/etc/mysql/my.cnf** (aunque según la versión de mysql puede que la configuración esté en el archivo **/etc/mysql/mysql.conf.d/mysqld.cnf**) para realizar las modificaciones que se describen a continuación. Comentamos el parámetro **bind-address** que sirve para que escuche a un servidor:

                #bind-address 127.0.0.1
Le indicamos el archivo donde almacenar el log de errores. De esta forma, si por ejemplo al reiniciar el servicio cometemos algún error en el archivo de configuración, en el archivo de log nos mostrará con detalle lo sucedido:

                log_error = /var/log/mysql/error.log
Establecemos el identificador del servidor.

                server-id = 1
El registro binario contiene toda la información que está disponible en el registro de actualizaciones, en un formato más eficiente y de una manera que es segura para las transacciones:

                log_bin = /var/log/mysql/bin.log
Guardamos el documento y reiniciamos el servicio:

                /etc/init.d/mysql restart

Si no nos ha dado ningún error la configuración del **maestro**, podemos pasar a la siguiente parte.

* Lo segundo sería la cofiguración de **mysql** en el **esclavo**; la configuración es similar a la del maestro, con la diferencia de que el server-id en esta ocasión será 2.

Reiniciamos el servicio en el esclavo:

                /etc/init.d/mysql restart


Una vez más, si no da ningún error, habremos tenido éxito. Podemos volver al maestro para crear un usuario y darle permisos de acceso para la replicación. Entramos en mysql y ejecutamos las siguientes sentencias:

                mysql> CREATE USER esclavo IDENTIFIED BY 'esclavo';
                mysql> GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%'
                IDENTIFIED BY 'esclavo';
                mysql> FLUSH PRIVILEGES;
                mysql> FLUSH TABLES;
                mysql> FLUSH TABLES WITH READ LOCK;
Para finalizar con la configuración en el maestro, obtenemos los datos de la BD que vamos a replicar para posteriormente usarlos en la configuración del esclavo:

                mysql> SHOW MASTER STATUS;

<img width="718" alt="Captura de pantalla 2019-05-24 a las 16 09 47" src="https://user-images.githubusercontent.com/23367091/58333738-60e1e580-7e3e-11e9-859a-d820031b71e4.png">


Volvemos a la **máquina esclava**, entramos en **mysql** y le damos los datos del **maestro**. Es importante destacar que sólo si trabajamos con versiones **inferiores** a mysql 5.5 estos datos se pueden introducir directamente en el archivo de configuración. Como lo más seguro es que estemos trabajando con una versión de mysql superior a la 5.5, entraremos en el entorno de **mysql** y ejecutamos la siguiente sentencia **(ojo con la IP, con el valor de "master_log_file" y del "master_log_pos" del maestro)**:

                mysql> CHANGE MASTER TO MASTER_HOST='192.168.31.200',
                MASTER_USER='esclavo', MASTER_PASSWORD='esclavo',
                MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=501,
                MASTER_PORT=3306;
Por último, arrancamos el esclavo y ya está todo listo para que los demonios de MySQL de las dos máquinas repliquen automáticamente los datos que se introduzcan/modifiquen/borren en el servidor maestro:

                mysql> START SLAVE;

Ahora, podemos hacer pruebas en el maestro y deberían replicarse en el esclavo automáticamente.

Por último, volvemos al maestro y volvemos a activar las tablas para que puedan meterse nuevos datos en el maestro:

                mysql> UNLOCK TABLES;

Si toda la configuración ha sido realizada de forma existosa, ahora cualquier modificación que se haga en el **maestro** se realizará de forma automática en el **esclavo** sin necesidad de ejecutar nada de forma manual.
