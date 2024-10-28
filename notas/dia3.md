
# Repaso rápido

- Concepto de repo distirbuido, donde cada persona tiene un repo diferente.
- 3 zonas de trabajo:
  - Working directory   Mi caperta de trabajo (la que guardo y veo en el HDD)
  - Staging area        Una carpeta paralela, desde la que se generan commit
  - Repository          Almacen de commits:
                          - Guardan foto completa de area de trabajo
                          - Parten siempre de otro (al menos de 1)
                          - Siempre van a asociados a una rama(al menos a 1)
- Variable HEAD:
  - Apunta a lo que veo en el working directory
  - Me permite refenciar al commit en el que estoy y anteriores:
    - HEAD~5
    - HEAD^^
  
# Operaciones

## Flujo estandar de trabajo

- git init
- git add . | git add <archivo> | git add :/ | git add *
- git commit -m 'mensaje'
- git log | git log --oneline
- git diff...
- git checkout      VIAJAR AL PASADO (cambiar el HEAD)

## Intentar dejar un historial limpio

- git commit --amend --no-edit  Reescribir el último
- git reset HEAD~2              Eliminar commits del historial
        DONDE TRABAJA EL COMANDO        REPORITORIO      STAGING    WORKING
  - soft                                    √                x         x
  - mixed (default)                         √                √         x
  - hard                                    √                √         √
- git rebase -i HEAD~3
- git rebase -i --root
  - GUAU !!!! squash, fixup, rewrite.... PERO CON CUIDAO!!!!
  - Super potente para limpiar el historial (operaciones de mantenimiento a bajo nivel)

## Trbajo a nivel de patch
- git revert HEAD~2         Generar nuevos commits de revrsión de cambios!
                                PATCH : LA DIFERENCIA ENTRE DOS COMMITS
- git cherry-pick <commit>  Prepara un commit con un PATH AÑADIDO
- git stash                     Amacena temporalmente un PATCH (lo que tengo en WD)

## Trabajo con ramas

- git branch                 Ver las ramas
- git merge --ff-only
- git merge 
- git rebase

### Workflows: RAMAS

- master/main: Contiene commits aptos para ir a producción
  - Nunca se hacen commits en esta rama
  - Los commits llegan de otras ramas con merge de tipo ff
- develop/dev/desa: Contiene lo siguiente que va a ir a producción en la siguiente versión (abierto a nuevas funcionalidades)
- release: Contiene lo que va a ir a producción ya cerrado...(Un release candidate) en cuanto haya superado las pruebas de sistema y aceptación
- feature: Contienen el desarrollo que voy haciendo de una característica
- persona: Contienen el desarrollo que una persona va haciendo de una característica del sistema
- hotfix (Saltan todos los procedimientos establecidos.. actuaciones de emergencia)


### Estrategias de fusión de cambios (Llevar cosas de una rama a otra)

#### Merge de tipo ff

Copiar un commit de una rama a otra (no siempre es posible)

#### Merge tradicionales

Generan un commit de fusión de cambios, donde pueden aparecer conflictos

#### Rebase

---

# Workflows


--develop--C1----------C3
            \         / <- MERGE ff (copio el commit sin perder nada)
 --feature1--C1--C2--C3
            

ESTO ESTA PROHIBIDO
                            Commit de fusión de cambios
                                v
--develop--C1----------C3------C7  ***** INTEGRACION CONTINUA
            \         /       /
 --feature1--C1--C2--C3      /
              \             /
   --feature2--C1--C4--C5--C6


ESTO ESTA BIEN (si hay conflictos me los como yo en mi rama... no jodo develop)
                            
                            
--develop--C1----------C3-----------C7
            \         /   \  (1)   /
 --feature1--C1--C2--C3     \     /  Esto si es FF. 
              \               \  /   (2)
   --feature2--C1--C4--C5--C6--C7


     git merge develop (estando en feature2)            (1)
     git merge --ff-only feature2 (estando en develop)  (2)
        ^^^ Antes de este punto he de:
                - Pruebas UNITARIAS
                - Refactorizar
                - Limpiar el historial | No llevar el historial de feature2 a develop (squash)
                - Pruebas de INTEGRACION!

                                                                         JENKINS, BAMBOO, AZURE DEVOPS, GITLAB CI, GITHUB ACTIONS
                            ---hotfix-----C7 -> CHF1 (PROD)         PIPELINE DE AUTOMATIZACION
                                         /
                            --master----C7                          ENTREGA CONTINUA
                                       / ^ FF
                       --- release ---C7 < Pruebas de sistema/aceptación
                                       /            Quiero el sistema en un entorno de pre, sometido a 
                                      /            pruebas de sistema
                                     /              -> OK -> SUBO A MASTER EN AUTO (MERGE FF)
                                    / ^ FF
