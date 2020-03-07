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

los eventos pueden tener alguna accion asociadas con las mismas. De mosdo que, si por ejemplo, si se hace click sobre un enlace se 
obtenga el destino de dicho link.

Para la mayoria de los eventos, los diferentes `handlers` de enventos de JavaScript son llamados antes de que la accion por defecto
sea ejecutada. Si el `handler` no nos indica ninguna accion en particular, se ejecutara la que este establecida por defecto

JavaScript nos facilita un metodo `preventDefault`, que se usa dentro de los handlers para evitar cualquier tipo de accion por defautl
de dichos eventos. Tenemos por ejemplo <i>Example6.html</i>:

    <a href="https://developer.mozilla.org/">MDN</a>
    <script>
    let link = document.querySelector("a");
    link.addEventListener("click", event => {
        console.log("Nope.");
        event.preventDefault();
    });
    </script>

Donde podemos destacar que creamos un enlace dentro de una etiqueta `<a>`, dicha etiqueta la guardamos como objeto, y asociamos un 
`event handler` para cuando se realize un evento de tipo `click`. Dicho evento lo que hara es mostrar un mensaje, para posteriormente
eliminar la accion por defecto.

Aunque el metodo `preventDefault` es util se aconseja no usarlo a no ser que se tenga una muy buena razon, ya que puede romper el
comportamiento de la pagina.


## key Events

Las teclas tienen asociadas diferentes eventos:

* `Keydown`: este evento sucede cuando se oprime una tecla, pero no se limita solo a cuando fisicamente se oprime, sino que si se 
mantiene mandara un evento cada vez que el teclado mande señal de dicha tecla, hasta que se deja de oprimir.

* `keyup` : este evento sucede cuando se termina de oprimir una tecla 

        <p>This page turns violet when you hold the V key.</p>
        <script>
        window.addEventListener("keydown", event => {
            if (event.key == "v") {
            document.body.style.background = "violet";
            }
        });
        window.addEventListener("keyup", event => {
            if (event.key == "v") {
            document.body.style.background = "";
            }
        });
        </script>

En el codigo podemos ver que se crea un `handler` tanto para cuando se presiona la tecla `v` como cuando se suelta. Este codigo podremos
encontrarlo en <i>Example7.html</i>

Otra cosa importante a destacar es que podemos registrar combinaciones de cualquier tipo de teclas presionadas al `handler` para definir
un compotamiento del programa adecuado a dicho evento. En <i>Example8.html</i> tenemos :

    <p>Press Control-Space to continue.</p>
    <script>
    window.addEventListener("keydown", event => {
        if (event.key == " " && event.ctrlKey) {
        console.log("Continuing!");
        }
    });
    </script>

Donde podremos ver que el evento regustra cuando hacemos la combinacion `espacio` + `control`.


## Pointer events

Actualmente existen 2 formas de apuntar a las cosas en pantalla: el raton y las pantallas tactiles. Cada una nos proporciona un tipo
de evento diferente


### mouse clicks

Cuando se realiza un click con el raton se pueden leer diferentes eventos:

* `mousedown` 
* `mouseup`

Ambos tienen un comportamiento similar a `keydown` y a `keyup`. Otro evento que podemos encontrarnos es :

* `doubleclick`

Cuando dos clicks ocurren seguidos.6

### mouse motion

Cada vez que el puntero se mueve, se crea un evento `mousemove`. A travez de este evento podemos seguir la posicion del raton. Un ejemplo e esto lo encontramos en 
<i>Example9.html</i>:

    <p>Drag the bar to change its width:</p>
    <div style="background: orange; width: 60px; height: 20px">
    </div>
    <script>
    let lastX; // Tracks the last observed mouse X position
    let bar = document.querySelector("div");
    bar.addEventListener("mousedown", event => {
        if (event.button == 0) {
        lastX = event.clientX;
        window.addEventListener("mousemove", moved);
        event.preventDefault(); // Prevent selection
        }
    });

    function moved(event) {
        if (event.buttons == 0) {
        window.removeEventListener("mousemove", moved);
        } else {
        let dist = event.clientX - lastX;
        let newWidth = Math.max(10, bar.offsetWidth + dist);
        bar.style.width = newWidth + "px";
        lastX = event.clientX;
        }
    }
    </script>


En este ehemplo podemos ver que controlamos 2 eventos, el primero es el de clickar sobre una zona anaranjada, y el segundo es el de `mousemove` mientras mantenemos el click presionado.

## Scroll events

