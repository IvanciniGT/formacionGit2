
# Diferencias grandes de git con otros SCM:

- Commit son snapshots completos (no paquetes de cambios)
  Si git necesita conocer los cambios que se incorporaron en un commit se hace bajo demanda.
- No existe el concepto de REPOSITORIO CENTRAL
  Cada usuario tiene su propio REPOSITORIO LOCAL
  Y pueden sincronizar mediante repositorios (2000 de ellos... los que quieran)  REMOTOS
  Y eso evita el concepto ASQUEROS DE CVS y SNV de BLOQUEO DE ARCHIVOS

# · árboles de git... que es como funciona internamente:

- Working dir:      Mi carpeta en el HDD
- Área de staging:  (.git) Una carpeta paralela a mi carpeta del HDD donde voy poniendo als cosas que quiero que se guarden definitivamente en el repositorio
- Repositorio:      (.git) Una BBDD que guarda COMMITS asociados a ramas 

# Flujo de trabajo básico de git en local:

mkdir proyecto
cd proyecto
git init
(Creo archivos)
git add .                       # Copiar los archivos a staging
git rm --cached ARCHIVO         # Borrar archivos de staging
git rm ARCHIVO                  # Lo borra de mi carpeta y de staging
rm ARCHIVO                      # Lo borra de mi carpeta pero no de staging
mv ARCHIVO CARPETA              # Lo quiero mover en mi carpeta a un directorio
 ^^
 PROHIBIDO!!!!
git mv ARCHIVO CARPETA          # Lo quiero mover en mi carpeta a un directorio
                                # Y lo muevo también en el área de staging
git status                      # Ver el estado del proyecto:
                                #  - DIFERENCIAS entre WD & Staging
                                #  - DIFERENCIAS entre Staging & Repo
git diff                                                # Ver las diferencias entre archivos
git diff archivo.de.marras                              # Local (WD) y staging
git diff --cached archivo.de.marras                     # staging y el ultimo commit
git diff COMMIT_ID --cached archivo.de.marras           # staging y un commit
git diff COMMIT_ID archivo.de.marras                    # WD y un commit
git diff COMMIT_ID1..COMMIT_ID2 archivo.de.marras       # Entre 2 commits
git diff COMMIT_ID1 COMMIT_ID2 archivo.de.marras        # Entre 2 commits

git log --oneline               # Ver los commits del repo en la rama actual
git branch                      # Ver las ramas que tengo
git checkout COMMIT_ID          # Viajar al pasado (llevar el HEA a otro lado)
git checkout -                  # Volver al presente
git checkout RAMA               # Volver al presente en mi rama

# La gran variable HEAD

Una variable que apunta al commit que veo en mi carpeta de trabajo: WORKING DIRECTORY
Es el commit desde el que generaría el nuevo commit si hago un commit ahora...
    Siempre y cuando no haya commits posteriores (en ese caso estamos en estado detached HEAD)
Puedo cambiar la cabeza (HEAD) con git checkout COMMIT_ID
Puedo usarlo en lugar del COMMIT_ID actual. MUY COMODO
    Incluso como referencia a commit pasados:
      - HEAD^       HEAD^^^^^
      - HEAD~1      HEAD~5      HEAD~
  
# Motivos para crear un commit:

1. Tengo cambios que quiero que queden guardados para su inclusión en el proyecto
2. Quiero compartir código con otro
3. Copia de seguridad de mi código (si se me quema el ordenador)
4. Punto de restauración 
5. Porque quiero, puedo y no me da miedo!

Y eso significa una cosa:
Que el historial va a acabar hecho un DESMADRE
Y un historial hecho un desmadre = NO TENER HISTORIAL !

Al final el objetivo del historial es:
- git restore
- git revert
- git cherry-pick
- git blame
- git bisect

Esas operaciones las veremos más adelante.
De momento vamos a concentrarnos en conseguir un HISTORIAL DE PUTA MADRE!

# Generar un historial de puta madre: REESCRITURA DEL HISTORIAL

Puede haber muchos motivos por los que el historial se convierta en un desmadre... y no vamos a poder evitarlos todos (por eso le haré mantenimiento al historial).

Vamos a ver algunbos de los motivos más comunes y cómo nos podemos enfrentar a ellos:

## Motivo 1:

