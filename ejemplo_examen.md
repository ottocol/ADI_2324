# Ejemplo de Examen práctico de Aplicaciones Distribuidas en Internet

> Nota: El presente enunciado es solo un ejemplo. Está basado en la *app* de lista de la compra que hemos ido haciendo durante el curso y por tanto algunas de las funcionalidades que aquí se piden ya se han implementado en ejemplos de clase. En enero se usará otro ejemplo de *app* distinto aunque similar.

Partiendo de [esta versión del API REST de la lista de la compra](https://moodle2023-24.ua.es/moodle/mod/resource/view.php?id=102941), que implementa las funcionalidades básicas CRUD, añadir las siguientes funcionalidades:

- En el *backend*: añadir la funcionalidad de *login* usando tokens JWT. Para simplificar podéis asumir que usuarios y contraseñas se almacenan en memoria y que por defecto cualquier usuario que haga login correctamente puede realizar cualquier operación sobre cualquier item de la lista (crear, leer, actualizar o borrar)

- *Frontend*: crear un *mini-frontend* basado en Vue en el que si el usuario no está logueado aparezca un formulario de login y en caso contrario aparezca la lista de tareas. No es necesario usar estado centralizado con Pinia. Cada tarea debe aparecer con un botón para borrarla que sea totalmente funcional.

- Añade un "web app manifest" al frontend para que la app tenga un icono y sea instalable en el escritorio del móvil (no es necesario que crees el icono, referencia un nombre de archivo aunque no exista).

- Hacer una "traza" explicando qué pasa en la *app* cuando se pulsa un botón de borrar tarea (cómo se procesa el evento y por dónde va pasando el flujo de ejecución hasta que se actualiza el HTML donde la tarea borrada ya no aparece).


## Aclaraciones al enunciado y al examen

- Como ya se ha comentado varias veces, al examen podéis llevar toda la documentación, ejemplos de código, etc. que consideréis necesario, aunque no tendréis acceso a Internet, por lo que lo más sencillo es que lo llevéis en un *pendrive* o similar. El examen se debe realizar en los equipos del laboratorio.

- En el examen no se pedirá implementar funcionalidades que no hayamos visto con detalle en teoría y/o prácticas. Por ejemplo sí se podría pedir implementar alguna funcionalidad con Pinia pero no con Angular o React.

- Aunque la parte fundamental del examen consistirá en añadir funcionalidades a la *app*, como se ve en la última pregunta también se puede pedir que expliquéis con detalle cómo funciona alguna parte del código que añadáis (para asegurar que lo entendéis y no solo hacéis un copia/pega).

- El tiempo de realización del examen será aproximadamente de 2 horas y 15 minutos.
 