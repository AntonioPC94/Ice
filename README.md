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

A continuación, utilizaremos el módulo de post explotación "suggester" para ver qué exploits podemos aplicar sobre la aplicación vulnerable.

![ICE14]()

El único parámetro que tenemos que modificar en este módulo, es el de la sesión.

![ICE15]()

Una vez lo hayamos modificado, iniciaremos el módulo con "run".

Como observamos en la siguiente imagen, hemos encontrado 14 exploits que nos servirían para explotar la vulnerabilidad encontrada en la aplicación, pero en este caso, vamos a quedarnos con el segundo que nos aparece en la lista: exploit/windows/local/bypassuac_eventvwr

![ICE16]()

Ahora lo que vamos a hacer, es buscar dicho exploit y vamos a lanzárselo a la aplicación para ver si conseguimos elevar nuestros privilegios en el sistema objetivo.

![ICE17]()

Los dos parámetros que modificaremos, son: SESSION y payload.

![ICE18]()
![ICE19]()

Una vez lo hayamos modificado, iniciaremos el módulo con "run".

![ICE20]()

Nota: La shell que tenemos ahora, no nos hace ser autoridad máxima del sistema, pero veremos qué podemos hacer con ella próximamente.

Ahora que tenemos otra shell abierta en el sistema objetivo, vamos a upgradearla a una "Meterpreter" para poder ejecutar el comando "getprivs" y ver si el proceso en el que se ha creado la shell tenemos permisos suficientes para seguir escalando privilegios.

Para ello, usaremos el siguiente módulo de Metasploit:

![ICE21]()

Modificamos las opciones pertinentes y lo lanzamos. Una vez lanzado, se nos abrirá una segunda sesión con una "Meterpreter" creada.

![ICE22]()

Ahora tendremos 3 sesiones abiertas:

![ICE23]()
