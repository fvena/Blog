# Publicar tu librería

A la hora de crear una librería, no deberías pensar solo en su desarrollo, también tienes que tener en cuenta como vas a publicarla y mantenerla para que puedan utilizarla tus usuarios. Tener un proceso de lanzamientos sólido como una roca implica tener en cuenta:

1. **Registro de cambios** - Todos los cambios de tu proyecto deben estar reflejados y recogidos para que otros usuarios puedan seguir el proceso de desarrollo.
2. **Versionado** - Mantener un sistema de nombre de versiones que permita a los usuarios diferenciar cada uno o saber que tipo de cambios introduce cada versión.
3. **Publicación** - Cada nueva versión debe publicarse inmediatamente para que tus usuarios puedan actualizar sus proyectos, pero publicar una librería puede tener muchas tareas asociadas: pasar test, publicarla en npm, actualizar la documentación, ...

Podemos automatizar todo este proceso con la herramienta [semantic-release](https://github.com/semantic-release/semantic-release) para que lo haga por nosotros. Pero antes de hablar sobre la implementación, es importante entender como trabaja y definir algunos conceptos para que podamos personalizarlos o solucionar problemas.

## Formato de los mensajes de los commit

**semantic-release** utiliza los mensajes de los commit para determinar el tipo de cambios en el código. Siguiendo unas convenciones para los mensajes de los commit, semanti-release automaticamente determina el siguiente número de versión, genera un registro de los cambios y publica una nueva versión.

Por defecto, semantic-release utiliza la convención [Angular commit message conventions](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines). Nosotros en la sección anterior, configuramos nuestro proyecto con varias herramientas que nos permiten validar los commits.

### Versionado semántico

`semantic-release` utiliza el **[versionado semántico](https://semver.org/lang/es/)** para determinar el número de versión. El versionado semántico es un convenio o estándar a la hora de definir la versión de tu código, dependiendo de la naturaleza del cambio que estás introduciendo, se identifican 3 tipos de cambios:

- **Parche** - Cuando arreglamos algún error error en la librería siendo el cambio retrocompatible, es decir, al actualizar la librería deben solucionarse los errores sin que el usuario tenga que modificar su código.
- **Menor** - Cambio que añade alguna característica nueva al software o modifica alguna ya existente, pero que sigue siendo compatible con el código existente. El usuario tampoco tendría que modificar su código si actualiza la librería. Un cambio menor también puede incluir soluciones de errores.
- **Mayor** - Cambio drástico en el software. No es compatible con el código de versiones anteriores, en este caso, si el usuario actualiza la librería deberá hacer cambios en su código o no funcionará. Un cambio de versión mayor también puede incluir nuevas funcionalidades y solución de errores.

El número de versión se compone de tres cifras separadas por un punto, y cada tipo de cambio se vé corresponde con una cifra, `MAYOR`.`MENOR`.`PARCHE`, de forma que si tenemos la versión `1.3.7` y queremos actualizar a la versión `1.3.9`, sabemos que solo se han corregido errores y que no tendríamos que modificar nuestro código.

De esta forma, tenemos un lenguaje común entre desarrolladores y consumidores a la hora de hablar de versiones. Y un usuario puede saber que tipo de cambios tendrá una nueva versión, y si necesitaría tocar su código.

> **Versión 0.X.X**
> Cuando el número de versión mayor es 0, indica que es una versión en desarrollo, y por lo tanto está sujeta a frecuentes cambios que pueden implicar cambios en el uso de la librería obligando al usuario a modificar el código.

## Acciones

Además `semantic-release` realiza otras muchas acciones por defecto por ti:

| Acción                    | Descripción                                                                        |
| ------------------------- | ---------------------------------------------------------------------------------- |
| Verificar condiciones     | Verifica todas las condiciones para iniciar la publicación                         |
| Obtener la última versión | Obtiene el commit correspondiente a la última versión                              |
| Analizar los commits      | Determina el tipo de versión en base al listado de commits desde la última versión |
| Verificar la versión      | Comprueba los criterios para lanzar una nueva versión                              |
| Generar anotaciones       | Genera la descripción de la versión                                                |
| Crear la etiqueta Git     | Crea la etiqueta Git correspondiente a la nueva versión                            |
| Preparación               | Prepara la nueva versión                                                           |
| Publicación               | Publica la nueva versión                                                           |
| Notificación              | Notifica las nuevas versiones o errores                                            |

## Integración continua (CI)

`semantic-release` hará todo el trabajo por nosotros, pero esto puede llevar un rato, especialmente si tiene que realizar test o realizar muchas comprobaciones, y lo que queremos después de publicar una nueva versión es apagar el ordenador e irnos a tomarn algo.

Sería mucho mejor que todo esto se realizara en un servicio en la nube y sería aún mejor si se ejecute automáticamente cada vez que mergeemos un pull-request en la master.

Aquí entra en juego las acciones de GitHub, donde definiremos todas las tareas que tiene que realizar para hacer una publicación.

## Requisitos previos

Antes de poder automatizar la publicación de nuestra librería, tenemos que comprobar algunos campos de nuestro `package.json` para que `semantic-release` pueda ejecutarse correctamente:

- [ ] El campo `version` debe ser inicialmente `0.0.0-development`
- [ ] El campo `name` indica el nombre de nuestra librería y debe coincidir con el de nuestro repositorio.
- [ ] El campo `repository` debe estar definido con la url de nuestro repositorio

```json
{
  "name": "javascript-library-starter",
  "version": "1.0.0",
  "repository": {
    "type": "git",
    "url": "https://github.com/fvena/javascript-library-starter"
  },
  ...
}
```

## Configurar semantic release

**semantic-release** tiene varias opciones de configuración que nos permiten configurar su flujo de trabajo, en nuestro caso nos quedaremos con las opciones por defecto, aunque añadiremos un par de plugins. En el archivo `package.json` añadiremos la siguiente configuración:

```json
{
  ...
  "release": {
    "plugins": [
      "@semantic-release/commit-analyzer",
      "@semantic-release/release-notes-generator",
      "@semantic-release/npm",
      "@semantic-release/github",
      "@semantic-release/changelog",
      "@semantic-release/git"
    ]
  }
}
```

Los plugins por defecto utilizados por semantic-release son:

- **@semantic-release/commit-analyzer** - Determina el tipo de versión analizando los commits
- **@semantic-release/release-notes-generator** - Genera las notas de la versión con los cambios en base a los commits
- **@semantic-release/npm** - Publica la nueva versión en NPM
- **@semantic-release/github** - Publica la nueva versión en GitHub

Aunque estos plugins están configurados por defecto, si añadimos otros plugins debemos indicarlos para que semantic-release sepa el orden en el que deben ejecutarse. Sino los añades, posiblemente no funcionen los que hayas elegido.

Aparte nosotros vamos a añadir otros dos plugins:

- **@semantic-release/changelog** - Crea y actualiza el archivo `CHANGELOG.md` con los cambios de la versiones
- **@semantic-release/git** - Genera un commit y actualiza el repositorio con los cambios en el archivo `CHANGELOG.md` y `package.json`.

## Acciones de GitHub

**TODO** Explicar que son las acciones de GitHub

El siguiente paso será definir las tareas que tiene que realizar GitHub cada vez que hagamos un push a la rama master. Para definir las acciones de GitHub, crearemos un directorio `.github` en la raíz de nuestro proyecto, y dentro de este creamos un nuevo directorio llamado `workflows` donde añadiremos el archivo yaml `release.yml`.

El directorio `.github` es el predeterminado por GitHub para buscar todo lo relacionado con su configuración, en futuras versiones añadiremos más cosas. GitHub buscará por defecto los workflows en la carpeta `workflows`. Podemos utilizar el nombre que queramos para el archivo, ya que definiremos dentro el nombre para nuestro workflow.

```sh
touch .github/workflows/release.yml
```

```yaml
name: Release

on:
  push:
    branches: [master]

jobs:
  Release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Install dependencies
        run: npm ci

      - name: Build library
        run: npm run build

      - name: Run Semantic Release
        run: npx semantic-release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

Vamos a analizarlo por partes

```yaml
name: Release
```

Le da nombre al workflos, elige un nombre que tenga sentido para ti y te permita identificarlo, eres libre de escribir lo que quieras.

```yaml
on:
  push:
    branches: [master]
```

La propiedad `on` le dice a GitHub cuando debe ejecutar el workflow, en este caso hemos especificado que se ejecute cuando hagamos un push a la rama master.

```yaml
jobs:
  Release:
    runs-on: ubuntu-latest
```

La propiedad `jobs` agrupa las diferentes tareas que vamos a realizar, por ahora solo vamos a definir una, encargada de publicar una versión.

Después indicamos el sistema operativo de nuestra máquina virtual donde se va a ejecutar nuestro workflow, como que estamos utilizando `node.js` para desarrollar nuestra librería, especificamos que queremos utilizar la última versión de ubuntu.

A continuación debemos indicar los pasos necesarios para realizar nuestra tarea

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v2

  - name: Install dependencies
    run: npm ci

  - name: Build library
    run: npm run build

  - name: Run Semantic Release
    run: npx semantic-release
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

1. **Checkout** - Clona nuestro repositorio
2. **Install dependencias** - Instala las dependencias de nuestro proyecto con las versiones exactas que vienen en el archivo package-lock.json
3. **Build library** - Compila nuestra librería
4. **Run Semantic Release** - Ejecuta semantic release para que haga todo lo que le hemos pedido:

   - Calcular el nuevo número de versión
   - Generar las notas con el cambio de versión
   - Publicar la librería en GitHub y NPM
   - Generar el archivo CHANGELOG.md
   - Realizar un commit para guardar los cambios en nuestro repositorio

Dado que algunos pasos necesitan autenticación y no hay ninguna persona sentada mirando la ejecución del script que pueda introducir nuestro nombre de usuario y contraseña, debemos pasar un token válido que le de permiso para realizar las acciones que le hemos pedido.

## Obtener los tokens de autenticación

Por seguridad, nunca introducimos aquí nuestros token, ya que cualquiera que inspeccione el repositorio podría verlos, en su lugar utilizaremos unas variables de entorno específicas de GitHub, que nos permiten almacenarlas en el proyecto de forma segura.

Dado que el workflow se ejecuta dentro de nuestro repositorio de GitHub, y tenemos que ser un usuario autorizado para poder hacer push o pull-request, no es necesario que obtengamos un token para GitHub, por defecto, GitHub genera uno de forma automática de forma transparente para nosotros, por lo que solo nos quedaría obtener un token valido de NPM y configurarlo en nuestro proyecto.

Para obtener un token de NPM, vamos a nuestra cuenta y seleccionamos la opción `Auth Tokens` de nuestro menú de usuario. Una vez dentro seleccionamos `Create New Token`-

A continuación, te preguntará que tipo de token quieres crear, **TODO**

Copia el token que has creado y vamos a añadirlo a nuestro repositorio.

## Añadir el token NPM a nuestro repositorio

De vuelta a nuestro repositorio de GitHub, dentro de los ajustes del repositorio seleccionamos la pestaña `Secrets` y hacemos click en `New Secret` para añadir una nueva variable de entorno secreta en nuestro repositorio.

En nombre introducimos `NPM_TOKEN` y en value pegamos nuestro token. Una vez guardemos nuestra variable secret, no podremos volver a verla, y solo los usuarios admin con acceso al repositorio podrían modificarla en un futuro.

## Publicar nuestra primera versión

Ya solo nos queda probar que todo funciona. Vamos a nuestro proyecto, realiza un nuevo commit con los últimos cambios y subelos a la rama master de tu repositorio.

Si todo ha ido bien, dirigete a la pestaña `actions` de tu repositiorio y verás como se está ejecutando tu workflow ...

**TODO** Añadir imágenes de todo el proceso <https://medium.com/javascript-in-plain-english/publish-update-npm-packages-with-github-actions-e4d786ffd62a>

## Flujo de desarrollo

Por defecto la primera versión que se crea es la 1.0.0 [Explicación](https://blog.greenkeeper.io/introduction-to-semver-d272990c44f2)

**TODO** Definir el flujo para crear versiones beta/alpha y continuar el desarrollo con la versión 1.0.1-beta

- [https://blog.greenkeeper.io/introduction-to-semver-d272990c44f2](<https://blog.greenkeeper.io/introduction-to-semver-d272990c44f2>

- [https://github.com/semantic-release/semantic-release/blob/master/docs/recipes/pre-releases.md](https://github.com/semantic-release/semantic-release/blob/master/docs/recipes/pre-releases.md))
