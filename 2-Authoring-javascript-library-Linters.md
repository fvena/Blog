# Configurar ESlint y Prettier en VS Code

Configurar el editor para que automáticamente vigile errores y corrija el formato de nuestro código, nos permite concentrarnos más en la funcionalidad que en el propio código.

Para conseguirlo utilizaremos dos herramientas:

- **ESlint** Es un programa que nos avisa de problemas en nuestro código al momento. Esto nos permite detectar errores sin tener que compilar o ejecutar nuestro código a la vez que también nos ayuda a mantener un mismo estilo de codificación a lo largo de todo el proyecto. También permite solucionar algunos errores de forma automáticamente.

- **Prettier** Es un formateador de código que aplica unas reglas predefinidas a nuestro código de forma automáticamente, normalmente se suele configurar para que se ejecute al guardar un archivo.

## Instalación

Comenzaremos instalando ESlint y Prettier.

```sh
npm install eslint prettier --save-dev
```

También necesitaremos crear los archivo de configuración de ESlint y Prettier:

```sh
touch .eslintrc.json
touch .prettierrc.json
```

Comenzaremos configurando ESLint, indicándole en que entornos se ejecutará nuestra librería y que versión de EMACScript vamos a utilizar para su desarrollo:

```json
{
  "env": {
    "browser": true,
    "es2021": true,
    "node": true
  },
  "parserOptions": {
    "ecmaVersion": 12,
    "sourceType": "module"
  }
}
```

- **env** - Define las variables globales predefinidas. Determinados entornos o frameworks, tienen variables globales predefinidas como `window` en los navegadores o `process` en Node.js. Para evitar que ESlint nos generé un error por no encontrar dicha variable debemos indicar los entornos en los que se ejecutará nuestra librería.
  En nuestro caso estamos indicando que añada las variables globales del navegador, node.js y la última versión de ECMAScript.

- **parserOptions** - Le dice a ESlint que versión de javascript vamos a utilizar, en nuestro utilizaremos la última versión de ECMAScript. También le estamos indicando que nuestra librería es un módulo EMACScript.

Por defecto ESlint utiliza [Espree](https://github.com/eslint/espree) para compilar el código, pero nosotros vamos a utilizar Babel y queremos que Eslint también la utilice. Para ello instalamos las siguientes librerías.

```sh
npm install babel-eslint eslint-plugin-import --save-dev
```

Y ahora añadimos en la configuración de ESlint la siguiente propiedad

```json
{
  ...
  "parser": "babel-eslint",
}
```

El siguiente paso sería definir las reglas de estilo que debe seguir nuestro código, pero con cientos de reglas posibles sería muy tedioso, por suerte, ESlint permite heredar (extender) estas reglas de otra configuración. Existen algunas guías de estilo muy populares, nosotros utilizaremos la de airbnb.

```sh
npm install eslint-config-airbnb-base --save-dev
```

Actualizaremos nuestra configuración de ESlint indicando que herede de la configuración base de airbnb.

```json
{
  ...
  "extends": ["airbnb-base"],
}
```

Prettier tiene sus propias reglas de estilo y algunas coinciden con las de ESlint, pudiendo provocar conflictos. Para hacer que se entiendan, instalaremos las siguientes librerías.

```sh
npm install eslint-config-prettier eslint-plugin-prettier --save-dev
```

- **eslint-config-prettier** - Configuración de ESlint que modifica algunas reglas para evitar conflictos con Prettier.
- **eslint-plugin-prettier** - Permite ejecutar Prettier como una regla de ESlint y que nos alerte de errores en nuestro código relacionados con las reglas de Prettier.

En nuestro archivo de configuración actualizaremos la propiedad `extends` y añadiremos las propiedad `plugins` y `rules`.

```json
{
  ...
  "extends": ["airbnb-base", "prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

Ten en cuenta que cuando heredamos de varias configuraciones el orden importa, y en caso de conflicto con una regla, siempre se utilizará la de la última configuración, por lo que en nuestro caso, si una regla difiere entre la configuración de airbnb y Prettier, se utilizará la de Prettier.

Finalmente nuestro archivo completo de configuración de ESlint quedaría:

```json
{
  "env": {
    "browser": true,
    "es2021": true,
    "node": true
  },
  "extends": ["airbnb-base", "prettier"],
  "parser": "babel-eslint",
  "parserOptions": {
    "ecmaVersion": 12,
    "sourceType": "module"
  },
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

El último paso será configurar Prettier. Si bien la configuración por defecto sería la más correcta y seguro está ampliamente justificada, en mi caso 80 caracteres como longitud máxima para una linea se me queda corto, y prefiero ir más holgado.

En el archivo de configuración de Prettier `.prettierrc.json` añadimos la siguiente configuración.

```json
{
  "printWidth": 120,
  "singleQuote": true
}
```

## Configurar el editor VS Code

Configurar y ajustar VS Code es todo un arte, hay cientos de tutoriales y videos al respecto, y en el fondo no deja de ser algo muy personal. No obstante podemos definir algunas opciones a nivel de proyecto para compartirlas con otros desarrolladores o mantenerlas a lo largo del tiempo.

Comenzaremos instalando las extensiones ESLint y Prettier de VS Code.

VS Code nos proporciona dos ámbitos (scopes) para sus ajustes:

- **Ajustes de Usuario** (User Settings) - Ajustes que se aplican globalmente a todos los proyectos.
- **Ajustes del Espacio de trabajo** (Workspace Settings) - Ajustes que solo se aplican en ese espacio de trabajo.

Los ajustes del espacio de trabajo sobreescriben los de usuario y son específicos de un proyecto.

Comenzaremos creando un espacio de trabajo, para ello abre en VS Code el directorio de tu proyecto y selecciona la opción guardar como espacio de trabajo del menú principal de VS Code: `File` > `Save Workspace As...`.

Esto genera el siguiente archivo con la información de tu espacio de trabajo, te aconsejo que modifiques el nombre por defecto `workspace.code-workspace` por el nombre de tu librería `my-library.code-workspace`, ya que este define el nombre de tu espacio de trabajo.

```json
{
  "folders": [
    {
      "path": "."
    }
  ],
  "settings": {}
}
```

La propiedad `folders` indica los directorios que componen el espacio de trabajo y en `settings` puedes añadir los ajustes de VS Code específicos para tu proyecto.

```json
{
  "folders": [
    {
      "path": "."
    }
  ],
  "settings": {
    // ===
    // Eslint + Prettier
    // ===
    "editor.formatOnSave": true,
    "editor.formatOnPaste": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": false,
      "source.fixAll.eslint": true,
      "source.fixAll.stylelint": true
    },
    "eslint.alwaysShowStatus": true,
    "eslint.run": "onSave",
    "eslint.validate": ["javascript"],
    "prettier.singleQuote": true,
    "prettier.printWidth": 120,
  }
}

Básicamente todos los ajustes de Eslint y Prettier están orientados a corregir los posibles errores en el momento de guardar un archivo.
```

## Scripts NPM

Es común encontrar en los proyectos scripts para pasar los linters o prettier, pero en nuestro los hemos configurado para que se pasen automáticamente al guardar los cambios y además en la siguiente parte, añadiremos a git la capacidad de pasarlos antes de realizar un commit.

De esta forma mantenemos al mínimo la interfaz de nuestro proyecto.
