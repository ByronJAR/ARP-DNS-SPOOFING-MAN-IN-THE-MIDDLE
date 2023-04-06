# ARP-DNS SPOOFING (MAN IN THE MIDDLE)

![logo.png](ARP-DNS%20SPOOFING%20(MAN%20IN%20THE%20MIDDLE)%209903ed6f34284af080de079c1535ed03/logo.png)

![mitmasdasdasd.png](ARP-DNS%20SPOOFING%20(MAN%20IN%20THE%20MIDDLE)%209903ed6f34284af080de079c1535ed03/mitmasdasdasd.png)

# ¿Qué es un ataque ARP Spoofing?

Primeramente debemos de entender de que se trata el protocolo ARP. Resumidamente, la función de este protocolo consiste en establecer relaciones entre direcciones IP y direcciones MAC de los dispositivos conectados a la red. Mediante el siguiente enlace se puede entender la funcionalidad de este protocolo más a profundidad.

[Qué es el protocolo ARP y cómo funciona en redes IPv4](https://www.redeszone.net/tutoriales/internet/que-es-protocolo-arp/)

El ARP spoofing es una técnica de ataque utilizada en redes de computadoras, que implica falsificar las direcciones MAC en las respuestas ARP (Address Resolution Protocol) enviadas a una red. Esta técnica se utiliza comúnmente para interceptar el tráfico de red, redirigirlo a un atacante y, potencialmente, robar información confidencial.

Para realizar el ataque, utilizaremos Bettercap. Corresponde a un sniffer (Interceptor), en otras palabras nos permite visualizar el tráfico de los dispositivos de una red. Sin embargo para lograr esto ultimo, deberemos efectuar el famoso ataque llamado **Man in the middle.** Una de las técnicas para realizarlo es la llamada ARP-SPOOFING, la cual, como bien dice el titulo es la que estaremos utilizando.

Cabe recalcar que esta prueba de concepto fue realizada en el sistema operativo llamado Kali Linux.

# **ARP SPOOFING**

- Escanear dispositivos en la red
    
    ```bash
    net.probe on
    ```
    
- Visualizar la información mediante una tabla, esto muestra la información de una formas más amigable
    
    ```bash
    ticker on
    ```
    
    Esta tabla de puede observar en la siguiente imagen.
    
    ![2.PNG](ARP-DNS%20SPOOFING%20(MAN%20IN%20THE%20MIDDLE)%209903ed6f34284af080de079c1535ed03/2.png)
    

- En un ataque ARP spoofing hacia la gateway, el atacante envía paquetes ARP falsificados a los dispositivos de la red, haciéndoles creer que la dirección MAC del atacante es en realidad la dirección MAC de la gateway. De esta manera, todo el tráfico que se envía a través de la gateway pasa primero por el atacante, quien puede interceptarlo, modificarlo o redirigirlo según sus intereses.
    
    
    Para efectuar lo que se describe anteriormente, vamos a efectuar un ARP SPOOFING contra el gateway.
    
    ```bash
    set arp.spoof targets <ip-gateway>
    arp.spoof on
    set net.sniff.verbose false
    net.sniff on
    ```
    
    Una vez ejecutados dichos comandos empezaremos a interceptar todo el tráfico de la red LAN, incluso podemos interceptar credenciales que no viajen cifradas. Aquí se puede resaltar la importancia del protocolo HTTPS y el cifrado en general. Como el cifrado asimétrico por ejemplo.
    
    Para realizar la prueba de concepto utilizaremos la siguiente página web [http://testphp.vulnweb.com/login.php](http://testphp.vulnweb.com/login.php) , la cual corre mediante el protocolo HTTP, es decir, no utiliza SSL, por lo tanto los datos no viajan cifrados.
    
    Si nos intentamos loggear en la página mientras estamos sniffeando el tráfico en la red, bettercap captará las credenciales y nos las mostrará en pantalla.
    
    ![3.PNG](ARP-DNS%20SPOOFING%20(MAN%20IN%20THE%20MIDDLE)%209903ed6f34284af080de079c1535ed03/3.png)
    

# DNS SPOOFING

Si estas leyendo esto, deberías de saber que es un servidor DNS, sin embargo resumidamente, estos servidores nos permiten convertir nombres de dominio a direcciones IP. De este modo, es más fácil navegar en internet, ya que en lugar de recordar direcciones IP, únicamente debemos de recordar un nombre de dominio, por ejemplo www.facebook.com.

Haciendo uso de ARP-SPOOFING el atacante puede interceptar las solicitudes DNS enviadas por los dispositivos de la red y enviar respuestas DNS falsas para redirigir el tráfico a sitios web falsos. Debido a que todo el tráfico de la red está pasando por el atacante, los dispositivos de la red creen que las respuestas DNS falsas son legítimas y conectan al usuario al sitio falso.

Aprovechándonos del ARP-SPOOFING, efectuaremos este ataque. Gracias a Bettercap, la ejecución de este ataque es sumamente sencilla.

- Primeramente debemos efectuar el ataque ARP-SPOOFING, sin embargo, en lugar de realizarlo contra el gateway, lo realizaremos contra la máquina víctima.
    
    ```bash
    set arp.spoof targets <ip-victima>
    arp.spoof on
    ```
    
    Podemos comprobar si se realizó el ARP-SPOOFING correctamente visualizando la tabla ARP en nuestra máquina víctima. En mi caso la máquina víctima era Windows, por lo tanto para visualizar dicha tabla lo realicé de la siguiente manera.
    
    ```bash
    arp -a
    ```
    
    ![Screenshot 2023-04-06 162623.png](ARP-DNS%20SPOOFING%20(MAN%20IN%20THE%20MIDDLE)%209903ed6f34284af080de079c1535ed03/Screenshot_2023-04-06_162623.png)
    
    **Como podemos ver, logramos cambiar la tabla ARP de la PC víctima y poseemos la misma dirección MAC del Gateway.**
    
- Una vez ejecutados los comandos anteriores, realizaremos el DNS-SPOOFING, para ello ejecutaremos los siguientes comandos.
    
    Primeramente, inicié un servidor apache en mi máquina Kali. Para iniciar un servidor en apache basta con ejecutar el siguiente comando.
    
    ```bash
    service apache2 start
    ```
    
    Ahora realizaremos el DNS SPOOFING de la siguiente manera,
    
    ```bash
    set dns.spoof.domains <nombrededominio.com>
    set dns.spoof.address <ip-de-maquina-con-servidor-falso>
    dns.spoof on
    ```
    
    En mi caso realicé el DNS SPOOFING contra el nombre de dominio [ubuntu.com](http://ubuntu.com). A continuación se muestra que pasa si ingreso al nombre de dominio ubuntu.com en la máquina víctima.
    
    ![Screenshot 2023-04-06 163430.png](ARP-DNS%20SPOOFING%20(MAN%20IN%20THE%20MIDDLE)%209903ed6f34284af080de079c1535ed03/Screenshot_2023-04-06_163430.png)
