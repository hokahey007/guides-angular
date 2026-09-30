# Guía rápida: crear un fork y mantenerlo actualizado en GitHub

> [!info] Objetivo
> Aprender a crear un **fork** de un repositorio de GitHub, clonarlo en nuestro equipo y mantenerlo actualizado cuando el repositorio original reciba nuevos cambios.

En esta guía utilizaremos como repositorio original:

```text
https://github.com/hokahey007/guides-angular.git
```

---

## Índice

- [[#1. ¿Qué es un fork?|1. ¿Qué es un fork?]]
- [[#2. Crear el fork en GitHub|2. Crear el fork en GitHub]]
- [[#3. Clonar nuestro fork|3. Clonar nuestro fork]]
- [[#4. Comprobar los repositorios remotos|4. Comprobar los repositorios remotos]]
- [[#5. Añadir el repositorio original como upstream|5. Añadir el repositorio original como upstream]]
- [[#6. Comprobar el estado antes de actualizar|6. Comprobar el estado antes de actualizar]]
- [[#7. Actualizar el fork desde el repositorio original|7. Actualizar el fork desde el repositorio original]]
  - [[#7.1. Descargar los cambios del repositorio original|7.1. Descargar los cambios del repositorio original]]
  - [[#7.2. Integrar los cambios en nuestra rama main|7.2. Integrar los cambios en nuestra rama main]]
  - [[#7.3. Actualizar nuestro fork en GitHub|7.3. Actualizar nuestro fork en GitHub]]
- [[#8. Alternativa rápida utilizando git pull|8. Alternativa rápida utilizando git pull]]
- [[#9. Actualizar el fork desde la web de GitHub|9. Actualizar el fork desde la web de GitHub]]
- [[#10. Ciclo habitual de actualización|10. Ciclo habitual de actualización]]
- [[#11. Trabajar en nuestras propias ramas|11. Trabajar en nuestras propias ramas]]
- [[#12. Qué ocurre si aparecen conflictos|12. Qué ocurre si aparecen conflictos]]
- [[#13. Errores habituales|13. Errores habituales]]
- [[#14. Esquema completo|14. Esquema completo]]
- [[#15. Chuleta de comandos|15. Chuleta de comandos]]

---

## 1. ¿Qué es un fork?

Un **fork** es una copia de un repositorio de GitHub que se crea dentro de nuestra propia cuenta.

En este caso partiremos de:

```text
hokahey007/guides-angular
```

El esquema inicial será:

```text
Repositorio original
hokahey007/guides-angular
        │
        │ Fork
        ▼
Nuestra cuenta de GitHub
mi-usuario/guides-angular
```

El fork permite disponer de una copia propia del proyecto sin modificar directamente el repositorio original.

> [!important]
> Un fork no es lo mismo que un `clone`.
>
> - **Fork**: crea una copia del repositorio dentro de nuestra cuenta de GitHub.
> - **Clone**: descarga un repositorio desde GitHub a nuestro equipo.

---

## 2. Crear el fork en GitHub

Accedemos al repositorio:

```text
https://github.com/hokahey007/guides-angular
```

En la parte superior de GitHub pulsamos:

```text
Fork
```

Seleccionamos nuestra cuenta como destino.

GitHub creará un repositorio similar a:

```text
https://github.com/mi-usuario/guides-angular
```

> [!note]
> En esta guía utilizaremos `mi-usuario` como ejemplo.
>
> Cada alumno deberá sustituirlo por su nombre de usuario real de GitHub.

---

## 3. Clonar nuestro fork

Debemos clonar **nuestro fork**, no directamente el repositorio original.

Desde nuestro fork pulsamos:

```text
Code → HTTPS
```

Copiamos la URL y ejecutamos:

```bash
git clone https://github.com/mi-usuario/guides-angular.git
```

Entramos en el proyecto:

```bash
cd guides-angular
```

Podemos comprobar que Git reconoce correctamente el repositorio:

```bash
git status
```

---

## 4. Comprobar los repositorios remotos

Ejecutamos:

```bash
git remote -v
```

Inicialmente aparecerá algo parecido a:

```text
origin  https://github.com/mi-usuario/guides-angular.git (fetch)
origin  https://github.com/mi-usuario/guides-angular.git (push)
```

Git ha creado automáticamente el remoto:

```text
origin
```

`origin` representa **nuestro fork en GitHub**.

---

## 5. Añadir el repositorio original como upstream

Ahora indicaremos a Git cuál es el repositorio original del que procede nuestro fork.

Ejecutamos:

```bash
git remote add upstream https://github.com/hokahey007/guides-angular.git
```

Comprobamos el resultado:

```bash
git remote -v
```

Deberíamos obtener algo similar a:

```text
origin    https://github.com/mi-usuario/guides-angular.git
upstream  https://github.com/hokahey007/guides-angular.git
```

La relación es:

| Remoto | Representa |
|---|---|
| `origin` | Nuestro fork en GitHub |
| `upstream` | Repositorio original |

> [!tip] Regla fácil de recordar
> **origin** → mi repositorio  
> **upstream** → repositorio original

La configuración de `upstream` solo es necesario realizarla **una vez**.

---

## 6. Comprobar el estado antes de actualizar

Antes de sincronizar el proyecto es recomendable comprobar que no tenemos cambios locales pendientes:

```bash
git status
```

Lo ideal es obtener un mensaje equivalente a:

```text
nothing to commit, working tree clean
```

Si tenemos archivos modificados, conviene decidir antes qué hacer con ellos:

- crear un commit;
- guardarlos temporalmente;
- o descartar los cambios si no son necesarios.

> [!warning]
> Actualizar una rama que contiene modificaciones locales sin guardar puede provocar conflictos o impedir determinadas operaciones de Git.

---

## 7. Actualizar el fork desde el repositorio original

Supongamos que se han añadido nuevas guías o se han modificado archivos en:

```text
hokahey007/guides-angular
```

Nuestro fork puede quedarse desactualizado.

El proceso recomendado consta de tres pasos.

---

### 7.1. Descargar los cambios del repositorio original

Ejecutamos:

```bash
git fetch upstream
```

`fetch` consulta el repositorio original y descarga sus nuevas referencias, commits y ramas.

> [!important]
> `git fetch upstream` **no modifica todavía nuestros archivos de trabajo**.
>
> Solo actualiza la información que Git conoce sobre el repositorio original.

Podemos comprobar las ramas:

```bash
git branch -a
```

Podremos encontrar referencias como:

```text
main
remotes/origin/main
remotes/upstream/main
```

Su significado es:

```text
main
└── nuestra rama local

origin/main
└── rama main de nuestro fork

upstream/main
└── rama main del repositorio original
```

---

### 7.2. Integrar los cambios en nuestra rama main

Nos situamos en `main`:

```bash
git switch main
```

Integramos los cambios descargados:

```bash
git merge upstream/main
```

El proceso ha sido:

```text
Repositorio original
upstream/main
      │
      │ git fetch upstream
      ▼
Información descargada
      │
      │ git merge upstream/main
      ▼
Rama local main
```

Ahora nuestra rama local contiene los últimos cambios del repositorio original.

Podemos comprobar el historial reciente con:

```bash
git log --oneline --graph --decorate -10
```

---

### 7.3. Actualizar nuestro fork en GitHub

En este momento hemos actualizado el repositorio **local**, pero nuestro fork alojado en GitHub puede seguir desactualizado.

Lo sincronizamos mediante:

```bash
git push origin main
```

El flujo completo es:

```text
Repositorio original
    upstream/main
         │
         │ fetch + merge
         ▼
Repositorio local
       main
         │
         │ push
         ▼
Nuestro fork
    origin/main
```

---

## 8. Alternativa rápida utilizando git pull

Los comandos:

```bash
git fetch upstream
git merge upstream/main
```

pueden sustituirse por:

```bash
git pull upstream main
```

Por tanto, una actualización rápida podría realizarse mediante:

```bash
git switch main
git pull upstream main
git push origin main
```

> [!note]
> Para aprender Git resulta más claro utilizar inicialmente:
>
> ```bash
> git fetch upstream
> git merge upstream/main
> ```
>
> Así distinguimos entre **descargar cambios** e **integrarlos**.

---

## 9. Actualizar el fork desde la web de GitHub

GitHub también permite sincronizar un fork desde su interfaz web.

Dentro de nuestro fork puede aparecer la opción:

```text
Sync fork
```

Y posteriormente:

```text
Update branch
```

Esto permite actualizar la rama del fork con los cambios del repositorio original.

Después, si también tenemos un clon local, deberemos descargar esos cambios:

```bash
git switch main
git pull origin main
```

> [!tip]
> Existen por tanto dos formas habituales de sincronización:
>
> **Desde Git:**
>
> ```text
> upstream → local → origin
> ```
>
> **Desde GitHub:**
>
> ```text
> upstream → origin → local
> ```

Para aprender el funcionamiento de los repositorios remotos es recomendable practicar primero el procedimiento con `upstream`.

---

## 10. Ciclo habitual de actualización

Una vez configurado el proyecto, el ciclo normal será:

```bash
git switch main
git status
git fetch upstream
git merge upstream/main
git push origin main
```

De forma resumida:

```text
UPSTREAM
hokahey007/guides-angular
        │
        │ fetch
        ▼
Repositorio local
        │
        │ merge
        ▼
local/main
        │
        │ push
        ▼
ORIGIN
mi-usuario/guides-angular
```

> [!summary] Regla fundamental
> Para mantener nuestro fork actualizado:
>
> **`upstream` → repositorio local → `origin`**

---

## 11. Trabajar en nuestras propias ramas

Si queremos realizar modificaciones propias, es recomendable mantener `main` sincronizada con el proyecto original y trabajar en una rama independiente.

Primero actualizamos `main`:

```bash
git switch main
git pull upstream main
```

Después creamos una rama:

```bash
git switch -c mis-cambios
```

Realizamos las modificaciones.

Añadimos los archivos:

```bash
git add .
```

Creamos el commit:

```bash
git commit -m "Actualiza contenidos de Angular"
```

Subimos la rama a nuestro fork:

```bash
git push -u origin mis-cambios
```

El esquema será:

```text
main
│
├── sincronizada con upstream/main
│
└── mis-cambios
      ├── modificaciones propias
      ├── commits propios
      └── push → origin/mis-cambios
```

Esta estrategia facilita que `main` continúe funcionando como referencia del repositorio original.

---

## 12. Qué ocurre si aparecen conflictos

Puede producirse un conflicto si nosotros y el repositorio original hemos modificado las mismas líneas de un archivo.

Durante un `merge`, Git indicará qué archivos están afectados.

Podemos comprobarlos mediante:

```bash
git status
```

Dentro del archivo podremos encontrar marcas similares a:

```text
<<<<<<< HEAD
nuestro contenido
=======
contenido procedente de upstream
>>>>>>> upstream/main
```

Debemos:

1. decidir qué contenido conservar;
2. eliminar las marcas de conflicto;
3. guardar el archivo;
4. añadir el archivo resuelto:

```bash
git add nombre-del-archivo
```

5. finalizar el merge:

```bash
git commit
```

Finalmente actualizamos nuestro fork:

```bash
git push origin main
```

> [!warning]
> No debemos borrar las marcas de conflicto sin revisar qué versión del contenido queremos conservar.

---

## 13. Errores habituales

### Error: `upstream` ya existe

Si ejecutamos:

```bash
git remote add upstream https://github.com/hokahey007/guides-angular.git
```

y obtenemos:

```text
remote upstream already exists
```

significa que ya lo habíamos configurado.

Podemos comprobarlo mediante:

```bash
git remote -v
```

---

### Error: hemos clonado directamente el repositorio original

Si `git remote -v` muestra:

```text
origin https://github.com/hokahey007/guides-angular.git
```

significa que probablemente hemos clonado el repositorio original en lugar de nuestro fork.

Nuestro `origin` debería apuntar a:

```text
https://github.com/mi-usuario/guides-angular.git
```

---

### Cambiar la URL de origin

Si necesitamos corregirla:

```bash
git remote set-url origin https://github.com/mi-usuario/guides-angular.git
```

Comprobamos:

```bash
git remote -v
```

---

### Consultar directamente la URL de upstream

Podemos ejecutar:

```bash
git remote get-url upstream
```

Debería mostrar:

```text
https://github.com/hokahey007/guides-angular.git
```

---

### No sabemos en qué rama estamos

Ejecutamos:

```bash
git branch
```

La rama activa aparecerá marcada con `*`.

También podemos utilizar:

```bash
git status
```

---

## 14. Esquema completo

```text
                  REPOSITORIO ORIGINAL
              hokahey007/guides-angular
                    upstream/main
                          │
                          │
                   git fetch upstream
                          │
                          ▼
                 ┌─────────────────┐
                 │ REPOSITORIO     │
                 │ LOCAL           │
                 │ guides-angular  │
                 └────────┬────────┘
                          │
               git merge upstream/main
                          │
                          ▼
                     local/main
                          │
                          │
                  git push origin main
                          │
                          ▼
                    NUESTRO FORK
              mi-usuario/guides-angular
                     origin/main
```

Conceptualmente:

```text
UPSTREAM
Repositorio original
      │
      ▼
    LOCAL
      │
      ▼
   ORIGIN
Nuestro fork
```

---

## 15. Chuleta de comandos

### Configuración inicial

Clonar nuestro fork:

```bash
git clone https://github.com/mi-usuario/guides-angular.git
```

Entrar en el repositorio:

```bash
cd guides-angular
```

Añadir el repositorio original:

```bash
git remote add upstream https://github.com/hokahey007/guides-angular.git
```

Comprobar los remotos:

```bash
git remote -v
```

---

### Actualización habitual

```bash
git switch main
git status
git fetch upstream
git merge upstream/main
git push origin main
```

---

### Actualización rápida

```bash
git switch main
git pull upstream main
git push origin main
```

---

### Comandos de comprobación útiles

Estado del repositorio:

```bash
git status
```

Rama actual:

```bash
git branch
```

Ramas locales y remotas:

```bash
git branch -a
```

Repositorios remotos:

```bash
git remote -v
```

Últimos commits:

```bash
git log --oneline --graph --decorate -10
```

---

> [!summary] Resumen final
> Para trabajar correctamente con un fork debemos diferenciar tres elementos:
>
> | Elemento | Ejemplo |
> |---|---|
> | Repositorio original | `hokahey007/guides-angular` |
> | Nuestro fork | `mi-usuario/guides-angular` |
> | Repositorio local | carpeta `guides-angular` de nuestro equipo |
>
> El ciclo normal de actualización será:
>
> ```text
> upstream → local → origin
> ```
>
> y se implementa mediante:
>
> ```bash
> git fetch upstream
> git merge upstream/main
> git push origin main
> ```
