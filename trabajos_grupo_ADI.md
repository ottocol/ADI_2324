# Trabajos en grupo
## Normas básicas

> **La fecha límite de entrega será el viernes 15 de enero de 2024**


- **En grupos de 2-4 personas**, aunque excepcionalmente se podrían hacer de manera individual. No más de 4 personas.
- Será válido **cualquier tema relacionado con la materia** de la asignatura (desarrollo web, especialmente del lado del cliente). El trabajo debe ir más allá de lo que explicamos en clase, detallando aspectos que aquí se vean con menos profundidad o temas que aun estando relacionados no vemos en la asignatura.
- Al final del documento os pongo una lista de posibles temas. No hay problema en que varios grupos elijáis el mismo tema (siempre que no os copiéis, claro 😁). Por supuesto podéis proponer vuestros propios temas.
- A la mayor brevedad posible **enviadme una tutoría o un email con el nombre de l@s integrantes del grupo y el tema elegido para el trabajo**. No hay un plazo para esto pero cuanto antes lo hagáis antes podéis poneros a trabajar. Yo os contestaré con el OK o si creo que el tema elegido puede ser demasiado trivial/difícil/no aconsejable por el motivo que sea también os lo diré.

## Contenido

- El objetivo es que todos podamos aprender algo sobre un tema que no hayamos visto en la asignatura, de manera que se valorará la claridad en las explicaciones y que sea ilustrativo sobre el tema. También se valorará el rigor técnico.
- Puede ser un trabajo teórico, teórico/práctico o bien solo práctico. Este último caso sería desarrollar algo que teóricamente hayamos visto en clase pero no hayamos implementado (por ejemplo, implementación de un API GraphQL para vuestra práctica, desarrollo de una app con notificaciones *push* para apps web móviles,...)
- Se debe incluir una **lista de referencias** (libros, sitios web, videos, etc) de donde hayáis sacado la información. Es evidente que si no sois expertos en el tema la información no podéis aportarla vosotros directamente, pero lo suyo es que elijáis unas cuantas referencias y ordenéis y sinteticéis el contenido de todas ellas. Si se detecta que el trabajo es un plagio en gran medida de una sola fuente  implicará el suspenso.

## Baremo de evaluación

> Dado que la asignatura es eminentemente práctica no se valorarán los trabajos exclusivamente teóricos, aunque por supuesto un trabajo puede contener ciertos apartados teóricos (típicamente los iniciales). Por ejemplo si vais a hacer un trabajo sobre microservicios, no lo hagáis exclusivamente teórico, incluid una implementación de un microservicio (en el lenguaje que queráis, pero que funcione y se pueda probar y que no sean simples fragmentos de código).

El trabajo debe entregarse en forma de video tipo presentación o *screencast* junto con el código desarrollado, este podrá entregarse en un .zip o (recomendado) dejarse en un repositorio estilo Github. Mínimo 5 minutos duración. Máximo 15. Podéis subirlo a Youtube o similar o dejarlo compartido en algún servicio de compartición de archivos. No es necesario que incluyáis documentación escrita.

- Hasta 7 puntos por el contenido en sí y por el código desarrollado para el trabajo (no es lo mismo un tutorial de React poniendo como ejemplo un contador que desarrollando una app de tareas pendientes)
- Hasta 1.5 por la presentación del video (claridad de la exposición, organización, cuidado en las ayudas visuales - por ejemplo transparencias iniciales para mostrar conceptos, efectos visuales - por ejemplo zoom en el código en determinado momento para mostrar un detalle,...) 
- 0.5 puntos por votar al resto de grupos (el procedimiento de votación se explica en el siguiente apartado)
- Hasta 1 punto asignado por el resto de grupos 

### Puntuación de los compañeros

- Supongamos N grupos
- Cada grupo tiene 0,75*N puntos. A cada trabajo de los compañeros se les puede asignar {2,1,0} puntos
- Se ordenan todos los grupos en 10 secciones, de manera que los round(N/10) primeros tienen +1, los segundos +0.9, y así sucesivamente
- Ejemplo:
    - Supongamos 25 grupos
    - Cada grupo tiene 18 puntos para asignar
    - Se ordenan según puntos de mayor a menor, los 3 primeros tienen +1, los 3 siguientes + 0,9...y el último 0,2

Cuando se hayan entregado todos los trabajos pondré una web con los enlaces para que podáis verlos. Tendréis un plazo aproximado de 1 semana para verlos y repartir vuestros votos. 

## Listado de temas posibles

> Este listado no es excluyente, podéis proponer cualquier otro tema que no esté aquí mientras esté relacionado con la asignatura, es solo un punto de partida para daros ideas.

- Frameworks para Node alternativos a express. Desarrollo de un API REST de ejemplo con uno de ellos.
- APIs GraphQL: alternativa a REST que permite que el cliente haga una especie de *queries* para obtener exactamente la información que necesita. Implementación de un API propio o demo de cómo se hacen consultas a un API ya existente como el API GraphQL de Github.
- Tutorial de cualquier framework Javascript que no hayamos usado en prácticas: Svelte, Angular, React, SolidJS. Alternativa: mostrar una aplicación pequeña en estos frameworks y compararla con lo que habéis visto de Vue (semejanzas/diferencias)
- Microservicios
- Microfrontends (aplicación de la idea de microservicios al *frontend*)
- Aplicaciones serverless en Amazon AWS o Microsoft Azure
- Programación Funcional Reactiva en Web: una alternativa a la forma de programar webs "tradicional", sobre todo apropiada para manejar eventos en tiempo real. RxJS es la librería más conocida en JS, aunque hay otras como BaconJS.
