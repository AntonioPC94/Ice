# Máquina: Ice

**Tryhackme: Ice**

Lo primero que haremos, será lanzar un NMAP para ver qué puertos tiene abiertos la máquina:

![ICE1](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE1.png)
![ICE2](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE2.png)
![ICE3](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE3.png)

En la imagen anterior podemos ver varios puertos abiertos, los más interesantes a primera vista podrían ser:

- Puerto 445: Servicio SMB
- Puerto 8000: Servidor Icecast streaming media server

A continuación, lanzaremos de nuevo NMAP pero cargando su módulo de detección de vulnerabilidades (vuln) para intentar encontrar alguna vulnerabilidad en los servicios anteriormente mencionados.

![ICE4](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE4.png)
![ICE5](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE5.png)

Como se observa en las imágenes anteriores, los servicios mencionados son vulnerables. El CVE de la vulnerabilidad localizada en el protocolo SMB, es el CVE-2017-0143 y el de la vulnerabilidad localizada en el programa Icecast streaming media server, es el CVE-2007-6750.

Ahora vamos a iniciar Metasploit para comenzar a explotar las distintas vulnerabilidades que hemos encontrado en el sistema objetivo.

Una vez iniciado, buscaremos la vulnerabilidad encontrada en el servicio SMB y configuraremos las distintas opciones para posteriormente lanzar el exploit:

![ICE6](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE6.png)

En este caso, solo configuraremos el RHOSTS y el payload:

![ICE7](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE7.png)

Una vez configurado el exploit, lo lanzaremos con "run".

![ICE8](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE8.png)

Como observamos en la imagen anterior, hemos obtenido shell. Ahora haremos un "whoami" para ver qué tipo de usuario somos en el sistema.

![ICE9](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE9.png)

Somos "nt authority\system", la mayor autoridad del sistema.

A continuación, vamos a mandar la sesión que hemos obtenido a segundo plano para ver qué podemos hacer ahora con la aplicación vulnerable que encontramos anteriormente.

Primeramente, vamos a buscar en la página "cvedetails" información sobre la aplicación Icecast. Como observamos en la siguiente imagen, existe una vulnerabilidad cuyo CVE es CVE-2004-1561, que te permite realizar un desbordamiento de búfer en la máquina vulnerable.

![ICE10](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE10.png)

Ahora que sabemos esto, nos iremos a Metasploit, buscaremos el CVE que obtuvimos anteriormente y seleccionaremos el único exploit que nos aparece.

![ICE11](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE11.png)

En este caso, solo configuraremos el RHOSTS:

![ICE12](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE12.png)

Una vez configurado el exploit, lo lanzaremos con "run".

![ICE13](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE13.png)

Como observamos en la imagen anterior, hemos obtenido shell y hemos conseguido acceso como usuario "Dark-PC\\Dark".

Bien, ya tenemos acceso a la máquina mediante dos vías, pero ahora nos vamos a centrar en la segunda shell que hemos conseguido, ya que lo que queremos es llegar a ser "NT AUTHORITY SYSTEM" desde esta.

A continuación, utilizaremos el módulo de post explotación "suggester" para ver qué exploits podemos aplicar sobre la aplicación vulnerable.

![ICE14](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE14.png)

El único parámetro que tenemos que modificar en este módulo, es el de la sesión.

![ICE15](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE15.png)

Una vez lo hayamos modificado, iniciaremos el módulo con "run".

Como observamos en la siguiente imagen, hemos encontrado 14 exploits que nos servirían para explotar la vulnerabilidad encontrada en la aplicación, pero en este caso, vamos a quedarnos con el segundo que nos aparece en la lista: exploit/windows/local/bypassuac_eventvwr

![ICE16](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE16.png)

Ahora lo que vamos a hacer, es buscar dicho exploit y vamos a lanzárselo a la aplicación para ver si conseguimos elevar nuestros privilegios en el sistema objetivo.

![ICE17](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE17.png)

Los dos parámetros que modificaremos, serán: SESSION y payload.

![ICE18](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE18.png)
![ICE19](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE19.png)

Una vez los hayamos modificado, iniciaremos el módulo con "run".

![ICE20](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE20.png)

Nota: **Explicar una vez haya resuelto la duda que tengo.**

Ahora que tenemos otra shell abierta en el sistema objetivo, vamos a upgradearla a una "Meterpreter" para poder ejecutar el comando "getprivs" y ver si el proceso en el que se ha creado la shell tenemos permisos suficientes para seguir escalando privilegios.

Para ello, usaremos el siguiente módulo de Metasploit:

![ICE21](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE21.png)

Modificamos las opciones pertinentes y lo lanzamos. Una vez lanzado, se nos abrirá una segunda sesión con una "Meterpreter" creada.

![ICE22](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE22.png)

Ahora tendremos 3 sesiones abiertas (4 si tuviésemos abierta la del Eternal Blue, pero como hemos reiniciado la máquina, la hemos perdido):

![ICE23](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE23.png)

Ahora abriremos la nueva shell y ejecutaremos el comando "getprivs" para ver qué permisos tenemos en dicha shell:

![ICE24](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE24.png)

Como observamos en la imagen anterior, tenemos muchos permisos en la shell, pero el que más nos interesa, es el que se encuentra resaltado que se llama "SeTakeOwnershipPrivilege", ya que este nos permite tomar posesión de archivos u otros objetos del sistema (Por ejemplo, un proceso del sistema).

Bien, antes de pasar a la acción, debemos pasar a un proceso que realmente tenga los permisos que necesitamos para interactuar con el servicio lsass, el servicio responsable de la autenticación dentro de Windows. Primero, enumeraremos los procesos usando el comando "ps".

![ICE25](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE25.png)

Ahora vamos a buscar un proceso que tenga la misma arquitectura y los mismos permisos que el servicio lsass. Por ejemplo, el servicio de impresión nos serviría en este caso a la perfección.

![ICE26](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE26.png)

A continuación, vamos a migrar nuestra Meterpreter a dicho proceso utilizando el siguiente comando:

![ICE27](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE27.png)

Ahora, usaremos "getuid" para ver si hemos conseguido escalar privilegios de manera exitosa:

![ICE28](https://github.com/AntonioPC94/Ice/blob/b495337a9e7770cd03a4a4d080b19a85a1a6b770/img/ICE28.png)

Ahora somos NT AUTHORITY\SYSTEM, la mayor autoridad del sistema.
