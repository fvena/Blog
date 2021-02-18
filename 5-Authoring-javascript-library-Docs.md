# Documentar nuestra librería

Los proyectos de código abierto generan credibilidad y reputación, pero sin una documentación sólida, no tendrán mucho alcance. Parte del éxito de grandes proyectos de código abierto como React, Vue, Cordova,... se debe al tiempo y esfuerzo que han dedicado para tener una buena documentación, en ocasiones, hay equipos dedicados unicamente a la documentación.

A menudo los desarrolladores subestiman el arte de saber escribir documentos y el valor de tener una buena documentación. Muchas veces esperan que unos pocos ejemplos de código sean suficientes para que un recién llegado comprenda la librería y sepa como utilizarla.

Existen diferentes formas de documentar un librería:

## Readme

El archivo readme ofrece el primer contacto con los potenciales usuarios de nuestra librería. Debe ser breve e informativo para ayudarnos a vender nuestra librería.

Comienza describiendo como puede ayudar tu librería a los usuarios y que problemas resuelve. Puedes dar algunos ejemplos de casos de uso comunes que ayuden al usuario a entender su funcionalidad.

Luego indica los pasos necesarios para instalar la librería, configurarla y algún ejemplo sencillo de como se utiliza.

Finalmente incluye el tipo de licencia y como pueden otros usuarios contribuir al proyecto.

Para este proyecto he creado dos archivos README, uno con la información de este proyecto y otro que sirva como plantilla para documentar la librería. Más adelante veremos como podemos eliminar la documentación de esta plantilla y sustituirla por la plantilla de forma automática.

## Guía

Si hemos realizado una librería sencilla, quizás podamos documentarla en el archivo `README.md`, pero si queremos realizar una documentación más completa con un diseño propio, deberemos desarrollar nuestra propia página de documentación.

En esta parte también podemos simplificar enormemente el proceso para conseguir un entorno que nos permita centrarnos unicamente en escribir la documentación y que reúna los siguientes requisitos:

- Configuración mínima y rápida
- Que pueda coexistir con nuestro proyecto
- Diseño atractivo y fácil de modificar
- Debe aceptar markdown con superpoderes (mostrar código, demos, imágenes, videos, ...)
- Sistema de navegación (router) para poder separar nuestra documentación en diferentes partes
- Tiene que actualizarse la versión online automáticamente cuando lancemos una nueva versión
- Olvidarnos de contratar un servidor, configurarlo, subir los cambios, ...
- Optimizado para el SEO

Aunque la lista parece muy larga podemos conseguirlo con las siguientes herramientas:

- **GitHub Pages** - Nos permite publicar la documentación desde nuestro repositorio, sin costes económicos y casi sin configuración.
- **Didor Docs** - Completa herramienta para documentar proyectos con estilo.
- **semantic-release** - Compila y publica la documentación automáticamente con cada nueva versión.

## Añadir Didor Docs

1. Instalamos Didor Docs:

```sh
npm install @didor\docs --save-dev
```

2. Añadimos dos scripts a nuestro package.json, uno nos permite lanzar la documentación de manera local mientras la estamos desarrollando, el otro compila una versión lista para ser publicada:

```json
{
  ...
  "scripts": {
    "dev": "rollup -c -w",
    "build": "rollup -c",
    "docs": "didor serve",
    "docs:build": "didor build"
  },
}
```

3. Creamos el directorio donde almacenaremos nuestra documentación y nuestro primer archivo de documentación:

```sh
mkdir docs
touch docs/README.md
```

4. Añadimos el siguiente código a nuestro archivo

```markdown
# Título

Contenido
```

Con esto ya tenemos nuestra documentación lista para empezar a desarrollarla, ahora lanzamos el siguiente scripts para comenzar a documentar:

```sh
npm run docs
```

## Publicar la documentación en GitHub pages

Ahora que ya hemos documentado nuestra librería, el siguiente paso será publicarla para que todos puedan acceder a ella.

Puedes publicar tu documentación en tu propio servidor, netlify, GitHub pages,... Para este tutorial vamos a utilizar GitHub pages y volveremos a utilizar GitHub Actions para automatizar el proceso.

El primer paso es entender que es GitHub Pages:

