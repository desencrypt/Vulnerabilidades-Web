# IDOR (Insecure direct object reference)

## ¿Que es una IDOR? Ejemplos

Una IDOR es un tipo de vulnerabilidad en el Access Control, que aparece cuando en una aplicacion, el usuario puede acceder a sus objetos directamente.

* Vulnerabilidad IDOR con Direct Reference a objetos de una DB
 ``https://insecure-website.com/customer_account?customer_number=132355 ``
 
Si modificamos el parametro:

``customer_number``

El atacante podria ser capaz de hacer un privesc horizontal, alterando el customer_number, ya que se podria poner como admin.

* Vulnerabilidad IDOR con Direct Reference a un archivo estatico.
En este caso tendriamos la siguiente URL, con su respectiva URI:

``https://insecure-website.com/static/12144.txt``

Si fuzzeamos por el /static/ buscando .txt podriamos sacar otros .txt con credenciales de otros usuarios.

## Diferencias entre BAC y IDOR

Bien, como ya hemos visto antes, una IDOR es un tipo de vulnerabilidad en donde el usuario puede acceder a sus objetos directamente solo cambiando un valor en el parametro de la URL, mientras que en el BAC el usuario puede entrar en algun recurso, o hacer alguna accion la cual no deberia de ser capaz de hacerla.

## ¿Que es el Access Control?
El Access Control es la aplicacion que hace restricciones sobre quien realizar acciones o tener acceso a los recursos que han solicitado. En el contexto de las aplicaciones web, el Access Control, es dependiente de la autenticacion y la gestion de la sesion.

* Autenticacion: Identifica al usuario y confirma que el usuario es el que dice ser
* Gestion De La Sesion: Identifica cuales peticiones HTTP subsecuentes son hechas por ese mismo user
* Control De Acceso: Determina donde el usuario es permitido a hacer la accion que esta intentando hacer

El Broken Access Control es una vulnerabilidad muy comun y normalmente considerada critica. El diseño y gestion de los Access Controls, es algo muy complejo.