Cada vez que un elemento es movido con la rueda del raton o `scrolled`, el evento `scroll` es lanzado. Este tiene diferentes usos tales como saber que es lo que el
usuario esta visualizando actualmente, o bien, para mostrar algun ripo de progreso en el contenido a mostrar. En <i>Example10.html</i>

    <style>
    #progress {
        border-bottom: 2px solid blue;
        width: 0;
        position: fixed;
        top: 0; left: 0;
    }
    </style>
    <div id="progress"></div>
    <script>
    // Create some content
    document.body.appendChild(document.createTextNode(
        "supercalifragilisticexpialidocious ".repeat(1000)));

    let bar = document.querySelector("#progress");
    window.addEventListener("scroll", () => {
        let max = document.body.scrollHeight - innerHeight;
        bar.style.width = `${(pageYOffset / max) * 100}%`;
    });
    </script>

Se nos muestra una cantidad de texto enorme, y a medida que bajamos o subimos una barra de color azul aumenta o disminuye para 
representar la cantidad de contenido que hemos visto y la que falta por ver.

## Focus events

A medida que el usuario navega por una pagina, es inevitable que tenga que interactuar con alguno de los elementos dispuestos dentro
del contenido de dicha pagina. Se le llama `focus` a aquel elemento que esta actualmente en interaccion directa con el usuario, de 
modo que si el usuario selecciona un `textbox` dicho elemento sera el que tenga el focus actual de la ventana, lo que le permitira al
usuario al momento de escribir, que el contenido sea enviado a dicha `textbox`.

Podemos encontrar un ejemplo de esto en <i>Example11.html</i>, en donde tendremos dos campos para introducir informacion, y vemos que
al principio los marcos son de color negro, y al seleccionarlos, obtienen el focus y cambian a un gris.

    <p>Name: <input type="text" data-help="Your full name"></p>
    <p>Age: <input type="text" data-help="Your age in years"></p>
    <p id="help"></p>

    <script>
    let help = document.querySelector("#help");
    let fields = document.querySelectorAll("input");
    for (let field of Array.from(fields)) {
        field.addEventListener("focus", event => {
        let text = event.target.getAttribute("data-help");
        help.textContent = text;
        });
        field.addEventListener("blur", event => {
        help.textContent = "";
        });
    }
    </script>


## Load events

El evento `load` es lanzado al cargar una pagina. Esto usualmente es usado para programar la inicializacion de acciones que requieran
de todo el documento para ser ejecutados.

Por otro lado cuando una pagina es cerrada o redirigida hacia otro sitio, tenemos que se ejecuta el evento `beforeunload`. El uso 
principal del evento es para prevenir hacciones accidentales del usuario tales como cerrar un documento. Si se previene el 
comportamientocon este evento y se establece una propiedad `returnValue` dentro de un evento, sobre un string, el buscador nos mostrara 
un dialogo preguntando si realmente se quiere salir de la pagina.


## Events and event loop

En el contexto del `evento loop`, los `handler events` se comportan como cualquier otra notificacion asincrona. Estas son programadas para correr antes de que tengan ocacion de ejecutarse.

El hecho de que los eventos pueden ser procesados solo cuando nada mas este corriendo, con lo que si el `event loop` esta asociado
a algun otro trabajo, cualquier interaccion con la pagina sera retrasada hasta que el proceso termine. Con lo que si se programa
demasiados trabajos, independientemente si tenemos algunos trabajos largos o varios trabajos cortos, la pagina se volvera lenta
y horrible de usar.

Si se quiere realizar realmente mantener procesos de consumo de tiempo en segundo plano, sin bloquear la pagina, el navegador nos 
da `web workers`. Un <b>web worker</b> es un proceso de JavasCriptque trabaja de forma paralela al respecto del script principal.

En un caso en el que se haga la raiz cuadrada de un nuemero pesado, y que consuma bastante tiempo de ejecucion que queremos que se 
ejecute en un hilo paralelo. Deberemos escribir un fichero llamado `code/squareworker.js`. Esto responde a los mensajes resultantes
de computar la raiz cuadrada y envia una respuesta :

    addEventListener("message", event => {
        postMessage(event.data * event.data);
    });


Para evitar el problema de tener multiples hilos tocando los mismos datos, los trabajadores no comparten un ambito global o cualqier 
otra informacion perteneciente al ambito del script principal. En cambio, puedes comunicarte con mensajes.

El siguiente codigo genera un `worker` que ejecuta el script anterior, lo envia en algunos mensajes y expulsa la respuesta.

    let squareWorker = new Worker("code/squareworker.js");
        squareWorker.addEventListener("message", event => {
         console.log("The worker responded:", event.data);
    });
    squareWorker.postMessage(10);
    squareWorker.postMessage(24);

La funcion `postMessage`, creara un evento `message` en el recividor. El script creado por el `worker` envia y recive mensajes a travez 
del objeto `Worker`, donde el `worker` se comunica con el script que lo creo mediante un mensaje y escichando directamente en su 
`global scope`.

Solamente vaores que pueden ser representados como `JSON` pueden ser enviados como mensajes, en caso contrario se recivira una copia de 
ellos, mas que el valor en si. 



## timer

## Debouncing

## Sumary
