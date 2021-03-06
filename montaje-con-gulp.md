# Automatización de tareas con Gulp
* El [texto original](http://www.smashingmagazine.com/2014/06/11/building-with-gulp/) aparece en la revista Smashing Magazine a mediados de 2014.

* [Gulp.js en Github](https://github.com/gulpjs/gulp/blob/master/docs/API.md)

* [Flujo de trabajo con Gulp.js](http://maximilianschmitt.me/posts/gulp-js-tutorial-sass-browserify-jade/)

* [Gulp.js y Bootstrapp 3](http://stackoverflow.com/questions/21994582/using-gulp-to-compile-bootstrap-less-files-into-main-bootstrap-cs)

## Contenido
* [¿Que es Gulp?](#que-es-gulp)

* [Instalar Gulp](#instalar-gulp)

* [Uso de Gulp](#uso-de-gulp)

* [Canales de proceso](#canales-de-proceso)

* [gulp.src](#gulp-src)

* [Definición de tareas](#definición-de-tareas)

* [Tareas por defecto](#tareas-por-defecto)

* [Complementos](#complementos)

* [El complemento gulp-load-plugins](#el-complemento-gulp-load-plugins)

* [Seguimiento de archivos](#seguimiento-de-archivos)

* [Recarga de cambios en el navegador](#recarga-de-cambios-en-el-navegador)

* [¿Por que Gulp?](#por-que-gulp)

La optimización de los recursos web y la comprobación del funcionamiento de la página en diferentes navegadores no son la parte más divertida del proceso de diseño. Por suerte, se trata de un conjunto de tareas repetitivas que pueden ser automatizadas con las herramientas adecuadas para así aumentar tu eficiencia.

Gulp es un sistema de montaje que puede mejorar tu desarrollo de páginas web mediante la automatización tareas comunes, como la compilación del CSS pre-procesado, la minimización de Javascript y la recarga del navegador. En este artículo, veremos como puedes usar Gulp para cambiar tu flujo de trabajo de desarrollo, haciéndolo más rápido y más eficiente. 

## ¿Que es Gulp?
Gulp es un sistema de montaje, lo que significa que puedes usarlo para automatizar tareas corrientes en el desarrollo de páginas web. Está montado sobre Node.js y tanto el código fuente de Gulp, como tu archivo Gulp, en el que se definen las tareas, está escritos en Javascript (o si lo prefieres, en CoffeScript o algo parecido). Esto lo hace perfecto para los desarrolladores front-end: puedes crear tareas para limpiar tu Javascript y tu CSS, interpretar tus plantillas y compilar tu LESS cuando cambia algún archivo (estos solo son algunos ejemplos), y todo ello en un lenguaje que te es familiar.

Gulp por si mismo no hace en realidad gran cosa, pero existe un número ingente de complementos -plugins- que puedes encontrar en la [página de complementos](http://gulpjs.com/plugins) o efectuando una búsqueda `gulpplugin` usando `npm`. Hay, por ejemplo, complementos para [ejecutar JSHint](https://www.npmjs.org/package/gulp-jshint/), para [compilar CoffeeScript](https://www.npmjs.org/package/gulp-coffee/), para [ejecutar test con Mocha](http://npmjs.org/package/gulp-mocha) o incluso para [actualizar el número de tu versión](http://npmjs.org/package/gulp-bump).

Existen otras herramientas de montaje, como [Grunt](http://gruntjs.com/), o más recientemente, [Broccoli](http://www.solitr.com/blog/2014/02/broccoli-first-release/), pero considero que Gulp es superior (vease la sección [¿Por que Gulp?](#--por-que-gulp--). He reunido una larga [lista de herramientas de montaje escritas con Javascript](https://gist.github.com/callumacrae/9231589). 
Gulp es código libre y lo puedes encontrar en [Github](https://github.com/gulpjs/gulp/).

## Instalar Gulp
Instalar Gulp es realmente fácil. Primero instala el paquete de Gulp globalmente:
```shell
$ npm install -g gulp
```
Luego instálalo en tu proyecto (como dependencia para el desarrollo):
```shell
$ npm install --save-dev gulp
```
## Uso de Gulp

Vamos a crear una tarea en Gulp para minimizar uno de nuestros archivos Javascript.
  1. Crea un archivo `gulpfile.js`. En este es en el que definirás tus tareas Gulp, que se ejecutarán mediante la orden `gulp`.
  2. Incluye lo siguiente en este archivo `gulpfile.js`:

    ```javascript
    var gulp = require("gulp"),
      uglify = require("gulp-uglify");
  
    gulp.task("minify", function () {
      gulp.src("js/app.js")
        .pipe(uglify())
        .pipe(gulp.dest("build"));
    });
    ```
  3. Instala `gulp-uglify` usando `npm`:
[UglifyJS](https://github.com/mishoo/UglifyJS) – un intérprete, compresor y embellecedor para JavaScript

```shell
$ npm install --save-dev gulp-uglify
$ gulp minify
```

Suponiendo que hubiera un archivo `app.js` en el directorio `js` se creará un nuevo archivo `app.js` en el directorio de montaje con el contenido minimizado de `js/app.js`.

¿Que es lo que ha pasado? En el archivo `gulpfile.js` hacemos varias cosas. Primero cargamos los módulos `gulp` y `gulp-uglify`:

```javascript
var gulp = require("gulp"),
  uglify = require("gulp-uglify");
```
A continuación definimos una tarea que llamamos `minify` que al ejecutarse llamará la función incluida como segundo parámetro:
```javascript
gulp.task("minify", function () {
  // ...
});
```
Finalmente, y aquí es donde se complica la cosa, definimos lo que deben hacer nuestras tareas:
```javascript
gulp.src("js/app.js")
  .pipe(uglify())
  .pipe(gulp.dest("build"));
```
A menos que estés familiarizado con las `streams` -canal de procesos-, que no suele ser el caso con los desarrolladores front-end, el código anterior no significará gran cosa.

## Canales de procesos
Los canales de procesos te capacitan para enviar una serie de datos a través de un conjunto de funciones, en general pequeñas, que  modifican los datos y luego los pasan a la siguiente función.

En el ejemplo anterior, la función `gulp.src()` toma una cadena con la que coincide un archivo o varios (conocidos como un "glob") y crea una corriente de objetos que representan a esos archivos. Luego estos son canalizados hacia la función `uglify()`, la cual toma los objetos archivo y devuelve nuevos objetos archivo con el código minimizado. Esta salida se canaliza hacia la función `gulp.dest()`, que guarda los archivos modificados.

Cuando solo hay una tarea que ejecutar la función no hace gran cosa. De todas maneras, considera el código siguiente:
```javascript
gulp.task("js", function () {
  return gulp.src("js/*.js")
    .pipe(jshint())
    .pipe(jshinr.reporter("default"))
    .pipe(uglify())
    .pipe(concat("app.js"))
    .pipe(gulp.dest("build"));
});
```
Para ejecutarlo por ti mismo, instala `gulp`, `gulp-jshint`, `gulp-uglify` y `gulp-concat`.

Esta tarea cogerá todos los archivos que coincidan con `js/*.js` (es decir, todos los archivos Javascript del directorio `js`), ejecutará JSHint sobre ellos e imprimirá el resultado, ejecutará `uglify` sobre cada uno de ellos y luego los concatenará (en un único archivo) alojándolos en `build/app.js`.

Si estás familiarizado con Grunt, verás que esto es bastante diferente de como lo hace Grunt. Grunt no usa canales de procesos -streams-; en su lugar, toma archivos, ejecuta tareas individuales sobre ellos y los guarda como nuevos archivos, repitiendo el proceso para cada tarea. Esto resulta en un montón de accesos al sistema de archivos, haciéndolo más lento que Gulp.

Para una lectura más pormenorizada sobre canales de procesos -streams-, écha un vistazo a [Stream Handbook](https://github.com/substack/stream-handbook).

## gulp.src
La función `gulp.src()` usa un `glob` -literalmente, pegote- (es decir, una cadena que coincide con uno o más archivos) o un vector de pegotes y devuelve un canal de procesos que puede canalizarse hacia los complementos -plugins-.

Gulp usa [node-glob](https://github.com/isaacs/node-glob) para obtener los archivos a partir del pegote o pegotes que has indicado. Lo más fácil es usar un ejemplo para explicarlo:
* `js/app.js`

Apunta al archivo exacto
* `js/*.js`

Apunta a todos los archivos con extensión `.js` solo en el directorio `js/`
* `js/**/*.js`

Apunta a todos los archivos con extensión `.js` en el directorio `js/` y en todos sus subdirectorios
* `!js/app.js`

Excluye a `js/app.js`de la coincidencia, lo cual resulta útil cuando deseas hallar coincidencia con todos los archivos de un directorio excepto con uno en particular.
* `*.+(js|css)`

Apunta a todos los archivos en el directorio raiz que terminen en `.js` o `css`.

Hay más posibilidades, pero no se suelen usar comúnmente en Gulp. Echa un vistazo a la documentación de [Minimatch](https://github.com/isaacs/minimatch) para descubrirlas.

Digamos que tenemos un directorio de nombre `js` que contiene archivos Javascript, unos minimizados y otros no, y que queremos crear una tarea que minimice los archivos que aún no lo están. Para ello, apuntamos a todos los archivos Javascript en el directorio y excluimos luego a todos los archivos con extensión `.min.js`:
```javascript
gulp.src(["js/**/*.js", "!js/**/*.min.js"])
```

## Definición de tareas
Para definir una tarea usa la función `gulp.task()`. Cuando defines una tarea sencilla, esta función toma dos argumentos: el nombre de la tarea y la función a ejecutar.
```javascript
gulp.task("saludo", function () {
  console.log("¡Hola mundo!");
});
```

La ejecución de `gulp saludo` resulta en la impresión de "¡Hola mundo!" en la consola.

Una tarea puede ser una lista de otras tareas. Supongamos que queremos definir una tarea de montaje que ejecute otras tres tareas `css`, `js` e `ìmgs`. Podemos hacerlo especificando un vector de tareas en lugar de la función:
```javascript
gulp.task("build", ["css", "js", "imgs"]);
```
Estas se ejecutarán asíncronamente, por lo que no puedes suponer que la ejecución de la tarea `css` ha concluido cuando empieza `js` -de hecho lo más probable es que no sea así. Para garantizarnos que una tarea ha concluido antes de que empiece la siguiente, puedes especificar las dependencias mediante la combinación del vector de tareas con la función. Por ejemplo, para definir una tarea `css` que comprueba que la tarea `greet` ha terminado de ejecutarse antes de empezar, puedes hacer esto:

```javascript
gulp.task("css", ["greet"], function () {
  // Procesamiento de `css`
});
```

Ahora, cuando ejecutas la tarea `css`, Gulp ejecutará la tarea `greet`, esperará a que termine y después llamará a la función que hayas especificado.

## Tareas por defecto
Puedes definir una tarea por defecto que se ejecuta cuando ejecutas simplemente `gulp`. Puedes hacerlo definiendo una tarea a la que denomines `default`.
```javascript
gulp.task("default", function () {
  // Tu tarea por defecto
});
```
## Complementos
Puedes usar con Gulp gran número de complementos -de hecho más de 600-. Los encontrarás listados en la [página de complementos](http://gulpjs.com/plugins/) o buscando por `gulpplugin` en `npm`. Algunos complementos están etiquetados como "gulpfriendly" -amigables con Gulp-; no se trata de complementos, pero están diseñados para trabajar bien con Gulp. Ten en cuenta de que buscando directamente en `npm` no podrás ver si un complemento ha sido incluido en la lista negra (si mirás al pie de la página de complementos, verás que se ha incluido a muchos).

Muchos de los complementos son bastante fáciles de usar, están bien documentados y se ejecutan de la misma manera (canalizando un flujo de objetos archivo hacia él). Estos modifican como de costumbre los archivos (aunque algunos, como los validadores, no lo hacen) y devuelven los nuevos archivos que han de pasarse al siguiente complemento.

Vamos a extendernos sobre la tarea `js` mencionada:
```javascript
var gulp = require("gulp"),
  jshint = require("gulp-jshint"),
  uglify = require("gulp-uglify"),
  concat = require("gulp-concat");

gulp.task("js", function () {
  return gulp.src("js/*.js")
            .pipe(jshint())
            .pipe(jshint.reporter("default"))
            .pipe(uglify())
            .pipe(concat("app.js"))
            .pipe(gulp.dest("build));
});
```
Aquí usamos tres complementos: [`gulp-jshint`](https://github.com/wearefractal/gulp-jshint), [`gulp-uglify`](https://github.com/terinjokes/gulp-uglify) y [`gulp-concat`](https://github.com/wearefractal/gulp-concat). Podrás ver, en los archivos README de los complementos, que todos son bastante fáciles de usar; disponen de opciones, pero en general las opciones por defecto son suficientemente buenas.

Habrás notado que llamamos al complemento JSHint dos veces. Esto es así, porque primero se ejecuta JSHint sobre los archivos, que solo añade una propiedad `jshint` a los objetos archivo sin devolver nada. Puedes leer dicha propiedad tu mismo o pasarla al `reporter` -informador- "default" de JSHint o a otro `reporter`, como [`jshint-stylish`](https://github.com/sindresorhus/jshint-stylish).

Los otros dos complementos son más claros: la función `uglify()` minimiza el código y la función `concat("app.js")` concatena todos los archivos en un único archivo `app.js`.

## El complemento gulp-load-plugins
Un módulo que encuentro especialmente útil es `gulp-load-plugins`, que carga automáticamente cualquier complemento Gulp de tu archivo `package.json` y los añade a un objeto. Su uso más básico es el siguiente:
```javascript
var gulpLoadPlugins = require("gulp-load-plugins"),
    plugins = gulpLoadPlugins();
```
Puedes hacerlo todo en una sola línea, pero no me gustan las llamadas `require` inmediatas:
```javascript
var plugins = require("gulp-load-plugins")(); 
```
Después de ejecutar este código, el objeto `plugins` contiene todos los complementos, re-formateando sus nombres (por ejemplo, `gulp-ruby-sass` se cargaría como `plugins.rubySass`). Puedes usarlos después como si los hubieras requerido -`require("...")- normalmente. Por ejemplo, nuestra anterior tarea `js` quedaría reducida a lo siguiente:
```javascript
var gulp = require("gulp"),
  gulpLoadPlugins = require("gulp-load-plugins"),
  plugins = gulpLoadPlugins();
  
gulp.task("js", function () {
  return gulp.src("js/*.js")
  .pipe(plugins.jshint())
  .pipe(plugins.jshint.reporter("default"))
  .pipe(plugins.uglify())
  .pipe(plugins.concat("app.js"))
  .pipe(gulp.dest("build"));
});
```
Esto asume que tu archivo `package.json` es algo parecido a esto:
```javascript
{
  "devDependencies": {
    "gulp-concat": "~2.2.0",
    "gulp-uglify": "~0.2.1",
    "gulp-jshint": "~1.5.1",
    "gulp": "~3.5.6"
  }
}
```
En este ejemplo, no es realmente mucho más corto. De todas maneras, con archivos Gulp más grandes y más complicados, se reduce un montón de inclusiones a solo una o dos lineas.

La versión 0.4.0 de `gulp-load-plugins`, salida a principios de marzo (de 2014), añade la carga laxa de complementos -lazy plugin loading-, lo que mejora su rendimiento. Los complementos no se cargan hasta que no son llamados, lo que significa que no tienes que preocuparte de que los paquetes de `package.json` no usados afecten al rendimiento (aunque sería recomendable que los eliminaras). En otras palabras, si ejecutas una tarea que solo requiere dos complementos, esta no cargará todos los complementos que requieren las demás tareas.

## Seguimiento de archivos
Gulp tiene la habilidad de hacer seguimiento de los cambios en archivos y luego ejecutar una o varias tareas cuando estos se producen. Esta característica es sorprendentemente útil (y, para mi, probablemente la más útil de Gulp). Puedes guardar tu archivo `.less`, y Gulp lo convertirá en otro `.css` y recargará el navegador sin que tu tengas que hacer nada.

Para hacer seguimiento de uno o varios archivos usa la función `gulp.watch()`, que toma un `glob` -pegote- o un vector de estas  pseudo cadenas (lo mismo que `gulp.src()`) y también un vector de tareas a ejecutar o una función del tipo `callback` -retrollamada-.

Digamos que tenemos una tarea de montaje que convierte nuestros archivos de plantillas en HTML, y nosotros queremos definir una tarea de seguimiento de cambios en los archivos de plantillas y que ejecute una tarea para convertirlos en HTML. Podemos usar la función `watch` como sigue:
```javascript
gulp.task(`watch`, function () {
  gulp.watch("templates/*.tmpl.html", ["build"])
});
```
Ahora, cuando modifiquemos un archivo de plantilla, la tarea `build` se ejecutará y se generará el HTML.

También puedes pasar a la función `watch` -de seguimiento- una función del tipo `callback` -de ejecución subordinada-, en lugar de un vector de tareas. En este caso, la función subordinada recibirá un objeto del tipo evento que contendrá alguna información acerca del suceso que lanza la función subordinada -callback-:
```javascript
gulp.watch("templates/*.tmpl.html", function (event) {
  console.log("Event type: " + event.type);
  // added, changed o deleted
  
  console.log("Event path: " + event.path);
  // ruta del archivo modificado
});
```

Otra característica interesante de `gulp.watch()` es que devuelve lo que llamamos un vigilante -watcher-. Usa al vigilante para observar sucesos adicionales o para incluir archivos en el seguimiento -`watch`-. Por ejemplo, para ejecutar una lista de tareas y llamar a una función subordinada -callback-, puedes añadir una escucha -listener- al evento `change` del `watcher` -vigilante- devuelto:
```javascript
var watcher = gulp.watch("templates/*tmpl.html", ["build"]);
watcher.on("change", function (event) {
  console.log("Event type: " + event.type);
  // added, change o deleted
  
  console.log("Event.path: " + event.path);
  // ruta del archivo modificado
});
```

Además del evento `change`, puedes atender a cierto número de eventos:
* `end`

Se dispara cuando termina el `watcher` -vigilante- (lo que significa que las tareas y funciones subordinadas no volverán a ser llamadas más cuando cambien los archivos)
* `error`

Se dispara cuando sucede un error.
* `ready`

Se dispara cuando se localizan los ficheros y están siendo observados.
* `nomatch`

Se dispara cuando la pseudo-cadena -glob- no apunta a ningún archivo.

El objeto `watcher` también contiene algunos métodos que puedes llamar:
* `watcher.end()`

Detiene el objeto (de manera que ya no se llamará a ninguna tarea ni a ninguna función subordinada -callback-)
* `watcher.files()`

Devuelve una lista de archivos que están siendo observados por el objeto `watcher`
* `watcher.add(glob)`

Añade archivos que coinciden con la pseudo-cadena especificada al objeto `watcher` (también acepta una función subordinada -callback- como un segundo argumento)
* `watcher.remove(filepath)`

Elimina un archivo concreto del objeto `watcher`

## Recarga de cambios en el navegador
Puedes hacer que Gulp recargue o actualice el navegador cuando tu o, para el caso, cualquier otra cosa, como una tarea Gulp, modifique un archivo. Hay dos maneras de hacerlo. La primera es usar el complemento `LiveReload`  y la segunda es usar `BrowserSync`.

### LiveReload -recarga activa-
[`LiveReload`](http://livereload.com/) se combina con extensiones del navegador (incluida [una extensión Chrome](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)) para recargar tu navegador cada vez que se detecta un cambio en el archivo. Puede usarse con la extensión [`gulp-watch`](https://www.npmjs.org/package/gulp-watch) o con el `gulp.watch()` incluido descrito antes. Aquí tienes un ejemplo del archivo README del [repositorio de `gulp-livereload`](https://github.com/vohof/gulp-livereload):

```javascript
var gulp = require('gulp'),
  less = require('gulp-less'),
  livereload = require('gulp-livereload'),
  watch = require('gulp-watch');

gulp.task('less', function() {
  gulp.src('less/*.less')
    .pipe(watch())
    .pipe(less())
    .pipe(gulp.dest('css'))
    .pipe(livereload());
});
```
Este código vigila los cambios en todos los archivos que coincidan con `less/*.less`. Cuando se detecta un cambio, se generará el correspondiente (archivo) `css`, lo archivará y recargará el navegador.

### BrowserSync -sincronización del navegador-
Hay una alternativa a `LifeReload`. [`BrowserSync`](http://browsersync.io/)es similar en cuanto que muestra los cambios en el navegador, pero dispone de numerosas funciones más.

Cuando modificas el código, `BrowserSync` o bien recarga la página, o, si se trata de `css`, inyecta el `css`, lo que significa que la página no necesita ser recargada. Esto resulta muy útil si tu página no es resistente a la recarga. Imagínate que estás trabajando en cuatro eventos click en tu aplicación de página única y que refrescando la página te encontrarías de nuevo en la página de inicio. Con `LiveReload` tendrías que hacer click cuatro veces cada vez que hicieras un cambio. `BrowserSync`, en cambio, simplemente inyectaría los cambios cuando modificaras el `css`, de manera que no necesitarías repetir los clicks. `BrowserSync` es mejor para probar tus diseños en varios navegadores.

`BrowserSync`también sincroniza los clicks, las acciones de formulario y la posición del cursor entre (varios) navegadores. Puedes abrir unos cuantos navegadores en tu equipo de sobremesa y otro en tu móvil y luego moverte por tu página. Los enlaces se podrían activar en todos ellos, y según bajaras por la página, descenderías por ella en todos tus dispositivos (y además, normalmente con suavidad). Cuando introdujeras texto en un formulario, se introduciría en todas las ventanas. Y cuando no quisieras que tuviera este comportamiento podrías desactivarlo.

`BrowserSyns` no requiere un complemento en el navegador, porque él se ocupa de servir tus archivos por ti (incluso a través de servidores intermedios -proxies- si son dinámicos) y sirve un archivo de órdenes -script- que abre una puerta -socket- entre el navegador y el servidor. Esto nunca me ha dado problemas en el pasado.

No existe en realidad un complemento para Gulp porque `BrowserSync` no manipula archivos, así que no podría funcionar como tal. Pero el módulo `BrowserSync`de `npm` puede ser llamado directamente desde Gulp. Lo primero es instalarlo via `npm`:
```shell
npm install --save-dev browser-sync
```
Después el siguiente archivo `gulpfile.js` iniciará `BrowserSync` y vigilará algunos archivos:
```javascript
var gulp = require('gulp'),
  browserSync = require('browser-sync');

gulp.task('browser-sync', function () {
  var files = [
    'app/**/*.html',
    'app/assets/css/**/*.css',
    'app/assets/imgs/**/*.png',
    'app/assets/js/**/*.js'
    ];

  browserSync.init(files, {
    server: {
      baseDir: './app'
      }
    });
});
```
La ejecución de `gulp browser-sync` vigilaría a los archivos coincidentes a la espera de cambios e iniciaría un servidor que serviría los archivos en el directorio `./app`.

El desarrollador de `BrowserSync` ha descrito en su repositorio de [BrowserSync + Gulp](https://github.com/shakyShane/gulp-browser-sync) algunas cosas más que puedes hacer (con este módulo).

## ¿Por que Gulp?
Como hemos mencionado, Gulp es una de las [varias herramientas](https://gist.github.com/callumacrae/9231589) de montaje disponibles en Javascript y también hay herramientas de montaje no escritas en Javascript, incluida Rake. ¿Por qué deberías usar Gulp?

Las dos herramientas de montaje más populares en Javascript son Grunt y Gulp. Grunt era muy popular en 2013 y cambio completamente como desarrollaba la gente páginas webs. Hay miles de complementos disponibles para ello, para hacer de todo desde limpiar, minimizar y concatenar código, hasta instalar paquetes usando Bower e iniciar un servidor Express. Este planteamiento es muy diferente del de Gulp, que solo tiene complementos para realizar pequeñas tareas individuales con archivos. Puesto que las tareas solo son Javascript (a diferencia del gran objeto que usa Grunt), no necesitas un complemento; simplemente puedes iniciar un servidor de Express.

Las tareas de Grunt tienden a estar sobre-configuradas, lo que requiere un objeto voluminoso que contendría propiedades de las que realmente no querrías tener que preocuparte, mientras que la misma tarea en Gulp puede suponer solo unas pocas lineas. Veamos un archivo simple `gruntfile.js` que define una tarea `css` para convertir nuestro LESS en CSS y después ejecutar [Autoprefixer ](https://github.com/ai/autoprefixer) sobre ello:
```javascript
grunt.initConfig({
  less: {
    development: {
      files: {
        "build/tmp/app.css": "assets/app.less"
        }
      }
    },

  autoprefixer: {
    options: {
      browsers: ['last 2 version', 'ie 8', 'ie 9']
      },
      multiple_files: {
        expand: true,
        flatten: true,
        src: 'build/tmp/app.css',
        dest: 'build/'
        }
      }
});

grunt.loadNpmTasks('grunt-contrib-less');
grunt.loadNpmTasks('grunt-autoprefixer');

grunt.registerTask('css', ['less', 'autoprefixer']);
```
Compáralo con el archivo `gulpfile.js` que hace lo mismo:
```javascript
var gulp = require('gulp'),
  less = require('gulp-less'),
  autoprefix = require('gulp-autoprefixer');

gulp.task('css', function () {
  gulp.src('assets/app.less')
      .pipe(less())
      .pipe(autoprefix('last 2 version', 'ie 8', 'ie 9'))
      .pipe(gulp.dest('build'));
});
```
La versión `gulpfile.js` es considerablemente más legible y pequeña.

Como Grunt accede al sistema de archivos con muchísima más frecuencia que Gulp, Gulp es casi siempre más rápido que Grunt. Para un pequeño archivo LESS, el archivo `gulpfile.js` tardaría unos 6 milisegundos. El archivo `gruntfile.js` tarda 50 milisegundos -más de ocho veces más. Este es un pequeño ejemplo, pero con archivos más grandes, la cantidad de tiempo se incrementa significativamente.
