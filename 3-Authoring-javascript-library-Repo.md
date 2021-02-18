# Control de versiones

## Crear un repositorio en GitHub

Accede a tu cuenta de GitHub y pulsa sobre el botón `new repository`.

Completa los campos, y pulsa el botón `Create repository`

## Añadir el repositorio a nuestro proyecto

Una vez creado el repositorio, debemos vincularlo a nuestro proyecto, pero antes crearemos un archivo de configuración para git donde le indicaremos que archivos debe ignorar.

```sh
touch .gitignore
```

Ahora le indicamos que debe ignorar las carpetas `node_modules`, `dist` y todos los archivos del macOS generados automáticamente `DS_Store`.

```
# OS Files
.DS_Store

# Dependencies
node_modules

# Dev/Build Artifacts
dist
```

El siguiente paso será iniciar un repositorio local al que añadiremos nuestro repositorio de GitHub como repositorio remoto.

```sh
git init
git remote add origin https://github.com/fvena/javascript-library-template.git
```

Ten en cuenta que la ruta de tu repositorio será diferente a la mía, asegurate de copiarla correctamente y no utilices la del ejemplo.

Por último actualizaremos nuestro `package.json` con los datos de nuestro nuestro repositorio.

```json
{
  ...
  "description": "GitHub template for starting new javascript libraries",
  ...
  "author": "Francisco Vena <fvena32@gmail.com>",
  "license": "MIT",
  "keywords": [
    "template",
    "javascript",
    "library"
  ],
  "repository": {
    "type": "git",
    "url": "https://github.com/fvena/javascript-library-template"
  },
  "homepage": "https://github.com/fvena/javascript-library-template",
  "bugs": "https://github.com/fvena/javascript-library-template/issues",
}
```

## El arte de escribir los mensajes de los commits

Escribir buenos mensajes en nuestros commits puede considerarse un arte que lleva tiempo desarrollar, existen cientos de estrategias a la hora de escribir nuestros commits, pero al igual que utilizamos reglas de estilo con nuestro código y linters que las comprueben, podemos hacer lo mismo con los commits.

