### Instalacion de paquetes

- Instalamos "bind9" y "dnsutils" con apt:

`sudo apt install bind9 dnsutils`

![image](https://github.com/R3TR0R0C4/Useful-Self-Hosted/assets/95719205/e2926197-5867-44ba-82be-a2619781f121)


### Configuraciónes
Hay varias formas de configurar bind9, puede ser como un servidor cache, servidor primario o servidor secundario:

- Como servidor cache, bind9 guardara la información de las consultas que se hacen a servidores externos (como 1.1.1.1, o 8.8.8.8).
- Como servidor primario, el servidor leerà los datos de una zona (Ej. 192.168.0.0/24) apartir de un archivo host y serà autoritario de dicha zona.
- Como servidor secundario, el servidor obtendrà los datos de zona desde otro servidor DNS que sea autoritario de dicha zona (un servidor primario).


#### Configuración como servidor cache
