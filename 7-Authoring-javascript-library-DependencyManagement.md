# Gestión de dependencias

Para controlar las dependencias de nuestro proyecto, te recomiendo añadir `dependentabot`. Este bot comprueba automáticamente tus dependencias y te avisa si hay alguna actualización nueva.

Solo tienes que acceder a su web [dependentabot](https://app.dependabot.com/) con tu cuenta de GitHub. Luego dale acceso a tu repositorio.

Solo tienes que pulsar el botón `Create config file` el cual te mostrará una configuración por defecto, puedes copiar los datos y añadirlos manualmente en un fichero `.github/dependabot.yml`

[Configurar dependentabot en github](https://docs.github.com/en/github/managing-security-vulnerabilities/configuring-dependabot-security-updates)
[Tutorial](https://betterprogramming.pub/how-to-keep-your-dependencies-secure-and-up-to-date-92578c7f3c9c)

```yml
version: 2
updates:
- package-ecosystem: npm
  directory: "/"
  schedule:
    interval: weekly
    time: "04:00"
  open-pull-requests-limit: 10
```