> Me voy a comer... y quiero un commit para tener una copia de seguridad

    git commit -m 'A comer!!!!!!'

  Y después qué?
    
    git commit --amend -m 'Ahora a cenar!!!!'
    SOBRESCRIBE EL ULTIMO COMMIT... Cuidao.. genera un commit nuevo .. con nuevo ID:
    - El de antes lo pierdo
    - Como lo haya publicado el anterior (push) lo flipo!!! Porque al intentar publicar de nuevo.. me va a decir que mi rama ha divergido y son IRRECONCILIABLES!: 
      - git push --force... Sobreescribes la rama completa en el servidor
          Si tengo compañeros que ya hayan descargado esa rama... No tengo España para esconderme... Me estan buscando kalashnikov en mano para darme besitos.....

    Cuando puedo usar esto:
    - Cuando aún no he publicado eso
    - Es una rama privada

## Motivo 2:

> Acabo de escribir un código y le hago todas sus pruebas...y funciona... y quiero hacer un commit!
> Ya que me voy a meter en refactorizar... y la puedo regar.. y quiero volver atrás.

    DESARROLLO <-> PRUEBAS -> OK -> REFACTORIZACION <-> PRUEBAS -> OK
    <------ 50% del trabajo ------> <------ 50% del trabajo -------->
            8 horas de trabajo              8 horas de trabajo

NO SE PORQUE COJONES el día 0 que empiezo a estudiar de informática y desarrollo de software NO ME DICES y me TATUAN EN EL PECHO:
    El software es un producto MANTENIBLE !

        git commit -m 'Ahora a refactorizar!!!!'
        git commit --amend -m 'Listo!!!!'

## Motivo 3:

> Desarrollo exploratorio. No tengo npi de qué voy a hacer pa que estoy funcione.
> Hago un commit y juego.. a ver a donde llego.. Que llego a algun lao = GUAY
> Que no... marcha atrás.

        git commit -m 'Ahora a jugar!!!!'
        git commit --amend -m 'Listo!!!!'

El git commit AMEND me ayuda a ir generando un historial limpio.

Generar un historial limpio.. es imposible en todos los casos.. Y muchas veces la riego.

## Motivo 4: LA REGAMOS

        git commit -m 'Pruebas listas.. todo funcionando: Ahora a refactorizar'
        git commit --amend -m 'Ahora si que si! ACABAO' (1) # Archivo1.txt *

        git commit -m 'Otra cosa que hago.. y la tengo también' # Archivo2.txt
        git commit -m 'Caguendiez... que ahora resulta que no funciona lo de antes de antes... pero ahora si' # Archivo1.txt

        VOY A QUERER BORRAR el (1)... Si borro... estoy contando la realidad del proyecto.. su historia real? NO... Me importa? NO...

        > Opción 1: Más CURRO
          git reset --mixed HEAD~2
          git add Archivo1.txt
          git commit -amend -m 'Funcionalidad 1 lista'
          git add Archivo2.txt
          git commit -m 'Funcionalidad 2 lista'

        > Opción 2: QUE ES LA GUAY !
          Esto va a requerir de técnicas más avanzadas: git rebase --interactive

## Motivo 5: Me he despistao... soy humano

    git commit -m 'A comer'
    git commit -m 'A cenar'
    git commit -m 'Listo.. a refactorizar'
    git commit -m 'Listo.. acabao'

    COÑOLE!! Si no he ido haciendo amends.... Y ahora tengo desmadre!
    Quiero agruparlos? NO QUIERO AGRPARLOS.. pa' que ... en el último ya está todo.. que no son incrementales.. que son fotos completas!!!!!

        git reset --soft HEAD~3

    git reset : BORRA LOS ULTIMOS N COMMITS COMMITS del repositorio
    Pero.. tiene 3 modos de uso:
        - soft
        - mixed
        - hard (Y ESTE ES PELIGROSO TU !!!!!) que aquí puedo perder código.. y luego lo mismo me llevo 2 min para recuperarlo
    
    Los 3 borrar los últimos n commits del repo, cuando ejecuto un 
        git reset [MODO QUE SEA ] HEAD~(N+1)
    Con git reset digo a cual voy... CUAL SI ME VALE... y desde ese se borran los siguientes (ese no se borra)
    Además de repo, tenemos Area de Staging y Working Directory...

    El soft solo borra del repo... y deja el resto como estaba.
    El mixed borra del repo y restaura el área de staging al estado del commit que fuera... pero deja el working directory como está
    El hard borra del repo, restaura el área de staging al estado del commit que fuera y restaura el working directory al estado del commit que fuera.

                AREA
    MODO        REPO            STAGING         WORKING
    SOFT         √               X               X
    MIXED        √               √               X
    HARD         √               √               √

