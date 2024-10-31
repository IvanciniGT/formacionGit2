En los remotos vamos a poder dar permisos a usuarios, para que puedan interactuar (WRITE/READ) a nuestro repositorio: Se soporta en GITHUB, GITLAB, BITBUCKET

También nos ofrecen la posibilidad de proteger RAMAS frente a escritura, para que no se puedan hacer cambios directamente en ellas (En este caso nombramos a los autorizados a hacerlo)
    - rama master/main
      Antiguamente en el mundo de la informática se usaban mucho los términos master/slave, El movimiento WOKE ha hecho que se cambie a main. Esos términos se han reemplazado por main/workers
    - rama develop
    - rama release
    - Muy probablemente vamos a proteger las ramas hotfix

    En el mundo ideal nadie debería de poder trabajar con esas ramas. NADIE , NADIE, de nadie debería poder hacer ningún cambio en esas ramas.

    Entonces, cómo sube un commit a master? Si NADIE puede crearlo, ni subirlo (con un merge ff)???
        JENKINS, GITHUB ACTIONS, GITLAB CI/CD, BAMBOO <- PROGRAMAS NO HUMANOS

# Merge / Pull request

Entonces... cómo pasan cosas a develop? MERGE / PULL REQUEST

Solicitudes de fusión de cambios. En GITHUB se llaman Pull Request, en GITLAB Merge Request, en BITBUCKET Pull Request.

Eso es una funcionalidad que no es de git. Es una funcionalidad de los servicios de alojamiento de repositorios remotos. Básicamente me permiten a mi solicitar la incorporación de unos cambios (PATCH) al último commit de una rama del repo... que estará protegida.

Cuando hago una solicitud de fusión de cambios, indico:
- Commit origen
- Rama destino (se entiende que la rama destino es la rama protegida y quiero aplicar el cambio al último commit de esa rama)
- Explicaré un poco lo que estoy metiendo (en el mensaje de la solicitud)

Eso va a dar lugar a un proceso BUROCRATICO. Estas herramientas me generarán un hilo de discusión, donde se podrá comentar el cambio, se podrá hacer un análisis de código, se podrá hacer un análisis de calidad, pruebas... y cuando todo esté OK, la persona a la que mande la solicitud de fusión de cambios, la ejecutará.

Normalmente estas herramientas me informan de si la incorporación de los cambios generaría conflictos o no. Si generan conflictos, me tocará a mi resolverlos antes de que se pueda hacer la fusión.

# Fork
                            
                                           (Documentacion)
--------dev-------C10----C11 --- C14 --------------- C13'
                                    \                / ^^ REBASE+ff | MERGE TRADICIONAL | MERGE SQUASH
                                    C14' --C12' -- C13'
                                            antes de comer
                                                    antes de cenar

El problema es que nosotros es lo hemos podido hacer ya que: teníamos una rama "rama" dentro del repo... que si hemos podido crear, lo que no podía era hacer la fusión de los cambios en la rama protegida.

El problema es que puede que no tenga NI SIQUIERA permisos para abrir nuevas ramas (ES EL MUNDO OPENSOURCE), aquí sale el concepto de FORK.
Un FORK es una copia que hago de un repo, a mi nombre. Y como está a mi nombre, puedo crear las ramas que quiera y jugar como quiera con ellas.

Lo que me ofrecen estas herramientas (GITLAB, GITHUB, BITBUCKET) es la posibilidad de realizar una fusión de cambios desde un repo forkeado a un repo original.
                                Iván
    Menchu                      Hacer una PR(MR) que apunte (cuyo origen) sea una rama de un repo forkeado de este.
    REPO ORIGINAL               PR (Origen: repoForkeado/rama) (Destino: este repo/develop)
        \
        fork
           \
           REPO COPIA DEL ORIGINAL PIRATA
           Ivan             \                       
                            rama ivan ---> C18---> C19


---


# Git blame

Identificar la última persona que modificó cada línea de un archivo.

$ git blame -L 10,20 archivo

# Git bisect

Me permite localizar el commit culpable de un bug.
En ocasiones (en proyectos grandes, con mucho historial) no es fácil identificar al commit culpable de un bug. 
Para trabajar bien con este comando, necesitaríamos algún tipo de script que nos permita identificar si el bug está presente o no en un commit: PRUEBA AUTOMATIZADA.

Lo que hace es una búsqueda binaria en el repo = BUSQUEDA EN UN DICCIONARIO

$ git bisect start         # Arrancamos un proceso de búsqueda binaria
# Me posicionaré en un commit que sepá que tiene el error... suele ser el último
                            $ git bisect bad HEAD          # Indicamos que el commit actual tiene el bug
$ git bisect bad

Y marco un commit en el que sé que funcionaba lo que sea.

$ git bisect good 3f4e2d3

10          BAD
9
8
7                   < CHECKOUT ... git bisect good/bad
6
5                   < CHECKOUT ... git bisect good/bad
4           GOOD
3
2
1

Lo ideal, es que después del good y el bad inicial, ejecutase:

$ git bisect run ./script.sh  # JUNIT, MSTEST

$ git bisect reset

# Git worktree

Me permite tener abiertas varios WORKING DIR apuntando a ramas diferentes en paralelo en mi maquina..

$ git worktree add ../nuevacarpeta rama2

$ git worktree add -b rama-hotfix1 ../nuevacarpeta

$ git worktree remove ../nuevacarpeta

$ git worktree list

Si se me ocurre borrar la carpeta a nivel de SO, sin usar git worktree remove, se me queda en un estado inconsistent el repo. En este caso ejecuto:

$ git worktree prune

---

# Submodulos de git

Me permiten tener un repositorio, y que carpetas de este repo apunten a commits concretos de otros repositorios:
ME SIRVE PARA CONTROLAR DEPENDENCIAS

    proyectopadre/          <- REPO DE GIT, con su remoto vinculado
        codigo/
        modulo1/            <- REPO DE GIT, con su remoto vinculado
        modulo2/            <- REPO DE GIT, con su remoto vinculado

    proyectopadre2/
        codigo/
        modulo1/            <- REPO DE GIT(COMMIT|TAG), con su remoto vinculado
        modulo3/            <- REPO DE GIT(COMMIT|TAG), con su remoto vinculado

Esto me permite hacer clone solo de un modulo... si solo quiero trabajar en ello.
O hago el clone del proyecto complejo... un cone especial: git clone --recurse-submodules

# Para ver los submodulos
$ git submodule 

# Para clonar un repo con submodulos
$ git clone --recurse-submodules URL

# Para añadir un submodulo
$ git submodule add URL(del repo del submodulo) subcarpeta

# Para quitarme un submodulo
$ git submodule deinit subcarpeta

---

Si trabajo en ADA: Módulos de ADA
Si trabajo en JAVA/MAVEN: Proyecto multimodulo de maven, donde cada modulo es a su vez un submodulo de git
Si trabajo en Angular: Proyecto de angular con módulos, donde cada modulo es un submodulo de git


Me da libertad total a la hora de trabajar para elegir si quiero extraer un proyecto completo o solo un modulo.