![](https://i.imgur.com/1Xyr3a5.png)

## Diferentes tipos de Access Controls

1. Vertical Access Control:
El Vertical Access Control, es un mecanismo el cual restringe el acceso a funcionalidades sensibles, no aptas para todo tipos de usuarios. Con estos Vertical Access Control, los diferentes tipos de usuarios tienen acceso a las diferentes funciones de la aplicacion. Por ejemplo, un administrador puede ser capaz de modificar o borrar cualquier cuenta de usuario.

2. Horizontal Access Control:
El Horizontal Access Control es un mecanismo que restringe la accesibilidad a funcionalidades o informacion de otros usuarios/roles. Por ejemplo, una aplicacion de banca permitiria a un usuario a ver sus propias transacciones, pero no la de otros usuarios.

3. Context-Dependent Access Control:
El Context-Dependent Access Control, controla el acceso restringido a una funcionalidad y a los recursos, basandose en el estado de la aplicacion, y de la interaccion del usuario con la pagina.

El Context-Dependent Access Control previene que un usuario haga acciones en un mal orden. Por ejemplo, en una pagina de compra, como Amazon, puede prevenir que los usuarios modifiquen el contenido de su carrito despues de que hayan hecho su pago.


# PortSwigger (Labs)

## 1. Funcionalidad de administración desprotegida (Unprotected admin functionality)

1. Vaya al laboratorio y vea el archivo robots.txt añadiendo /robots.txt a la URL del laboratorio. Observe que la línea Disallow revela la ruta al panel de administración.

![](https://i.imgur.com/5ildTfZ.png)

2. En la barra de URL, sustituye /robots.txt por /administrator-panel para cargar el panel de administración.

![](https://i.imgur.com/8ExbfWk.png)

3. Borra a carlos.

![](https://i.imgur.com/JoEI5AO.png)

## 2. Funcionalidad de administración desprotegida con una URL impredecible (Unprotected admin functionality with unpredictable URL)

1. Revise el código fuente de la página de inicio del laboratorio utilizando Burp Suite o las herramientas de desarrollo de su navegador web.
2. Observa que contiene algo de JavaScript que revela la URL del panel de administración.

![](https://i.imgur.com/96XukYJ.png)

3. Cargue el panel de administración y elimine a carlos.

![](https://i.imgur.com/PefJnpH.png)

## 3. Rol del usuario controlado por el parámetro de la petición(User role controlled by request parameter)

1. Navega a /admin y observa que no puedes acceder al panel de administración.

![](https://i.imgur.com/4rxqq3B.png)

2. Vaya a la página de acceso. En Burp Proxy, active la interceptación y habilite la interceptación de la respuesta.

![](https://i.imgur.com/d5jZjkh.png)

3. Complete y envíe la página de inicio de sesión, y reenvíe la solicitud resultante en Burp.
4. Observe que la respuesta establece la cookie Admin=false.

![](https://i.imgur.com/kC3B4FQ.png)

5. Cámbiela por Admin=true.

![](https://i.imgur.com/ghHgFOC.png)

6. Cargue el panel de administración y elimine a carlos.

![](https://i.imgur.com/L6M2TqH.png)

## 4. Rol del usuario puede ser modificado en el perfil del usuario (User role can be modified in user profile)

1. Inicie sesión con las credenciales suministradas y acceda a la página de su cuenta.

2. Utilice la función proporcionada para actualizar la dirección de correo electrónico asociada a su cuenta.

![](https://i.imgur.com/Fs61UPx.png)

3. Observe que la respuesta contiene su ID de rol.

![](https://i.imgur.com/M9Jvq7I.png)

4. Envíe la solicitud de envío de correo electrónico a Burp Repeater, añada “roleid”:2 en el JSON del cuerpo de la solicitud y vuelva a enviarla.

![](https://i.imgur.com/H4IeKIa.png)

![](https://i.imgur.com/FDQxkfr.png)

5. Observa que la respuesta muestra que tu roleid ha cambiado a 2.

6. Vaya a /admin y borre a Carlos.

![](https://i.imgur.com/8OU5Ozi.png)

## 5. El control de acceso basado en la URL puede ser burlado (URL-based access control can be circumvented)

1. Intenta cargar /admin y observa que se bloquea. Observa que la respuesta es muy simple, lo que sugiere que puede provenir de un sistema de front-end.
![](https://i.imgur.com/OMbyLRQ.png)

2. Envíe la solicitud a Burp Repeater. Cambie la URL en la línea de solicitud a / y añada la cabecera HTTP X-Original-URL: /invalid. Observe que la solicitud devuelve una respuesta “no encontrada”. Esto indica que el sistema back-end está procesando la URL de la cabecera X-Original-URL.

![](https://i.imgur.com/U4i1JZo.png)

![](https://i.imgur.com/xVHOCe1.png)

Algunas aplicaciones soportan cabeceras no estándar como X-Original-URL o X-Rewrite-URL para permitir anular la URL de destino en las peticiones con la especificada en el valor de la cabecera.
Este comportamiento puede ser aprovechado en una situación en la que la aplicación esté detrás de un componente que aplique una restricción de control de acceso basada en la URL de la solicitud.
El tipo de restricción de control de acceso basado en la URL de la petición puede ser, por ejemplo, bloquear el acceso desde Internet a una consola de administración expuesta en /console o /admin.

Para detectar el soporte de la cabecera X-Original-URL o X-Rewrite-URL, se pueden aplicar los siguientes pasos.

3. Cambie el valor de la cabecera X-Original-URL a /admin. Observe que ahora puede acceder a la página de administración.

![](https://i.imgur.com/ML5M7M8.png)

4. Para eliminar el usuario carlos, añada ?username=carlos a la cadena de consulta real, y cambie la ruta X-Original-URL a /admin/delete.

![](https://i.imgur.com/GV9GoEj.png)

## 6. El control de acceso basado en métodos puede ser burlado (Method-based access control can be circumvented)

1. Inicie sesión con las credenciales de administrador.

2. Vaya al panel de administración, promueva a carlos, y envíe la petición HTTP al Burp Repeater.
![](https://i.imgur.com/6mOn5nB.png)
3. Abra una ventana de navegador privada/de incógnito, e inicie sesión con las credenciales de no administrador.
![](https://i.imgur.com/3gO8sAP.png)

4. Intente volver a promocionar a carlos con el usuario no-admin copiando la cookie de sesión de ese usuario en la petición existente del Burp Repeater, y observe que la respuesta dice “Unauthorized”.
![](https://i.imgur.com/hKYDNSl.png)

5. Cambie el método de POST a POSTX y observe que la respuesta cambia a “missing parameter”.
![](https://i.imgur.com/R0RSct3.png)

6. Convierta la solicitud para utilizar el método GET haciendo clic con el botón derecho y seleccionando “Change request method”.

![](https://i.imgur.com/TUgRL85.png)

7. Cambie el parámetro de nombre de usuario por su nombre de usuario y vuelva a enviar la solicitud.
![](https://i.imgur.com/gWxIEBL.png)

## 7. ID de usuario controlado por el parámetro de la petición (User ID controlled by request parameter)

1. Inicie sesión con las credenciales suministradas y vaya a la página de su cuenta.
2. Tenga en cuenta que la URL contiene su nombre de usuario en el parámetro “id”.
3. Envíe la solicitud al Burp Repeater.
4. Cambie el parámetro “id” por carlos.
![](https://i.imgur.com/5avGJD6.png)

![](https://i.imgur.com/vVxOfsP.png)

5. Recupere y envíe la API key para carlos.
![](https://i.imgur.com/94wBWuz.png)

## 8. ID de usuario controlado por el parámetro de la petición, con ID de usuario impredecible (User ID controlled by request parameter, with unpredictable user IDs)

1. Busca una entrada del blog de carlos.

![](https://i.imgur.com/GB1jpqC.png)

2. Haz clic en carlos y observa que la URL contiene su ID de usuario.
3. Anota este ID.

![](https://i.imgur.com/sbGFu2F.png)

4. Inicie sesión con las credenciales suministradas y acceda a la página de su cuenta.
5. Cambia el parámetro “id” por el ID de usuario guardado.
![](https://i.imgur.com/anHy9QG.png)
![](https://i.imgur.com/bSLhwZ2.png)

6. Recupere y envíe la API key.

## 9. ID de usuario controlado por el parámetro de la petición con fuga de datos en la redirección  (User ID controlled by request parameter with data leakage in redirect)

1. Inicie sesión con las credenciales suministradas y acceda a la página de su cuenta. Envíe la solicitud al Burp Repeater.
2. Cambia el parámetro “id” por carlos.
![](https://i.imgur.com/exODgwI.png)

3. Observa que, aunque la respuesta te redirige ahora a la página de inicio, tiene un cuerpo que contiene la clave de API perteneciente a carlos.
![](https://i.imgur.com/Fqj7Fnc.png)

4. Envía la API key.
![](https://i.imgur.com/KzfRFDG.png)

## 10. ID de usuario controlado por el parámetro de la petición con revelación de contraseña (User ID controlled by request parameter with password disclosure)

1. Inicie sesión con las credenciales suministradas y acceda a la página de la cuenta de usuario.
2. Cambie el parámetro “id” en la URL por “administrator”.
![](https://i.imgur.com/ruUzblA.png)

![](https://i.imgur.com/2Cm4Miz.png)
3. Vea la respuesta en Burp y observe que contiene la contraseña del administrador.

![](https://i.imgur.com/t2Ijpb4.png)

4. Acceda a la cuenta de administrador y elimine a carlos.

![](https://i.imgur.com/3QeihFp.png)

## 11. Insecure direct object references

1. Seleccione la pestaña “Live chat”. Envíe un mensaje y luego seleccione “View transcript”.

![](https://i.imgur.com/vocupHh.png)

![](https://i.imgur.com/r5lzPaz.png)

2. Revisa la URL y observa que las transcripciones son archivos de texto a los que se les ha asignado un nombre de archivo que contiene un número creciente.
3. Cambie el nombre del archivo a 1.txt y revise el texto.
![](https://i.imgur.com/J5uhxPq.png)

4. Observa que hay una contraseña dentro de la transcripción del chat.
5. Vuelva a la página principal del laboratorio e inicie sesión con las credenciales robadas.

![](https://i.imgur.com/tl01aZh.png)

## 12. Proceso de varios pasos sin control de acceso en uno de ellos

1. Inicie sesión con las credenciales de administrador.
2. Vaya al panel de administración, promueva a carlos, y envíe la solicitud HTTP de confirmación al Burp Repeater.
![](https://i.imgur.com/OncLjUK.png)

3. Abra una ventana de navegador privada/de incógnito, e inicie sesión con las credenciales de no administrador.
4. Copie la cookie de sesión del usuario no-administrador en la solicitud existente del Repeater, cambie el nombre de usuario por el suyo, y repítalo.
![](https://i.imgur.com/3MkMpAN.png)
![](https://i.imgur.com/BBKFUoa.png)

## 13. Control de acceso basado en el Referer

Inicie sesión con las credenciales de administrador.
Vaya al panel de administración, promueva a Carlos, y envíe la petición HTTP al Repetidor Burp.
Abra una ventana de navegador privada/de incógnito, e inicie sesión con las credenciales de no administrador.
Navega a /admin-roles?username=carlos&action=upgrade y observa que la petición es tratada como no autorizada debido a la ausencia de la cabecera Referer.
Copie la cookie de sesión del usuario no-admin en la solicitud existente del Burp Repeater, cambie el nombre de usuario por el suyo y repítalo.





