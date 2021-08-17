# Cross-Site Scripting (XSS)

En esta sección, explicaremos qué es el cross-site scripting, describiremos las diferentes variedades de vulnerabilidades del cross-site scripting y explicaremos cómo encontrar y prevenir el cross-site scripting.

## ¿Qué es cross-site scripting (XSS)?
El cross-site scripting (también conocido como XSS) es una vulnerabilidad de seguridad web que permite a un atacante comprometer las interacciones que los usuarios tienen con una aplicación vulnerable. Permite a un atacante eludir la same origin policy, que está diseñada para segregar diferentes sitios web entre sí. Las vulnerabilidades de cross-site scripting normalmente permiten que un atacante se haga pasar por un usuario víctima, lleve a cabo cualquier acción que el usuario pueda realizar y acceda a cualquiera de los datos del usuario. Si el usuario víctima tiene acceso privilegiado dentro de la aplicación, entonces el atacante podría obtener el control total sobre toda la funcionalidad y los datos de la aplicación.

## ¿Cómo funciona XSS?

El cross-site scripting funciona manipulando un sitio web vulnerable para que devuelva JavaScript malicioso a los usuarios. Cuando el código malicioso se ejecuta dentro del navegador de una víctima, el atacante puede comprometer completamente su interacción con la aplicación.

