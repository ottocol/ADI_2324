
<!-- .slide: class="titulo" -->
# Tema 2
# Autenticación en APIs REST


---

## Seguridad en APIs REST

La seguridad es un tema complejo, aquí vamos a hablar únicamente de **autenticación**


<img class="r-stretch" src="img_3/seguridad.png">

---

## Contenidos

1. Sesiones
2. HTTP Basic
3. Tokens
4. OAuth

---

<!-- .slide: class="titulo" -->
# 1. Sesiones

---

<img class="r-stretch" src="img_3/xkcd_stateless.png">

* Aunque HTTP es originalmente un protocolo sin estado, la mayoría de aplicaciones web clásicas guardan estado en el servidor
* **Sesión**: conjunto de datos que "se recuerdan" mientras vamos navegando entre páginas (usuario autentificado, carro de la compra, ...)

---

## Cómo funcionan las sesiones

- Sin entrar en detalles, cada cliente envía automáticamente al servidor en cada petición una *cookie* con un valor único (que generó el servidor en la primera petición)
- Mediante ella podemos identificar al cliente, y guardar datos de él en el servidor (credenciales, carro de la compra, preferencias, ,...)

<img class="r-stretch" src="img_3/session.png">

---


## A favor de las sesiones

- Todos los lenguajes del lado del servidor ofrecen soporte
- En el lado del cliente no hay que hacer nada especial, al usar *cookies*, que están soportadas en todos los navegadores de forma automática

---



## En contra de las sesiones

"filosofía" **REST**: el API no debe recordar el estado, el estado actual se *transfiere* con cada petición

---

## En contra de las sesiones

**Escalabilidad**: es mucho más fácil escalar una app con servidores *stateless*. Al cliente no le importa qué instancia sirva las peticiones, podemos redirigirlas, arrancar nuevos servidores, parar los que ya hay, etc.

![](img_3/SimpleStateless.png)

---

<!-- .slide: class="titulo" -->
# 2. Autenticación con HTTP Basic

---

## HTTP Basic

- Mecanismo **estándar** de autenticación en HTTP
- Como HTTP **no tiene estado** hay que enviar las credenciales **en cada petición**. 
- Se envía **login** y **password** en Base64 (==¡sin cifrar!), separados por ":" dentro de la cabecera `Authorization`

```http
Authorization: Basic cGVwaXRvOjEyMzQ1Ng==
```


---

¿De verdad cada vez que se realice una operación protegida el usuario tiene que introducir login y password?