> En el caso de antes
    git reset HEAD~3

        git reset --soft HEAD~3                          [git reflog para recuperar commits borrados]
        Se borran los 3 cómmits ultimos y me deja en el HEAD~3... que ahora es el nuevo HEAD
        Que pasa con mi carpeta de trabajo? No se toca... queda igual.. es decir, con todo mi código GUAY... el que había en el último commit.
        Qué pasa con mi área de staging? Se queda igual.. por lo qué?
        En el siguiente commir se guarda todo el codigo que tengo ahí.. 
            que es el mismo que tengo en el working dir = CODIGO GUAY
    
    git commit --amend 'Todo OK!!!'

        COMMIT 1: 'A comer'
        COMMIT 2: 'A cenar'
        COMMIT 3: 'Listo.. a refactorizar'
        COMMIT 4: 'Listo.. acabao'
            vvvv
        COMMIT 1: 'Todo OK!!!'

    Lo he arreglado facil.


## Motivo 6: 

    git commit -m 'Funcionalidad guay que si quiero'
    git commit -m 'Otra funcionalidad Lista... a refactorizar'
    QUE ESO YA NO HACE FALTA.... Que te pongas a currar en otra cosa!

    git reset --hard HEAD~1
        Esto es como si nunca hubiera hecho nada el tema ese!

## Motivo 7

    Federico: git commit -m 'ya está tu lo de la suma'
    Menchu:   git commit -m 'RF002-Operación resta - Acabada'
    Felipe:   git commit -m 'Operación multiplicacion lista'

    Está bien... no está mal.. pero... me puede apetecer NORMALIZAR LOS MENSAJES UN POQUITO
        'RF001-Operación suma - Acabada'
        'RF002-Operación resta - Acabada'
        'RF003-Operación multiplicación - Acabada'

    git rebase --interactive HEAD~3


---

# Operaciones más avanzadas para gestión del proyecto.

## Motivo 8

git commit -m 'RF004- Operación valor absoluto - Acabada'
git commit -m 'RF005- Operación división - Acabada'
Y ahora llega el corbatilla de turno y dice... no lo vamos aponer en este sprint tu! Quitalo!

    git revert HEAD~

    ESTO GENERA UN COMMIT DE REVERSION DE CAMBIOS. Y CUIDAO CON ESTO!

    Commit n:   'RF003- Operación multiplicación - Acabada'
    Commit n+1: 'RF004- Operación valor absoluto - Acabada'
    Commit n+2: 'RF005- Operación división - Acabada'

        git revert HEAD~
        git revert HEAD~1
    
    Lo que hace es calcular el patch del commit HEAD~ y aplicarlo en negativo al HEAD actual
    para generar un nuevo commit de reversión de cambios, que deja el sistema como estaba en el último commit sin los CAMBIOS ! que se hicieron en el commit HEAD~

    PATCH: Diferencia entre 2 commits: git diff HEAD~2 HEAD~
        La diferencia sería Añadir la operación valor absoluto (Meter de la línea 20 a la 25 del fichero1.codigo)
    
    Modifico el código actual aplicando el inverso de ese patch = 
        Quitar la operación valor absoluto (Borrar de la línea 20 a la 25 del fichero1.codigo)
    
    Y genero commit nuevo, con mensaje: 'Revert "RF004- Operación valor absoluto - Acabada"'

    Para pasar a tener el historial:
    Commit n:   'RF003- Operación multiplicación - Acabada'
    Commit n+1: 'RF004- Operación valor absoluto - Acabada'
    Commit n+2: 'RF005- Operación división - Acabada'
    Commit n+3: 'Revert "RF004- Operación valor absoluto - Acabada"'
    

# Cherry pick = OPUESTO DE REVERT

Calcula el patch de un commit y lo aplica en positivo sobre el ultimo commit.

    Commit n:   'RF003- Operación multiplicación - Acabada'
    Commit n+1: 'RF004- Operación valor absoluto - Acabada'
    Commit n+2: 'RF005- Operación división - Acabada'
    Commit n+3: 'Revert "RF004- Operación valor absoluto - Acabada"'
    Commit n+4: 'RF006- Operación elevación al cuadrado - Acabada'

