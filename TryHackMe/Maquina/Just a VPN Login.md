
  THM : **Just a VPN Login**<br>
  Difficulty : **Easy**<br>
  Room link : https://tryhackme.com/room/justavpnlogin<br>

Se nos dice que hacemos parte del equipo de SOC, en nuestro primer turno encontramos con una alerta de que la señorita Susan inicio sesion en Singapur con la IP **37.19.201.132**, aunque también se nos informa de que efectivamente ella está en Singapur, pero no ha inciado sesion en la VPN de la empresa. <br>
Por otro lado mientras usaba una red Wi-Fi pública en una cafetería, de repente se le solicitó que instalara una herramienta de "verificación de seguridad", lo cual hizo. La telemetría del host revela un binario sospechoso con el hash  **b8e02f2bc0ffb42e8cf28e37a26d8d825f639079bf6d948f8debab6440ee5630**

Para esta tarea se nos pide que respondamos una serie de preguntas: <br><br>
¿Cuál es el número ASN relacionado con la IP?<br>
Para esto pasaremos la IP por algún analizador como VirusTotal, el cual nos arroja el resultado de **212238** (Datacamp Limited)<br><br>
¿Qué servicio se ofrece desde esta IP?<br>
VPN<br><br>
¿Cuál es el nombre del archivo relacionado con el hash?<br>
Ahora pasamos el hash que nos dieron por algun analizador Hash, y nos muestra que es un troyano llamado **zY9sqWs.exe**<br><br>
¿Cuál es la firma de amenaza que Microsoft asignó al archivo?<br>
Microsoft lo registró como **Trojan:Win32/LummaStealer.PM!MTB**<br><br>
Uno de los dominios contactados forma parte de un gran clúster de infraestructura maliciosa.
Según su certificado HTTPS, ¿cuántos dominios están vinculados a la misma campaña?<br>
Para este punto se usó el hash compartido, donde se encontró varias direcciones de dominio y una de ellas hacia comunicación con otros dominios más (gadgethgfub.icu), siendo un total de **151** dominios en relación
<br><br>
El archivo coincide con una de las reglas YARA creadas por "kevoreilly".
¿Qué línea aparece en el campo "condición" de la regla?<br>
Esto es sencillo, solo buscamos entre los documentos del repositorio Git de Kevo, entre todos los archivos YARA está Lumma.yar, la linea de código en ese campo es: "**uint16(0) == 0x5a4d and any of them**"<br><br>
El archivo también se menciona en un informe de inteligencia sobre amenazas.
¿Cuál es el título del informe que menciona este hash?<br>
Haciendo una busqueda tenemos un artículo titulado: **Behind the Curtain: How Lumma Affiliates Operate**<br><br>
¿Con qué equipo empezó a colaborar el autor del malware a principios de 2024?<br>
A inicios de 2024, los operadores de Lumma concretaron una de sus colaboraciones más significativas al asociarse con el equipo de **GhostSocks**<br><br>
Una filial con sede en México, relacionada con la misma familia de malware, también utiliza otros programas de robo de información.
¿Cuál de estos programas ataca a los sistemas Android?<br>
Entre los programas están Meduza Stealer y **CraxsRAT** para atacar dispositivos móviles<br><br>
El informe indica que los afiliados responsables del malware utilizan los servicios de AnonRDP. ¿Con qué subtécnica de Mitre ATT&CK se corresponde esto?<br>
Los servicios de AnonRDP se relacionan con la subtécnica de **Mitre ATT&CK T1583.003**
<br><br><br>

## Conclusion
Es importante hacer una busqueda de información de cada pista que tengamos a disposición o que vayamos encontrando cuando se trata de una alerta o posible amenaza de seguridad, recopilar información, clasificarla y relacionarla 
 