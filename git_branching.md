# Git branching

Definir una estrategia para gestionar tu repositorio es clave, pueden existir tantas estrategias como desarrolladores, pero las más comunes son:

* [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)
* [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html)
* [GitLab Flow](https://about.gitlab.com/topics/version-control/what-is-gitlab-flow/)
* [One Flow](https://www.endoflineblog.com/oneflow-a-git-branching-model-and-workflow)

Si bien **Git Flow** es la más conocida, no te la recomiendo para desarrollar tu librería, ya que está pensado principalmente en tener varias versiones activas. Seguramente tu librería no tendrá distintas versiones que mantener, simplemente tendrás una única versión que irás mejorando a lo largo del tiempo y que siempre estará actualizada.

Yo te recomiendo el flujo de **Github**, es simple y sencillo pero muy efectivo, se basa en los siguientes principios

**1. Todo lo que haya en la rama master está en producción**
La rama master siempre está actualizada y desplegada en producción, en el peor de los casos será desplegada en unas horas.

**2. Trabaja siempre en una rama nueva a partir de la rama master**

Siempre que quieras desarrollar una nueva funcionalidad o solucionar un problema trabaja en una rama nueva que parta de la rama master. La rama master siempre estará actualizada, por lo que tienes la seguridad que partes con la última versión.

Utiliza siempre nombres descriptivos para tus ramas, del tipo `add-avatar` o `add-login-page` en vez de `dev`, no solo te permitirá saber que estabas haciendo en una rama si la has aparcado un tiempo, también te permitirá saber en que están trabajando tus compañeros.

**3. Realiza los commits en tu rama local y sube regularmente los cambios a la misma rama en el servidor**

Recuerda hacer commits y subirlos frecuentemente, te facilitarán la vida si tienes que deshacer cambios, revisarlos, pedir ayuda a un compañero o si tu equipo sale ardiendo. Además no van a romper nada porque están en su propia rama.

También te permite con un simple `git fetch` saber en que están trabajando tus compañeros.

**4. Realiza un `pull request` a la master**

Un `pull request` sirve para que otra persona valide el código antes de fusionarlo en la rama master. Es recomendable que al menos una persona apruebe los cambios antes.

Recuerda actualizar tu rama con los cambios de la rama master antes de realizar el `pull request`, te permitirá resolver conflictos, o posibles errores provocados por los cambios realizados por otros desarrolladores.

**5. Publica los cambios inmediatamente**

Una vez que hemos fusionado los cambios en la rama master debemos publicarlos para que siempre esté disponible la última versión de nuestra librería. Personalmente prefiero olvidarme de esta parte y automatizarla con `semantic-release` + `github actions`.

Puedes añadir un bot que comunique al resto de desarrolladores que hay una versión nueva, para que sepan que tienen que actualizar su rama antes de hacer el pull request.

**6. Nunca volveremos a un commit anterior**

Si hay un problema con el último commit, lo revertiremos o añadiremos uno nuevo que solucione el problema. Recuerda que la rama master se utiliza como punto de partida para desarrollar una nueva funcionalidad, si partimos de un commit que después ha sido eliminado, te provocará muchos dolores de cabeza.

```sh
# Elimina el commit, pero se mantienen los cambios que vuelven a aparecer en el stage de git
git reset --soft HEAD~1

# Elimina el commit y los cambios realizados
git reset --hard HEAD~1
```

## Conclusión

El objetivo es tener un flujo que nos permita centrarnos en lo verdaderamente importante, desarrollar nuestra idea, dedicarle mucho tiempo y capacidad en mantener complicados flujos de trabajo con git, no es rentable.

Comienza con un sistema sencillo y luego ve añadiendo complejidad si tu proyecto te lo pide: diferentes releases, versionas alpha y beta, ...
