# Práctica 4 SWAP 
#### Realizado por Antonio Carlos Perea Parras 
#### DNI:45944310Q

## * Contenidos de la Práctica
El objetivo de esta práctica es llevar a cabo la configuración de seguridad de la granja
web. Para ello realizaremos las siguientes acciones:

* Instalar un certificado SSL para configurar el acceso HTTPS a los servidores.
* Configurar las reglas del cortafuegos para proteger la granja web.

---

## * Instalar un certificado SSL autofirmado para configurar el acceso por HTTPS
Un **certificado SSL** sirve para brindar seguridad al visitante de su página web, una
manera de decirles a sus clientes que el sitio es auténtico, real y confiable para
ingresar datos personales.

Existen diversas formas de obtener un **certificado SSL** e instalarlo en nuestro servidor
web para poder servir páginas mediante el protocolo HTTPS, para ello, lo principal es
conseguir un certificado que podremos conseguir de las siguientes formas:

* Mediante una autoridad de certificación.
* Crear nuestros propios certificados SSL auto-firmados usando la herramienta
openssl.
* Utilizar certificados del proyecto Certbot (antes Let’s Encrypt).

## Generar e instalar un certificado autofirmado
Para generar un **certificado SSL autofirmado** en Ubuntu Server solo debemos activar
el módulo **SSL de Apache**, generar los certificados y especificarle la ruta a los
certificados en la configuración. Así pues, como root ejecutaremos:        

        a2enmod ssl
        service apache2 restart
        mkdir /etc/apache2/ssl
        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout
        /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt


<img width="962" alt="Captura de pantalla 2019-05-08 a las 12 28 45" src="https://user-images.githubusercontent.com/23367091/57369097-13eae780-718d-11e9-8a9d-73324e14ffa1.png">



* Editamos el archivo de configuración del sitio default-ssl:

        nano /etc/apache2/sites-available/default-ssl

* Y agregamos estas lineas debajo de donde pone SSLEngine on:

        SSLCertificateFile /etc/apache2/ssl/apache.crt
        SSLCertificateKeyFile /etc/apache2/ssl/apache.key

* Activamos el sitio default-ssl y reiniciamos apache:

        a2ensite default-ssl
        service apache2 reload


    <img width="796" alt="Captura de pantalla 2019-05-08 a las 12 32 40" src="https://user-images.githubusercontent.com/23367091/57369469-e8b4c800-718d-11e9-8dcd-7790baff991e.png">


Una vez reiniciado **Apache**, accedemos al servidor web mediante el **protocolo HTTPS**
y veremos, si estamos accediendo con un navegador web, que en la barra de
dirección sale en rojo el https, ya que se trata de un certificado autofirmado.

Para hacer peticiones por HTTPS utilizando la herramienta curl, ejecutaremos:

        curl -k https://ipmaquina1/index.html

![Captura de pantalla 2019-05-08 a las 12 41 51](https://user-images.githubusercontent.com/23367091/57369759-b061b980-718e-11e9-971a-2f7369d31eec.png)


Por último, y como queremos que la **granja** nos permita usar el HTTPS, debemos
configurar el **balanceador** para que también acepte este tráfico (puerto 443). Para
hacer esto, copiaremos la pareja de archivos (el .crt y el .key) a todas las máquinas de
la granja web. No debemos generar más certificados, sino que los archivos apache.crt
y apache.key que generamos en el primer servidor en el paso anterior vamos a
copiarlos al otro servidor y al balanceador. Para copiarlos podemos usar scp o rsync.


<img width="797" alt="Captura de pantalla 2019-05-08 a las 12 46 10" src="https://user-images.githubusercontent.com/23367091/57369973-4a296680-718f-11e9-93de-c6779639da6a.png">


 ## **Para que todo esto funcione debemos recrear lo mismo que hemos realizado en la máquina1 pero en la máquina2 y reiniciar Apache tras ello.**

 Ahora ya podremos hacerle peticiones por HTTPS a la IP del balanceador.

![Captura de pantalla 2019-05-08 a las 12 51 30](https://user-images.githubusercontent.com/23367091/57370274-0b47e080-7190-11e9-8bd1-2a21a5d500b3.png)

---

## * Configuración del cortafuegos

Un cortafuegos es un componente esencial que protege la granja web de accesos
indebidos. Actúa como el guardián de la puerta al sistema web,
permitiendo el tráfico autorizado y denegando el resto.

En general, todos los paquetes TCP/IP que entren o salgan de la granja web deben
pasar por el cortafuegos, que debe examinar y bloquear aquellos que no cumplan los
criterios de seguridad establecidos.

## Configuración del cortafuegos iptables en Linux

**iptables** es una herramienta de cortafuegos, de espacio de usuario, con la que el
superusuario define reglas de filtrado de paquetes. Se basa en establecer una lista de reglas con las que definir qué acciones hacer con cada paquete en función de la información que incluye.

Para configurar adecuadamente **iptables** en una máquina Linux, conviene establecer
como reglas por defecto la denegación de todo el tráfico, salvo el que permitamos
después explícitamente.


## Uso de la aplicación iptables

Toda a información sobre la herramienta está disponible en su página de manual y
usando la opción de ayuda:

        man iptables
        iptables –h

**EJEMPLO DE man iptables**

<img width="802" alt="Captura de pantalla 2019-05-08 a las 13 55 22" src="https://user-images.githubusercontent.com/23367091/57373563-f6237f80-7198-11e9-811a-9f49ac7b6cbe.png">


Para comprobar el estado del cortafuegos, debemos ejecutar:

        iptables –L –n -v

Para lanzar, reiniciar o parar el cortafuegos, y para salvar las reglas establecidas hasta
ese momento, ejecutaremos respectivamente:

        service iptables start
        service iptables restart
        service iptables stop
        service iptables save


Lo habitual es crear un **script** que se ejecute en el arranque del sistema. Veamos a
continuación un ejemplo de **script** para la configuración básica de una máquina Linux:

<img width="799" alt="Captura de pantalla 2019-05-08 a las 13 58 19" src="https://user-images.githubusercontent.com/23367091/57373872-c3c65200-7199-11e9-9751-7b8a2414f46e.png">
<img width="797" alt="Captura de pantalla 2019-05-08 a las 13 58 44" src="https://user-images.githubusercontent.com/23367091/57373873-c4f77f00-7199-11e9-9f23-26121e572c2f.png">
