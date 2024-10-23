
# Qué es git?

SCM: sistema de control de código fuente.

Git lo creo Linus. Al crear Linux, los scm tardaban días en hacer los merge... no le servían.
En las notas originales de GIT (creadas por Linus) metió un comentario:
- Cuando no sepamos como enfrentar un problema, la decisión será hacer lo OPUESTO A CVS.

## Git es un SCM distribuido

Qué significa eso? Cada persona tiene su propio REPOSITORIO.
El proyecto (su historia REAL) solo la puedo averiguar SI SUMO TODOS ESOS REPOSITORIOS, que son DIFERENTES (y deben serlo) entre sí.

## SCM?

* Poder hacer desarrollos en paralelo / Trabajar varias personas en un proyecto = RAMAS
  - Controlar Conflictos
  - Poder hacer merge/rebase
* Recuperar código pasado / Ayudarme a identificar Errores (Cuándo, quién, por qué?) / Deshacer (revert)
  - Mantener un histórico COJONUDO del código (OJO IMPORTANTE!)
- Git actúa como punto de partida en este sentido. Es lo que me permitirá:
  - Gestionar el ciclo de vida del software (maven, dotnet, gradle, npm, jenkins)
- Guardar el código -> Carpeta compartida en RED

Al final en un SCM lo que acabamos generando es un REPOSITORIO... es una especia de BBDD donde acumulamos las versiones de nuestro proyecto + metadatos.

En el repo lo que guardamos son COMMITS

## Qué es un Commit?

UN COMMIT es una FOTO COMPLETA en un momento dado del tiempo de mi proyecto. Como si le doy a la carpeta BOTON DERECHO DEL RATON y METER EN UN ZIP.

NI DE COÑA es un conjunto de cambios. ESO ERA UN COMMIT EN CVS o en SVN... Y por eso serán un DESASTRE DE SISTEMAS DE SCM.

Los commits los guardamos unos detrás de otros (cada commit parte de un commit anterior - o más-)...

Si eso es así... en que se diferencia GIT de un sistema de copias de seguridad ?
En GIT y en General en los SCM los commits se guardan en RAMAS = Branches

Si GIT en un momento dado necesita saber la diferencia entre 2 archivos, commits.. lo que sea, esa diferencia SE CALCULA BAJO DEMANDA... no está pre-calculada... Y eso, así suene extraño, ilógico, es lo que hace que GIT VAYA TAN RAPIDO.

## BRANCH ?

Una linea de evolución de mi proyecto paralela a otras lineas de evolución en el tiempo.

---

## Los tres árboles de GIT.

Git va a controlar 3 cosas por separado:

- Working DIR: Mi carpeta de trabajo
- Staging (área de preparación): Os la podéis imaginar como otra carpeta paralela a mi carpeta de trabajo
- Repositorio: Colección de ramas (con commits dentro)

Todos los comandos de git lo único que hacen es mover cosas entre esos 3 sitios.
Hay un problema GRANDE CON GIT.. Es una herramienta muy potente, MUY POTENTE!
Pero es una herramienta que no se aprende por ciencia infusa.. ni usándola.
O la estudio (o me la cuentan) o voy bien jodido. Y son 4 conceptos.

## Flujo básico para trabajar con GIT (en una rama)

0. Instalar git y configurar git (a nivel de host o a nivel de proyecto)
1. Crear un repositorio: git init
2. Empiezo a crear archivos en mi carpeta
3. Agregarlos al área de staging: git add
4. Realizar un commit... que básicamente es hacer una copia de seguridad de lo que tengo en staging y guardarla (la copia de seguridad) en mi repositorio: git commit -m 'MENSAJE'

    WORKING DIR                     AREA DE STAGING(.git)                 REPOSITORIO(.git)

    c:\miproyecto

        archivo1.txt(v4) --add-->   archivo1.txt(v3)      --commit-->     C1[archivo1.txt(v1)]
                    [copiar el archivo a 
                    esa otra carpeta]
        archivo2.txt(v1) --add->    archivo2.txt(v1)      --commit-->     C2[archivo1.txt(v1)+archivo2.txt(v1)]

                                                                          C3[archivo1.txt(v2)+archivo2.txt(v1)]

    Tengo 5 copias, con 4 versiones (entre las 5 copias)

    ¿Qué pasa con el área de staging cuando hago un commit? Se mantiene
    De dónde viene esta duda? git status
    Después de hacer un commit me dice git status?

