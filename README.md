# Practica 4: Manejando Eventos


## Evente Handlers 

Muchos sistemas al momento de manejar instrucciones de entrada suelen usar una cola, en donde se almacenan temporalmente dichas 
instrucciones hasta que son ejecutadas eventualmente. Esto requiere que, en sistemas software, se tenga un programa que valla 
revisando dicha cola para ir extrayendo y ejecutando lo que encuente ahi.

Existe algo que se llama `polling`, que es la cantidad de tiempo en la que ocurre un evento y el programa descrito anteriormente 
reaccionando a dicho evento. El `polling` trae como consecuencia que el programa se muestre en un estado no responsivo.

Un mecanismo alternativo, es simplemente activar nuestro codigo cuando ocurra un evento, en vez de mantenerlo en una alerta constante.
Los navegadores, tienen esta caracteristica implementada, dejandonos registrar funciones como manejadores para eventos especificos.
Ejemplo <i>Example1.html</i>:

    <p>Click this document to activate the handler.</p>
    <script>
    window.addEventListener("click", () => {
        console.log("You knocked?");
    });
    </script>

Como podemos ver en el codigo, dentro de la etiqueta `script` nos encontramos con la siguiente instruccion:

    window.addEventListener("click", () => {

Donde `windows` representa al propio navegador como un objeto, y `addEventListener` es lo que nos permite crear handlers de eventos 
en este caso estamos creando uno, que se activa al ocurrir un evento identificado como `click`, ejecutando la funcion anonima

    () => {
        console.log("You knocked?");
    }


## Eventos y nodos de DOM

Cada `handler` de eventos de navegador esta registrado dentro de un contexto. Por ejemeplo, en el codigo dentro de <i>Example1.html</i>
vemos que estara registrado, o enlazado, en la ventana actual del navegador. Como consecuencia el `handler` estadra dentro del `DOM`,
de modo que cuando el evento ocurra solo podra ser evaluado en el contexto de donde dicho objeto este registrado. Como podremos ver en <i>Example2.html</i>:

    <button>Click me</button>
    <p>No handler here.</p>
    <script>
    let button = document.querySelector("button");
    button.addEventListener("click", () => {
        console.log("Button clicked.");
    });
    </script>

Como vemos, el codigo html crea un boton y un mensaje. Cuando se hace click sobre el boton se genera un evento que es interceptado por la funcion
`button.addEventListener` el cual escuchara el evento llamado `click` y ejecutando posteriormente la funcion anonima a modo de callback, mostrando el mensaje por 
consola.

En el siguiente codigo, encontrado en el fichero <i>Example3.html</i>:

    <button>Act-once button</button>
    <script>
    let button = document.querySelector("button");
    function once() {
        console.log("Done.");
        button.removeEventListener("click", once);
    }
    button.addEventListener("click", once);
    </script>

Como podemos ver hay una nueva instruccion, `document.querySelector` que se encarga de devolvernos el primer elemento del codigo http
que coincida con lo que recibe por parametro, en este caso es un elemento de tipo `button`. Luego podemos ver que tenemos declarado una 
funcion que al ser llamada mostrara un mensaje y posteriormente eliminara la relacion del evento `click` con dicha funcion. De esta
forma cuando se vuelva a hacer click sobre el boton, este no hara nada, pues el evento `click` no estara enlazado a ninguna funcion
para que la maneje a modo de `handler`.

Una vez fuera de la funcion nos encontramos con la instruccion que permitira ejecutar la funcion `once` cuando el evento `click` ocurra.

## Event Objects    

Para poder controlar un evento, podemos ademas pasar como parametro el propio evento para obener o manejar mas informacion. El siguiente
codigo pertenece a <i>Example4.html</i>:

    <button>Click me any way you want</button>
    <script>
    let button = document.querySelector("button");
    button.addEventListener("mousedown", event => {
        if (event.button == 0) {
        console.log("Left button");
        } else if (event.button == 1) {
        console.log("Middle button");
        } else if (event.button == 2) {
        console.log("Right button");
        }
    });
    </script>

Como vemos tenemos el evento `mousedown` que se activa cuando un dispositivo apuntador hace algun tipo de click sobre algun elemento, en
este caso podemos ver que al menos hay 3 opciones:

* `event.button` == 0, si se hizo click con el boton izquierdo
* `event.button` == 1, si se hizo click con el boton central
* `event.button` == 2, si se hizo click con el boton derecho

## Propagacion

Cuando un nodo tienen hijos, tambien recibira los eventos que ocurran dentro de los hijos. De modo que si un boton dentro de un parrafo es clicado, el `event handler`
del parrafo tambien podra ver/recibir el evento `click`.

Pero si tanto el parrafo y el boton tienen su propio `handler`, el mas especifico tendra preferencia (siendo en el ejemplo el del boton). El evento se ira propagando de 
forma inversa, desde el mas especifico hasta el mas general.  Si embargo, en cualquier punto del nodo podemos llamar a la instruccion `stopPropagation` para detener 
este proceso.

    <p>Press Control-Space to continue.</p>
    <script>
    window.addEventListener("keydown", event => {
        if (event.key == " " && event.ctrlKey) {
        console.log("Continuing!");
        }
    });
    </script>

En el ejemplo anterior (<i>Example5.html</i>), vemos que podemos registrar eventos al objeto `window`,de modo que, en el caso especifico
de que se lea alguna tecla, y esta o estas sean las teclas `ctrl` y `espacio` ejecutara el evento.


## Default Actions

## key Events

## Pointer events

### mouse clicks

### mouse motion

### Touch events

## Scroll events

## Focus events

## Load events

## Events and event loop


## timer

## Debouncing

## Sumary
