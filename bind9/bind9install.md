## Instalacion de paquetes

- Instalamos "bind9" y "dnsutils" con apt:

`sudo apt install bind9 dnsutils`

![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/e2926197-5867-44ba-82be-a2619781f121)


## Configuraciónes
Hay varias formas de configurar bind9, puede ser como un servidor cache, servidor primario o servidor secundario:

- [Como servidor cache](bind9install.md#configuración-como-servidor-cache), bind9 guardara la información de las consultas que se hacen a servidores externos (como 1.1.1.1, o 8.8.8.8).
- [Como servidor primario](bind9install.md#configuración-como-servidor-primario), el servidor leerà los datos de una zona (Ej. 192.168.0.0/24) apartir de un archivo host y serà autoritario de dicha zona.
- [Como servidor secundario](bind9install.md#configuración-como-servidor-secundario), el servidor obtendrà los datos de zona desde otro servidor DNS que sea autoritario de dicha zona (un servidor primario).


### Configuración como servidor cache
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

---

### Configuración como servidor primario

#### Configuracion archivos de zona directa

  Para configurar un servidor DNS primario, necesitaremos establecer los forwarders también como en la [configuración anterior](bind9install.md#configuración-como-servidor-cache)

  Necesitaremos crear una zona en el directorio, lo mas facil és usar el archivo `/etc/bind/db.empty` como plantilla no debemos borrar este archivo asi que usaremos la orden cp:

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/2ffebbef-7021-40fd-bfaf-002681fee48d)

  Después editaremos el archivo con un editor de texto:

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/a69588d0-2d9e-471d-ac99-f4c4b55dc09c)

  Este és un ejemplo de configuración: [db.roca.local](configZonas/zonadb.roca.home)

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/c1c2c372-4f45-4496-8550-e43fa2d50c34)

  Necesitaremos cambiar `localhost.` por nuestro dominio, en mi caso `roca.home` (en el caso de configurar servidores secundarios, este campo suele rellenarse con ns1.dominio.tld) y luego el FQDN que pasarà de ser `root.localhost.` a `root.roca.home.`

  Y añadiremos una linea nueva por cada registro.

#### Configuracion de zonas

  Después necesitamos editar `/etc/bind/named.conf.local`

  Por cada zona nueva necesitaremos añadir un nuevo "bloque" de configuración, donde indicaremos el dominio y la ubicación del archivo de zona:
  
  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/9307d096-6b6e-41e3-9149-17d41554bfc5)

  Aqui podemos ver como responde a los subdominios de roca.home:

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/b04c6fa6-45ee-4914-85e1-bb3991f83217)
  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/2a70832e-35b1-4091-a1d2-06f3198f2f90)

<br>

#### Configuracion archivos de zona inversa

  Necesitaremos crear una zona inversa en el directorio, lo mas facil és usar el archivo `/etc/bind/db.127` como plantilla, no debemos borrar este archivo asi que usaremos la orden cp:

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/1087068d-aac6-422b-ab21-26d20c0c6bb1)

Esta és la configuración que usuaremos para la zona inversa, al igual que antes, necesitaremos cambiar `localhost.` por nuestro dominio, en mi caso `roca.home` (en el caso de configurar servidores secundarios, este campo suele rellenarse con ns1.dominio.tld) y luego el FQDN que pasarà de ser `root.localhost.` a `root.roca.home.`

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/d98adccc-b996-4183-9e80-3838977f921c)

  Despues declararemos los PTR (pointers), estos en el primer campo tendràn en el primer campo el ultimo octeto de la ip (31 en este caso) IN i PTR para indicar que és una entrada que apunta a un dominio, y despues el FQDN que vamos a visitar:
  
  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/e0da7173-038b-4831-95be-d638743394fd)

  Después editaremos el archivo `/etc/bind/named.conf.local` y añadimos el siguiente bloque de configuración, tenemos que cambiar "0.168.192." por la IP de nuestra red, y "/etc/bind/db.192.168.0" por el nombre del archivo de la zona inversa:

  ![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/99abb2fc-438b-4f16-9b15-eb1aedadddfa)

---

### Configuración como servidor secundario
