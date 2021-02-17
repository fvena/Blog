# Desarrollar una librería Javascript con Rollup

Nuestro objetivo es desarrollar y publicar una librería que pueda utilizarse tanto en aplicaciones del lado del cliente como del servidor.

Necesitamos cumplir con los siguientes casos de uso:

1. La biblioteca está escrita en ES6+, usando `import` y `export`.
2. La biblioteca puede utilizarse con una etiqueta `<script>`.
3. La biblioteca puede utilizarse en una aplicación web que utilice un empaquetador moderno (webpack, Rollup, parcel).
4. La biblioteca puede utilizarse en una aplicación de node.

Técnicamente, significa que nuestra librería tiene que funcionar en lso siguientes contextos:

Utilizando la etiqueta `<script>`:

```html
<html>
  <head>
    <script src="scripts/my-library.min.js"></script>
  </head>

  <body>
    <div id="app" />
    <script>
      myLibrary.helloWorld();
    </script>
  </body>
</html>
```

En aplicaciones web utilizando ES6+:

```js
import myLibrary from "my-library";

myLibrary.helloWorld();
```

En una aplicación Node:

```js
const myLibrary = require("my-library");
myLibrary.helloWorld();
```

**Nota**: Queremos que nuestros usuarios llamen solo a los bits necesarios para su aplicación, recuerda exportar tu funcionalidad por separado cuando sea posible para reducir el tamaño de la aplicación.

```js
// Aplicación Web
import { helloWorld } from "my-library";

helloWorld();

// Aplicación Node
const { helloWorld } = require("my-library");
helloWorld();
```

Para conseguir todo esto utilizaremos Rollup, el motivo principal es que Rollup es muy rápido (aunque no el más rápido), requiere una configuración mínima y cumple con todos los requisitos.

Una vez que nuestra librería esté escrita, utilizaremos Rollup para exportar el código en los siguientes formatos:

1. **UMD (Universal Module Definition)**: Nos permite llamar a nuestra librería dentro de la etiqueta `<script>`. Proporciona una versión de nuestra librería minimizada y compilada para ser compatible con los navegadores.

2. **ESM (Module ES2015)**: Permite a los empaquetadores (webpack, Rollup, ...) importar nuestra librería, eliminar el código no utilizado y compilarla como elijan.

3. **CJS (CommonJS)**: Es el formato elegido para Node.js, este formato permite el uso de `require` para utilizar la librería

También generaremos un _source map_ por cada formato, para que nuestros usuarios puedan debuguear la librería si lo necesitan.

El primer paso será crear un proyecto:

```sh
mkdir my-library
cd my-library
npm init -y
```

Este comando ha generado un paquete json con la configuración mínima por defecto. Ahora continuamos añadiendo algunas dependencias:

Obiamente necesitamos Rollup.

```sh
npm install rollup --save-dev
```

Sabemos que necesitamos compilar el código para el formato UMD, por lo que instalaremos babel.

```sh
npm install @babel/core @babel/preset-env --save-dev
```

También necesitamos que Rollup utilice babel y minifique el código, para ello instalaremos los siguientes plugins.

```sh
npm install @rollup/plugin-babel rollup-plugin-terser --save-dev
```

Y finalmente, queremos que nuestra librería pueda utilizarse con `import` desde la carpeta `node_modules`.

```sh
npm install @rollup/plugin-node-resolve --save-dev
```

El listado final de dependencias de nuestra librería quedaría:

```json
"dependencies": {},
"devDependencies": {
  "@babel/core": "^7.12.10",
  "@babel/preset-env": "^7.12.11",
  "@rollup/plugin-babel": "^5.2.2",
  "@rollup/plugin-node-resolve": "^11.1.0",
  "rollup": "^2.38.1",
  "rollup-plugin-terser": "^7.0.2"
}
```

También necesitaremos un directorio para nuestro código, y un archivo de configuración para babel y otro para Rollup:

```sh
mkdir src
touch rollup.config.js
```

La configuración de babel será muy sencilla, solo necesitamos decirle a babel que queremos utilizar la versión más reciente de Javascript. Añadiremos el siguiente código al final de nuestro archivo `package.json`.

```json
{
  ...
  "presets": [["@babel/env", { "modules": false }]]
}
```

En nuestro archivo de configuración de rollup, comenzaremos importando los plugins necesarios:

```js
import { nodeResolve } from "@rollup/plugin-node-resolve";
import { terser } from "rollup-plugin-terser";
import babel from "@rollup/plugin-babel";
```

También vamos a importar el package.json, para utilizar algunos de sus campos.

```js
import pkg from "./package.json";
```

Luego vamos a crear un banner con información de la librería (nombre, versión, autor y licencia) que será añadido al final de nuestro archivo compilado, sería como añadir nuestra firma al final de un documento.

```js
const banner = `/*!
 * ${pkg.name} v${pkg.version}
 * (c) ${pkg.author}
 * Released under the ${pkg.license} License.
 */
`;
```

El siguiente paso es definir nuestra configuración de Rollup y exportarla:

```js
const config = [];

export default config;
```

Vamos a configurar Rollup para que haga dos cosas, por lo que añadiremos dos objetos de configuración:

Para UMD: Cogerá nuestro código, lo compilará con babel, le reducirá su tamaño con terser y lo exportará como un archivo UMD.