--develop--C1----------C3-----------C7----C8----C9 v(1.2.0)---> CHF1(cherrypick) INTEGRACION CONTINUA
            \         /   \  (1)   /
 --feature1--C1--C2--C3     \     /  Esto si es FF
              \               \  /   (2)
   --feature2--C1--C4--C5--C6--C7

-- ivan -> C(a comer) -> C(a cenar) -> C(a dormir) -> C(pte de refactorizar) -> C(Listo)


# Hay muchas estrategias de fusión de cambios:


--develop--C1----------C3-----------C7
            \         /   \  (1)   /
 --feature1--C1--C2--C3     \     /  Esto si es FF. 
              \               \  /   (2)
   --feature2--C1--C4--C5--C6--C7


## REBASE

Cambio de base en la rama. Desde qué commit sale la rama feature2 (comparandolo con develop)?
Cuál es el último commit en común entre las 2 ramas? C1

--develop--C1----------C3
            \         /  
 --feature1--C1--C2--C3  
              \          
   --feature2--C1--C4--C5--C6--C7[C6+(C3-C1)]

Pues voy a cambiar la base de la rama feature2... para que ya no sea C1... sino C3 (el último commit de develop)

--develop--C1----------C3
            \         /  \
 --feature1--C1--C2--C3   \
                           \          
                --feature2--C3--C4'[C4+(C3-C1)]--C5'[C5+(C3-C1)]--C6'[C6+(C3-C1)]

--develop--C1----------C3--------------------C6'
            \         /  \                   /
 --feature1--C1--C2--C3   \                 /
                           \               /
                --feature2--C3--C4'--C5'--C6'

    Lo que documento en un commit qué es? el parche? o el global? EL PARCHE
    Lo que guardo es el global

      PARCHE (PATCH)          GLOBAL
C1=   Alta                       Alta
C2=   Suma                       Alta + Suma
C3=   Resta                      Alta + Suma + Resta
C4=   Multiplicación             Alta + Multiplicación
C5=   División                   Alta + Multiplicación + División
C6=   Potencia                   Alta + Multiplicación + División + Potencia
C4'=  Multiplicación             Alta + Suma + Resta + Multiplicación
C5'=  División                   Alta + Suma + Resta + Multiplicación + División
C6'=  Potencia                   Alta + Suma + Resta + Multiplicación + División + Potencia
C7=   Fusión de C3(suma + [resta])  + C6(potencia + [multiplicación + división])
C7'?  COÑO SI NO HAY !!!!!

Y si miro el historial de la rama develop o de la rama feature2, son identicos entre si:
- C1 -> C2 -> C3 -> C4' -> C5' -> C6'

TENGO UN HISTORIAL LINEAL Y LIMPIO (sin commits raros)
Sin telas de araña.. sin commits que vengan de 2 commits que me vuelven loco!

Los rebases nos encantan... Los adoramos... pero:
- 1. Me cuentan la realidad del proyecto? NO... y no pasa nada en muchos casos... pero tengo que saber que estoy contando la historia a mi manera
- 2. Reescriben commits! PROBLEMAS POTENCIALES:
  - Si ya lo he distribuido (push) FOLLON DE COJONES
     Si ya hubiera distribuido (push) C4, C5 y C6... y ahora quiero subir el C4', C5' y C6' git me deja? A PRIORI NO
     Me va a decir que las ramas han divergido.... Y entonoces yo se lo puedo suplicar: --force
     Pero ahora el problemón lo tienen otros... cuando quieran hacer un git fetch que les va a decir lo mismo... FOLLON
- 3. La probabilidad de CONFLICTOS SE MULTIPLICA! Más o menos.
     Serán los mismos conflictos... pero en más sitios! 
 
El usar rebase implica el seguir una serie de normas: (es una gozada el rebase... pero entendiendo lo que se hace)
 - Siempre antes de un push, rebase
 - Debo estar haciendo rebases continuos, para evitar ese problema de los conflictos que hablabamos
   Todas las mañanas, lo primero: REBASE! **** AL MENOS !
   - Deseablemente, antes de cada commit, rebase.




---
# Metodología tradicional

HITO 1: 10-Noviembre: **R1, R2, R3**

    Qué pasaba si el día 10 de Noviembre no estaba el R3?
    - El proyecto va con retraso
    - Ostías pa' tos' laos'
    - Se replanifica el HITO: Nueva fecha: 15-Noviembre

HITO 2: 20-Noviembre: R4, R5, R6
HITO 3: 30-Noviembre: R7, R8, R9

# Metodologías ágiles

Entregar el producto de forma incremental! > OBJETIVO: RAPIDO FEEDBACK y rápida respuesta a los cambios

Esto nos ayuda a lidiar con muchos problemas que teníamos al aplicar metodologías en cascada (taradicionales)... pero esto ha venido con sus propios problemas!