Si ahora quisiera recuperar la operación valor absoluto: 

    git cherry-pick HEAD~3

    Commit n:   'RF003- Operación multiplicación - Acabada'
    Commit n+1: 'RF004- Operación valor absoluto - Acabada'
    Commit n+2: 'RF005- Operación división - Acabada'
    Commit n+3: 'Revert "RF004- Operación valor absoluto - Acabada"'
    Commit n+4: 'RF006- Operación elevación al cuadrado - Acabada'
    Commit n+5: 'RF004- Operación valor absoluto - Acabada'

    Casos de uso del cherry pick:
        Abro una rama de hotfix desde un commit pasado y hago un commit en ella para un arreglo... y su a producción 
        Pero ese cambio le quiero en la rama dev actual: 
            git switch dev
            git cherry-pick HOTFIX_COMMIT_ID 

---

# Ramas: Git workflow

Qué procedimiento de trabajo con git vamos a seguir en el proyecto: 
Qué ramas tengo, como abro ramas, donde puedo hacer qué tipo de fusiones de cambios.
Todo eso tiene que estar totalmente consensuado en el proyecto.

El 90% de los conflictos en git vienen por no tener claro el workflow del proyecto y/o
de tener un flujo de trabajo que no se ajusta a la realidad del proyecto.

A esto se le da muy poca importancia... Y ES LA CLAVE DE TRABAJAR CON GIT !

Sea cual sea el flujo de trabajo que elijamos, hay algunas reglas básicas:

- MASTER , MAIN: 
  - Lo que hay en esta rama SE CONSIDERA APTO PARA PRODUCCION !
  - NUNCA haremos un commit en esta rama. MOTIVO DE DESPIDO (del que lo hace y del que lo permitió)
  - Y entonces, como llegan commits a esta rama? MERGE de tipo FF (Fast Forward) de la RAMA DE RELEASE

Lo normal es trabajar en la rama: DEVELOP, DEV, DESA:
  Si trabajo en un proyecto yo y mi primo, 
  - En esta rama iremos haciendo los commits...
 Si trabajo en un entorno más grande / serio / con más personas... 
  - En esta rama tengo funcionalidades ACABADAS y TESTEADAS y REFACTORIZADAS... listas para pruebas finales y paso a producción ( o eso creo)
  - Solo debería de aceptar commits de ramas de personas o features individuales... y solo mediante un MERGE de tipo FF (Fast Forward)

Lo normal (y de nuevo depende ya del workflow que decida) es tener otra rama llamada: RELEASE, RC, QA, STAGING, PREPROD, etc... 
  - En esa rama tenemos un RELEASE CANDIDATE... que es lo que vamos a pasar a producción en cuantito le hayamos hecho las pruebas finales (SISTEMA + ACEPTACION)..
  - A esta rama solo le pueden llegar commits de DEVELOP y solo mediante un MERGE de tipo FF (Fast Forward)

Y partir de ahí ( de desa) voy abriendo ramas para los cambios que quiera ir haciendo en el proyecto.
Hay empresas / proyectos donde abrimos ramas asociadas a Funcionalidades (FEATURE), en otras abrimos ramas asociadas a personas (IVAN, MENCHU).

Si tengo ramas de FEATURE, yo Iván, que estoy trabajando en esa rama,debería abrir mi rama de persona: ramaFetuare1Ivan... y trabajar en ella.

Mi máquina es MI REINO... NADIE ME DICE QUE HACER EN MI MAQUINA... y en mi máquina me podré abrir todas las ramas que me vengan bien!



--develop--C1----------C3
            \         / <- MERGE ff (copio el commit sin perder nada)
 --feature1--C1--C2--C3
            

ESTO ESTA PROHIBIDO
                            Commit de fusión de cambios
                                v
--develop--C1----------C3------C7
            \         /       /
 --feature1--C1--C2--C3      /
              \             /
   --feature2--C1--C4--C5--C6


ESTO ESTA BIEN (si hay conflictos me los como yo en mi rama... no jodo develop)
                            
                            
--develop--C1----------C3-----------C7
            \         /   \  (1)   /
 --feature1--C1--C2--C3     \     /  Esto si es FF
              \               \  /   (2)
   --feature2--C1--C4--C5--C6--C7


     git merge develop (estando en feature2)            (1)
     git merge --ff-only feature2 (estando en develop)  (2)
        ^^^ Antes de este punto he de:
                - Pruebas UNITARIAS
                - Refactorizar
                - Limpiar el historial
                - Pruebas de INTEGRACION!

                            --master----C7
                                       / ^ FF
                       --- release ---C7 < Pruebas de sistema/aceptación
                                     / ^ FF
--develop--C1----------C3-----------C7
            \         /   \  (1)   /
 --feature1--C1--C2--C3     \     /  Esto si es FF
              \               \  /   (2)
   --feature2--C1--C4--C5--C6--C7
