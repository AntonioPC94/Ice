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

Una vez iniciado, buscaremos la vulnerabilidad encontrada en el servicio SMB y configuraremos las distintas opciones para posteriormente lanzar el exploit:

![ICE6]()

En este caso, solo configuraremos el RHOSTS y el payload:

![ICE7]()

Una vez configurado el exploit, lo lanzaremos con "run".

![ICE8]()

Como observamos en la imagen anterior, hemos obtenido shell. Ahora haremos un "whoami" para ver qué tipo de usuario somos en el sistema.

![ICE9]()

Somos "nt authority\system", la mayor autoridad del sistema.

A continuación, vamos a mandar la sesión que hemos obtenido a segundo plano para ver qué podemos hacer ahora con la aplicación vulnerable que encontramos anteriormente.

Primeramente, vamos a buscar en la página "cvedetails" información sobre la aplicación Icecast. Como observamos en la siguiente imagen, existe una vulnerabilidad cuyo CVE es CVE-2004-1561, que te permite realizar un desbordamiento de búfer en la máquina vulnerable.

![ICE10]()

Ahora que sabemos esto, nos iremos a Metasploit, buscaremos el CVE que obtuvimos anteriormente y seleccionaremos el único exploit que nos aparece.

![ICE11]()

En este caso, solo configuraremos el RHOSTS:

![ICE12]()

Una vez configurado el exploit, lo lanzaremos con "run".

![ICE13]()

Como observamos en la imagen anterior, hemos obtenido shell y hemos conseguido acceso como usuario "Dark-PC\\Dark".

Bien, ya tenemos acceso a la máquina mediante dos vías, pero ahora nos vamos a centrar en la segunda shell que hemos conseguido, ya que lo que queremos es llegar a ese "NT AUTHORITY SYSTEM" desde esta.

Si hacemos un "sysinfo", veremos que la arquitectura del sistema operativo que está corriendo en la máquina objetivo, es de 64 bits. 

Para ganar estabilidad en el sistema objetivo, vamos a migrar el proceso de la "Meterpreter" a uno de 64 bits. Para ello, utilizaremos el siguiente módulo de Metasploit: **post/windows/manage/archmigrate**

El único parámetro que tenemos que modificar en este módulo, es el de la sesión.

![ICE14]()

Una vez lo hayamos modificado, iniciaremos el módulo con "run".

![ICE15]()

Para comprobar que el proceso se ha migrado correctamente, nos iremos de nuevo a la sesión que tenemos abierta en la máquina objetivo y haremos un "sysinfo".

![ICE16]()

La migración se ha efectuado correctamente.