ITERACION 1: (Sprint): **10-Noviembre**: R1, R2, R3 -> PRODUCCIÓN

    Si aquí paso a producción??? que necesito haber hecho? PRUEBAS A NIVEL DE PRODUCCION!
    Y pruebo el 5% de la funcionalidad (que es lo que entrego)
    Por supuesto en un entorno de pruebas.
    2 instalaciones: PRUEBAS y EN PRODUCCION

ITERACION 2: (Sprint): 20-Noviembre: R4, R5, R6 -> PRODUCCIÓN +10% de la funcionaldiad

    Pruebas: 5% anterior + 10% nuevo... que he tocao código que lo he mismo he jodido algo!

ITERACION 3: (Sprint): 30-Noviembre: R7, R8, R9 -> PRODUCCIÓN +10% de la funcionalidad

    Pruebas: 15% anterior + 10% nuevo... que he tocao código que lo he mismo he jodido algo!

Las pruebas se multiplican y las instalaciones también! con respecto a las que hacíamos en met. tradicionales.

De donde sale la pasta? y el tiempo? y los recursos? para esto? NO HAY PASTA, NI HAY TIEMPO NI HAY RECURSO !!!!! Y entonces cual es la solución? AUTOMATIZAR !

## Extraído del manifiesto ágil: 

> El software funcionando es la MEDIDA principal de progreso. > DEFINE UN INDICADOR PARA 
                                                                UN CUADRO DE MANDO
  -----------------------   --------------------------------
  sujeto                        copula
                         -----------------------------------
                         predicado

La MEDIDA principal de progreso es el "SOFTWARE FUNCIONANDO"
------------------------------- ----------------------------
sujeto                          predicado
   NUCLEO DEL SUJEJO

La forma en la que vamos a medir cómo vamos avanzado con el proyecto es el concepto "SOFTWARE FUNCIONANDO" (1)

"SOFTWARE FUNCIONANDO": Software que funciona, que hace lo que tiene que hacer.
    ¿Quién dice esto? Quién dice que el software está listo, está guay, que es lo que tiene que ser?
        ~~- CLIENTE.~~ PERDÓN... al cliente le tiene que llegar ya algo que funcione.
                       QUE FUNCIONE y que esté guay NO ES SU RESPONSABILIDAD... ES LA MIA !!!!!
        LAS PRUEBAS !!!!!               (1)
---

# Devops

No son personas (ni perfiles), ni herramientas.. es una cultura, un movimiento una filosofía en pro de la automatización. CHICOS Y CHCAS!!! A AUTOMATIZAR? QUÉ? TODO LO QUE SE PUEDA ENTRE EL DEV--->OPS

            Automatizables?         HERRAMIENTAS
PLAN            no
CODE            no
BUILD           si
                                    java: MAVEN, GRADLE, SBT, ANT
                                    js:  NPM, YARN, WEBPACK
                                    C++: MAKE, CMAKE
                                    Ada: GNAT, MAKE
-----------------------------------------------------> DESARROLLO AGIL
TEST
   definición   no
   ejecución    si
                                    JUNIT, MOCHA, JEST, MSTEST, UNITTEST, PYTEST
                                    Web: SELENIUM, CYRPRESS, KARMA, KATALON
                                    Mobile: Appium, Calabash, Espresso
                                    Desktop: WinAppDriver, UFT
                                    Rendimiento: JMeter, Gatling, LoadRunner
                                    Servicios web: SOAPUI, POSTMAN, ReadyAPI, Karate
-----------------------------------------------------> INTEGRACION CONTINUA: PRODUCTO? INFORME DE PRUEBAS EN TIEMPO REAL! (1)

Tener CONTINUAMENTE la última versión del producto instalada en un entorno de INTEGRACION sometida a pruebas AUTOMATIZADAS
RELEASE (LIBRERACION)
    (poner la última versión generada en manos de mi cliente)
                si
-----------------------------------------------------> ENTREGA CONTINUA (CONTINUOUS DELIVERY)
DEPLOY          si
-----------------------------------------------------> DESPLIEGUE CONTINUO (CONTINUOUS DEPLOYMENT)
OPERATE         si
MONITOR         si
-----------------------------------------------------> DEVOPS COMPLETO

Primero: TODO LO ANTERIOR SE PUEDE HACER ... parte de tener el código bien organizado en mi SCM (git)
Si eso, nada de lo anterior es posible.
Pero me temo que es más complejo!

El objetivo es que cuando un desarrollador hace COMMIT, a los 20 minutos, sin mediación humana de NINGUN TIPO, el código esté en producción. Y para eso:
- O estoy volao!
- O tengo la sangre muy fría cuando le doy a ente(me haré popo!!!!)
- O tengo mucha confianza en lo que estoy haciendo!

---

# Pruebas de software