Vamos a utilizar [Conventional Commits](https://www.conventionalcommits.org/es/), se trata de un conjunto sencillo de reglas que permiten crear un historial de commits más explicito. Usar esta convención tiene muchas ventajas:

- Facilita mantener un historial de cambios (Changelog), ya que cada commit indica el tipo de cambio realizado.
- Permite determinar automáticamente los cambios de versión basado en los tipos de commits (Versionado semántico).
- Indica la naturaleza de los cambios a otros integrantes del equipo o usuarios de nuestra librería.
- Permite automatizar algunos procesos a la hora de su publicación
- Facilita que otras personas puedan contribuir al proyecto o exploren el proceso de desarrollo.

El mensaje del commit debe estar estructurado de la siguiente forma:

```
<tipo>[ámbito opcional]: <descripción>

[cuerpo opcional]

[nota de pie opcional]
```

El **tipo** indica la naturaleza de los cambios, por defecto existen dos tipos:

- **feat** - Cuando añadimos una nueva funcionalidad
- **fix** - Cuando solucionamos un error

Existen otros tipos permitidos, los más extendidos están basados en [the Angular convention](https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#-commit-message-guidelines). Nosotros también los utilizaremos para poder hacer commits más atómicos.

- **build** - Cambios que solo afectan al _desarrollo_ como añadir librerías y configurarlas (ESLint, Prettier, gulp, ...).
- **ci** (Continuous Integration) - Cambios relacionados con la integración continua de nuestro proyecto (travis, GitHub actions, Circle, ...).
- **docs** - Cambios solo en la documentación del proyecto
- **perf** (Performance) - Cambios en el código para mejorar el rendimiento de la aplicación
- **refactor** - Cambios en el código que no modifican la funcionalidad o solucionan errores
- **style** (Code style) - Cambios que afectan a la funcionaliad del código (punto y coma, espacios, tabulaciones, ...)
- **test** - Los cambios están relacionados con la creación y edición de los test

El **ámbito** es optativo, e indica que partes de nuestro código son afectadas por el commit. Podemos definir el ámbito de los cambios por el tipo de funcionalidad (autenticación, usuarios, tienda, blog,...), por la estructura de nuestro código (componentes, servicios, estilos, páginas) o cualquier otra agrupación que nos interese. Además, al definir el ámbito, las herramientas que generan el historial de cambios, los agruparan por el ámbito, dando como resultado un mejor historial de cambios.

La **descripción** debe ser corta e indicar los cambios realizados en el commit

Algunos ejemplos de commits:

```
fix(models): Al registrar un usuario que ya existe no se muestra un error
feat(models): añadido el módelo de Usuario
feat(services): añadido el servicio Productos
feat(services): añadido el servicio de Autenticación
```

## Automatizar y controlar el estilo de nuestros commits

Tener en cuenta todo esto a la hora de escribir nuestros commits, puede ser complicado, y mantenerlo a lo largo de todo el proyecto más aún. Por suerte tenemos algunas herramientas que nos facilitarán el trabajo:

- **[Commitizen](<https://github.com/commitizen/cz-cli>** - Es una herramienta que nos guía paso a paso a la hora de escribir los mensajes de nuestros de commits para que cumplan el formato definido.

- **[Commitlint](https://github.com/conventional-changelog/commitlint)** - Es un linter, que valida los mensajes de nuestros commits para asegurarse que están escritos con el formato correcto.

- **[Husky](https://github.com/typicode/husky/tree/master)** - Es una herramienta que nos permite ejecutar acciones antes o después de las acciones de git (git hooks), a la vez que interactura con los archivos afectados en cada acción (commit, pull, push, ...).

Comenzaremos instalando `commitlint` para comprobar si nuestros commits están escritos de forma correcta. Como todos los linters, `commitlint` tiene cientos de reglas que podemos configurar, pero podemos extender una configuración predefinida, así que también instalaremos un configuración que se adapte al estilo `conventional commits`.

```sh
npm install @commitlint/cli @commitlint/config-conventional --save-dev
```

Después añadairemos la configuración al final del archivo `package.json` donde le indicamos que herede la configuración de `config-conventional`.

```json
{
  ...
  "extends": ["@commitlint/config-conventional"]
}
```

Ahora mediante el comando `` podemos comprobar si el último cumple el estilo que hemos definido. Pero esto no es muy práctico, nosotros queremos que compruebe el mensaje cuando lo escribimos, y que en caso de no ser valido, cancele el commit.

Para comprobar nuestros commits antes de crearlos instalaremos `husky`, que nos permite ejecutar tareas con cada acción de git.

```sh
npm install husky --save-dev
```

Podemos configurar `husky` directamente en el archivo `package.json` y reducir el número de archivos de configuración. Comenzaremos indicándole que ejecute commitlint cuando escribamos el mensaje de commit, el cual viene dentro de la constante `HUSKY_GIT_PARAMS`.

```json
{
  ...
  "husky": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
}
```

Ahora, si intentamos realizar un commit que no cumple el formato especificado, recibiremos un error indicando que el título no es válido y el commit será cancelado.

```sh
git add .
git commit -m 'Commit con mensaje no válido'
```

Ya nos hemos asegurado que todos los commits van a cumplir un formato específico, pero recordar siempre como era o los tipos que existen puede ser complicado. Así que vamos a instalar `commitizen`, una herramienta que nos guía paso a paso para escribir el commit perfecto.

```sh
npm install commitizen --save-dev
```

También debemos indicar a `commitizen` que estilo de commits queremos utilizar para que nos pueda guiar correctamente. Vamos a utilizar el cli de `commitizen` para instalar y configurar automáticamente la configuración para `conventional commits`.

```sh
npx commitizen init cz-conventional-changelog --save-dev --save-exact
```

Este comando habrá añadido en nuestro `package.json` la siguiente configuración:

```json
{
  ...
  "config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
  }
}
```

También ha implementado un nuevo comando git `git cz` que nos permite realizar un commit utilizando la asistente paso a paso de `commitizen`. Pero recordar este nuevo comando y luchar contra años utilizando `git commit` puede ser una tarea dura, gracias a `husky` también podemos modificar este compartimiento para ejecutar `commitizen` con el comando `git commit`.

> Recuerda que hemos instalado `commitizen` a nivel de nuestro proyecto, esto implica que el comando `git cz` no funcionará, como tampoco funciona directamente ninguna otra librería descargada localmente.
> La forma más sencilla de probarlo sería añadir y ejecutar el script `"commit": "git-cz"`

```json
{
  ...
  "husky": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
    "prepare-commit-msg": "exec < /dev/tty && git cz --hook || true"
  }
}
```

A partir de ahora si realizamos un `git commit` se ejecutará el asistente que nos guiará paso a paso para escribir el mensaje de nuestro commit. No te preocupes si por inercia escribes algún mensaje al hacer el commit, este no se tendrá en cuenta.

El asistente cuenta con los siguientes pasos:

1. Seleccionar el tipo de error, puedes usar las flechas del teclado para moverte entre las distintas opciones.
2. Indicar el ámbito
3. Descripción corta del commit
4. Descripción larga, por si necesitamos explicar mejor o con más detalle los cambios realizados.
5. Indicar si hay `breaking changes`, es decir, los cambios no son compatibles con versiones anteriores y el usuario tendrá que modificar su código.
6. Indicar si el cambio afecta a algún error abierto (útil si gestionamos los errores a través de gitlab o GitHub).

> Al tener opciones por defecto o en algunos casos ser optativos podemos saltarnos un paso pulsando enter. También podemos cancelar el proceso y salirnos sin hacer el commit en cualquier paso pulsando `ctrl + c`.

## Mantener nuestro repositorio inmaculado

No deberiamos subir a nuestro repositorio código mal formateado o que no cumpla la guía de estilo de codificación definida en ESLint. Podemos utilizar asegurarnos que nuestro código pasa todos linters configurados antes realizar el commit y subirlo al repositorio. Para ello utilizaremos nuevamente `husky` y la herramienta `lint-staged` que como su propio nombre indica, nos permite ejecutar los linters sobre los archivos que se encuentran el `stage` de git .

```sh
npm install lint-staged --save-dev
```

El último paso será añadir la configuración de `lint-staged` e indicar a `husky` que la ejecute antes de hacer un commit. `lint-staged` nos permite definir que comando ejecutar por cada tipo de archivo y también podemos configurarlo dentro del archivo `package.json`.

```json
  ...
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
      "prepare-commit-msg": "exec < /dev/tty && git cz --hook || true"
    }
  },
  "lint-staged": {
    "*.js": "eslint --fix --debug",
    "*": "prettier -w -u"
  },
```

Por un lado hemos añadido un nuevo hook en `hasky` donde le indicamos que antes de realizar un commit ejecute `lint-staged`. Luego hemos configurado `lint-staged`:

- Comprobar con eslint todos los archivos javascript, y de paso solucionar los posibles errores de forma automática
- Comprobar y formatear cualquier tipo de archivo con prettier

> Recuerda que esta comprobación solo se realizará sobre los archivos que vamos a comitear (staged files), por lo que no es necesario especificar que directorios deben ignorar.

Después de todo esto, nuestro archivo `package.json` quedaría:

```json
{
  "name": "javascript-library-template",
  "version": "1.0.0",
  "description": "GitHub template for starting new javascript libraries",
  "main": "dist/cjs/index.js",
  "module": "dist/esm/index.js",
  "files": [
    "dist"
  ],
  "author": "Francisco Vena <fvena32@gmail.com>",
  "license": "MIT",
  "keywords": [
    "template",
    "javascript",
    "library"
  ],
  "repository": {
    "type": "git",
    "url": "https://github.com/fvena/javascript-library-template"
  },
  "homepage": "https://github.com/fvena/javascript-library-template",
  "bugs": "https://github.com/fvena/javascript-library-template/issues",
  "scripts": {
    "dev": "rollup -c -w",
    "build": "rollup -c"
  },
  "devDependencies": {
    ...
  },
  "babel": {
    "presets": [
      "@babel/env", { "modules": false }
    ]
  },
  "commitlint": {
    "extends": [
      "@commitlint/config-conventional"
    ]
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
      "prepare-commit-msg": "exec < /dev/tty && git cz --hook || true"
    }
  },
  "lint-staged": {
    "*.js": "eslint --fix --debug",
    "*": "prettier -w -u"
  },
  "config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
  }
}
```

El último paso será realizar un commit y comprobar que todo funciona correctamente:

```sh
git add .
git commit
```

## Errores con Husky

Puede que `husky` no te funcione correctamente, existen algunas cuestiones a tener en cuenta si esto ocurre.

1. `Husky` realiza cambios directamente en la propia carpeta oculta de .git, esto quiere decir que si instalamos `husky` antes de ejecutar `git init`, no se instalará correctamente.
2. Puede que no se hayan añadido correctamente los hooks a git o haya otros, en este caso, probaremos a eliminar los hooks de git y volveremos a instalar `husky`.

```sh
rm -rf .git/hooks/
npm uninstall husky
npm install --save-dev husky
```

3. La última opción se debe a que tengamos alguna versión instalada globalmente o a la versión de npm, en este caso actualizamos npm, borramos el repositorio local de git y la carpeta con las librerías, desinstalamos husky e iniciamos git otra vez, por último volvemos instalar husky.

```sh
git rev-parse --git-path hooks
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
rm -rf ./node_modules/
rm -rf .git
npm uninstall husky --save-dev
git init
npm install husky --save-dev
```

## Atomic commit

Un patrón muy extendido en programación a la hora de crear funciones es que la función solo haga una sola cosa. Podemos extrapolar este concepto a la hora de realizar commits. Seguro que alguna vez has realizado un commit donde había decenas de archivos con cientos de cambios, o uno donde solucionas varios errores a la vez.

Realizar commit más frecuentemente y más específicos nos puede facilitar el día de mañana a nosotros o a un compañero encontrar como solucionaste un error, o como implementaste una nueva funcionalidad.

El concepto de Atomic commit acepta muchas interpretaciones, hay gente que defiende que habría que hacer un commit por cada función que escribamos, en mi caso me vale con hacer un commit por funcionalidad, error o cambio acotado, aunque implique varios archivos. La cuestión es que te permita tener un mayor control y seguimiento de los cambios de tu proyecto.
