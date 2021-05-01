# Documentar nuestra librería

Los proyectos de código abierto generan credibilidad y reputación, pero sin una documentación sólida, no tendrán mucho alcance. Parte del éxito de grandes proyectos de código abierto como React, Vue, Cordova,... se debe al tiempo y esfuerzo que han dedicado para tener una buena documentación.

A menudo los desarrolladores subestiman el arte de saber escribir documentos y el valor de tener una buena documentación. Muchas veces esperan que unos pocos ejemplos de código sean suficientes para que un recién llegado comprenda la librería y sepa como utilizarla.

Existen diferentes formas de documentar un librería:

## Readme

El archivo readme ofrece el primer contacto con los potenciales usuarios de nuestra librería. Debe ser breve e informativo para ayudarnos a vender nuestra librería.

Comience describiendo como puede ayudar la librería a los usuarios y que problemas resuelve. Puedes dar algunos ejemplos de casos de uso comunes que ayuden al usuario a entender su funcionalidad. Un párrafo de apertura bien escrito es un gran comienzo para cualquier archivo Readme. Si tu archivo es demasiado largo, es recomendable añadir una tabla de contenidos.

Si hemos realizado una librería sencilla, quizás podamos documentarla en el archivo `README.md`, pero si queremos realizar una documentación más completa con un diseño propio, deberemos desarrollar nuestra propia página de documentación.

Dependiendo del tiempo y las ganas que tengas, puedes optar por hacerte una página web desde cero con la documentación de tu librería o utilizar alguna de las muchas herramientas que existen para generar páginas web a partir de archivos markdown (vuepress, nuxt, next, ...).

Todas estás herramientas nos permite con una configuración mínima generar un sitio web a partir de archivos markdown, además suelen tener plantillas y componentes que pueden instalarse y rapidamente podemos tener una buena para desarrollar nuestra documentación.

En esta parte también podemos simplificar enormemente el proceso para conseguir un entorno que nos permita centrarnos unicamente en escribir la documentación y que reuna los siguientes requisitos:

- Configuración mínima y rápida
- Que pueda coexistir con nuestro proyecto
- Practicamente los únicos archivos que debería tener serían los markdown de documentación
- Diseño atractivo y fácil de modificar
- Debe aceptar markdown con superpoderes (mostrar código, demos, imágenes, videos, ...)
- Tiene que actualizarse automaticamene al lanzar una nueva versión
- No tengamos que preocuparnos de contratar un servidor y subir los archivos.
- Optimizado para el SEO

Aunque la lista parece muy larga podemos conseguirlo con un par de herramientas:

- **Github Pages** - Nos permite generar y publicar la documentación desde nuestro proyecto, sin costes económicos y casi sin configuración.
- **Vuepress ** - Nos da todo el en
