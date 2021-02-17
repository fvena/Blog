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

- **Github Pages** - Nos permite publicar la documentación desde nuestro repositorio, sin costes económicos y casi sin configuración.
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

## Publicar la documentación en Github pages

Ahora que ya hemos documentado nuestra librería, el siguiente paso será publicarla para que todos puedan acceder a ella.

Puedes publicar tu documentación donde tu quieras, en tu propio servidor, en netlify, ... Para este tutorial vamos a utilizar github pages y volveremos a utilizar github actions para automatizar el proceso

Comenzaremos entrando en los ajustes de nuestro repositorio de Github
