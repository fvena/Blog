# Test

## Instalar jest

Comenzaremos instalando `jest` y `babel-jest`. Recuerda que estamos usando la última versión de javascript en nuestra librería, y al igual que los navegadores, jest no la soporta, como ya estamos utilizando babel para transpilar el código, lo usamos también para los test.

```sh
npm install jest babel-jest --save-dev
```

El siguiente paso será configurar jest en nuestro `package.json`, siguiendo nuestra política de tener el mínimo número de archivos de configuración posible.

```json
"jest": {
  "collectCoverageFrom": [ "<rootDir>/src/**/*.js" ],
  "coverageReporters": [ "text", "html", "lcov" ],
  "testEnvironment": "node",
  "testMatch": [ "<rootDir>/tests/**/*.spec.js" ],
  "transform": {
    ".*\\.(js)$": "babel-jest"
  },
  "verbose": true,
  "watchPathIgnorePatterns": [ "<rootDir>/node_modules/" ]
},
```

Jest viene preconfigurado para poder ejecutar los test sin tener que hacer nada, no obstante vamos a hacer algunos cambios para optimizar la ejecución de los test y que :

- **testEnvironment** - Entorno por defecto para hacer los test. Debemos indicar a Jest que entorno debe utilizar para realizar los test, por defecto su valor es `jsdom` y lo usaremos cuando nuestra librería vaya a utilizarse en un navegador. Usaremos `node` cuando nuestra librería trabaje en node. Esta configuración se debe a la carga por defecto de determinadas variables globales según el entorno, como `document` que solo se encuentra en el entorno del navegador.

- **testMatch** - Array de patrones glob para que Jest detecte los test, por defecto su valor es `[ "**/__tests__/**/*.[jt]s?(x)", "**/?(*.)+(spec|test).[jt]s?(x)" ]`, es decir, busca cualquier archivo `.js` o `.ts` que se encuentre en la carpeta `__test__` o cualquier archivo en todo nuestro proyecto que termine en `spec.js`, `spec.ts`, `test.js`, `test.ts`. En nuestro caso y para optimizar, le vamos a indicar que nuestros test se encuentran dentro de la carpeta `tests` y terminan en `spec.js`.

- **transform** - Como estamos utilizando babel para transpilar nuestro código, queremos que jest también la utilice para pasar los test. Previamente hemos instalado el módulo `babel-jest` que permite a jest utilizar babel.

- **verbose** - Indica si en el terminal se muestra un listado con todos los test realizados o solo el resultado final. Sobretodo al principio, es interesante ver el listado de los test que se han pasado para ver que lo estamos haciendo correctamente

- **watchPathIgnorePatterns** - Cuando levantamos los test para que se pasen automáticamente al realizar cambios, podemos determinar que directorios no queremos que se tengan en cuenta, en nuestro caso, estamos indicando que no pase los test si hay cambios en el directorio `node_modules`.

Luego tenemos que decirle a eslint que vamos a utilizar jest, por lo que modificaremos su configuración en el archivo `.eslintrc.json`.

```json
{
  ...
  "env": {
    "browser": true,
    "es2021": true,
    "node": true,
    "jest": true
  },
}
```

El siguiente paso será automatizar los test para que se pasen siempre que hagamos un merge a la rama master, junto con el resto de acciones que ya teníamos definidas. En nuestro archivo `.github/workflows/test-and-release.yaml` le indicamos que pase los test justo después de instalar las dependencias:

```yaml
- name: Install dependencies
  run: npm ci

- name: Run tests
  run: npm run test

- name: Build library
  run: npm run build
```

Por último añadiremos los scripts en nuestro `package.json` para pasar los test:

```json
{
  ...
  "scripts": {
    ...
    "test": "jest",
    "test:watch": "jest --watch"
  },
}
```

## Cobertura de los test

Jest lleva preconfigurado la cobertura de los test, para activarla solo debemos modificar la configuración de jest y añadir un nuevo script que especifique que al pasar los test se calcule la cobertura de los test.

Vamos a nuestro archivo `package.json` y dentro de la configuración de jest añadimos los siguientes parámetros:

```json
{
  ...
  "jest": {
    "collectCoverageFrom": [ "<rootDir>/src/**/*.js" ],
    "coverageReporters": [
      "text",
      "html",
      "lcov"
    ],
    ...
  }
}
```

- **collectCoverageFrom** - Un array donde indicamos mediante patrones glob, que archivos deben tenerse en cuenta a la hora de calcular la cobertura de test. En nuestro caso solo queremos que tenga en cuenta los archivos javascript que se encuentran dentro de la carpeta `src`.

- **coverageReporters** - Lista con los diferentes tipos de informes con la cobertura de código.
  - **text** - Nos muestra un resumen en la consola al finalizar los test.
  - **html, lcov** - Muestra los resultados en una página web, será especialmente útil si luego subimos nuestra cobertura de test a alguna herramienta como codecov.

Cuando comprobamos la cobertura del código, se genera una carpeta `.coverage` con la información. En nuestro caso no queremos subir a nuestro repositorio nada que pueda autogenerarse, por lo que la añadiremos al archivo `.gitignore`.

```sh
# Dev/Build Artifacts
dist
coverage
```

Por último añadiremos es script para pasar nuestros y que a su vez nos calcule la cobertura de test en nuestro código.

```json
"scripts": {
  ...
  "test": "jest",
  "test:watch": "jest --watch",
  "test:coverage": "jest --coverage"
},
```

## Badge con la cobertura de test

Si queremos representar la cobertura de test de nuestro código como un badge, debemos realizar varios pasos:

1. Cuando pasemos los test de forma automática con las acciones de github, vamos a indicarle que pase el test con cobertura y que luego envíe el resultado a codecov.

```yaml
- name: Install dependencies
  run: npm ci

- name: Run tests
  run: npm run test:coverage

- name: Upload Coverage
  run: bash <(curl -s https://codecov.io/bash)

- name: Build library
  run: npm run build
```

**TODO** Añadir flujo para crearse una cuenta en codecov, vincularla y añadir el badge al readme.
