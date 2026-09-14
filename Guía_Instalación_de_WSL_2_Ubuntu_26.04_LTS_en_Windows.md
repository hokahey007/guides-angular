<style>
:root {
  --primary: #2563eb;
  --primary-dark: #1e3a8a;
  --secondary: #475569;
  --success: #15803d;
  --warning: #b45309;
  --danger: #b91c1c;
  --surface: #f8fafc;
  --surface-blue: #eff6ff;
  --surface-green: #f0fdf4;
  --surface-yellow: #fffbeb;
  --surface-red: #fef2f2;
  --border: #cbd5e1;
  --code-bg: #f6f8fa;
  --code-header: #171717;
  --text: #1e293b;
}
.code-card {
  margin: 1.2rem 0;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  overflow: hidden;
  background: var(--code-bg);
}
.code-title {
  padding: 0.45rem 0.9rem;
  background: var(--code-header);
  color: white;
  font-family: Arial, sans-serif;
  font-size: 0.78rem;
  font-weight: bold;
  letter-spacing: 0.08em;
}
.code-card pre {
  margin: 0;
  padding: 1rem;
  overflow-x: auto;
  background: var(--code-bg);
}
.code-card code {
  font-family: Consolas, "Courier New", monospace;
  font-size: 0.92rem;
  color: #111827;
}
.concept-card {
  margin: 1rem 0;
  padding: 1rem 1.2rem;
  border-left: 5px solid var(--primary);
  border-radius: 6px;
  background: var(--surface-blue);
}
.success-card {
  margin: 1rem 0;
  padding: 1rem 1.2rem;
  border-left: 5px solid var(--success);
  border-radius: 6px;
  background: var(--surface-green);
}
.warning-card {
  margin: 1rem 0;
  padding: 1rem 1.2rem;
  border-left: 5px solid var(--warning);
  border-radius: 6px;
  background: var(--surface-yellow);
}
.danger-card {
  margin: 1rem 0;
  padding: 1rem 1.2rem;
  border-left: 5px solid var(--danger);
  border-radius: 6px;
  background: var(--surface-red);
}
</style>

# 🐧 WSL 2 + Ubuntu 26.04 LTS

## Guía de instalación y configuración en Windows

> **Objetivo de la unidad**
>
> Instalar **WSL 2** y **Ubuntu 26.04 LTS** sobre Windows y preparar un entorno Linux integrado adecuado para trabajar con herramientas de desarrollo como Git, Java, Node.js, Angular, Maven, PostgreSQL o Docker.

---

## 📑 Contenidos