![](https://i.imgur.com/r9CVqPZ.png)


## ¿Cuáles son los tipos de ataques XSS?

Hay tres tipos principales de ataques XSS. Estos son:

* XSS reflejado, donde el script malicioso proviene de la solicitud HTTP actual.
* XSS almacenado, donde el script malicioso proviene de la base de datos del sitio web.
* XSS basado en DOM, donde la vulnerabilidad existe en el código del lado del cliente en lugar del código del lado del servidor.

## Cross-site scripting reflejado

XSS reflejado es la variedad más simple de cross-site scripting. Surge cuando una aplicación recibe datos en una solicitud HTTP e incluye esos datos dentro de la respuesta inmediata de una manera insegura.

Aquí hay un ejemplo simple de una vulnerabilidad XSS reflejada:

```xml
https://web-insegura.es/estado?mensaje=Todo+está+bien.
<p>Estado: Todo está bien.</p> 
```

La aplicación no realiza ningún otro procesamiento de los datos, por lo que un atacante puede construir fácilmente un ataque como este:

```xml
 https://web-insegura.es/estado?mensaje=<script>/*+Cosas+malas+aquí...+*/</script>

<p>Estado: <script>/* Cosas malas aquí... */</script></p> 
```

Si el usuario visita la URL construida por el atacante, entonces el script del atacante se ejecuta en el navegador del usuario, en el contexto de la sesión de ese usuario con la aplicación. En ese momento, el script puede realizar cualquier acción y recuperar los datos a los que el usuario tiene acceso.

## Cross-site scripting almacenado

El XSS almacenado (también conocido como XSS persistente o de segundo orden) surge cuando una aplicación recibe datos de una fuente que no es de confianza e incluye esos datos en sus respuestas HTTP posteriores de una manera insegura.

Los datos en cuestión pueden enviarse a la aplicación a través de solicitudes HTTP; por ejemplo, comentarios en una publicación de blog, apodos de usuario en una sala de chat o detalles de contacto en un pedido de un cliente. En otros casos, los datos pueden provenir de otras fuentes no confiables; por ejemplo, una aplicación de correo web que muestra mensajes recibidos a través de SMTP, una aplicación de marketing que muestra publicaciones en redes sociales o una aplicación de monitoreo de red que muestra datos en paquetes del tráfico de la red.

A continuación, se muestra un ejemplo sencillo de una vulnerabilidad XSS almacenada. Una aplicación de tablero de mensajes permite a los usuarios enviar mensajes, que se muestran a otros usuarios:

```xml
<p>¡Hola, este es mi mensaje!</p>
```

La aplicación no realiza ningún otro procesamiento de los datos, por lo que un atacante puede enviar fácilmente un mensaje que ataque a otros usuarios:

```xml
<p><script>/* Cosas malas aquí... */</script></p>
```

## Cross-site scripting basado en DOM

El XSS basado en DOM (también conocido como DOM XSS) surge cuando una aplicación contiene algo de JavaScript del lado del cliente que procesa datos de una fuente que no es de confianza de una manera insegura, generalmente escribiendo los datos en el DOM.

En el siguiente ejemplo, una aplicación usa algo de JavaScript para leer el valor de un campo de entrada y escribir ese valor en un elemento dentro del HTML:

```xml
var busqueda = document.getElementById('busqueda').value;
var resultados = document.getElementById('resultados');
resultados.innerHTML = 'Has buscado: ' + busqueda; 
```

Si el atacante puede controlar el valor del campo de entrada, puede construir fácilmente un valor malicioso que haga que se ejecute su propio script:
```xml
Usted buscó: <img src=1 onerror='/* Cosas malas aquí... */'>
```

En un caso típico, el campo de entrada se completaría a partir de parte de la solicitud HTTP, como un parámetro de cadena de consulta de URL, lo que permite al atacante lanzar un ataque utilizando una URL maliciosa, de la misma manera que el XSS reflejado.


## ¿Para qué se puede utilizar XSS?

Un atacante que explota una vulnerabilidad de cross-site scripting suele ser capaz de:

* Hacerse pasar por el usuario víctima.
* Llevar a cabo cualquier acción que el usuario pueda realizar.
* Leer cualquier dato al que el usuario pueda acceder.
* Capturar las credenciales de acceso del usuario.
* Realizar un defacement del sitio web.
* Inyectar la funcionalidad de un troyano en el sitio web.

## Impacto de las vulnerabilidades XSS

El impacto real de un ataque XSS generalmente depende de la naturaleza de la aplicación, su funcionalidad y datos, y el estado del usuario comprometido. Por ejemplo:

* En una aplicación de folletos, donde todos los usuarios son anónimos y toda la información es pública, el impacto suele ser mínimo.
* En una aplicación que contenga datos sensibles, como transacciones bancarias, correos electrónicos o registros sanitarios, el impacto será normalmente grave.
* Si el usuario comprometido tiene privilegios elevados dentro de la aplicación, entonces el impacto será generalmente crítico, permitiendo al atacante tomar el control total de la aplicación vulnerable y comprometer a todos los usuarios y sus datos.

## Cómo encontrar y comprobar las vulnerabilidades XSS

La gran mayoría de las vulnerabilidades XSS pueden encontrarse de forma rápida y fiable utilizando el escáner de vulnerabilidades web de Burp Suite.

Las pruebas manuales de XSS reflejado y almacenado normalmente implican el envío de alguna entrada simple y única (como una cadena alfanumérica corta) en cada punto de entrada de la aplicación, identificando cada ubicación en la que la entrada enviada es devuelta en las respuestas HTTP, y probando cada ubicación individualmente para determinar si la entrada adecuadamente elaborada puede ser utilizada para ejecutar JavaScript arbitrario. De este modo, se puede determinar el contexto en el que se produce el XSS y seleccionar un payload adecuado para explotarlo.

La comprobación manual del XSS basado en el DOM que surge de los parámetros de la URL implica un proceso similar: colocar alguna entrada simple y única en el parámetro, utilizar las herramientas de desarrollo del navegador para buscar esta entrada en el DOM y comprobar cada ubicación para determinar si es explotable. Sin embargo, otros tipos de DOM XSS son más difíciles de detectar. Para encontrar vulnerabilidades basadas en el DOM en entradas no basadas en URL (como document.cookie) o en sumideros no basados en HTML (como setTimeout), no hay nada que sustituya la revisión del código JavaScript, que puede llevar mucho tiempo. El escáner de vulnerabilidades web de Burp Suite combina el análisis estático y dinámico de JavaScript para automatizar de forma fiable la detección de vulnerabilidades basadas en el DOM.

## Content security policy

Content security policy (CSP) es un mecanismo del navegador que pretende mitigar el impacto de las cross-site scripting y algunas otras vulnerabilidades. Si una aplicación que emplea CSP contiene un comportamiento similar al XSS, entonces el CSP puede dificultar o impedir la explotación de la vulnerabilidad. A menudo, el CSP puede ser eludido para permitir la explotación de la vulnerabilidad subyacente.

## Cómo prevenir ataques XSS

Prevenir el cross-site scripting es trivial en algunos casos, pero puede ser mucho más difícil dependiendo de la complejidad de la aplicación y de las formas en que maneja los datos controlables por el usuario.

En general, la prevención eficaz de las vulnerabilidades XSS probablemente implique una combinación de las siguientes medidas:

* Filtrar la entrada a la llegada. En el punto en el que se recibe la entrada del usuario, filtrar lo más estrictamente posible basándose en lo que se espera o en la entrada válida.
* Codificar los datos en la salida. En el punto en el que los datos controlables por el usuario se emiten en las respuestas HTTP, codificar la salida para evitar que se interprete como contenido activo. Dependiendo del contexto de salida, esto podría requerir la aplicación de combinaciones de codificación HTML, URL, JavaScript y CSS.
* Utilizar las cabeceras de respuesta adecuadas. Para evitar el XSS en las respuestas HTTP que no están destinadas a contener HTML o JavaScript, puede utilizar las cabeceras Content-Type y X-Content-Type-Options para asegurarse de que los navegadores interpretan las respuestas de la forma que usted pretende.
* Content Security Policy. Como última línea de defensa, puede utilizar el Content Security Policy (CSP) para reducir la gravedad de cualquier vulnerabilidad XSS que aún se produzca.

## Preguntas frecuentes sobre cross-site scripting

¿Qué tan comunes son las vulnerabilidades XSS? Las vulnerabilidades XSS son muy comunes, y XSS es probablemente la vulnerabilidad de seguridad web que ocurre con más frecuencia.

¿Qué tan comunes son los ataques XSS? Es difícil obtener datos fiables sobre los ataques XSS del mundo real, pero probablemente se explote con menos frecuencia que otras vulnerabilidades.

¿Cuál es la diferencia entre XSS y CSRF? XSS implica hacer que un sitio web devuelva JavaScript malicioso, mientras que CSRF implica inducir a un usuario víctima a realizar acciones que no tiene la intención de realizar.

¿Cuál es la diferencia entre XSS e inyección SQL? XSS es una vulnerabilidad del lado del cliente que se dirige a otros usuarios de la aplicación, mientras que la inyección de SQL es una vulnerabilidad del lado del servidor que se dirige a la base de datos de la aplicación.

¿Cómo evito XSS en PHP? Filtre sus entradas con una lista blanca de caracteres permitidos y use sugerencias de tipo o conversión de tipo. Escape sus salidas con htmlentities y ENT_QUOTES para contextos HTML, o escape Unicode JavaScript para contextos JavaScript.

¿Cómo evito XSS en Java? Filtre sus entradas con una lista blanca de caracteres permitidos y use una biblioteca como Google Guava para codificar su salida HTML para contextos HTML, o use JavaScript Unicode escapes para contextos JavaScript.


## Reflected XSS into HTML context with nothing encoded

Laboratorio: XSS reflejado en un contexto HTML sin nada codificado

Este laboratorio contiene una simple vulnerabilidad reflejada de cross-site scripting en la funcionalidad de búsqueda.

Para resolver el laboratorio, realice un ataque de cross-site scripting que llame a la función de alerta.

1. Copia y pega lo siguiente en el cuadro de búsqueda:
```
<script>alert(1)</script>
```

2. Haz clic en “Buscar”.

## Reflected XSS into HTML context with most tags and attributes blocked

Laboratorio: XSS reflejado en el contexto HTML con la mayoría de las etiquetas y atributos bloqueados

Este laboratorio contiene una vulnerabilidad de cross-site scripting reflejada en la funcionalidad de búsqueda, pero utiliza un firewall de aplicaciones web (WAF) para protegerse contra vectores XSS comunes.

Para resolver el laboratorio, realice un ataque de cross-site scripting que eluda el WAF y alerte a ``document.cookie.``

1. Inyectar un payload XSS estándar como: `` <img src=1 onerror=alert(document.cookie)>``
En Javascript hay un concepto llamado evento que se utiliza para referirse al instante justo en el que ocurre un determinado suceso.
Los eventos se utilizan normalmente en combinación con funciones, y la función no se ejecutará antes de que se produzca el evento.
Explicación payload:

* evento onerror: Ejecuta un JavaScript si se produce un error al cargar una imagen
* función alert(): Mostrar un cuadro de alerta
* propiedad document.cookie: crear, leer y borrar cookies

2. Observe que este payload se bloquea. En los próximos pasos, utilizaremos Burp Intruder para comprobar qué etiquetas y atributos se bloquean.

    ![](https://i.imgur.com/8okyS11.png)
    
3. Con su navegador proxy de tráfico a través de Burp Suite, utilice la función de búsqueda en el laboratorio. Envíe la solicitud resultante a Burp Intruder.

4. En Burp Intruder, en la pestaña Positions, haga clic en “Clear §”. Sustituya el valor del término de búsqueda por ``<>``

5. Coloque el cursor entre los símbolos de menor que/mayor que y haga clic dos veces en “Add §”, para crear una posición de payload. El valor del término de búsqueda debe tener ahora el siguiente aspecto <§§>

    ![](https://i.imgur.com/dSaW5P0.png)

6. Visite la cheatsheet XSS y haga clic en “Copy tags to clipboard”.

7. En Burp Intruder, en la pestaña de payloads, haga clic en “Paste” para pegar la lista de etiquetas en la lista de payloads. Haga clic en “Start attack”.

8. Cuando el ataque haya terminado, revisa los resultados. Observa que todas los payloads causaron una respuesta HTTP 400, excepto el payload de body, que causó una respuesta 200. etiqueta HTML body: define el cuerpo del documento, contiene todo el contenido de un documento HTML

    ![](https://i.imgur.com/4oUumy5.png)

9. Vuelva a la pestaña Positions en Burp Intruder y sustituya el término de búsqueda por: 
``<body%20=1>``

10. Coloque el cursor antes del carácter = y haga clic dos veces en “Add §”, para crear una posición de payload. El valor del término de búsqueda debe tener ahora el siguiente aspecto ``<body%20§§=1>``

11. Visita la cheatsheet XSS y haz clic en “copy events to clipboard”.

    ![](https://i.imgur.com/XLo4Rq2.png)

12. En Burp Intruder, en la pestaña de payloads, haga clic en “Clear” para eliminar las payloads anteriores. A continuación, haga clic en “Paste” para pegar la lista de atributos en la lista de payloads. Haga clic en “Start attack”.

13. Cuando el ataque haya terminado, revisa los resultados. Observa que todas los payloads causaron una respuesta HTTP 400, excepto el payload onresize, que causó una respuesta 200.
* evento onresize: ocurre cuando se cambia el tamaño de la ventana del navegador

14. Ve al servidor del exploit y pega el siguiente código, sustituyendo ``your-lab-id`` por tu ID de laboratorio: ``<iframe src="https://your-lab-id.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=alert(document.cookie)%3E" onload=this.style.width='100px'>``

* elemento iframe: permite incrustrar otra página HTML en la página actual
* evento onload: Ejecuta un JavaScript inmediatamente después de que se haya cargado una página
* this: palabra clave de JavaScript que se refiere al objeto al que pertenece. Cuando se utiliza en controladores de eventos, se refiere al elemento HTML que recibió el evento
* style: propiedad de JavaScript para manipular el atributo style de un elemento HTML
* propiedad width: establece el ancho de un elemento.
* Resumen: Al cargar la página el iframe tomará un ancho de 100px, esto disparará el evento onresize que mostrará una alerta con las cookies de la víctima

    ![](https://i.imgur.com/hOS4P8l.png)
    
    ![](https://i.imgur.com/gwJS1tC.png)

15. Haga clic en “Store” y “Deliver exploit to victim”.

## Reflected XSS into HTML context with all tags blocked except custom ones

Laboratorio: XSS reflejado en el contexto HTML con todas las etiquetas bloqueadas excepto las personalizadas

Este laboratorio bloquea todas las etiquetas HTML excepto las personalizadas.

Para resolver el laboratorio, realiza un ataque de cross-site scripting que inyecte una etiqueta personalizada y alerte automáticamente document.cookie.

Empezar con Intruder para ver qué etiquetas se permiten como en el anterior ejercicio.

![](https://i.imgur.com/HNk0p9Y.png)

IMPORTANTE: Solo funciona en Chrome

``<script>
location = 'https://your-lab-id.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
</script> ``

* location: Variable global, palabra reservada de JavaScript. No se puede utilizar como variable, etiqueta o nombre de función. Hace referencia al objeto window.location. Para obtener la dirección de la página actual (URL) y redirigir el navegador a una nueva página
* window: Representa la ventana del navegador. Todos los objetos, funciones y variables globales de JavaScript se convierten automáticamente en miembros del objeto window. El objeto window es el espacio de nombres de trabajo por defecto
* evento onfocus: ejecuta un JavaScript cuando un campo de entrada toma el foco, es decir es cuando es seleccionado
* atributo tabindex: especifica el orden de tabulación de un elemento (cuando se utiliza la tecla “Tab” para navegar). Este atributo puede utilizarse en cualquier elemento HTML. 1 es el primero

2. Haga clic en “Store” y “Deliver exploit to victim”.

Esta inyección crea una etiqueta personalizada con el ID x, que contiene un controlador de eventos onfocus que activa la función de alert. El hash al final de la URL se enfoca en este elemento tan pronto como se carga la página, haciendo que se llame al payload del alert.

## Reflected XSS with event handlers and href attributes blocked

Laboratorio: XSS reflejado con manejadores de eventos y atributos href bloqueados

Este laboratorio contiene una vulnerabilidad XSS reflejada con algunas etiquetas en una lista blanca, pero todos los eventos y atributos anchor (ancla HTML, para insertar un enlace en un documento) href están bloqueados.

Para resolver el laboratorio, realice un ataque de cross-site scripting que inyecte un vector que, al hacer clic, llame a la función de alert.

Tenga en cuenta que debe etiquetar su vector con la palabra “Click” para inducir al usuario simulado del laboratorio a hacer clic en su vector. Por ejemplo:``<a href="">Click me</a>``

Intruder para sacar etiquetas permitidas.

![](https://i.imgur.com/wSIB3Rg.png)

``<svg><animate xlink:href=#xss attributeName=href values=javascript:alert(1) /><a id=xss>``
``<text x=20 y=20>XSS</text></a>``

``<svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a>``

* etiqueta svg: es un contenedor para gráficos SVG. SVG dispone de varios métodos para dibujar trazados, cajas, círculos, texto e imágenes gráficas.
* elemento animate de SVG: se utiliza para animar un atributo o propiedad a través del tiempo.
* xlink:href: para referirse a un elemento que desea animar.
* Si no se proporciona el atributo xlink:href, el elemento de destino será el elemento padre inmediato del elemento de animación actual.
* attributeName: especifica el atributo a animar.
* atributo href: especifica la URL de la página a la que va el enlace.
* javascript:: dispara una acción JavaScript utilizando el atributo href
* values: para especificar una lista de valores para la animación.
* elemento text: define un elemento gráfico que consiste en texto.
* atributo x: define una coordenada del eje x en el sistema de coordenadas del usuario.
* atributo y: define una coordenada del eje y en el sistema de coordenadas del usuario.
* Las posiciones se miden en píxeles desde la esquina superior izquierda.

![](https://i.imgur.com/D1ImNAB.png)

* etiqueta a: define un hipervínculo, que se utiliza para enlazar de una página a otra.
* 
Visite la siguiente URL, sustituyendo your-lab-id por su ID de laboratorio:
``https://your-lab-id.web-security-academy.net/?search=%3Csvg%3E%3Ca%3E%3Canimate+attributeName%3Dhref+values%3Djavascript%3Aalert(1)+%2F%3E%3Ctext+x%3D20+y%3D20%3EClick%20me%3C%2Ftext%3E%3C%2Fa%3E``

## Reflected XSS with some SVG markup allowed

Laboratorio: XSS reflejado con algunas etiquetas SVG permitidas

Este laboratorio tiene una simple vulnerabilidad XSS reflejada. El sitio bloquea las etiquetas comunes pero omite algunas etiquetas y eventos SVG.

Para resolver el laboratorio, realiza un ataque de cross-site scripting que llame a la función ``alert().``

![](https://i.imgur.com/vKhKSjh.png)

1. Inyectar un payload XSS estándar, como: ``<img src=1 onerror=alert(1)>``
2. Observe que este payload se bloquea. En los próximos pasos, utilizaremos Burp Intruder para comprobar qué etiquetas y atributos se bloquean.
3. Con su navegador pasando el tráfico por proxy a través de Burp Suite, utilice la función de búsqueda en el laboratorio. Envíe la solicitud resultante a Burp Intruder.
4. En Burp Intruder, en la pestaña Posiciones, haga clic en “Clear §”.
5. En la plantilla de solicitud, sustituya el valor del término de búsqueda por <>
6. Coloque el cursor entre los símbolos de menor que/mayor que y haga clic dos veces en “Add §” para crear una posición de payload. El valor del término de búsqueda debe ser ahora <§§>
7. Visite la cheatsheet XSS y haga clic en “Copy tags to clipboard”.
8. En Burp Intruder, en la pestaña de payloads, haga clic en “Paste” para pegar la lista de etiquetas en la lista de payloads. Haga clic en “Start attack”.
9. Cuando el ataque haya terminado, revisa los resultados. Observa que todos los payloads provocaron una respuesta HTTP 400, excepto los que utilizaban las etiquetas ``<svg>``, ``<animatetransform>``, ``<title>`` e ``<image>``, que recibieron una respuesta 200.

* animatetransform: anima un atributo de transformación en su elemento de destino.

![](https://i.imgur.com/5i7lpJH.png)


10. Vuelve a la pestaña Positions en Burp Intruder y sustituye el término de búsqueda por: ``<svg><animatetransform%20=1>``
11. Coloque el cursor antes del carácter = y haga clic dos veces en “Add §” para crear una posición de payload. El valor del término de búsqueda debe ser ahora: ``<svg><animatetransform%20§§=1>``

12. Visita la cheatsheet XSS, selecciona svg -> animatetransform y haz clic en “Copy events to clipboard”.

![](https://i.imgur.com/ewGsas6.png)


13. En Burp Intruder, en la pestaña de payloads, haga clic en “Borrar” para eliminar las payloads anteriores. A continuación, haga clic en “Paste” para pegar la lista de atributos en la lista de payloads. Haga clic en “Start attack”
14. Cuando el ataque haya terminado, revisa los resultados. Observa que todos los payloads causaron una respuesta HTTP 400, excepto el payload onbegin, que causó una respuesta 200.

![](https://i.imgur.com/L6slt0v.png)


* evento onbegin: Se dispara cuando la línea de tiempo comienza en un elemento.
``https://your-lab-id.web-security-academy.net/?search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E``

## Reflected XSS into attribute with angle brackets HTML-encoded

Laboratorio: XSS reflejado en el atributo con los símbolos de menor que/mayor que codificados en HTML

Este laboratorio contiene una vulnerabilidad de cross-site scripting reflejada en la funcionalidad de búsqueda del blog donde los símbolos de menor que/mayor que están codificados en HTML. Para resolver este laboratorio, realice un ataque de cross-site scripting que inyecte un atributo y llame a la función ``alert``.

1. Envíe una cadena alfanumérica aleatoria en el cuadro de búsqueda y, a continuación, utilice Burp Suite para interceptar la solicitud de búsqueda y enviarla a Burp Repeater.
2. Observe que la cadena aleatoria se ha reflejado dentro de un atributo entre comillas.
3. Reemplace su entrada con la siguiente carga útil para escapar del atributo citado e inyecte un controlador de eventos: ``"onmouseover="alert(1)``

* evento onmouseover: se produce cuando el puntero del ratón se mueve sobre un elemento, o sobre uno de sus hijos.

4. Comprueba que la técnica ha funcionado haciendo clic con el botón derecho del ratón, seleccionando “Copy URL” y pegando la URL en tu navegador. Al pasar el ratón por encima del elemento inyectado (input de búsqueda), debería activarse una alerta.

![](https://i.imgur.com/t2Gp20o.png)

## Stored XSS into anchor href attribute with double quotes HTML-encoded

Laboratorio: XSS almacenado en el atributo anchor ``href`` con comillas dobles codificadas en HTML

Este laboratorio contiene una vulnerabilidad de cross-site scripting almacenada en la funcionalidad de comentarios. Para resolver este laboratorio, envíe un comentario que llame a la función alert cuando se haga clic en el nombre del autor del comentario

1. Publica un comentario con una cadena alfanumérica aleatoria en la entrada “Website” y luego utiliza Burp Suite para interceptar la solicitud y enviarla al Burp Repeater.
2. Haga una segunda petición en el navegador para ver el post y utilice Burp Suite para interceptar la petición y enviarla al Burp Repeater.
3. Observe que la cadena aleatoria en la segunda pestaña del Repeater se ha reflejado dentro de un atributo anchor href.
4. Repita el proceso de nuevo pero esta vez sustituya su entrada por el siguiente payload para inyectar una URL de JavaScript que llame a alert: ``javascript:alert(1)``
5. Comprueba que la técnica ha funcionado haciendo clic con el botón derecho, seleccionando “Copy URL” y pegando la URL en tu navegador. Al hacer clic en el nombre de su comentario debería activarse una alerta.

![](https://i.imgur.com/xUJKZ13.png)

## Reflected XSS in canonical link tag

Laboratorio: XSS reflejado en la etiqueta de enlace canónico (link)

Este laboratorio refleja la entrada del usuario en una etiqueta de enlace canónico y escapa de los símbolos de menor que/mayor que.

Para resolver el laboratorio, realice un ataque de cross-site scripting en la página de inicio que inyecte un atributo que llame a la función alert.

Para ayudarte con tu hazaña, puedes suponer que el usuario simulado presionará las siguientes combinaciones de teclas:

* ALT+SHIFT+X
* CTRL+ALT+X
* Alt+X

Tenga en cuenta que la solución prevista para este laboratorio sólo es posible en Chrome.


``Takito``



# __bWAPP CASOS PRÁCTICOS CROSS-SITE SCRIPTING (XSS)__

El XSS  o Cross-site scripting es una vulnerabilidad que permite a un tercero inyectar código

Para la practica de del XSS Reflected y el XSS Stored he usado la maquina bWAPP

Reflected(GET,POST,JSON,AJAX/JSON)

Stored

# __bWAPP TIPOS DE XSS__

- Reflected XSS (Definición y caso práctico)

- Stored XSS (Definición y caso práctico)

- SelfXss (solo definición)

- mXss (solo definición)

- DOM XSS (solo definición)

 </br>
 
## __Reflected XSS__

### __XSS - Reflected (GET)__

En este caso estamos viendo un formulario el cual cuando lo completamos vemos que cambia la URL y nos da la bienvenida.

![](https://i.imgur.com/2BtaJ6x.png)

Al agregar codigo en el formulario vemos que la URL cambia y tambien la respuesta


```xml
/bWAPP/xss_get.php?firstname=<b>goncho</b>&lastname=goncho&form=submit
```
![](https://i.imgur.com/DJTWxM0.png)

También probamos a agregar un alert

```xml
<script>alert('GONCHO')</script>
```
![](https://i.imgur.com/7kVfwam.png)

</br>

### __XSS - Reflected (POST)__


Ahora vamos a probar a hacer un xss reflected en POST

![](https://i.imgur.com/21cOoCu.png)

y vemos que ha interpretado el codigo y vemos su respuesta

![](https://i.imgur.com/3tM494q.png)

inspeccionamos el codigo de la web 

![](https://i.imgur.com/hC0qyup.png)

Podemos utilizar las dos casillas del formulario para añadir codigo de esta forma 

![](https://i.imgur.com/XZf8aFg.png)

y asi seria su respuesta

![](https://i.imgur.com/Ic6TbGN.png)

el código de la web

![](https://i.imgur.com/LyVkkqX.png)

</BR>
</BR>


### __XSS - Reflected (JSON)__

Vamos a probar a buscar una película cualquiera

![](https://i.imgur.com/NM99ICP.png)

y esta es su respuesta

![](https://i.imgur.com/4zAkZHj.png)

Probamos a intentar inyectar código 
```xml
<script>alert('GONCHO')</script>
```
![](https://i.imgur.com/WpdJES2.png)

y al inspeccionar el código de la web nos damos cuenta que el script queda atrapado

![](https://i.imgur.com/e67iG32.png)

Por lo que podriamos colar un script de dos distintas formas

```xml
"}]}';alert('GONCHO')</script>
```

o cerrando por completo el script y abriendo otro distinto
```xml
"}]}';</script><script>alert('GONCHO')</script>
```
esta sería su respuesta

![](https://i.imgur.com/N0mtvyi.png)

También podriamos realizar un robo de cookies, de las dos formas que hemos visto anteriormente

```xml
"}]}';alert('GONCHO')</script>
```

```xml
"}]}';</script><script>alert(document.cookie)</script>
```
![](https://i.imgur.com/VMr27Ft.png)

### __XSS - Reflected (AJAX/JSON)__

Deberiamos colocar el siguiente código, el cual indica que si no hay una imagen llamada x, se ejecutará el alert debido al onerror

```xml
<img src=x onerror=alert('GONHO')>
```
![](https://i.imgur.com/arcuq4Y.png)


## __Stored XSS__

Vamos a practicar un ejemplo de XSS Stored

Cambiaremos el input tipo hidden, y lo convertiremos en tipo text

![](https://i.imgur.com/2tas2zK.png)

Por lo que se nos abrira una nueva casilla en el formulario

![](https://i.imgur.com/EazTuHq.png)


y agregaremos el siguiente codigo

```xml
"><img src=x onerror=alert (2)>
```
![](https://i.imgur.com/2hPMF4W.png)

y esta sería la respuesta

 </br>
 
![](https://i.imgur.com/zH4gJ8s.png)

 </br>
 </br>
 
 ## __DOM XSS__

El DOM XSS ocurre cuando el código malicioso se ejecuta dentro del Document Object Model SelfXss

 ## __Self XSS__

El DOM XSS ocurre cuando el código malicioso que escribes solo te afecta a ti y no puedes utilizarlo contra otros.

## __mXSS__
 El mXSS ocurre cuando el navegador interpreta código benigno y lo convierte en código malicioso.












