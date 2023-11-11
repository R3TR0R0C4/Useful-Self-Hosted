### Instalacion de paquetes

- Instalamos "bind9" y "dnsutils" con apt:

`sudo apt install bind9 dnsutils`

![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/e2926197-5867-44ba-82be-a2619781f121)


### Configuraciónes
Hay varias formas de configurar bind9, puede ser como un servidor cache, servidor primario o servidor secundario:

- [Como servidor cache](bind9install.md#configuración-como-servidor-cache), bind9 guardara la información de las consultas que se hacen a servidores externos (como 1.1.1.1, o 8.8.8.8).
- [Como servidor primario](bind9install.md#configuración-como-servidor-primario), el servidor leerà los datos de una zona (Ej. 192.168.0.0/24) apartir de un archivo host y serà autoritario de dicha zona.
- [Como servidor secundario](bind9install.md#configuración-como-servidor-secundario), el servidor obtendrà los datos de zona desde otro servidor DNS que sea autoritario de dicha zona (un servidor primario).


#### Configuración como servidor cache
Para configurar el servidor como dns cache, necesitaremos dns-forwarders, estos són los servidores que se consultaran para la primera vez que se consulte un dominio

* Para configurar el servicio necesitamos editar el archivo `/etc/bind/named.conf.options` con un editor de texto_

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/0c57181c-c063-4eb7-81c9-80a777864955)

  Y necesitaremos quitar los comentarios de las lineas de forwarders de abrir a cerrar:

  [Archivo named.conf.options](named.conf.options/named.conf.options)

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/b32316b9-dc60-405c-978f-5446b249e582)
<br>
  Para comprovar que funciona correctamente usaremos nslookup, podemos ver que si indicamos el servidor local quien nos responde és 192.168.0.31, podemos ver que nos responde correctamente:

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/8c750418-cd8c-4ab4-b212-5004b210cd34)

  Y si no le indicamos ningun servidor veremos el DNS predeterminado del sistema
  
  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/c103abd5-98e0-4187-9988-d36e814368c3)
<br>
<br>

#### Configuración como servidor primario

Para configurar un servidor DNS primario, necesitaremos establecer los forwarders también como en la [configuración anterior](bind9install.md#configuración-como-servidor-cache)

Necesitaremos crear una zona en el directorio, lo mas facil és usar el archivo `/etc/bind/db.empty` como plantilla no devemos borrar este archivo asi que usaremos la orden cp:

![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/74314735-6bb6-4a7d-b791-4913e2951cd3)


<br>
<br>
#### Configuración como servidor secundario