![](http://i0.kym-cdn.com/entries/icons/original/000/002/758/areyoufucking.jpg)

---

## HTTP Basic en una *app*. Qué ve el usuario final

- Cuando accede a una URL protegida en el servidor con HTTP Basic (la configuración depende del servidor) el navegador muestra automáticamente un cuadro de diálogo de login

![](img_3/ventana_login.png)

- En cada sucesiva petición a páginas protegidas **el navegador envía automáticamente login y password** en la cabecera `Authorization`


---


## Intento de acceso sin credenciales

Según la especificación HTTP cuando se intenta acceder a un recurso protegido sin `Authorization`, el servidor debería responder con un _status_ 401 y una cabecera `WWW-Authenticate`

```http
401 UNAUTHORIZED HTTP/1.1
...
WWW-Authenticate: Basic realm="nombre del realm"
```

---

Cuando el navegador recibe un `401` + cabecera `WWW-Authenticate` es cuando hace que "salte" el cuadro de diálogo de login

![](img_3/ventana_login.png)

Si no queremos que aparezca (formulario personalizado), habrá que "saltarse" el estándar (*status* distinto de 401 u obviar la cabecera `WWW-Authenticate`)

---


## Si queremos un formulario de login personalizado...

1. El usuario introduce *login* y *password* en un formulario, y se hace una llamada al API simplemente para **comprobar que son correctos** (el API debería ofrecer esta operación)
    - Si son OK, se **almacenan en el navegador** (típicamente con un API muy sencillo de usar llamado *Local Storage*)
    - Si son incorrectos se muestra error
2. Como las credenciales están almacenadas en el navegador, con Javascript podemos adjuntarlas en cada petición al API (*veremos ejemplos en la parte de programación del cliente*).

---

## A favor de HTTP Basic

- **Estándar** HTTP
- **Sencillo** de implementar
- Es **stateless**, no obliga a mantener estado en el servidor

---

## En contra de HTTP Basic

- Login y password se transmiten sin cifrar. Por tanto hay que usar **HTTPS**. Una mejora es **HTTP Digest**, que hace un _hash_ MD5 de los datos.
- Si la seguridad se ve comprometida hay que cambiar el *password*, lo que es tedioso para el usuario.

---

<!-- .slide: class="titulo" -->
# 3. Autenticación con *tokens*

---

## Tokens

1.  Cuando se hace *login* el servidor nos devuelve un **token** (valor idealmente único e imposible de falsear)
2.  Para cualquier operación restringida debemos **enviar el token en la petición**


<img class="r-stretch" src="img_3/SubwayToken.jpg">
<div class="caption">Un "token" en el mundo real</div>
---

## JSON Web Token (JWT)

*   [Estándar IETF](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). Hay implementación en multitud de lenguajes.
*   Es una cadena en formato JSON formada por 3 partes:
    1.  **Cabecera**: indica el tipo de token y el algoritmo de firma. Se codifica en Base64\. Ejemplo: `{"typ":"JWT", "alg":"HS256"}` (indica que esto es un "JWT" y se firmará con HMAC SHA-256)
    2.  **Payload**: lo que queremos almacenar en el token en formato JSON (p.ej. `{"login":"adi"}`) y codificado en Base64URL
    3.  **Firma**: se aplica un algoritmo de _hash_ sobre la cabecera, el payload y una clave secreta que solo conoce el servidor y se pasa a Base64URL
    4.  Las tres partes se concatenan con '.'

```bash
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJob2xhIjoibXVuZG8ifQ.pJPDprjxsouVfaaXau-Fyspj6rpKc7_hCui1RSaERAE    
```


---

## Comprobar si un JWT es auténtico

*   El servidor toma la cabecera y el _payload_ (recordar que no están cifrados, solo en Base64) y la clave secreta, y vuelve a aplicar el _hash_. Si no coincide con la firma, el token no es válido.
*   En teoría no se puede generar un token si no se conoce la clave secreta, y esta no se puede averiguar a partir de un token auténtico (el *hash* no es invertible)
*   Recordar que, **todo se transmite "en claro"**: Base64 es una codificación, no un cifrado. Por tanto normalmente habrá que usar HTTPS si no se quiere que el _payload_ sea legible

---

## Fecha de expiración

* En el _payload_ se suele incluir una fecha de expiración del _token_. En el estándar se especifica el uso de `exp` (con el nº segundos desde el 1/1/1970). Si el *token* ya ha expirado el servidor debería devolver el *status* 401
* De paso solucionamos el problema de que el mismo _payload_ siempre genera el mismo JWT si no cambiamos el _secret_ 

---

## Ejemplo de código

```javascript
var jwt = require('jwt-simple');
var moment = require('moment');  //para trabajar cómodamente con fechas

var payload = {
    login: 'pepito',
    exp: moment().add(7, 'days').valueOf()
}

var secret='123456';

//crear el JWT a partir de payload + secret
var token = jwt.encode(payload, secret);
console.log(token);

//validar el JWT. "decode" comprueba que sea válido y nos devuelve el payload
try {
  //si el token no fuera válido esto generaría una excepción
  var decoded = jwt.decode(token, secret);
  //si llegamos aquí, todo ha ido bien
  console.log("¡¡Token válido!!. Payload: " + decoded);
}
catch(error) {
  console.log(error)
}

//en realidad el payload se puede sacar decodificando Base64
//lo importante del método "decode" es que chequea la firma
//Con 'split' partimos el token por los ".", devuelve un array de Strings
//El [1] será el segundo fragmento 
var payload = token.split(".")[1]
console.log(Buffer.from(payload, "Base64").toString())

//si manipulamos un JWT e intentamos hacer un decode se lanzará una excepción
var tokenManipulado = token+"a"
try {
  jwt.decode(tokenManipulado, secret)
} catch (error) {
  console.log(error)
}
```

[https://replit.com/@ottocol/Prueba-de-JWT#index.js](https://replit.com/@ottocol/Prueba-de-JWT#index.js) <!-- .element class="caption"  -->

---

## "Flujo" de uso de JWT

1. El **cliente presenta** las credenciales (normalmente **login+password**) al servidor y a cambio obtiene un token JWT. En el estándar no se especifica en qué parte de la petición/respuesta colocar los datos (credenciales/token JWT). 
2. En **cada operación restringida hay que enviar el JWT**. Se debería hacer en la cabecera `Authorization`. Se pone la palabra clave `Bearer` seguida del JWT.

```http
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...
```

---

## Demo

<img src="https://i.pinimg.com/originals/fb/28/54/fb2854a6ab709a9b2f74d010584d1b3f.jpg" class="r-stretch">

<div></div>

[Proyecto *online* en Glitch.com](https://glitch.com/edit/#!/frosted-continuous-crane)

---

## Middleware en Express

<img src="img_3/pipeline.png" class="r-stretch">


---


## JWT en una app para el usuario final

1. El usuario introduce su *login* y *password* en un formulario, y se hace una llamada al servidor para **obtener un *token***. Es decir, el API debe implementar esta operación. 
   - Si es OK, el JWT se almacena en el navegador (típicamente en el *local storage*)
2. **Con cada llamada** "protegida" al API **se adjunta automáticamente el *token*** (con Javascript en el navegador, no lo hace el usuario)
  - Si el *token* no es correcto o ha expirado, el servidor devolverá `401`. En caso de expiración, si teníamos almacenado *login/password* la *app* podría pedir un nuevo JWT  por el usuario de manera transparente


---

## Disclaimer

¡Cuidado!: almacenar información sensible como *tokens* JWT en el "local storage" podría acarrear problemas de seguridad ya que otro código JS en la misma página tendría acceso a él (vulnerabilidad XSS o *Cross-Site Scripting*)

- [https://stormpath.com/blog/where-to-store-your-jwts-cookies-vs-html5-web-storage](https://stormpath.com/blog/where-to-store-your-jwts-cookies-vs-html5-web-storage)
- [https://stackoverflow.com/questions/44133536/is-it-safe-to-store-a-jwt-in-localstorage-with-reactjs](https://stackoverflow.com/questions/44133536/is-it-safe-to-store-a-jwt-in-localstorage-with-reactjs)


La solución más común es almacenar el JWT en *cookies* de tipo `httpOnly`, que no se pueden leer desde JS pero se envían automáticamente al servidor con cada petición como todas las *cookies*

**esto es un problema del lado del cliente, no del servidor**

---


## A favor de los *tokens*

- Pueden tener distintos ámbitos (*scopes*) (=="permisos"). Un *password* nos permite realizar cualquier operación para la que estemos autorizados
- En caso de ser comprometidos, es mucho menos engorroso invalidar un *token* que hacer que el usuario cambie su *password*
- Se pueden usar también en aplicaciones nativas (escritorio, móviles) (las sesiones por ejemplo usan *cookies* que son algo propio del navegador)

---

<!-- .slide: class="titulo" -->

# 4. OAuth


---

## ¿Qué es OAuth?

- Protocolo de autorización diseñado originalmente para cuando nuestra aplicación quiere acceder al API de un tercero pero el usuario no está dispuesto a confiarnos su *password*
- Solución: el usuario se autentifica directamente con el API del tercero y este nos cede un *token*, válido durante un tiempo. Si hay problemas es mucho más sencillo anular el *token* que cambiar el password

<img src="https://assets.northflank.com/image_20_e96a3e3754.png" class="r-stretch">

---

Aunque OAuth fue diseñado sobre todo para el caso de uso anterior, la versión actual contempla [más "casos de uso"](https://oauth2.thephpleague.com/authorization-server/which-grant/) (**grant types** en el argot), que sirven para cualquier cliente sea de terceros o no.

Como resultado, OAuth se ha convertido en el **estándar de autenticación *de facto*** en APIs REST de acceso público



---

## ¿OAuth vs. JWT?

En realidad no son comparables. OAuth es un *protocolo* que indica cómo se deben comunicar cliente/servidor en el proceso de autenticación, JWT es un *mecanismo* de autenticación. 

De hecho, en OAuth podemos usar *tokens* JWT ya que OAuth no especifica cómo generar los *token*.


---

## Ejemplo con el API de Instagram

- Ejemplo del denominado *Authorization Code Grant* según OAuth 2
- Como condición previa debemos tener una app dada de alta en Meta como desarrolladores. Dicha app tiene un id único.

<img src="img_3/secuencia_auth_instagram.png" class="r-stretch">


---

## Para saber más

- Más sobre OAuth
   - [*Mitchell Anicas*, An introduction to OAuth 2](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2)
   - [*Aaron Parecki*, OAuth 2 simplified](https://aaronparecki.com/articles/2012/07/29/1/oauth2-simplified)

- Sobre seguridad en APIs en general: [*API Security in Action*](https://learning.oreilly.com/library/view/api-security-in/9781617296024/), Neil Madden, ed, Manning 2021

<img src="https://learning.oreilly.com/covers/urn:orm:book:9781617296024/600w/" class="r-stretch">



---

<!-- .slide: class="titulo" -->
# ¿Preguntas...?
