# Practica 1 Swap
#### Realizado por Antonio Carlos Perea Parras 
#### DNI:45944310Q

## * Instalación Ubuntu Server
En la primera parte de esta práctica hemos procedido a instalarar **Ubuntu Server** en dos máquinas virtuales (En mi caso con Virtual Box). En esta instalación hay que prestar importancia marcar la opción de **LAMP** que nos instalará **Apache, PHP y MySQL**

En la segunda máquina la instalación fue mucho más sencilla ya que solamente se clonó la primera máquina y se le cambio la dirección ip.

Aqui podemos ver las dos máquinas perfectamente configuradas y arrancadas. En mi caso arriba de la ventana de cada máquina virtual podemos ver como **Ubuntu Server** es la máquina 1 y el **Ubuntu Server clon** es la máquina 2.

<img width="1440" alt="Captura de pantalla 2019-03-21 a las 16 56 06" src="https://user-images.githubusercontent.com/23367091/54766022-d5a86180-4bfa-11e9-8665-186a08f2cb63.png">

---
## * Funcionamiento de las máquinas y sus ip
Primero observaremos en la siguiente imagen como las dos máquinas tienen distintas ip. La primera máquina tiene como ip **192.168.1.100** mientras que la segunda máquina tiene la ip **192.168.1.101**.

<img width="1440" alt="Captura de pantalla 2019-03-21 a las 17 08 12" src="https://user-images.githubusercontent.com/23367091/54766844-5025b100-4bfc-11e9-8de6-a4584cedb780.png">

---

Para comprobar que las máquinas funcionan correctamente lo que haremos será crear un archivo **hola.html** en la máquina 1 dentro de la carpeta **/var/www/html** y lo pasaremos a la misma carpeta pero de la máquina 2. En esta imagen podemos apreciar como se crea el archivo y su contenido.

<img width="797" alt="Captura de pantalla 2019-03-21 a las 17 18 52" src="https://user-images.githubusercontent.com/23367091/54767573-b52dd680-4bfd-11e9-944a-63059fa20736.png">

<img width="797" alt="Captura de pantalla 2019-03-21 a las 17 20 34" src="https://user-images.githubusercontent.com/23367091/54767581-b828c700-4bfd-11e9-9a89-a6f4c4c4e8d8.png">

Ahora accederemos a la máquina 2 desde la primera máquina con el comando **ssh** y su ip para comprobar que la segunda máquina funciona(dejaré en la máquina 2 un archivo que se llame **máquina2.txt** para que ver que estamos en esa máquina)

<img width="868" alt="Captura de pantalla 2019-03-21 a las 18 04 46" src="https://user-images.githubusercontent.com/23367091/54770734-1fe21080-4c04-11e9-977e-913139f66124.png">


Y ahora con el comando **curl** copiaremos el archivo **hola.html** a la máquina 2.

<img width="868" alt="Captura de pantalla 2019-03-21 a las 18 18 32" src="https://user-images.githubusercontent.com/23367091/54771532-c7ac0e00-4c05-11e9-969c-fa90e7d1ad76.png">
