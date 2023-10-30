# Práctica guiada: Javascript estándar en el cliente
# Estructura de los proyectos cliente y servidor

> IMPORTANTE: esta estructura **ya está creada** en la plantilla que hay en el moodle, **no hace falta hacer esto si usas la plantilla**, se incluye aquí solo por si quieres crearla de cero con tus propios proyectos

Vamos a ver aquí cómo se ha preparado la estructura de proyecto para la práctica. De momento tenemos el servidor del API REST de la lista de la compra que hicimos las primeras semanas. Añadiremos un proyecto para el cliente web de escritorio y modificaremos el servidor para que se pueda comunicar con el cliente.

## Creación del proyecto cliente. Instalación de Parcel

En el desarrollo usaremos un *bundler*, que es una herramienta que nos permitirá usar módulos ES6 aunque el navegador no los soporte. También podremos usar otras funcionalidades de JS todavía no implementadas en los navegadores actuales, ya que el *bundler* traducirá el código a la versión ES5 con la ayuda de un *transpilador* (normalmente Babel).

Además los *bundler* suelen ofrecer otras funcionalidades adicionales como importar CSS como si fueran módulos JS o minificar automáticamente el código para la versión de producción.

Actualmente el *bundler* más difundido es [Webpack](https://webpack.js.org), pero es conocido por necesitar una configuración "complicadilla", así que en nuestro caso usaremos [Parcel](https://parceljs.org) cuya configuración es especialmente sencilla.

Lo primero es inicializar el proyecto cliente e instalar Parcel en él:

```bash
mkdir client
cd client
npm init -y #creará un package.json con valores por defecto
npm i parcel
```
Además vamos a añadir al `package.json` un par de *scripts* para poder invocar luego a Parcel. La sección "scripts" quedaría como:

```javascript
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "devel": "parcel index.html",
    "build": "parcel build index.html"
  }
```

Una vez hecho esto podemos empezar a probar cómo importar módulos JS con `import` gracias a Parcel. Por ejemplo, este módulo JS

```javascript
//Archivo "saludador.js"
var saludos = ["¡Hola!", "Yiiiha!", "¿Cómo estás?", "Buenaaaas"]

function saludar() {
    var pos = Math.floor(Math.random()*saludos.length)
    return saludos[pos]
}

export {saludar}
```

Lo podemos usar en nuestro código con 

```javascript
//Archivo "main.js"
import {saludar} from './saludador.js'

document.addEventListener('DOMContentLoaded', function() {
    document.getElementById('mensaje').innerHTML = saludar()
})
```

Y ahora creamos el `index.html` que cargue el `main.js`

```html
<html>
<body>
  <h1 id="mensaje"></h1>
  <script src="./main.js" type="module"></script>
</body>
</html>
```

Para probarlo podemos hacer uso del servidor de desarrollo de Parcel, gracias a los scripts que antes hemos añadido al `package.json` ahora podemos hacer:

```bash
npm run devel
```

Esto abrirá un servidor web de desarrollo por el puerto 1234. Parcel incorpora *Hot Module Reloading*, lo que quiere decir (entre otras cosas) que si cambiamos cualquier módulo el efecto se verá inmediatamente sin necesidad de recargar la página.

En versiones anteriores a la 2 Parcel aplicaba automáticamente Babel para transpilar el código a versiones compatibles con navegadores antiguos. Si queremos soportar explícitamente navegadores antiguos que pueden requerir la transpilación podemos añadir el campo `browserslist` al `package.json`:

```javascript
"browserslist": [
    ">1%, ie 11"
]
```

Con esto le decimos a Parcel que considere como objetivo los navegadores con un *market share*>1% y también Internet Explorer 11.

> Hay otras formas de especificar los navegadores que usaremos como *target*, podemos indicar navegadores concretos, o decir que nos interesan las n últimas versiones, etc. Tenemos más información en el [proyecto Browserslist](https://github.com/browserslist/browserslist) que es en realidad independiente de Parcel


## CORS

Como hemos visto, tenemos dos servidores en funcionamiento:

- El servidor del API, que corre en el puerto 3000.
- El servidor del *frontend* que vais a implementar en esta práctica. Usaremos de momento un servidor de desarrollo que correrá por el puerto 1234.

De momento el servidor del *frontend* es de desarrollo, pero en producción también es útil que sea un servidor separado del API. Así podremos tener un servidor del *frontend* de escritorio, otro del *frontend* móvil, ... Todos los servidores de *frontends* harán peticiones REST al mismo servidor del API.

![](imag_js_estandar/estructura.png)

Pero hay un problema: un código Javascript que resida en un servidor solo puede hacer peticiones al mismo servidor con el mismo puerto, y por tanto un `.js` que nos descarguemos desde `http://localhost:1234` no puede hacer peticiones a `http://localhost:3000`. Esto es un mecanismo de seguridad estándar en el navegador para evitar peticiones lanzadas por JS "malicioso", pero representa un problema. Podemos cambiar este comportamiento del navegador habilitando una característica denominada CORS

Para añadir soporte de CORS al servidor del API lo más sencillo es usar un paquete `npm` que lo implemente, por ejemplo el paquete `cors`

* En el proyecto de la práctica anterior instalar el paquete `cors` con 

```bash
npm install cors
```

* En el código principal añadir:

```javascript
var cors = require('cors')
//Suponiendo "app" la variable obtenida como app=express()
app.use(cors())
```