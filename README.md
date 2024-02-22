# Máquina: Ice

**Tryhackme: Ice**

Lo primero que haremos, será lanzar un NMAP para ver qué puertos tiene abiertos la máquina:

![ICE1]()
![ICE2]()
![ICE3]()

En la imagen anterior podemos ver varios puertos abiertos, los más interesantes a primera vista podrían ser:

- Puerto 445: Servicio SMB
- Puerto 8000: Servidor Icecast streaming media server

A continuación, lanzaremos de nuevo NMAP pero cargando su módulo de detección de vulnerabilidades (vuln) para intentar encontrar alguna vulnerabilidad en los servicios anteriormente mencionados.

![ICE4]()
![ICE5]()

Como se observa en las imágenes anteriores, los servicios mencionados son vulnerables. El CVE de la vulnerabilidad localizada en el protocolo SMB, es el CVE-2017-0143 y el de la vulnerabilidad localizada en el programa Icecast streaming media server, es el CVE-2007-6750.

Ahora vamos a iniciar Metasploit para comenzar a explotar las distintas vulnerabilidades que hemos encontrado en el sistema objetivo.