Trabajar solo es muy aburrido!

## Trabajando en equipo
                                                ramaIvan: C1->C2->C3->C4
                                                    |
                                              REPO REMOTO git init --bare (es un repo que solo permite guardar commits en ramas... 
                                                    |                      ni tiene working dir ni tiene staging)
                                                    |
             Servidor de alojamiento de repositorios remotos de git (bitbucket, gitlab, github, apache httpd, carpeta en red)
                                                    |
      +---------------------------------------------+--------------------------+
      |                                                                        |
    IvanPC                                                                   MenchuPC
      |                                                                        |
      |- c:\miproyecto                                                         |- c:\menchuproyecto
                      \.git                                                                        \.git
                          REPO                                                               REPO
                          ramaIvan:                C1->C2->C3->C4                            origin/ramaIvan: C1->C2->C3->C4
                                                      v ff     ^ ff                                              v ff     ^^
                          NOMBRE_HORTERA/ramaIvan: C1->C2->C3->C4                            ramaIvan:        C1->C2->C3->C4

1- Creo un repo en pelotas en un servidor
2- Vincular mi repositorio al nuevo repo remoto en pelotas! git remote add NOMBRE_HORTERA URL_REPO_REMOTO_PELOTAS
3- git push
4- MENCHU !!! git clone
5- MENCHU genera commit4
6- MENCHU git push
7- git fetch            \
8- merge --ff-only      / git pull = git fetch + FUSION DE CAMBIOS (según tenga configurado en mi host: rebase o merge recursivo o merge ff). Antiguamente por defecto venía configurado merge recursivo. Hoy en día, con las nuevas versiones de git, me obliga a configurar qué opción quiero antes de permitirme hacer un pull. Si no lo he configurado, me rechaza el pull.

Cómo se copian commits de una rama a otra? git merge --ff-only


## Qué Tipos de FUSIONES DE CAMBIOS conocéis?

Merge de tipo FastForward (Copiar commits de una rama a otra)
Merge tradicionales (que implican fusión de cambios) - recursivos
Merge Octupus
    Merge Squash
Rebase

## git status

Git status me dice:
    - Qué diferencias hay entre Working dir (mi carpeta) y el área de staging.
    - Qué diferencias hay entre Área de Staging y el último Commit del REPO.

## Quiero saber la diferencia entre el commit actual y el primero

git diff 6e905a9 5b32583

JODERRR !!!!  Lo de los IDs...

### La variable HEAD

¿Qué es HEAD???? Una variable que apunta al commit en el que estoy (En working dir) En el que tengo la CABEZA!
- Puedo cambiarle el valor con git checkout COMMIT_ID
- Puedo usarla como variable para referirme al commit actual... o a los anteriores:
  - HEAD   Commit actual
  - HEAD^  Commit anterior
  - HEAD^^ Commit anterior al anterior
  - HEAD^^^^^^ Commit anterior al Commit anterior al Commit anterior al Commit anterior al Commit anterior al anterior...
  - HEAD~6  Commit anterior al Commit anterior al Commit anterior al Commit anterior al Commit anterior al anterior...

## Git checkout

Viajar al pasado... git checkout 6e905a9

Al hacerlo, dejamos el repo en estado DESASOCIADO !
Y esoes malo? No... Solo es que desde este commit no puedo generar un nuevo commit en mi rama... por qué?
Porque ya hay un commit detrás.

## Git push