1. [[#1. ¿Qué es WSL?|¿Qué es WSL?]]
2. [[#2. Arquitectura del entorno|Arquitectura del entorno]]
3. [[#3. Requisitos previos|Requisitos previos]]
4. [[#4. Comprobar la virtualización|Comprobar la virtualización]]
5. [[#5. Abrir PowerShell como administrador|Abrir PowerShell como administrador]]
6. [[#6. Comprobar WSL|Comprobar WSL]]
7. [[#7. Instalar WSL|Instalar WSL]]
8. [[#8. Actualizar WSL|Actualizar WSL]]
9. [[#9. Configurar WSL 2|Configurar WSL 2]]
10. [[#10. Distribuciones disponibles|Distribuciones disponibles]]
11. [[#11. Instalar Ubuntu 26.04|Instalar Ubuntu 26.04]]
12. [[#12. Primera ejecución|Primera ejecución]]
13. [[#13. Actualizar Ubuntu|Actualizar Ubuntu]]
14. [[#14. Herramientas básicas|Herramientas básicas]]
15. [[#15. Comprobar WSL 2|Comprobar WSL 2]]
16. [[#16. Sistema de archivos|Sistema de archivos]]
17. [[#17. Integración Windows/Linux|Integración Windows/Linux]]
18. [[#18. systemd|systemd]]
19. [[#19. Visual Studio Code|Visual Studio Code]]
20. [[#20. Comandos fundamentales|Comandos fundamentales]]
21. [[#21. Solución de problemas|Solución de problemas]]
22. [[#22. Copias de seguridad|Copias de seguridad]]
23. [[#23. Estructura para desarrollo|Estructura para desarrollo]]
24. [[#24. Comprobación final|Comprobación final]]
25. [[#25. Arquitectura final|Arquitectura final]]
26. [[#26. Resumen de comandos|Resumen de comandos]]
27. [[#27. Conceptos clave|Conceptos clave]]
28. [[#28. 🧪 Actividad de comprobación|🧪 Actividad de comprobación]]
29. [[#29. 🤔 Preguntas de reflexión|🤔 Preguntas de reflexión]]
30. [[#🎯 Resultado|🎯 Resultado]]

---

# 1. ¿Qué es WSL?

**WSL** son las siglas de:

> **Windows Subsystem for Linux**

o:

> **Subsistema de Windows para Linux**

WSL permite ejecutar una distribución GNU/Linux directamente sobre Windows.

Entre otras herramientas podremos utilizar:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>bash
git
ssh
curl
gcc
java
python
node
npm
docker
psql</code></pre>
</div>

Todo ello sin necesidad de instalar una máquina virtual tradicional con aplicaciones como VirtualBox o VMware.

<div class="concept-card">

### 💡 Idea clave

Con **WSL 2** utilizamos un auténtico **kernel Linux** ejecutado mediante una infraestructura de virtualización ligera e integrada con Windows.

</div>

---

# 2. Arquitectura del entorno

El equipo que utilizaremos tendrá aproximadamente esta arquitectura:

```text
┌─────────────────────────────────────────────┐
│                  WINDOWS 11                 │
│                                             │
│   ┌─────────────────────────────────────┐   │
│   │                WSL 2                │   │
│   │                                     │   │
│   │          Ubuntu 26.04 LTS           │   │
│   │                                     │   │
│   │ Git · Java · Node · npm · Maven     │   │
│   │ PostgreSQL · Docker · Python · ...  │   │
│   └─────────────────────────────────────┘   │
│                                             │
│       VS Code · Windows Terminal            │
└─────────────────────────────────────────────┘
```

Windows continúa siendo nuestro sistema operativo principal.

Ubuntu proporcionará el entorno Linux sobre el que instalaremos las herramientas de desarrollo.

---

# 3. Requisitos previos

Para realizar la instalación se recomienda disponer de:

| Requisito | Recomendación |
|---|---|
| 🪟 Sistema operativo | Windows 11 actualizado |
| 🧠 Arquitectura | Procesador de 64 bits |
| ⚙️ Virtualización | Intel VT-x / AMD-V habilitado |
| 🌐 Internet | Necesario para descargar WSL y Ubuntu |
| 👤 Usuario | Cuenta con permisos de administrador |
| 💾 Espacio | Al menos varios GB disponibles |

---

# 4. Comprobar la virtualización

WSL 2 necesita que el procesador tenga activada la virtualización por hardware.

Abrimos:

<div class="code-card">
<div class="code-title">WINDOWS</div>
<pre><code>Ctrl + Shift + Esc</code></pre>
</div>

Accedemos a:

```text
Administrador de tareas
       │
       └── Rendimiento
              │
              └── CPU
```

Buscamos:

```text
Virtualización: Habilitada
```

<div class="success-card">

### ✅ Resultado correcto

Si aparece **Virtualización: Habilitada**, podemos continuar.

</div>

Si aparece:

```text
Virtualización: Deshabilitada
```

será necesario acceder a la BIOS/UEFI.

Dependiendo del fabricante, la opción puede aparecer como:

- `Intel Virtualization Technology`
- `Intel VT-x`
- `AMD-V`
- `SVM`
- `SVM Mode`

---

# 5. Abrir PowerShell como administrador

Abrimos el menú Inicio y buscamos:

```text
PowerShell
```

o:

```text
Terminal
```

Seleccionamos:

> **Ejecutar como administrador**

<div class="warning-card">

### ⚠️ Importante

Las primeras operaciones deben realizarse desde **PowerShell o Windows Terminal con permisos de administrador**.

</div>

---

# 6. Comprobar WSL

Antes de instalar nada podemos comprobar si WSL está disponible.

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --version</code></pre>
</div>

Si WSL está instalado aparecerá información sobre componentes como:

```text
WSL
Kernel
WSLg
MSRDC
Direct3D
Windows
```

También podemos consultar su estado:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --status</code></pre>
</div>

---

# 7. Instalar WSL

Si todavía no está instalado ejecutamos:

<div class="code-card">
<div class="code-title">POWERSHELL · ADMINISTRADOR</div>
<pre><code>wsl --install</code></pre>
</div>

Este comando configura automáticamente los principales componentes necesarios para WSL.

Al finalizar puede aparecer un mensaje indicando que es necesario reiniciar Windows.

<div class="warning-card">

### 🔄 Reinicio

Si Windows solicita reiniciar el equipo, debemos hacerlo **antes de continuar con la configuración**.

</div>

---

# 8. Actualizar WSL

Después de reiniciar abrimos PowerShell y ejecutamos:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --update</code></pre>
</div>

Comprobamos nuevamente:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --version</code></pre>
</div>

También podemos comprobar el estado:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --status</code></pre>
</div>

---

# 9. Configurar WSL 2

Indicamos que las nuevas distribuciones deben utilizar **WSL 2**:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --set-default-version 2</code></pre>
</div>

<div class="concept-card">

### 💡 WSL 1 frente a WSL 2

Para nuestro entorno de desarrollo utilizaremos **WSL 2**, ya que proporciona un kernel Linux real y una compatibilidad considerablemente mayor con las herramientas actuales.

</div>

---

# 10. Distribuciones disponibles

Podemos consultar las distribuciones Linux disponibles:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --list --online</code></pre>
</div>

También podemos utilizar su versión abreviada:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl -l -o</code></pre>
</div>

Entre las distribuciones disponibles encontraremos diferentes versiones de Ubuntu.

Por ejemplo:

```text
NAME
Ubuntu
Ubuntu-26.04
Ubuntu-24.04
Ubuntu-22.04
...
```

Para este curso utilizaremos:

```text
Ubuntu-26.04
```

De esta forma todos trabajaremos con exactamente la misma versión.

---

# 11. Instalar Ubuntu 26.04

Ejecutamos:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --install -d Ubuntu-26.04</code></pre>
</div>

WSL descargará e instalará Ubuntu.

---

## 11.1. Problemas durante la descarga

Si la descarga se queda bloqueada podemos solicitar una descarga directa desde Internet:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --install --web-download -d Ubuntu-26.04</code></pre>
</div>

---

# 12. Primera ejecución

Podemos iniciar Ubuntu desde el menú Inicio:

```text
Inicio
  └── Ubuntu 26.04 LTS
```

También podemos hacerlo desde PowerShell:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl -d Ubuntu-26.04</code></pre>
</div>

Durante el primer inicio WSL terminará de preparar el sistema.

A continuación tendremos que crear un usuario Linux.

Por ejemplo:

```text
Enter new UNIX username: alumno
```

Indicamos:

```text
alumno
```

Después estableceremos una contraseña.

```text
New password:
Retype new password:
```

<div class="warning-card">

### 🔐 Contraseñas en Linux

Cuando escribimos una contraseña en la terminal Linux:

**no aparecen caracteres ni asteriscos en pantalla.**

No significa que el teclado haya dejado de funcionar.

Es el comportamiento normal de Linux.

</div>

---

## 12.1. Windows y Ubuntu utilizan usuarios diferentes

No debemos confundir nuestro usuario de Windows:

```text
C:\Users\Alumno
```

con nuestro usuario Linux:

```text
/home/alumno
```

Son cuentas diferentes.

---

## 12.2. Comprobar la versión instalada

Dentro de Ubuntu ejecutamos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>cat /etc/os-release</code></pre>
</div>

También podemos utilizar:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>lsb_release -a</code></pre>
</div>

---

## 12.3. Comprobar el kernel

Ejecutamos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>uname -r</code></pre>
</div>

Para obtener más información:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>uname -a</code></pre>
</div>

---

# 13. Actualizar Ubuntu

Una de las primeras operaciones que debemos realizar tras instalar cualquier distribución Linux es actualizar el sistema.

Primero actualizamos la información de los repositorios:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo apt update</code></pre>
</div>

Después actualizamos los paquetes:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo apt full-upgrade -y</code></pre>
</div>

Finalmente eliminamos paquetes que ya no sean necesarios:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo apt autoremove -y</code></pre>
</div>

---

## 13.1. ¿Qué significa `sudo`?

Algunas operaciones necesitan permisos administrativos.

Para ello utilizamos:

```text
sudo
```

Por ejemplo:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo apt update</code></pre>
</div>

Podemos interpretar `sudo` de forma simplificada como:

> **Ejecuta la siguiente orden con privilegios administrativos.**

---

## 13.2. ¿Qué es `apt`?

`apt` es el gestor de paquetes utilizado en Ubuntu.

Algunos comandos habituales son:

| Comando | Acción |
|---|---|
| `apt update` | Actualiza el catálogo de paquetes |
| `apt upgrade` | Actualiza paquetes |
| `apt install` | Instala paquetes |
| `apt remove` | Desinstala paquetes |
| `apt search` | Busca paquetes |
| `apt autoremove` | Elimina dependencias innecesarias |

---

# 14. Herramientas básicas

Instalaremos algunas herramientas que utilizaremos habitualmente durante el curso:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo apt install -y \
  build-essential \
  git \
  curl \
  wget \
  unzip \
  zip \
  ca-certificates \
  gnupg \
  software-properties-common</code></pre>
</div>

### ¿Qué estamos instalando?

| Paquete | Utilidad |
|---|---|
| `build-essential` | Compiladores y herramientas básicas |
| `git` | Control de versiones |
| `curl` | Transferencia de información mediante URL |
| `wget` | Descarga de archivos |
| `unzip` | Descompresión ZIP |
| `zip` | Creación de ZIP |
| `ca-certificates` | Certificados HTTPS |
| `gnupg` | Gestión de claves criptográficas |
| `software-properties-common` | Gestión de repositorios |

---

# 15. Comprobar WSL 2

Salimos de Ubuntu:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>exit</code></pre>
</div>

Desde PowerShell ejecutamos:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --list --verbose</code></pre>
</div>

También:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl -l -v</code></pre>
</div>

Podríamos obtener:

```text
  NAME             STATE           VERSION
* Ubuntu-26.04     Running         2
```

La parte importante es:

```text
VERSION
   2
```

<div class="success-card">

### ✅ Instalación correcta

Si Ubuntu aparece con `VERSION 2`, estamos trabajando con **WSL 2**.

</div>

---

## 15.1. Establecer Ubuntu 26.04 como distribución predeterminada

Si tenemos varias distribuciones instaladas:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --set-default Ubuntu-26.04</code></pre>
</div>

A partir de ese momento podremos abrir Ubuntu simplemente con:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl</code></pre>
</div>

---

## 15.2. Abrir directamente nuestro HOME

Podemos indicar a WSL que abra directamente nuestro directorio personal:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl ~</code></pre>
</div>

O especificando Ubuntu 26.04:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl ~ -d Ubuntu-26.04</code></pre>
</div>

---

# 16. Sistema de archivos

Nuestro directorio personal Linux se encuentra normalmente en:

```text
/home/usuario
```

Podemos comprobar nuestra ubicación:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>pwd</code></pre>
</div>

Por ejemplo:

```text
/home/alumno
```

Para volver en cualquier momento al directorio personal:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>cd ~</code></pre>
</div>

---

## 16.1. Crear una carpeta para proyectos

Crearemos:

```text
~/proyectos
```

Ejecutamos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>mkdir -p ~/proyectos
cd ~/proyectos
pwd</code></pre>
</div>

Resultado esperado:

```text
/home/alumno/proyectos
```

---

# 17. Integración Windows/Linux

WSL permite acceder fácilmente a ambos sistemas de archivos.

---

## 17.1. Acceder a Windows desde Linux

Las unidades de Windows se montan automáticamente dentro de:

```text
/mnt
```

Por ejemplo:

| Windows | WSL |
|---|---|
| `C:\` | `/mnt/c` |
| `D:\` | `/mnt/d` |
| `C:\Users` | `/mnt/c/Users` |

Podemos probar:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>ls /mnt/c</code></pre>
</div>

---

## 17.2. ¿Dónde debemos guardar los proyectos?

Esta cuestión es especialmente importante.

### ✅ Recomendado

```text
/home/alumno/proyectos
```

o:

```text
~/proyectos
```

### ❌ Evitar para proyectos Linux

```text
/mnt/c/Users/Alumno/proyectos
```

<div class="concept-card">

### 💡 Regla práctica

Si las herramientas que manipulan el proyecto son principalmente herramientas Linux:

- Git
- npm
- Node.js
- Maven
- Gradle
- Java
- GCC
- Docker

es preferible almacenar los archivos **dentro del sistema de archivos de Linux**.

</div>

Esto proporciona generalmente mejor rendimiento que trabajar continuamente contra NTFS mediante `/mnt/c`.

---

## 17.3. Acceder a Linux desde Windows

Desde el Explorador de archivos podemos introducir:

```text
\\wsl$
```

También:

```text
\\wsl.localhost
```

Podremos navegar aproximadamente por:

```text
Ubuntu-26.04
└── home
    └── alumno
        └── proyectos
```

---

## 17.4. Abrir una carpeta Linux en el Explorador

Desde Ubuntu podemos ejecutar:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>explorer.exe .</code></pre>
</div>

El símbolo:

```text
.
```

representa:

> **el directorio actual**

Por ejemplo:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>cd ~/proyectos
explorer.exe .</code></pre>
</div>

---

# 18. systemd

`systemd` es el sistema encargado, entre otras cosas, de administrar servicios Linux.

Podemos comprobar qué proceso se está ejecutando como proceso número 1:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>ps -p 1 -o comm=</code></pre>
</div>

Resultado esperado:

```text
systemd
```

También podemos ejecutar:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>systemctl status</code></pre>
</div>

Esto será especialmente útil cuando instalemos servicios como:

```text
PostgreSQL
MySQL
MariaDB
SSH
Docker
```

Por ejemplo:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo systemctl status postgresql</code></pre>
</div>

Reiniciar un servicio:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo systemctl restart postgresql</code></pre>
</div>

---

## 18.1. Activar `systemd` manualmente

En una instalación moderna normalmente no será necesario.

Si `systemd` no estuviera activo podemos editar:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>sudo nano /etc/wsl.conf</code></pre>
</div>

Introducimos:

<div class="code-card">
<div class="code-title">INI</div>
<pre><code>[boot]
systemd=true</code></pre>
</div>

Guardamos en `nano`:

```text
Ctrl + O
Enter
Ctrl + X
```

Después salimos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>exit</code></pre>
</div>

Y desde PowerShell:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --shutdown</code></pre>
</div>

Iniciamos nuevamente:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl</code></pre>
</div>

Comprobamos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>systemctl status</code></pre>
</div>

---

# 19. Visual Studio Code

Una de las grandes ventajas de WSL es poder utilizar:

> **Visual Studio Code ejecutándose en Windows**

mientras las herramientas de desarrollo se ejecutan:

> **dentro de Ubuntu**

La arquitectura será:

```text
┌──────────────────────────────┐
│           WINDOWS            │
│                              │
│     Visual Studio Code       │
│              │               │
│              ▼               │
│        Extensión WSL         │
└──────────────┬───────────────┘
               │
               │
               ▼
┌──────────────────────────────┐
│       Ubuntu 26.04 WSL       │
│                              │
│ Git                          │
│ Java                         │
│ Maven                        │
│ Node.js                      │
│ npm                          │
│ Angular CLI                  │
│ PostgreSQL                   │
│ ...                          │
└──────────────────────────────┘
```

---

## 19.1. Instalar la extensión WSL

En Visual Studio Code accedemos a:

```text
Extensions
```

Buscamos:

```text
WSL
```

e instalamos la extensión oficial de Microsoft.

---

## 19.2. Abrir un proyecto desde Ubuntu

Entramos en nuestra carpeta de proyectos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>cd ~/proyectos</code></pre>
</div>

Creamos un proyecto de prueba:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>mkdir ejemplo
cd ejemplo</code></pre>
</div>

Abrimos VS Code:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>code .</code></pre>
</div>

VS Code se ejecutará en Windows pero se conectará al entorno de Ubuntu.

En VS Code podremos ver una indicación similar a:

```text
WSL: Ubuntu-26.04
```

---

## 19.3. ¿Dónde se ejecutan las herramientas?

Si VS Code está conectado mediante WSL, herramientas como:

```text
Git
Java
Node.js
npm
Maven
Python
```

serán las instaladas dentro de Ubuntu.

Esto permite mantener el entorno de desarrollo perfectamente separado del software instalado directamente en Windows.

---

# 20. Comandos fundamentales

## 20.1. Comandos Linux

| Comando | Función |
|---|---|
| `pwd` | Muestra el directorio actual |
| `ls` | Lista archivos |
| `ls -la` | Lista todos los archivos |
| `cd carpeta` | Entra en una carpeta |
| `cd ..` | Sube un directorio |
| `cd ~` | Regresa al HOME |
| `mkdir carpeta` | Crea un directorio |
| `touch fichero` | Crea un fichero vacío |
| `cp` | Copia |
| `mv` | Mueve o renombra |
| `rm` | Elimina |
| `rm -r` | Elimina recursivamente |
| `clear` | Limpia la terminal |
| `sudo` | Ejecuta con privilegios |
| `apt` | Gestiona paquetes |
| `nano` | Editor de texto |
| `exit` | Sale de la sesión |

---

## 20.2. Comandos de WSL

Estos comandos se ejecutan desde Windows.

### Mostrar versión

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --version</code></pre>
</div>

### Mostrar estado

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --status</code></pre>
</div>

### Ver distribuciones instaladas

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl -l -v</code></pre>
</div>

### Ver distribuciones disponibles

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl -l -o</code></pre>
</div>

### Entrar en la distribución predeterminada

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl</code></pre>
</div>

### Entrar en Ubuntu 26.04

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl -d Ubuntu-26.04</code></pre>
</div>

### Abrir directamente el HOME

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl ~ -d Ubuntu-26.04</code></pre>
</div>

### Detener WSL

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --shutdown</code></pre>
</div>

### Actualizar WSL

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --update</code></pre>
</div>

---

# 21. Solución de problemas

## 21.1. `wsl` no se reconoce

Si aparece:

```text
'wsl' no se reconoce como un comando...
```

debemos comprobar:

1. Que Windows está actualizado.
2. Que estamos utilizando una versión compatible.
3. Que PowerShell está abierto como administrador.
4. Que las características necesarias están habilitadas.

---

## 21.2. WSL indica que la virtualización no está disponible

Abrimos:

```text
Administrador de tareas
    │
    └── Rendimiento
         │
         └── CPU
```

Comprobamos:

```text
Virtualización: Habilitada
```

Si está deshabilitada tendremos que activarla desde la BIOS/UEFI.

---

## 21.3. Activación manual de componentes

En condiciones normales no será necesario.

Si la instalación automática falla podemos habilitar manualmente WSL.

### Subsistema de Windows para Linux

<div class="code-card">
<div class="code-title">POWERSHELL · ADMINISTRADOR</div>
<pre><code>dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart</code></pre>
</div>

### Plataforma de máquina virtual

<div class="code-card">
<div class="code-title">POWERSHELL · ADMINISTRADOR</div>
<pre><code>dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart</code></pre>
</div>

Después reiniciamos Windows.

Finalmente:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --update</code></pre>
</div>

---

## 21.4. Eliminar completamente Ubuntu

<div class="danger-card">

### ⛔ Atención

La siguiente operación elimina completamente:

- programas,
- configuración,
- usuarios,
- proyectos,
- bases de datos,
- archivos

almacenados dentro de esa distribución.

</div>

Primero comprobamos el nombre:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl -l -v</code></pre>
</div>

Después:

<div class="code-card">
<div class="code-title">POWERSHELL · OPERACIÓN DESTRUCTIVA</div>
<pre><code>wsl --unregister Ubuntu-26.04</code></pre>
</div>

---

## 21.5. Reinstalar Ubuntu

Una vez eliminada:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --install -d Ubuntu-26.04</code></pre>
</div>

Obtendremos una instalación completamente nueva.

---

# 22. Copias de seguridad

WSL permite exportar una distribución Linux completa.

Por ejemplo:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --export Ubuntu-26.04 ubuntu-26.04-backup.tar</code></pre>
</div>

Se generará:

```text
ubuntu-26.04-backup.tar
```

que contendrá una copia de la distribución.

<div class="concept-card">

### 💡 ¿Qué puede contener el backup?

Una exportación de WSL puede incluir:

- aplicaciones instaladas,
- configuración,
- usuarios,
- proyectos,
- repositorios Git,
- bases de datos,
- herramientas de desarrollo.

</div>

---

# 23. Estructura para desarrollo

Para mantener nuestro trabajo organizado utilizaremos una estructura como:

```text
/home/alumno/
│
├── proyectos/
│   │
│   ├── java/
│   │
│   ├── angular/
│   │
│   ├── node/
│   │
│   ├── bases-datos/
│   │
│   └── ejercicios/
│   │
│   └── ...
│
└── .ssh/
```

Podemos crearla de una sola vez:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>mkdir -p ~/proyectos/{java,angular,node,bases-datos,ejercicios}</code></pre>
</div>

Comprobamos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>ls ~/proyectos</code></pre>
</div>

Obtendremos:

```text
angular
bases-datos
ejercicios
java
node
```

---

# 24. Comprobación final

Desde PowerShell ejecutamos:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl --version
wsl --status
wsl -l -v</code></pre>
</div>

Entramos en Ubuntu:

<div class="code-card">
<div class="code-title">POWERSHELL</div>
<pre><code>wsl ~ -d Ubuntu-26.04</code></pre>
</div>

Y comprobamos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>
<pre><code>cat /etc/os-release
uname -r
whoami
pwd
git --version
systemctl status</code></pre>
</div>

Nuestra instalación debería cumplir:

- ✅ WSL instalado.
- ✅ WSL actualizado.
- ✅ WSL versión 2.
- ✅ Ubuntu 26.04 LTS instalado.
- ✅ Usuario Linux creado.
- ✅ Ubuntu actualizado.
- ✅ Git instalado.
- ✅ `systemd` funcionando.
- ✅ Directorio `~/proyectos` creado.

---

# 25. Arquitectura final

Al finalizar tendremos aproximadamente:

```text
WINDOWS 11
│
├── Windows Terminal
│
├── Visual Studio Code
│      │
│      └── Extensión WSL
│
└── WSL 2
       │
       └── Ubuntu 26.04 LTS
              │
              ├── Git
              ├── curl
              ├── wget
              ├── build-essential
              ├── systemd
              │
              └── ~/proyectos
                     │
                     ├── java/
                     ├── angular/
                     ├── node/
                     ├── bases-datos/
                     └── ejercicios/
```

---

# 26. Resumen de comandos

## 🪟 Windows / PowerShell

| Objetivo | Comando |
|---|---|
| Instalar WSL | `wsl --install` |
| Actualizar WSL | `wsl --update` |
| Ver versión | `wsl --version` |
| Ver estado | `wsl --status` |
| Distribuciones disponibles | `wsl -l -o` |
| Distribuciones instaladas | `wsl -l -v` |
| Instalar Ubuntu 26.04 | `wsl --install -d Ubuntu-26.04` |
| Iniciar Ubuntu 26.04 | `wsl -d Ubuntu-26.04` |
| Abrir HOME | `wsl ~ -d Ubuntu-26.04` |
| Detener WSL | `wsl --shutdown` |

---

## 🐧 Ubuntu / Bash

| Objetivo | Comando |
|---|---|
| Actualizar repositorios | `sudo apt update` |
| Actualizar sistema | `sudo apt full-upgrade -y` |
| Limpiar dependencias | `sudo apt autoremove -y` |
| Mostrar ubicación | `pwd` |
| Listar archivos | `ls -la` |
| Ir al HOME | `cd ~` |
| Entrar en proyectos | `cd ~/proyectos` |
| Abrir Explorador | `explorer.exe .` |
| Abrir VS Code | `code .` |
| Ver kernel | `uname -r` |
| Ver Ubuntu | `cat /etc/os-release` |

---

# 27. Conceptos clave

| Concepto | Debemos recordar |
|---|---|
| **WSL** | Subsistema Linux integrado en Windows |
| **WSL 2** | Utiliza un auténtico kernel Linux |
| **Ubuntu** | Distribución GNU/Linux |
| **HOME** | Directorio personal del usuario |
| `~` | Representa nuestro HOME |
| `/mnt/c` | Unidad `C:` de Windows desde Linux |
| `/home/alumno` | HOME Linux del usuario |
| `sudo` | Ejecuta con privilegios |
| `apt` | Gestor de paquetes de Ubuntu |
| `systemd` | Sistema de gestión de servicios |
| `code .` | Abre VS Code en el directorio actual |
| `explorer.exe .` | Abre el Explorador de Windows |

---

# 28. 🧪 Actividad de comprobación

Realiza las siguientes operaciones sin copiar directamente los comandos anteriores.

### Parte 1. WSL

1. Comprueba la versión de WSL.
2. Comprueba las distribuciones instaladas.
3. Comprueba que Ubuntu utiliza WSL 2.

### Parte 2. Ubuntu

4. Accede a Ubuntu.
5. Comprueba tu usuario.
6. Comprueba el directorio en el que te encuentras.
7. Muestra la versión de Ubuntu.
8. Muestra la versión del kernel.

### Parte 3. Sistema de archivos

9. Accede a `~/proyectos`.
10. Crea un directorio llamado:

```text
prueba-wsl
```

11. Entra en él.
12. Crea un archivo:

```text
README.md
```

13. Comprueba que existe.
14. Abre la carpeta mediante el Explorador de Windows.

### Parte 4. VS Code

15. Abre el directorio utilizando:

```text
code .
```

16. Comprueba que VS Code indica que está conectado mediante WSL.

---

# 29. 🤔 Preguntas de reflexión

1. ¿Qué diferencia existe entre WSL 1 y WSL 2?
2. ¿Por qué WSL 2 necesita virtualización?
3. ¿Es el usuario Linux el mismo que el usuario de Windows?
4. ¿Qué representa `~` en Linux?
5. ¿Qué representa `/mnt/c`?
6. ¿Por qué es recomendable guardar los proyectos Linux en `/home/usuario`?
7. ¿Para qué utilizamos `sudo`?
8. ¿Qué función realiza `apt`?
9. ¿Qué es `systemd`?
10. ¿Dónde se ejecuta Java cuando VS Code está conectado a WSL y Java está instalado únicamente dentro de Ubuntu?

---

# 🎯 Resultado

Después de completar esta guía tendremos disponible un entorno:

**Windows 11 + WSL 2 + Ubuntu 26.04 LTS**

preparado para continuar instalando herramientas de desarrollo como:

```text
Git
Java / OpenJDK
Maven
Gradle
Node.js
npm
Angular CLI
PostgreSQL
Docker
Python
```

> [!TIP]
> Como regla general durante el curso, los proyectos que se ejecuten con herramientas Linux se almacenarán dentro de:
>
> `~/proyectos`
>
> en lugar de hacerlo dentro de `/mnt/c`.

---

## 🚀 Siguiente paso

Una vez configurado correctamente WSL, podremos preparar el entorno completo de desarrollo instalando:

```text
NVM
Node.js
npm
Angular CLI
Java 25
Maven
PostgreSQL
Git
Visual Studio Code + WSL
```
