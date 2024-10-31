# Stash

Un stash es un parche (un paquete de cambios, el diff entre mi working_dir y el ultimo commit), que guardo para poder aplicarlo posteriormente.

$ git stash 
$ git stash save "Mensaje de stash"                 #GUAY
$ git stash push -m "Mensaje de stash"              #GUAY

$ git stash list

$ git stash apply                                   #Aplica el último stash
$ git stash apply stash@{0}                         #Aplica el stash

$ git stash drop stash@{0}                          #Borra el stash
$ git stash clear                                   #Borra todos los stash

$ git stash pop                                     # apply + drop

$ git stash branch nombre_rama                      #Crea una rama desde donde estoy, aplicando el stash

Los cambios que voy haciendo en working dir, me los llevo de rama en rama. 
Esto me interesa?
- Si he empezado a escribir algo en una rama... y no debía, está guay
- Hay veces que no quiero... estaba haciendo cambios en rama "rama" y están a medias...
  Y al ir a master a abrir un hotfix, me llevo los cambios de "rama" a master... y no quiero eso!!! VAYA CAGADA

Los cambios en el área de staging también me los llevo de rama en rama!!! CAGADA ! o no! Pero en mi caso que quiero abrir un hotfix, no me interesa llevarme los cambios de la rama a la rama de hotfix.

1. Me voy a la rama master
2. Hago un checkout del commit que quiero arreglar (RELEASE)
3. Me abro rama hotfix
4. Hago los cambios
5. PREGUNTA? Este cambio que acabo de hacer, aplica solo a este commit? NO
   1. Lo quiero aquí: v0.1.0, v1.0.0 y en desa
   Opción A: TRABAJAR CON CHERRY-PICK
       1. git add :/ && git commit -m 'Operación division arreglada'
       2. git tag -a v0.1.1 -m 'Operación division arreglada'
       3. git checkout v1.0.0 && git cherry-pick v0.1.1
          git add :/ && git commit -m 'Operación division arreglada'
          git tag -a v1.0.1 -m 'Operación division arreglada'
       4. git checkout desa && git cherry-pick v0.1.1
          git add :/ && git commit -m 'Operación division arreglada'
    Opción B: TRABAJAR CON STASHES
       1. git add :/ && git stash save 'Operación division arreglada'
       2. git stash apply
       3. git commit -m 'Operación division arreglada'
       4. git tag -a v0.1.1 -m 'Operación division arreglada'
       5. git checkout v1.0.0 && git switch -c hotfix2
          git stash apply
          git commit -m 'Operación division arreglada'
          git tag -a v1.0.1 -m 'Operación division arreglada'
       7. git checkout desa && git stash apply
          git commit -m 'Operación division arreglada'

GIT STASH:
- Cuando me tengo que ir de una rama a otra y no quiero llevar los cambios
- Cuando tengo unos cambios que he hecho en una rama y quiero llevarlos a otras ramas: git stash
    HOTFIX

Alternativas:
- Haber hecho un commit... que luego me tengo que acordar de borrar
- Hacer 500 cherry-picks

---
GIT NOTES: EL GRAN DESCONOCIDO = ES UNA BRUTALIDAD (parece una gilipollez)

Hay un bug, que identifico en un commit. (GIT BISECT)
Como lo puedo apuntar.. en el commit, que tiene un bug?
- Cambiar el message: git rebase -i = DESASTRE MAS GRANDE QUE PODRIA HACER!!!
    El git rebase Interactivo: REESCRIBE EL COMMIT (CAMBIA EL ID)! Os recuerdo que ese comando solo 
    SOLO, Y SOLAMENTE podía usarlo para commits que no habían salido de mi máquina. 
- Añado un tag: git tag -a BUG-001289 COMMIT_ID -m 'Bug en la operación de división'
    Esto es matar moscas a cañonazos. Generar un tag para un bug... Si a lo mejor hay 5 bugs en el mismo commit
    El historial al sacarlo... MADRECITA me pierdo.
    Es más... puede ser que no quiera guardar solo el BUG-ID, sino que quiera guardar algo más de info que he rescatado: Información de la incidencia, el ticket, el enlace a la incidencia, Análisis de la incidencia, etc.

Las notas la gracia es que las puedo poner y quitar a posteriori sobre un commit, sin tener que reescribirlo.

           OPCIONAL.. pero es guay
         ///////////
    $ git notes --ref=bug add -m 'Bug en la operación de división.

    El bug se produce cuando el divisor es 0. En este caso, el resultado debería ser 0, pero se está devolviendo un NaN

    Se ha creado un ticket en JIRA para solucionar este bug: https://jira.com/bug-001289' COMMIT_ID

$ git notes remove COMMIT_ID
$ git notes edit COMMIT_ID
$ git notes show COMMIT_ID
$ git log --notes=TIPO_NOTA (lo que configuramos en el --ref=TIPO_NOTA)
$ git log --format='%h %N' --notes=TIPO_NOTA --all --graph

$ git push origin refs/notes/*
                  refs/notes/bug

$ git fetch origin refs/notes/*

---

# Servicios de alojamiento de repos remotos: GITLAB, GITHUB, BITBUCKET

GITHUB: Como SaaS
GITLAB: Como SaaS y OnPremise
BITBUCKET: Como SaaS y OnPremise

PULL REQUEST | MERGE REQUEST
Protected branches

Al trabajar tenemos 2 opciones:
- OPCION 1: 
    CREO EL REPO EN GITHUB, GITLAB, BITBUCKET (LO INICIALIZO) 
    después hago un clone!
    ... y el resto de la gente también hace CLONE
- OPCION 2: 
    CREO EL REPO EN LOCAL (git init), 
    también en GITHUB, GITLAB, BITBUCKET (SIN INICIALIZAR) 
    luego congiguro el remoto en local: git remote add origin URL
    después hago un push!
    ... y el resto de la gente un CLONE

---

# .gitignore

Es un fichero que indica que rutas que deben excluirse al hacer add.

```.gitignore

# Comentarios
/carpeta/fichero1                   # Ignora el archivo en esa ruta... desde la raíz del proyecto
carpeta/fichero1                    # Ignora cualquier archivo llamado fichero1 en cualquier carpeta
                                    # llamada "carpeta", independientemente de la profundidad a la 
                                    # que se encuentre esa carpeta "carpeta"
**/carpeta/fichero1                 # Igual que el anterior

fichero1                            # Ignora cualquier archivo llamado fichero1 en cualquier carpeta
                                    # del proyecto  

carpeta/                            # Ignora cualquier archivo en la carpeta "carpeta" y en sus         
                                    # subcarpetas... Da igual donde esté la carpeta "carpeta" 
/carpeta/                           # Ignora la carpeta "carpeta" del raiz del proyecto y todo
                                    # lo que tenga dentro
!/carpeta/fichero2                  # No ignores el fichero2 que está en la carpeta "carpeta"
```

https://ivanciniGT@bitbucket.org/ivanciniGT/pruebaformaciongit.git