```js
{
  // UMD
  input: "src/index.js",
  plugins: [
    nodeResolve(),
    babel({
      babelHelpers: "bundled",
    }),
    terser(),
  ],
  output: {
    banner,
    file: `dist/${pkg.name}.min.js`,
    format: "umd",
    name: "myLibrary",
    esModule: false,
    exports: "named",
    sourcemap: true,
  },
},
```

Para CJS/ESM: Cogerá nuestro código, lo procesará y lo exportará como un módulo ESM/CJS. En este caso no necesitamos compilarlo o reducir su tamaño, ya lo hará el empaquetador del cliente cuando construya su aplicación.

```js
{
  // CJS/ESM
  input: ["src/index.js"],
  plugins: [nodeResolve()],
  output: [
    {
      banner,
      dir: "dist/esm",
      format: "esm",
      exports: "named",
      sourcemap: true,
    },
    {
      banner,
      dir: "dist/cjs",
      format: "cjs",
      exports: "named",
      sourcemap: true,
    },
  ],
},
```

La configuración de salida es muy simple, indicamos donde se guardarán los archivos, su formato y que se generé un `sourcemap` para que puedan ser debugueados.

La propiedad `exports: "named"` indica que estamos utilizando nombres a la hora de hacer nuestras exportaciones en vez de default. Esto permite que nuestra librería se pueda consumir por partes facilitando que nuestros usuarios solo utilicen una parte del código.

```js
const method1 = () => {
  console.log("Método 1");
};
const method2 = () => {
  console.log("Método 2");
};

// Exportación utilizando nombres
export { method1, method2 };

// Exportación utilizando default
export default { method1, method2 };
```

```js
// Importar cuando se exportó utilizando nombres
// Solo importo los métodos que me interesan
import { method1 } from "myLib";

method1();

// Importar cuando se exportó utilizando default
// Aunque no utilice el `method2` lo estoy importando igualmente a mi proyecto
import myLib from "myLib";

myLib.method1();
```

Si utilizas algún linter para tu librería asegurate de configurarlo con la opción de exportar como nombre (esto solo deberías hacerlo cuando desarrolles una librería, en una aplicación es correcto utilizar default o incluso una combinación de ambas).

El archivo completo de configuración de Rollup, quedaría:

```js
import { nodeResolve } from "@rollup/plugin-node-resolve";
import { terser } from "rollup-plugin-terser";
import babel from "@rollup/plugin-babel";
import pkg from "./package.json";

const input = ["src/index.js"];

const banner = `/*!
 * ${pkg.name} v${pkg.version}
 * (c) ${pkg.author}
 * Released under the ${pkg.license} License.
 */
`;

export default [
  // UMD
  {
    input,
    plugins: [
      nodeResolve(),
      babel({
        babelHelpers: "bundled",
      }),
      terser(),
    ],
    output: {
      banner,
      file: `dist/${pkg.name}.min.js`,
      format: "umd",
      name: "myLibrary", // this is the name of the global object
      esModule: false,
      exports: "named",
      sourcemap: true,
    },
  },

  // ESM and CJS
  {
    input,
    plugins: [nodeResolve()],
    output: [
      {
        banner,
        dir: "dist/esm",
        format: "esm",
        exports: "named",
        sourcemap: true,
      },
      {
        banner,
        dir: "dist/cjs",
        format: "cjs",
        exports: "named",
        sourcemap: true,
      },
    ],
  },
];
```

Ahora que tenemos nuestras dependencias instaladas y hemos configurado babel y rollup, es el momento de escribir nuestra librería.

Crearemos la siguiente estructura de archivos:

```
src
├── goodbye
│   ├── goodbye.js
│   └── index.js
├── hello
│   ├── hello.js
│   └── index.js
└── index.js
```

Y el código será extremadamente sencilla:

```js
// src/index.js
export { default as hello } from "./hello";
export { default as goodbye } from "./goodbye";

// src/hello/index.js
export { default } from "./hello";

// src/hello/hello.js
export default function hello() {
  console.log("hello");
}

// src/goodbye/index.js
export { default } from "./goodbye";

// src/goodbye/goodbye.js
export default function goodbye() {
  console.log("goodbye");
}
```

El siguiente paso será ejecutar Rollup y decirle que haga su trabajo. Vamos a crear dos scripts en nuestro archivo `package.json`, uno para construir la librería y otro para desarrollo que compilará la librería cada vez que hagamos un cambio.

```json
"scripts": {
  "build": "rollup -c",
  "dev": "rollup -c -w"
},
```

Por último, necesitamos indicar como se exportará la librería, para poder publicarla en npm y que puedan utilizar nuestros usuarios.

Vamos a modificar la propiedad `main` y añadir dos propiedades más a nuestro archivo `package.json`

```json
"main": "dist/cjs/index.js",
"module": "dist/esm/index.js",
"files": [
  "dist"
],
```

La propiedad `files` le dice a npm que archivos debe compartir cuando un usuario instale nuestra librería con `npm install`.

## Publicando nuestra librería con NPM

### Utilizar nuestra librería con la etiqueta `<script>`

Una vez que publiquemos nuestra librería con NPM podemos utilizarla con la etiqueta `<script>` con [https://unpkg.com](https://unpkg.com), que nos permite obtener la librería en formato UMD directamente desde el repositorio de NPM con un link del tipo: [unpkg.com/my-library/my-library.min.js](unpkg.com/my-library/my-library.min.js).
