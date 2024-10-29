# Repositorios remotos.

Un repo remoto es un repositorio EN PELOTAS (DESNUDO) - (No tiene ni WK, ni Staging... SOLO TENGO REPO) con el que vamos a compartir RAMAS.
Como se crea uno un repo remoto:
$ git init --bare
Inicializado repositorio Git vacío en /Users/ivan/formacionGit2/remoto/
$ git init
Inicializado repositorio Git vacío en /Users/ivan/formacionGit2/remoto/.git/

---

HOY vamos a trabajar con remotos... PERO mañana que seguiremos con algunas cosas nuevas necesitaremos:
- GITHUB *
- GITLAB
- BITBUCKET

---


# Stash

Es un parche (la diferencia entre lo que tengo en mi working dir y el repo) que guardo en un lugar seguro para poder recuperarlo más adelante.

git diff HEAD > Lo dejo guardado.. para poder aplicarlo más adelante

---

# TAGS

Con los tags controlamos las releases de mi producto.
Sirven para lo que yo quiera de hecho. SOLO SON ETIQUETAS que yo pongo a un commit.
Lo que pasa es que, independientemente de las etiquetas que quiera ir poniendo: bug-012827
habitualmente usamos etiquetas para controlar releases.         "v1.2.3"

## Versionado de software 

Hay muchas formas de versionar software. Habitualmente en los proyectos seguimos el estándar semver.org

    v.1.2.3

                Cuándo se actualizan(suben)?
MAJOR 1         Breaking changes. Al introducir cambios que rompen la compatibilidad con versiones anteriores
                    Aplico los deprecated: QUITO COSAS DEL API, con o si modificación
MINOR 2         Nuevas funcionalidades
                Marco cosas como @Deprecated
                    + Arreglos de bugs
PATCH 3         Bug fixes. Cuando arreglo BUG (o varios)


En un entorno de producción, si los desarrolladores me dicen que necesitan un MySQL 5.7.11, que versión monto yo? 5.7.11^

mysql: 5.7

---

Con git usamos el comando git tag, para etiquetar un commit.

Esto pondría el tag al último commit
$ git tag -a v1.0.0 -m "Primera versión estable"

Si lo quiero poner a otro: 
$ git tag -a v1.0.0 1234567 -m "Primera versión estable"

Podemos ver todos los tags
$ git tag

Podemos ver el tag concreto
$ git show v1.0.0

Podemos eliminar un tag
$ git tag -d v1.0.0

Para enviarlos al remoto hay que hacerlo explícitamente
$ git push origin v1.0.0
$ git push origin --tags
$ git push v1.0.0
$ git push --tags