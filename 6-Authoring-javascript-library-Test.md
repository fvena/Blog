# Test

## Instalar jest

Comenzaremos instalando `jest` y `babel-jest`. Recuerda que estamos usando la última versión de javascript en nuestra librería, y al igual que los navegadores, jest no la soporta, como ya estamos utilizando babel para transpilar el código, lo usamos también para los test.

```sh
npm install jest babel-jest --save-dev
```

El siguiente paso será configurar jest en nuestro `package.json`, siguiendo nuestra política de tener el mínimo número de archivos de configuración posible.

```json
"jest": {
  "verbose": true,
  "collectCoverage": true,
  "coverageReporters": ["text"]
},
```

Jest viene preconfigurado para poder ejecutarlo sin tener que hacer nada, no obstante vamos a hacer un par de cambios:

* **verbose** - Indica si en el terminal se muestra un listado con todos los test realizados o solo el resultado final. Sobretodo al principio, es interesante ver el listado de los test que se han pasado para ver que lo estamos haciendo correctamente

* **collectCoverage** - Indica si se comprueba la cobertura del código testeado. Hay que tener en cuenta, que esto puede ralentizar bastante la ejecución de los test.

* **coverageReporters** - Indica como se mostrará el resultado de

[![HitCount](http://hits.dwyl.com/fvena/vite-plugin-i18n-resources.svg)](http://hits.dwyl.com/fvena/vite-plugin-i18n-resources)
