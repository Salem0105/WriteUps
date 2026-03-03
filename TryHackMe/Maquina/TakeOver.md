
  THM : **TakeOver**<br>
  Difficulty : **Easy**<br>
  Room link : https://tryhackme.com/room/takeover<br>


Trata de una maquina sencilla, antes de comenzar nos cuentan un poco sobre lo que pasa:

> Hello there,  
> 
> I am the CEO and one of the co-founders of futurevera.thm. In
> Futurevera, we believe that the future is in space. We do a lot of
> space research and write blogs about it. We used to help students with
> space questions, but we are rebuilding our support.  
> 
> Recently blackhat hackers approached us saying they could takeover and
> are asking us for a big ransom. Please help us to find what they can
> takeover.      Our website is located at 
> [https://futurevera.thm](https://futurevera.thm/)

Lo primero que nos dicen es que están renovando su suporte y luego que encontremos eso que los hace vulnerables dentro de su web

Comenzamos con reconocimiento y numeración:

    sudo nmap -p- -sVC -sC --open -sS -vvv -n -Pn 10.81.174.59 -oN scan.txt
Un escaneo a todos los puertos, con versión de servicios, solo puertos abiertos, detectando vulnerabilidades comunes, sin hacer ping, sin realizar solución DNS y muy verboso 

    Nmap scan report for 10.81.174.59
    Host is up, received user-set (0.0066s latency).
    Scanned at 2026-03-03 16:41:08 GMT for 18s
    Not shown: 65532 closed ports
    Reason: 65532 resets
    PORT    STATE SERVICE  REASON         VERSION
    22/tcp  open  ssh      syn-ack ttl 64 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
    80/tcp  open  http     syn-ack ttl 64 Apache httpd 2.4.41 ((Ubuntu))
    | http-methods: 
    |_  Supported Methods: GET HEAD POST OPTIONS
    |_http-server-header: Apache/2.4.41 (Ubuntu)
    |_http-title: Did not follow redirect to https://futurevera.thm/
    443/tcp open  ssl/http syn-ack ttl 64 Apache httpd 2.4.41 ((Ubuntu))
    | http-methods: 
    |_  Supported Methods: POST OPTIONS HEAD GET
    |_http-server-header: Apache/2.4.41 (Ubuntu)
    |_http-title: FutureVera
    | ssl-cert: Subject: commonName=futurevera.thm/organizationName=Futurevera/stateOrProvinceName=Oregon/countryName=US/localityName=Portland/organizationalUnitName=Thm
    | Issuer: commonName=futurevera.thm/organizationName=Futurevera/stateOrProvinceName=Oregon/countryName=US/localityName=Portland/organizationalUnitName=Thm

Encontramos 3 puertos abiertos, pero en este caso solo nos interesa el 80 por la página web.
Vemos lo siguiente "http-title: Did not follow redirect to https://futurevera.thm/" donde nos indica que probablemente el servidor usa **Virtual Hosting (vhost)** y no responde correctamente por IP sola.
Se debería proceder a agregar la IP y el dominio a /etc/hosts.
   
    sudo nano /etc/hosts
> 10.81.174.59	futurevera.thm

Luego de eso procedemos a mirar la página web buscando dentro archivos comunes manualmente, por si se llega a encontrar algo, pero no fue el caso.
Mientras hacemos una búsqueda de sub-dominios

    gobuster dns -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --domain futurevera.thm --no-error

Donde obtenemos lo siguiente...

    Starting gobuster in DNS enumeration mode
    ===============================================================
    support.futurevera.thm ::ffff:10.81.174.59
    portal.futurevera.thm ::ffff:10.81.174.59
    Progress: 4989 / 4989 (100.00%)
    ===============================================================
    Finished

Agregamos esos sub-dominios a /etc/hosts.
Pero si te lo preguntas, sí, obviamente no fue el primer comando que usé para enumerar sub-dominos, también busqué directorios y archivos, estos son algunos comando que usé:

    ffuf -u https://futurevera.thm/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H 'Host: FUZZ.futurevera.thm' -c -fc 302
----------
    gobuster dir -u http://futurevera.thm -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt --no-error -s 200 -x .txt,.php,.html

Curiosamente el resultado no era el mismo si se usaba la IP en ves del dominio http, con ffuf es mejor usar la IP, en fin quizás sea solo yo, pero también se encontraba otros sub-dominios como payroll y blog, lo mejor será seguir con la búsqueda de la bandera, al visitar portal.futurevera.thm no encontramos nada relevante o de interés como sucedió con los otros 2, pero sí con **support.futurevera.thm**, no a simple vista, pero sí dentro del certificado que llegué a revisar luego de un tiempo dando vueltas y había una DNS más dentro de ese certificado... 

> DNS Name: secrethelpdesk934752.support.futurevera.thm

Fue claro lo que debía hacer, un edit al /etc/hosts y luego ingresar, así fue, ahí estaba la bandera.

<br>

**Conclusión**: 

 - Se supone que debía ser una máquina sencilla y fácil, sí, pero ¿como se supone que debía ser resuelta normalmente en 4 minutos?
 - Te enseña a rebuscar, te pones como loco a enumerar todo lo que sea posible, ¿buscando qué cosa?, ni idea pero sigues buscando algo que ni siquiera sabes si estará
