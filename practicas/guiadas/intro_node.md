# Introducción básica a Node.js para aplicaciones web

Node.js (o simplemente Node) es una plataforma de desarrollo para implementar aplicaciones web en el lado del servidor escritas en Javascript. En principio surgió como una plataforma especialmente apropiada para aplicaciones asíncronas y en tiempo real. No obstante, dada la cada vez mayor popularidad de Javascript se está usando mucho para aplicaciones web más convencionales. 

> En la asignatura vamos a usar Node como plataforma de servidor no por sus virtudes con respecto a otras plataformas sino básicamente por permitirnos *usar el mismo lenguaje de programación en el cliente y en el servidor: Javascript*.

## Instalar Node


En la página de Node hay [instaladores](https://nodejs.org/en/download/) para OSX y Windows. Como alternativa, se puede usar un gestor de versiones de Node, que nos permite tener varias versiones coexistiendo en nuestra máquina y generalmente no necesita permisos de superusuario para instalarlas.

### Linux/OS X

`n` es un gestor de versiones de Node para Linux/OSX potente y fácil de usar e instalar.
Para instalar la última versión LTS de Node con `n`, simplemente tecleamos en una terminal:

```bash
curl -L https://git.io/n-install | bash
```
La instalación se hace en el directorio `$HOME/n`, para no requerir permisos de superusuario. A partir de aquí se recomienda leer la [documentación](https://github.com/tj/n) de `n` para saber cómo instalar versiones adicionales de Node o seleccionar la versión activa para cada proyecto.


### Windows

La propia Microsoft recomienda un gestor de versiones de Node denominada [nvm](https://nodejs.org/en/download/) (hay otro gestor de versiones de node llamado [nvm](https://github.com/nvm-sh/nvm) para Linux/OSX, aunque es otro proyecto distinto).

## ¡Hola Node!

Aquí tenemos el clásico "Hola mundo" en versión *app* web usando Node.js:

```javascript
//Cargamos el módulo http que implementa las funcionalidades de servidor web
var http = require('http');
//En Node creamos el propio servidor HTTP, a diferencia de otros *frameworks*
//donde creamos rutinas que se ejecutan en un servidor ya dado por el sistema
var server = http.createServer();
//Node está orientado a eventos 
//El evento 'request' se dispara al recibir una petición HTTP
//El segundo parámetro es el callback a ejecutar en respuesta al evento
server.on('request', function (req, res) {
    res.write('Hola soy Node.js');  
    res.end();
})
//Nos ponemos a escuchar por el puerto 3000
server.listen(3000, function() {
    console.log('Servidor Node.js en http://localhost:3000/');
});
```

Para escribir una aplicación web un poco más compleja necesitaríamos asociar diferentes rutas con diferentes funciones Javascript. Sin embargo el módulo `http` de Node no ofrece ninguna facilidad para implementar esto. Dentro del `server.on` tendríamos que ir chequeando la ruta dentro de bloques `if/else` para ejecutar el código apropiado. Como luego veremos, hay *frameworks* que facilitan mucho esta tarea. Nosotros usaremos uno de ellos, Express. 

## Gestión de paquetes

`Node` incluye un sistema de *paquetes* con gestión automática de dependencias al estilo de los usados en las distribuciones de linux (como los `.deb` o `.rpm`).

La herramienta de gestión de paquetes en Node es `npm` (similar al `apt-get` de Debian, o al Maven del mundo Java). Vamos a ver su uso básico. 

Comenzaremos por **instalar un paquete** para un proyecto. **En Node es habitual que las dependencias se instalen de modo local, en el propio directorio del proyecto**. Por ejemplo, vamos a instalar el paquete `colors`, para colorear la salida por la consola.

```bash
$ npm install colors  #o, más sencillo, "npm i colors"
```

> Al ejecutar el comando anterior veremos varios *warnings* en la consola (`WARN`), indicando que falta un archivo `package.json`. Aunque "lo suyo" sería crearlo, vamos a obviarlo de momento hasta que lo expliquemos en la sección siguiente.

El comando anterior **crea un subdirectorio `node_modules` en el directorio actual**, conteniendo el código del paquete `colors` (y los paquetes de los que depende, si los hubiera).

> Nótese que si distintos proyectos en los que estamos trabajando comparten dependencias, estas estarán repetidas en cada proyecto, ya que no tenemos un repositorio centralizado local (como sí pasa por ejemplo en Maven). De este modo evitamos problemas de versionado de dependencias, ya que cada proyecto usa directamente la versión que necesita, pero a costa de duplicar la información.

A partir de este momento ya podemos hacer uso del paquete con su correspondiente `require`. En el caso de `colors`, por ejemplo:

```javascript
var c = require('colors');
//Evidentemente cada paquete tiene su API así que habrá que consultar la documentación para saber cómo usarlo
//pone las letras en color verde
console.log(c.green('Saludos de Marte'));
//Pone cada letra de un color
console.log(c.rainbow('Todos amamos node.js'));
```

Habitualmente **cada paquete "exporta" un objeto a través del cual accedemos a su API**. Ese objeto es devuelto por `require`. En el caso de `colors` si leemos la documentación veremos que hay varios métodos para cambiar el color: `green`, `red`, `rainbow`, etc.

> Como hemos visto, las dependencias del proyecto se suelen instalar en modo *local* (o sea, en el mismo directorio del proyecto). Los paquetes que incluyen herramientas en línea de comandos se suelen instalar en modo *global* (o sea, en un directorio global compartido por todos los proyectos).

## Metadatos del proyecto: el archivo `package.json`

**Cada proyecto debería tener en su directorio raíz un archivo llamado `package.json`** con información sobre el mismo: nombre, versión, autor, dependencias, ... Gracias a este archivo podemos automatizar ciertas tareas como por ejemplo la ejecución del proyecto o la instalación en un solo paso de todas las dependencias.

Por ejemplo, aquí tenemos un `package.json` para nuestro `Hola Node` que incluye la dependencia de `colors` (luego veremos cómo lo hemos generado):

```json
{
  "name": "hola_node",
  "version": "1.0.0",
  "dependencies": {
    "colors": "^1.4.0"
  }
}
```

Los campos `name` y `version` son obligatorios. Podemos ver todos los posibles campos del `package.json` en la [documentación de referencia de npm](https://docs.npmjs.com/files/package.json).

> La versión o versiones de las dependencias que son válidas para el proyecto se especifican mediante *semver* o *semantical versioning*, que es una especie de estándar. Este estándar incluye unas normas sobre cómo numerar las versiones de los proyectos (`MAJOR.MINOR.PATCH`) y cuándo cambiar cada número de versión y además una sintaxis para especificar qué conjunto de versiones son compatibles con nuestro proyecto. Por ejemplo el `^` indica que nos vale cualquier versión igual o superior a la especificada mientras no cambie el número `MAJOR`. Podéis ver más información sobre el tema en la [documentación de semver](https://docs.npmjs.com/cli/v6/using-npm/semver) de npm y en esta [calculadora de *semver*](https://semver.npmjs.com/) en la que al meter una expresión de rango de versiones para un paquete npm, nos dará una lista de las incluídas en la misma. Por cierto,  de momento probablemente no queráis en vuestro proyecto [una versión de colors superior a la 1.4.0](https://fossa.com/blog/npm-packages-colors-faker-corrupted/) `:/`.

Gracias al `package.json` en los repositorios no es necesario adjuntar las librerías, solo el código propio. Cuando nos bajamos un proyecto Node de un tercero (por ejemplo de Github)  podemos **instalar todas sus dependencias** simplemente con

```bash
$ npm i 
```

Podemos crear el esqueleto inicial del `package.json` manualmente o bien ayudándonos del comando `npm init` que nos irá solicitando los datos del proyecto de manera interactiva.

Cada vez que instalamos un paquete en nuestro proyecto con `npm i` se añade automáticamente la referencia en el `package.json`.

> En versiones de npm anteriores a la 5, para modificar el `package.json` al instalar un paquete había que añadir `--save` al comando de instalación, por ejemplo `npm i colors --save`

Cuando queramos instalar paquetes que se usen solo en el desarrollo (por ejemplo para *testing*) podemos instalarlos con `npm i <paquete> --save-dev`. De este modo la referencia se añade al apartado `devDependencies` del `package.json`. Con un proyecto que nos hayamos bajado de otro sitio, si instalamos las dependencias con `npm i --production` no se instalarán las `devDependencies`, solo las de "producción". 

## Uso y definición de módulos

### ¿Cómo localiza node el código de un módulo?

En Node se establece una correspondencia uno a uno entre módulo y archivo. Cuando ponemos un nombre en el `require` se busca en un conjunto de directorios predefinidos un archivo con el mismo nombre literalmente o con la adición de la extensión `.js`. La búsqueda se hace de la siguiente forma:

- Se comprueba si es un módulo nativo de Node.
- En caso contrario, se comprueba si se ha dado una trayectoria (por ejemplo, `require('/home/Pepe/mi_modulo.js'`).
- En caso contrario, se busca en la carpeta `node_modules`
- Si no se encuentra, se sube de nivel un directorio y se busca en una carpeta `node_modules`, hasta llegar a la raíz del sistema de archivos.

En la documentación de Node se puede ver [más información sobre el sistema de módulos](https://nodejs.org/api/modules.html).


### Definir módulos propios

Lo visto hasta el momento nos basta para usar "librerías de terceros" pero si queremos hacer nuestros propios programas de forma modular necesitaremos saber también cómo definir módulos.

La variable "global" `exports` representa el objeto exportado por el módulo. Le asignaremos propiedades que serán visibles en nuestro código:

```javascript
//Archivo "saludador.js"
var saludos = ['Hola', 'Qué tal', 'Yiiihaa!'];

exports.saludar = function() {   
    var valor = Math.floor(Math.random()*saludos.length);
    return saludos[valor];
}
```

Ahora desde otro archivo podemos usar el módulo que hemos definido:

```javascript
//Cuando ponemos un nombre, se añade automáticamente el ".js"
var sal = require('./saludador');
console.log(sal.saludar());
```