Subir a origin el qué? UNA RAMA



---

## Origin???

El nombre cutre que git pone por defecto al primer repo remoto (que puedo tener 17 si quiero)

----
# Copia archivos al area de staging desde mi carpeta
git add PATH_SPEC

git add libreria.codigo       Copia este archivo a staging
git add *                     Copia todos los archivos de la carpeta actual y de las subcarpetas a staging...
                              SIEMPRE QUE NO SEAN OCULTOS
git add .                     Copia todos los archivos de la carpeta actual y de las subcarpetas a staging...
                              OCULTOS Y NO OCULTOS
git add :/                    Copia todos los archivos (OCULTOS Y NO OCULTOS) desde la carpeta raiz del proyecto... en adelante

git log 
git log --oneline

git commit -am ''             COPIA en automático los archivos QUE HAYAN CAMBIADO (los nuevos no) a STAGING antes de hacer el commit.
--- 

git SOLO controla Archivos

En git, hasta que un proyecto no tiene commits, no hay ramas.
Otra cosa es que estoy posicionado para el siguiente commit hacerlo en una nueva RAMA que se creará llamada master

Al crear un commit, git le asigna un ID. Qué es ese identificador? Es un hash del commit (zip comprimido)
Ese identificador no se crea así porque si. Ese ID sirve para verificar que nadie ha entrado por detrás a tocar los archivos.
El número es mu' largo... nos solemos apañar con los primero 7 dígitos.

---

# HASH? HUELLA

Qué es un algoritmo de HASH: desde que tenéis 7-8 años.. cuando os dan un DNI.
23012938 A

De donde sale la letra? ES UNA HUELLA del número

  23012938 | 23
           +----------
        17   1823722(me da igual)
         |
         RESTO = GUAY ! Estará entre [0-22]

         El ministerio da una tabla con las letras que corresponden a cada resto.

  Un algoritmo de huella es una función que dado un valor de entrada, genera un valor de salida que:
  - Siempre es el mismo para el mismo dato de entrada
  - Hay "muy poca probabilidad" de que 2 datos de entrada generen en mismo dato de salida
       1/24 = 4% 
  - Desde el dato de salida no puedo regenerar el dato de entrada (El dato de salida es un RESUMEN del dato de entrada)

---
git diff b1ce4c7 --cached libreria.codigo # Entre staging y el commit en repo
git diff --cached libreria.codigo # Entre staging y el último commit en repo
git diff b1ce4c7 libreria.codigo # Entre working dir y un commit en repo
git diff libreria.codigo # Entre working dir y staging
git diff b1ce4c7 5b32583 -- libreria.codigo
git diff b1ce4c7 5b32583

- libreria:
-
-  Operación suma (numero1, numero2) = numero1+numero2
+ libreria:

---

GIT CONTROLA CAMBIOS A NIVEL DE LINEA DE TEXTO
y la línea 
"libreria:"
no es igual a la linea: 
"libreria:
"

Una tiene un salto de linea al final y la otra no.

Y ESTA ES LA PRINCIPAL FUENTE DE CONFLICTOS EN GIT.
Siempre dejad lineas en blanco cuando trabajéis con git... MAS VALE QUE SOBREN QUE NO QUE FALTEN !!!!
Lo de los conflictos es una putada

```java
public class MiClase{
  public void metodo1(){
      System.out.println("Hola Mundo");
  }
}
```
```java
public class MiClase{
  public void metodo1(){
      System.out.println("Hola Mundo");
  }
  public void metodo2(){
      System.out.println("Adios Mundo CRUEL !!!!");
  }
}
```

```ada

  procedure metodo1 is
  begin
    put_line("Hola Mundo");
  end metodo1; 

```

---

Deshacer cambios
- Cambios no confirmados
- Cambios confirmados

Reescribir el historial! = ESTO ES LO MAS IMPORTANTE EN GIT:
El historial de mi repo NECESITA DE MANTENIMIENTO !!!!!
