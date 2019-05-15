## Realizado por Antonio Carlos Perea Parras
## DNI: 45944310Q

<H1>Ejercicios Voluntarios</H1>

<H2>Tema 1</H2>
<b>1. Buscar información sobre las tareas o servicios web para los que se usan más los programas que comentamos al
principio de la sesión:</b>

- <b>Apache</b>: El servidor HTTP Apache es un servidor web HTTP de código abierto, para plataformas Unix (BSD, GNU/Linux, etc.), Microsoft Windows, Macintosh y otras, que implementa el protocolo HTTP/1.1

- <b>Nginx</b>: es un servidor web de código abierto que, desde su éxito inicial como servidor web, ahora también es usado como proxy inverso, cache de HTTP, y balanceador de carga.

- <b>Thttpd</b>: es un servidor web de código libre disponible para la mayoría de las variantes de Unix. Se caracteriza por ser simple, pequeño, portátil, rápido, y seguro, ya que utiliza los requerimientos mínimos de un servidor HTTP.

- <b>Cherokee</b>: es un servidor web multiplataforma.2​ Su objetivo es ser rápido y completamente funcional, sin dejar de ser liviano comparado con otros servidores web. Es muy útil a la hora de administrar múltiples servidores web.

- <b>Node.js</b>: es un entorno en tiempo de ejecución multiplataforma, de código abierto, para la capa del servidor (pero no limitándose a ello) basado en el lenguaje de programación ECMAScript, asíncrono, con I/O de datos en una arquitectura orientada a eventos y basado en el motor V8 de Google. Fue creado con el enfoque de ser útil en la creación de programas de red altamente escalables, como por ejemplo, servidores web.


<H2>Tema 2</H2>

<b>1. Calcular la disponibilidad del sistema si tenemos dos
réplicas de cada elemento (en total 3 elementos en cada
subsistema).</b>

Vamos a ponernos en el caso de que los componentes fuesen fallando de forma sucesiva uno tras otro.

Para estudiar este porcentaje con 1 única unidad de cada componente: 

        As = Ac1 * Ac2 * Ac3 * ... * Acn

La disponibilidad inicial equivale a:

        As = 85% * 90% * 99'9% * 98% * 85% * 99% * 99'99% * 95% = 59'86%

Ahora bien con componentes replicados, el valor de disponibilidad aproximada de éste sistema equivale a la siguiente ecuación:

        As = Ac1 + ( (1 - Ac1) * Ac2 )

Si replicamos el componente web la disponibilidad sería:

        As = 85% + ( (1 - 85%) * 85% ) = 97'75%

Con 3 elementos del subsistema: 

        COMPONENTE Web --> 99'6625% 
        App --> 99'9% 
        Database --> 99'999999% 
        DNS --> 99'9992% 
        Firewall --> 99'6625% 
        Switch --> 99'9999% 
        Data Center --> 99'999999% 
        ISP	--> 99'9875% 
        TOTAL --> 99'2135%

<b>2. Buscar frameworks y librerías para diferentes lenguajes que
permitan hacer aplicaciones altamente disponibles con
relativa facilidad. Como ejemplo, examina PM2 https://github.com/Unitech/pm2 que sirve para administrar clústeres de NodeJS.</b>

MySQL Router para balancear bases de datos MySQL

<b>3. ¿Cómo analizar el nivel de carga de cada uno de los
subsistemas en el servidor?
Buscar herramientas y aprender a usarlas.
...¡o recordar cómo usarlas!</b>

Ejecutando **top** en la terminal, nos muestra el uso de la memoria y procesador.

El comando linux **df** nos informa acerca del espacio total, ocupado y libre en nuestro sistema.

<b>4. Buscar ejemplos de balanceadores software y hardware
(productos comerciales).
Buscar productos comerciales para servidores de
aplicaciones.
Buscar productos comerciales para servidores de
almacenamiento.</b>

- Balanceador Software: Haproxy. 
- Balanceador Hardware: Local Director (Cisco), BigIP (F5) Servidor de almacenamiento: Balanceador de carga Barracuda.
- Balanceador de aplicaciones: LoadMaster de KEMP.

<H2>Tema 3</H2>

