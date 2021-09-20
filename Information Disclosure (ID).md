# Information Disclosure (ID)

![hackeri](https://user-images.githubusercontent.com/88755387/131217031-bb888540-c4f1-490e-83b9-cd1141dfdbed.jpg)

## __Tabla de contenidos__

- [__Información principal__](#Qué-es-Information-Disclosure?)
- [__Tipos de ID__](#Diferentes-tipos-de-Information-Disclosure)
  - [Banner grabbing](#Banner-grabbing)
  - [Source Code Disclosure](#Source-Code-Disclosure)
  - [Filename and File path disclosure](#Filename-and-File-path-disclosure)
  - [Inappropriate handling of sensitive data](#Inappropriate-handling-of-sensitive-data)
- [__NTLM__](#NTLM)
- [__Protección frente ID's__](Como-prevenir-los-ataques-de-Information-Disclosure)

# __Qué es Information Disclosure?__

> La divulgación de información se produce cuando una aplicación web no protege adecuadamente la información confidencial, lo que provoca que se revele información o datos sensibles de los usuarios o cualquier cosa relacionada con ellos a un tercero.

Este tipo de problemas no se pueden explotar en la mayoría de los casos, pero se consideran problemas de seguridad de aplicaciones web porque permiten a los atacantes recopilar información relevante que se puede utilizar más adelante para lograr un mayor impacto en el ataque.

# __Diferentes tipos de Information Disclosure__

## __Banner grabbing__

> El banner grabbing es un proceso de recopilación de información como el sistema operativo, los detalles del servidor, el nombre del servicio que se ejecuta con su número de versión y mucha información al respecto.

En la mayoría de los casos, no implica la filtración de información crítica, sino más bien información que puede ayudar al atacante a través de la fase de explotación del ataque. Por ejemplo, si el objetivo filtra la versión de PHP que se está ejecutando en el servidor y resulta ser vulnerable a un Remote Code Execution (RCE) porque no se actualizó, los atacantes pueden aprovechar la vulnerabilidad conocida y tomar el control total de la aplicación web.

### __Example__

Podemos ver que Netsparker identificó una versión antigua de PHP ejecutándose en el host de destino. También puede usar la pestaña HTTP Request/Response en Netsparker para ver la respuesta HTTP del servicio de destino en formato sin procesar, en el que también se resalta la versión anterior de PHP.

![http_response_php_version](https://user-images.githubusercontent.com/88755387/130510165-a1735cc3-e546-442c-acf6-44f215570733.png)

## __Source Code Disclosure__

> Esto ocurre cuando el código del entorno de back-end de una aplicación web se expone al público. Si se revelan archivos de código fuente, un atacante puede usar dicha información para descubrir fallas lógicas.

### __Ejemplo__

Los navegadores web saben cómo analizar la información que reciben del encabezado HTTP Content-Type que envía el servidor web en la respuesta HTTP. Por ejemplo, en la captura de pantalla que os proporciono a continuación, podemos ver que el encabezado Content-Type está configurado en text/html, por lo que el navegador analiza el HTML y muestra la salida.

![mime_type_http_headerpng](https://user-images.githubusercontent.com/88755387/130511061-bf802e87-2700-4a59-b10c-86c5a5a4350f.png)

Aunque si el servidor web está mal configurado y, por ejemplo, envía el encabezado Content-Type: text/plain en lugar de Content-Type: text/html al servir una página HTML, el código se mostrará como texto sin formato en el navegador, lo que permite al atacante para ver el código fuente de la página.

## __Filename and File path disclosure__

> Esto puede suceder debido a un manejo incorrecto de la entrada del usuario, excepciones en el back-end o una configuración inapropiada del servidor web. En ocasiones, dicha información se puede encontrar o identificar en las respuestas de las aplicaciones web, páginas de error, información de depuración, etc.

### __Ejemplo__

Una prueba simple que un atacante puede hacer para verificar si la aplicación web revela algún nombre de archivo o ruta es enviar una cantidad de solicitudes diferentes que él cree que el objetivo podría manejar de manera diferente. Por ejemplo, al enviar la solicitud a continuación, la aplicación web devuelve una respuesta 403 (Prohibido):

`https://www.example.com/%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd`

Pero cuando el atacante envía la siguiente secuencia, obtiene una respuesta 404 (No encontrado):

`https://www.example.com/%5C../%5C../%5C../%5C../%5C../%5C../etc/doesntexist`

Dado que para la primera solicitud el atacante obtuvo un error 403 y para la segunda obtuvo un 404, sabe que en el primer caso el archivo en cuestión existe. Por lo tanto, el atacante puede utilizar el comportamiento de la aplicación web a su favor, como un exploit para comprender cómo está estructurado el servidor y verificar si una determinada carpeta o archivo existe en el sistema.

## __Inappropriate handling of sensitive data__

> Esto puede suceder cuando los datos confidenciales no se eliminan del código fuente o de otro lugar. Algunos datos como el nombre de usuario, la contraseña o algún comentario importante pueden estar presentes allí, lo que puede revelar algunos datos confidenciales.

# __NTLM__

> De forma predeterminada, en el entorno de dominio de Windows, la autenticación utiliza el protocolo Kerberos para autenticar y autorizar a los usuarios. Si el protocolo Kerberos no se puede utilizar por algún motivo, se utiliza NT LAN Manager (NTLM) como reserva. Podemos deshabilitar este comportamiento estableciendo la propiedad `AllowNtlm` en `false`. 

Los problemas a tener en cuenta al permitir NTLM son los siguientes:

- Expone el nombre de usuario del cliente. Si es necesario mantener la confidencialidad del nombre de usuario tenemos que establecer la propiedad `AllowNTLM` del enlace en `false`.
- No proporciona autenticación de servidor. Por lo tanto, el cliente no puede asegurarse de que se está comunicando con el servicio correcto cuando se usa como protocolo de autenticación.

## __Especificar las credenciales del cliente o la identidad no válida obliga al uso de NTLM__

Al crear un cliente, especificar sus credenciales sin un nombre de dominio o especificar una identidad de servidor no válida, hace que se utilice NTLM en lugar del protocolo Kerberos (si la propiedad `AllowNtlm` está configurada en `true`). 

Debido a que NTLM no realiza la autenticación del servidor, la información puede potencialmente divulgarse.

Por ejemplo, es posible especificar las credenciales de cliente de Windows sin un nombre de dominio, como se muestra en el siguiente código de Visual C#.

```C#
MyChannelFactory.Credentials.Windows.ClientCredential = new System.Net NetworkCredential("username", "password");
```
El código no especifica un nombre de dominio y, por lo tanto, se utilizará NTLM.

Si se especifica el dominio, pero también se especifica un nombre principal de servicio no válido mediante la función de identidad de punto final, se utilizará NTLM.

# __Como prevenir los ataques de Information Disclosure__

> Los problemas de seguridad en la divulgación de información pueden parecer triviales, pero no lo son. Permiten que los hackers obtengan información detallada y confidencial sobre el objetivo que quieren atacar simplemente realizando pruebas básicas y, a veces, simplemente buscando información en páginas públicas.

Las siguientes son algunos pasos a seguir para poder asegurarse de que las aplicaciones web estén bien protegidas contra los problemas de divulgación de información más obvios:

- Asegurarse de que su servidor web no envíe encabezados de respuesta o información de fondo que revele detalles técnicos sobre el tipo, la versión o la configuración de la tecnología back-end.

- Los datos confidenciales, los archivos y cualquier otro elemento de información que no necesite estar en los servidores web nunca deben cargarse en el mismo.

- No codificar credenciales, claves API, direcciones IP o cualquier otra información confidencial en el código, incluidos nombres y apellidos, ni siquiera en forma de comentarios.

- Siempre verificar si cada una de las solicitudes para crear/editar/ver/eliminar recursos tiene controles de acceso adecuados, evitando problemas de escalada de privilegios y asegurándo que toda la información confidencial permanezca tal y como su nombre indica.

- Asegurarse siempre de que existan los controles de acceso y las autorizaciones adecuados para impedir el acceso de los atacantes a todos los servidores, servicios y aplicaciones web.

- Configurar el servidor web para no permitir `directory listing` y asegurarse de que la aplicación web siempre muestre una página web predeterminada.

- Utilizar mensajes de error genéricos tanto como sea posible. No proporcionar a los atacantes pistas sobre el comportamiento de las aplicaciones innecesariamente.

- Asegurarse de que todos los servicios que se ejecutan en los puertos abiertos del servidor no revelen información sobre sus compilaciones y versiones.

- Configurar los tipos MIME correctos en el servidor web para todos los diferentes archivos que se utilizan en las aplicaciones web.


# __EJERCICIOS PRACTICOS PortSwigger__

## __Divulgación de información en mensajes de error__

1. Con el Burp en marcha, abra una de las páginas de productos.
2. En Burp, vaya a “Proxy” > “HTTP history” y observe que la solicitud GET para las páginas de productos contiene un parámetro productID. Envíe la petición GET /product?productId=1 al Burp Repeater. Tenga en cuenta que el productId puede ser diferente dependiendo de la página de producto que haya cargado.
![](https://i.imgur.com/Jv5mxUN.png)

3. En el Burp Repeater, cambie el valor del parámetro productId a un tipo de datos no enteros, como una cadena. Envíe la solicitud.
GET /product?productId=“example”
![](https://i.imgur.com/OMaMgye.png)
4. El tipo de datos inesperado provoca una excepción, y se muestra un stack trace completo en la respuesta. Esto revela que el laboratorio está utilizando Apache Struts 2 2.3.31.
5. Vuelva al laboratorio, haga clic en “Submit solution”, e introduzca 2 2.3.31 para resolver el laboratorio.
![](https://i.imgur.com/yVgZsK1.png)

## __Divulgación de información en la página de depuración__

1. Con el Burp en marcha, navegue hasta la página de inicio.
2. Vaya a la pestaña “Target” > “Site map”. Haga clic con el botón derecho del ratón en la entrada de nivel superior del laboratorio y seleccione “Engagement tools” > “Find comments”. Observe que la página de inicio contiene un comentario HTML que contiene un enlace llamado “Debug”. Éste apunta a /cgi-bin/phpinfo.php.
![](https://i.imgur.com/CkkcDO2.png)
3. En el mapa del sitio, haga clic con el botón derecho del ratón en la entrada de /cgi-bin/phpinfo.php y seleccione “Send to Repeater”.
![](https://i.imgur.com/XdbRG2d.png)
4. En el Burp Repeater, envíe la solicitud para recuperar el archivo. Observe que se muestra diversa información de depuración, incluida la variable de entorno SECRET_KEY.
![](https://i.imgur.com/S6kpjkV.png)
5. Vuelva al laboratorio, haga clic en “Submit solution” e introduzca la SECRET_KEY para resolver el laboratorio.
![](https://i.imgur.com/wQ38PMI.png)

## __Divulgación del código fuente mediante archivos de copia de seguridad__

1. Navegue hasta /robots.txt y observe que revela la existencia de un directorio /backup. Vaya a /backup para encontrar el archivo ProductTemplate.java.bak. Como alternativa, haga clic con el botón derecho del ratón en el laboratorio del Site map y vaya a “Engagement tools” > “Discover content”. A continuación, inicie una sesión de descubrimiento de contenido para descubrir el directorio /backup y su contenido.

![](https://i.imgur.com/fjlRt3o.png)

![](https://i.imgur.com/HXLt0I6.png)

![](https://i.imgur.com/PUq2Z00.png)

2. Vaya a /backup/ProductTemplate.java.bak para acceder al código fuente.

![](https://i.imgur.com/p9DJm57.png)
3. En el código fuente, observe que el constructor de conexiones contiene la contraseña codificada para una base de datos Postgres.
4. Vuelva al laboratorio, haga clic en “Submit solution” e introduzca la contraseña de la base de datos para resolver el laboratorio.

![](https://i.imgur.com/A30BcEh.png)

## __Evasión de la autenticación mediante la divulgación de información__

1. En Burp Repeater, navegue hasta GET /admin. La respuesta revela que el panel de administración sólo es accesible si se ha iniciado sesión como administrador, o si se solicita desde una IP local.

![](https://i.imgur.com/cqU7MV4.png)

2. Vuelva a enviar la solicitud, pero esta vez utilice el método TRACE:
TRACE /admin

3. Estudia la respuesta. Observe que la cabecera X-Custom-IP-Authorization, que contiene su dirección IP, se ha añadido automáticamente a su solicitud. Esto se utiliza para determinar si la solicitud procede o no de la dirección IP del localhost.

![](https://i.imgur.com/LmHi0Xo.png)

4. Vaya a “Proxy” > “Options”, desplácese hasta la sección “Match and Replace” y haga clic en “Add”. Deje la condición de Match en blanco, pero en el campo “Replace”, introduzca X-Custom-IP-Authorization: 127.0.0.1. Ahora Burp Proxy añadirá esta cabecera a todas las solicitudes que envíe.

![](https://i.imgur.com/SgG1VS0.png)

5. Vaya a la página de inicio. Observe que ahora tiene acceso al panel de administración, donde puede eliminar a Carlos.

![](https://i.imgur.com/icCpD60.png)

## __Divulgación de información en el historial de control de versiones__

1. Abra el laboratorio y busque /.git para ver los datos del control de versiones Git del laboratorio.

![](https://i.imgur.com/LNk7cju.png)

2. Descargue una copia de este directorio completo. Para los usuarios que no sean de Windows, la forma más fácil de hacerlo es utilizando el comando wget -r https://your-lab-id.web-security-academy.net/.git. Los usuarios de Windows tendrán que encontrar un método alternativo, o instalar un entorno tipo UNIX, como Cygwin, para poder utilizar este comando.

3. Explora el directorio descargado utilizando tu instalación local de Git. Observe que hay un commit con el mensaje “Remove admin password from config”.

4. Mira más de cerca el diff para el archivo admin.conf cambiado. Observa que el commit ha sustituido la contraseña de administrador codificada por una variable de entorno ADMIN_PASSWORD. Sin embargo, la contraseña codificada sigue siendo claramente visible en el diff.

5. Vuelva al laboratorio y acceda a la cuenta de administrador utilizando la contraseña filtrada.

6. Para resolver el laboratorio, abre la interfaz de administración y elimina la cuenta de Carlos.