> GitHub Pages es un servidor de sitios estáticos que toma los archivos HTML, CSS y Javascript directamente de un repositorio. Por defecto utiliza la rama master pero podemos especificar otra rama.

GitHub Pages genera un dominio para acceder al sitio web con nuestro nombre de usuario y el nombre de nuestro proyecto: [https://fvena.github.io/javascript-library-starter/](https://fvena.github.io/javascript-library-starter/) (si queremos, podemos cambiar el dominio por uno propio).

En nuestro caso, no queremos que utilice el contenido que se encuentra en la raíz de nuestro proyecto ya que no se trata de una página web. GitHub Pages nos permitiría seleccionar un directorio que funcione como el `root` de nuestro servidor, pero esto nos obligaría a tener que añadir la documentación compilada a nuestro repositorio.

> No deberíamos añadir a nuestro repositorio contenido que pueda generarse automaticamente: node_modules, dist, distdocs, ...

El _truco_ consiste en crear una nueva rama que llamaremos `gh-pages` que solo contenga los archivos de la carpeta `distdocs`, y luego decirle a GitHub Pages que utilice la rama `gh-pages` en vez de la rama master.

Con GitHub actions podemos crear un flujo que lo haga por nosotros de forma automática cada vez que actualicemos la rama master.

Comenzaremos creando un nuevo flujo en nuestro directorio `.github/workflows` llamado `deploy-docs.yaml` con el siguiente contenido:

```yaml
name: Deploy docs

on:
  push:
    branches: [master]

jobs:
  Deploy:
    name: Deploy docs
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Install dependencies
        run: npm ci

      - name: Build documentation
        run: npm run docs:build

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./distdocs
```

Como podrás ver el flujo es muy parecido al que hicimos para automatizar las publicaciones de una nueva versión:

1. **Checkout** - Clona nuestro repositorio
2. **Install dependencias** - Instala las dependencias de nuestro proyecto con las versiones exactas que vienen en el archivo package-lock.json
3. **Build documentation** - Genera nuestra documentación en el directorio `distdocs`
4. **Deploy** - Crea una rama nueva con el nombre por defecto `gh-pages` con el contenido del directorio `distdocs`.

En el paso **Deploy** utilizamos una acción creada por otro usuario, como cuando utilizamos una librería con npm, su configuración es muy sencilla, solo tenemos que indicarle el directorio que debe publicar.

> Existen otros parámetros que podrían interesarte, miralos en la página [https://github.com/peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)

## Activar GitHub Pages para ver la documentación

Ya tenemos una nueva rama en nuestro repositorio llamada `gh-pages` con nuestra documentación compilada. El siguiente paso es habilitar GitHub Pages y decirle que utilice esta rama (este paso solo tendremos que hacerlo la primera vez).

1. Dirigite a tu repositorio de GitHub.

2. Debajo del nombre de tu repositorio, haz click en <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-gear" aria-label="The gear icon" role="img"><path fill-rule="evenodd" d="M7.429 1.525a6.593 6.593 0 011.142 0c.036.003.108.036.137.146l.289 1.105c.147.56.55.967.997 1.189.174.086.341.183.501.29.417.278.97.423 1.53.27l1.102-.303c.11-.03.175.016.195.046.219.31.41.641.573.989.014.031.022.11-.059.19l-.815.806c-.411.406-.562.957-.53 1.456a4.588 4.588 0 010 .582c-.032.499.119 1.05.53 1.456l.815.806c.08.08.073.159.059.19a6.494 6.494 0 01-.573.99c-.02.029-.086.074-.195.045l-1.103-.303c-.559-.153-1.112-.008-1.529.27-.16.107-.327.204-.5.29-.449.222-.851.628-.998 1.189l-.289 1.105c-.029.11-.101.143-.137.146a6.613 6.613 0 01-1.142 0c-.036-.003-.108-.037-.137-.146l-.289-1.105c-.147-.56-.55-.967-.997-1.189a4.502 4.502 0 01-.501-.29c-.417-.278-.97-.423-1.53-.27l-1.102.303c-.11.03-.175-.016-.195-.046a6.492 6.492 0 01-.573-.989c-.014-.031-.022-.11.059-.19l.815-.806c.411-.406.562-.957.53-1.456a4.587 4.587 0 010-.582c.032-.499-.119-1.05-.53-1.456l-.815-.806c-.08-.08-.073-.159-.059-.19a6.44 6.44 0 01.573-.99c.02-.029.086-.075.195-.045l1.103.303c.559.153 1.112.008 1.529-.27.16-.107.327-.204.5-.29.449-.222.851-.628.998-1.189l.289-1.105c.029-.11.101-.143.137-.146zM8 0c-.236 0-.47.01-.701.03-.743.065-1.29.615-1.458 1.261l-.29 1.106c-.017.066-.078.158-.211.224a5.994 5.994 0 00-.668.386c-.123.082-.233.09-.3.071L3.27 2.776c-.644-.177-1.392.02-1.82.63a7.977 7.977 0 00-.704 1.217c-.315.675-.111 1.422.363 1.891l.815.806c.05.048.098.147.088.294a6.084 6.084 0 000 .772c.01.147-.038.246-.088.294l-.815.806c-.474.469-.678 1.216-.363 1.891.2.428.436.835.704 1.218.428.609 1.176.806 1.82.63l1.103-.303c.066-.019.176-.011.299.071.213.143.436.272.668.386.133.066.194.158.212.224l.289 1.106c.169.646.715 1.196 1.458 1.26a8.094 8.094 0 001.402 0c.743-.064 1.29-.614 1.458-1.26l.29-1.106c.017-.066.078-.158.211-.224a5.98 5.98 0 00.668-.386c.123-.082.233-.09.3-.071l1.102.302c.644.177 1.392-.02 1.82-.63.268-.382.505-.789.704-1.217.315-.675.111-1.422-.364-1.891l-.814-.806c-.05-.048-.098-.147-.088-.294a6.1 6.1 0 000-.772c-.01-.147.039-.246.088-.294l.814-.806c.475-.469.679-1.216.364-1.891a7.992 7.992 0 00-.704-1.218c-.428-.609-1.176-.806-1.82-.63l-1.103.303c-.066.019-.176.011-.299-.071a5.991 5.991 0 00-.668-.386c-.133-.066-.194-.158-.212-.224L10.16 1.29C9.99.645 9.444.095 8.701.031A8.094 8.094 0 008 0zm1.5 8a1.5 1.5 0 11-3 0 1.5 1.5 0 013 0zM11 8a3 3 0 11-6 0 3 3 0 016 0z"></path></svg> **Settings**.

3. En la sección **GitHub Pages**, selecciona en el desplegable dentro de `Source` la rama **gh-pages**.

4. Dejamos `root` como directorio base y hacemos click en **Save**.

Cuando termine, nos mostrará la ruta de nuestra documentación: [https://fvena.github.io/javascript-library-starter/](https://fvena.github.io/javascript-library-starter/).

> Es posible que pasen algunos segundos antes de que el contenido sea visible.

## Licencia

Una licencia de software es un contrato entre el desarrollador y los posibles usuarios indicando los usos que pueden hacerse del software. Hay mucha literatura sobre esto, y muchas opciones, en nuestro caso, al tratarse de proyecto open source, elegiremos la licencia MIT (se originó en el MIT, _Masschusetts Institute of Technology_).

Básicamente esta licencia permite que cualquier usuario pueda usar, copiar, modificar, fusionar, publicar, distribuir y/o vender copias del software, es decir, que pueda hacer lo que quiera con el.

Es posible que en un primer momento pienses que es demasiado, es tu código, tu tiempo, tu esfuerzo y tu ilusión, y permitir que cualquiera pueda usarlo o hacerlo suyo te dé reparo, pero lo cierto, es que esta plantilla o casi cualquier proyecto que desarrolles está utilizando cientos de librerías de personas que las han compartido.

Vamos, que la mayoría no podríamos desarrollar nada sin la colaboración desinteresada de millones de desarrolladores, piensa que esta será tu pequeña contribución por todo lo que has recibido.

Añadir una licencia es bastante sencillo, simplemente generamos un archivo `LICENSE` en la raíz de nuestro proyecto y copiamos el texto de nuestra licencia, en nuestro caso:

```md
MIT License

Copyright (c) <YEAR> <COPYRIGHT HOLDER>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

<details>
  <summary><strong>Traducción español</strong></summary>

  ```md
  MIT License

  Copyright <AÑO> <TITULAR DEL COPYRIGHT>

  Por la presente se concede permiso, libre de cargos, a cualquier persona que obtenga una copia de este software y de los archivos de documentación asociados (el "Software"), a utilizar el Software sin restricción, incluyendo sin limitación los derechos a usar, copiar, modificar, fusionar, publicar, distribuir, sublicenciar, y/o vender copias del Software, y a permitir a las personas a las que se les proporcione el Software a hacer lo mismo, sujeto a las siguientes condiciones:

  El aviso de copyright anterior y este aviso de permiso se incluirán en todas las copias o partes sustanciales del Software.

  EL SOFTWARE SE PROPORCIONA "COMO ESTA", SIN GARANTÍA DE NINGÚN TIPO, EXPRESA O IMPLÍCITA, INCLUYENDO PERO NO LIMITADO A GARANTÍAS DE COMERCIALIZACIÓN, IDONEIDAD PARA UN PROPÓSITO PARTICULAR E INCUMPLIMIENTO. EN NINGÚN CASO LOS AUTORES O PROPIETARIOS DE LOS DERECHOS DE AUTOR SERÁN RESPONSABLES DE NINGUNA RECLAMACIÓN, DAÑOS U OTRAS RESPONSABILIDADES, YA SEA EN UNA ACCIÓN DE CONTRATO, AGRAVIO O CUALQUIER OTRO MOTIVO, DERIVADAS DE, FUERA DE O EN CONEXIÓN CON EL SOFTWARE O SU USO U OTRO TIPO DE ACCIONES EN EL SOFTWARE.
  ```

</details>

<br>

Solo tienes que sustituir en la línea 3 `YEAR` por el año en curso y `COPYRIGHT HOLDER` con tu nombre:

```md
Copyright (c) 2021 Francisco Vena
```

## Colaboración

La base de todo proyecto open source es la colaboración, facilitar a tus usuarios que puedan colaborar...

Vamos a añadir un par de plantillas de error, estas plantillas se mostrarán cuando un usuario quiera reportar un error en nuestro repositorio. Crearemos dos, una para reportar errores y otra para solicitar nueva funcionalidad.

GitHub automatiza mucho este proceso, solo tienes que crear la carpeta `.github/ISSUE_TEMPLATE` y añadir las plantillas que quieras, GitHub la mostrará automaticamente cuando un usuario pulse en crear un nuevo error.

```sh
touch .github/ISSUE_TEMPLATE/reportar-un-error.md
touch .github/ISSUE_TEMPLATE/solicitar-funcionalidad.md
```

```md
---
name: Reportar un error
about: Reporta un error para ayudarnos a mejorar
title: ''
labels: bug
assignees: fvena
---

**Describe el error**
Una descripción clara y concisa del error.

**Reproducir el error**
Pasos para reproducir el error:

1. Ir a '...'
2. Hacer click en '....'
3. Hacer scroll hasta '....'
4. Ver el error

**Comportamiento esperado**
Una descripción clara y concisa de lo que esperabas que ocurriese.

**Captura**
Si lo ves necesario, añade una captura de pantalla para ayudar a explicar el error.

**Escritorio (completa la siguiente información):**

- SO: [ej. iOS]
- Navegador [ej. chrome, safari]
- Versión [ej. 22]

**Móviles (completa la siguiente información):**

- Dispositivo: [ej. iPhone6]
- SO: [ej. iOS8.1]
- Navegador [ej. stock browser, safari]
- Versión [ej. 22]

**Información adicional**
Cualquier otra información que pueda ser útil para reproducir el error.
```

```md
---
name: Solicitar funcionalidad
about: Sugerir una idea para este proyecto
title: ''
labels: enhancement
assignees: fvena
---

**¿Su solicitud de una nueva funcionalidad está relacionada con un problema? Por favor describalo.**
Una descripción clara y concisa de cual es el problema. Ej. Estoy siempre cansado de [...]

**Describe la solución que te gustaría**
Una descripción clara y concisa de que quieres que ocurra.

**Describa otras alternativas que hayas considerado**
Una descripción clara y concisa de cualquier solución alternativa o funcionalidad que hayas considerado.

**Información adicional**
Añada cualquier otra información o captura que pueda ser útil.
```

También podemos crearlas automáticamente desde el propio repositorio.