<b>1. Buscar con qué órdenes de terminal o herramientas
podemos configurar bajo Windows y bajo Linux el
enrutamiento del tráfico de un servidor para pasar el
tráfico desde una subred a otra.</b>

Con el comando **route** podemos configurar tanto en Linux como en Windows el enrutamiento del tráfico de un servidor para pasar el tráfico dessde una subred a otra.

<b>2. Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar bajo Windows y bajo Linux el filtrado y bloqueo de paquetes.</b>

- Windows: con el cortafuegos propio.
- Linux: con la configuración de nuestro cortafuegos con iptables.

<H2>Tema 4</H2>

<b>5. Probar las diferentes maneras de redirección HTTP.
¿Cuál es adecuada y cuál no lo es para hacer balanceo de
carga global? ¿Por qué?.</b>

301 y el 302: 301 – Moved Permanently: la página solicitada por el agente de usuario estará disponible de manera permanente bajo la URL redireccionada. La antigua URL será, por lo tanto, inválida. 

302 – Moved Temporarily: la página solicitada por el agente de usuario está temporalmente disponible bajo la nueva URL. 

A diferencia de la redirección 301, la antigua dirección sigue siendo válida.

A diferencia de la redirección 301, el código de estado 302 le comunica al crawler que la URL original debe seguir siendo indexada.

<H2>Tema 5</H2>

<b>1. Buscar información sobre cómo calcular el número de
conexiones por segundo.

        netstat -an | grep :80 | sort
        netstat | grep http | wc -l
        

Utilizando Apache, más especificamente con el comando:

        apache2ctl status | grep request

<b>3. Buscar información sobre características, funcionalidad,
disponibilidad para diversos SO, etc de herramientas para
monitorizar las prestaciones de un servidor.
Para empezar, podemos comenzar utilizando las clásicas de
Linux: top, vmstat, netstat</b>

- Netdata es una aplicación gratuita y de código abierto desarrollada para ofrecer a los usuarios un monitor en tiempo real del rendimiento de un sistema Linux, 
- iStat Menus esta aplicación concentra toda la información (incluso aquella que no da Monitor de Actividad, como la temperatura que los sensores de cada componente proporciona) en varios menús que se colocan en la barra superior de OS X.

<H2>Tema 6</H2>
<b>1.a) Aplicar con iptables una política de denegar todo el tráfico
en una de las máquinas de prácticas.
Comprobar el funcionamiento.</b>

        iptable -F
        iptables -P INPUT DROP
        iptables -P OUTPUT DROP
        iptables -P FORWARD DROP

<b>1.b) Aplicar con iptables una política de permitir todo el tráfico en una de las máquinas de prácticas.
Comprobar el funcionamiento.</b>

        iptables -F
        iptables -I INPUT -j ACCEPT

<b>3. Buscar información acerca de los tipos de ataques más
comunes en servidores web (p.ej. secuestros de sesión).
Detallar en qué consisten, y cómo se pueden evitar.</b>

- Ataques de inyección: más específicamente sqli (Structured Query Language Injection) es una técnica para modificar una cadena de consulta de base de datos mediante la inyección de código en la consulta.

- DDoS: La Denegación de Servicio consiste en congelar el funcionamiento de un sitio web. Estos son los intentos de inundar un sitio con solicitudes externas, por lo que ese sitio no podría estar disponible para los usuarios reales. Los ataques de denegación de servicio por lo general se dirigen a puertos específicos, rangos de IP o redes completas, pero se pueden dirigir a cualquier dispositivo o servicio conectado.

- Fuerza Bruta: intenta “romper” todas las combinaciones posibles de nombre de usuario + contraseña en una página web. Los ataques de fuerza bruta buscan contraseñas débiles para ser descifradas y tener acceso de forma facil.  

- Cross Site Scripting: inyectar scripts maliciosos en lo que serían sitios web inofensivos. Debido a que estos scripts parecen provenir de sitios web de confianza, el navegador de los usuarios finales casi siempre ejecuta la secuencia de comandos, la concesión de los piratas informáticos el acceso a la información contenida en las cookies o tokens de sesión utilizados con ese sitio. El XSS generalmente se utiliza para obtener acceso de un usuario de la cuenta.
