# Despliegue y desarrollo de HOSPITAL_2 en Tomcat 7 (VS Code)

## 1. Requisitos de entorno

- JDK 8 instalado: `C:\Program Files\Java\jdk1.8.0_231`
- Ant 1.9.x instalado: `C:\ant\apache-ant-1.9.14`
- Tomcat 7.0.96 instalado en:
  - `C:\Users\GG01173\Documents\apache-tomcat-7.0.96-windows-x64\apache-tomcat-7.0.96`
- Workspace de VS Code apuntando a la carpeta `HOSPITAL` (raíz del proyecto Ant).

## 2. Build y despliegue de HOSPITAL_2

HOSPITAL_2 es el código fuente que se empaqueta como `HOSPITAL.war`.

### 2.1 Build completo inicial

Script: `package.bat` (raíz de HOSPITAL)

- Fija `JAVA_HOME` a JDK 8 y ajusta `PATH`.
- Llama a Ant con:
  - `-Duse.hospital2=true`
  - Targets: `acercaDe clean compile copyLogs updateLogs package clean`
- Genera en `dist/`:
  - Todos los `.jar` de negocio y módulos.
  - Todos los `.war` de las aplicaciones web, incluyendo `HOSPITAL.war` basado en `HOSPITAL_2`.

Usar este build cuando:
- Se monta el entorno por primera vez.
- Se cambian librerías, configuración global o se quiere regenerar todo desde cero.

### 2.2 Build rápido de HOSPITAL_2 + BUSINESS

Script: `package_hospital_fast.bat` (raíz de HOSPITAL)

- Fija JDK 8.
- Llama a Ant con:
  - `-Duse.hospital2=true`
  - Targets: `compile-hospital package-hospital`
- Los targets nuevos en `build.xml` hacen:
  - `compile-hospital`:
    - Compila solo:
      - `HOSPITAL-BUSINESS`, `AFIP`, `ALFABETA`, `BIONEXO`, `VALIDADORES`, `HOSPITAL`.
  - `package-hospital`:
    - Genera solo:
      - `HOSPITAL-BUSINESS.jar`, `AFIP.jar`, `ALFABETA.jar`, `BIONEXO.jar`, `VALIDADORES.jar`.
      - `HOSPITAL.war` (usando `HOSPITAL_2`).
- Al final copia `dist\HOSPITAL.war` a:
  - `apache-tomcat-7.0.96\webapps\HOSPITAL.war`.

Este es el build recomendado para actualizar HOSPITAL en el día a día.

### 2.3 Tareas de VS Code para el build rápido

En `.vscode/tasks.json`:

- **`build-hospital2`**
  - Comando: `./package_hospital_fast.bat`
  - Carpeta de trabajo: raíz del proyecto.
- **`build-and-start-hospital2`**
  - Tarea compuesta que ejecuta en orden:
    1. `build-hospital2` (build rápido + copia del WAR).
    2. `start-tomcat7` (arranca Tomcat con Java 8).

## 3. Arranque de Tomcat con Java 8

### 3.1 Configuración de Java para Tomcat

En `apache-tomcat-7.0.96\bin\setenv.bat`:

```bat
@echo off

set "JAVA_HOME=C:\Program Files\Java\jdk1.8.0_231"
set "JRE_HOME=%JAVA_HOME%"
set "PATH=%JAVA_HOME%\bin;%PATH%"
```

Tomcat carga este archivo automáticamente al ejecutar `startup.bat` o `catalina.bat`.

### 3.2 Tareas de VS Code para Tomcat

En `.vscode/tasks.json`:

- **`start-tomcat7`**
  - Comando: `startup.bat`
  - `cwd`: carpeta `bin` de la instalación de Tomcat.
  - Variables de entorno:
    - `CATALINA_HOME` y `CATALINA_BASE` apuntando a la instalación.
- **`run-tomcat7-console`**
  - Comando: `./catalina.bat run`
  - `cwd`: `bin` de Tomcat.
  - Misma configuración de `CATALINA_HOME` y `CATALINA_BASE`.
  - Deja Tomcat corriendo en la terminal de VS Code con todos los logs visibles.

Tareas compuestas:

- **`build-and-start-hospital2`**
  - Build rápido + arranque normal (`startup.bat`).
- **`build-and-run-hospital-only-dev`** (ver siguiente sección) 
  - Compila solo HOSPITAL y arranca Tomcat en modo consola.

## 4. Flujo de desarrollo rápido: solo HOSPITAL

Objetivo: recompilar únicamente el módulo HOSPITAL y copiar las clases directamente al webapp desplegado en Tomcat, sin regenerar JARs ni WAR.

### 4.1 Target Ant `compile-hospital-dev`

En `build.xml` se agregó el target:

```xml
<target name="compile-hospital-dev" description="compile only HOSPITAL into Tomcat webapps" if="tomcat.hospital.dir">
    <tstamp/>
    <mkdir dir="${tomcat.hospital.dir}/WEB-INF/classes"/>
    <echo message="compile HOSPITAL (dev, to Tomcat)" />
    <javac srcdir="${hospital.src}" destdir="${tomcat.hospital.dir}/WEB-INF/classes" debug="on" encoding="${build.encoding}">
        <classpath refid="dist" />
        <classpath refid="hospital.lib" />
    </javac>
</target>
```

- `tomcat.hospital.dir` se pasa como propiedad al llamar a Ant.
- Se reutilizan los JARs ya presentes en `WEB-INF/lib`.

### 4.2 Script `build_hospital_dev.bat`

En la raíz de HOSPITAL:

```bat
@ECHO OFF

call cls

rem Forzar uso de JDK 8
set "JAVA_HOME=C:\Program Files\Java\jdk1.8.0_231"
set "PATH=%JAVA_HOME%\bin;%PATH%"

rem Ruta del webapp HOSPITAL desplegado en Tomcat (carpeta ya expandida)
set "TOMCAT_HOSPITAL_DIR=C:\Users\GG01173\Documents\apache-tomcat-7.0.96-windows-x64\apache-tomcat-7.0.96\webapps\HOSPITAL"

"C:\ant\apache-ant-1.9.14\bin\ant.bat" -Duse.hospital2=true -Dtomcat.hospital.dir="%TOMCAT_HOSPITAL_DIR%" compile-hospital-dev

pause
```

### 4.3 Tareas VS Code para el flujo rápido

En `.vscode/tasks.json`:

- **`build-hospital-only-dev`**
  - Comando: `./build_hospital_dev.bat`
  - Grupo: `build`.
- **`build-and-run-hospital-only-dev`**
  - `dependsOn`: `build-hospital-only-dev`, `run-tomcat7-console`.
  - `dependsOrder`: `sequence`.

Uso recomendado:

1. Arrancar Tomcat en consola con `run-tomcat7-console` (o usar la tarea compuesta).
2. Cada vez que se cambie código Java en HOSPITAL, ejecutar `build-hospital-only-dev`.
3. Refrescar el navegador en `http://localhost:8085/HOSPITAL/`.

## 5. Compilar y correr otro proyecto web: ejemplo AGP

El módulo AGP ya está integrado en `build.xml`.

### 5.1 Generar AGP.war

- Ejecutar un build que incluya los targets `compile` y `package`:
  - Opción completa: `package.bat`.
  - O desde Ant: `ant compile package`.
- El target `package` genera:
  - `dist/AGP.war` usando el contenido de `AGP/WebRoot`.

### 5.2 Desplegar AGP en Tomcat

1. Copiar `dist/AGP.war` a:
   - `apache-tomcat-7.0.96\webapps\AGP.war`
2. Arrancar Tomcat (`start-tomcat7` o `run-tomcat7-console`).
3. Probar en el navegador:
   - `http://localhost:8085/AGP/`

Si se hacen cambios frecuentes en AGP y se necesita un flujo rápido similar al de HOSPITAL, se pueden añadir targets análogos: `compile-agp-dev`, etc.

## 6. Cambios principales realizados en el proyecto

### 6.1 build.xml

- Propiedad global de encoding: `build.encoding = ISO-8859-1`.
- Todos los `javac` usan `encoding="${build.encoding}"`.
- Propiedad `use.hospital2` para seleccionar HOSPITAL_2 como fuente de HOSPITAL.
- Nuevos targets:
  - `init-hospital`, `compile-hospital`, `package-hospital` (build rápido de BUSINESS + HOSPITAL).
  - `compile-hospital-dev` (solo HOSPITAL hacia Tomcat).

### 6.2 Scripts de build

- `package.bat`:
  - Build completo con todos los módulos.
  - Fija JDK 8 y redirige la salida a archivos `package_*.txt`.
- `package_hospital_fast.bat`:
  - Build rápido de BUSINESS + HOSPITAL y copia `HOSPITAL.war` a `webapps`.
- `build_hospital_dev.bat`:
  - Compila solo HOSPITAL hacia `webapps\HOSPITAL\WEB-INF\classes`.

### 6.3 .vscode/tasks.json

- Tareas de build:
  - `build-hospital2` → `package_hospital_fast.bat`.
  - `build-hospital-only-dev` → `build_hospital_dev.bat`.
- Tareas de servidor:
  - `start-tomcat7` → `startup.bat`.
  - `run-tomcat7-console` → `./catalina.bat run`.
- Tareas compuestas:
  - `build-and-start-hospital2`.
  - `build-and-run-hospital-only-dev`.

## 7. Cambios en la instalación de Tomcat

### 7.1 setenv.bat

- Archivo `bin/setenv.bat` fijo para usar JDK 8:
  - `JAVA_HOME` / `JRE_HOME` / `PATH`.

### 7.2 server.xml

En `conf/server.xml`:

- Conector HTTP en puerto 8085.
- Contexto HOSPITAL configurado así:

```xml
<Host appBase="webapps" autoDeploy="true" name="localhost" unpackWARs="true">
    ...
    <Context docBase="HOSPITAL" path="/HOSPITAL" reloadable="true" source="org.eclipse.jst.jee.server:HOSPITAL_2">
      <Manager pathname=""/>
    </Context>
</Host>
```

- `docBase="HOSPITAL"` → usa la carpeta `webapps\HOSPITAL` expandida del WAR.
- `<Manager pathname=""/>` → desactiva carga/guardado de sesiones en disco y elimina errores de sesión antigua.

### 7.3 Limpieza de aplicaciones de ejemplo

- Las apps de ejemplo de Tomcat se movieron de `webapps` a `webapps-disabled`:
  - `docs`, `examples`, `host-manager`, `manager`.
- Con esto, en desarrollo solo se despliegan las apps realmente usadas y el arranque es más rápido.

---

Con esta guía se puede:
- Compilar y desplegar HOSPITAL_2 de forma completa o rápida.
- Arrancar Tomcat 7 con Java 8 desde VS Code (con o sin consola de logs).
- Iterar rápidamente sobre el módulo HOSPITAL.
- Generar y desplegar otros módulos web como AGP.

## 8. Configuración de VS Code para el proyecto Java (HOSPITAL_2)

El proyecto está pensado originalmente para Eclipse y usa el archivo `.classpath`, donde se incluye el contenedor
`org.eclipse.jst.j2ee.internal.web.container`. Ese contenedor hace que Eclipse agregue automáticamente todos los JAR de
`WEB-INF/lib` al classpath, pero la extensión de Java de VS Code **no entiende** este tipo de contenedores específicos
de Eclipse.

Para que VS Code resuelva correctamente las clases de JSF (por ejemplo `javax.faces.model.SelectItem`) y el resto de las
dependencias, hay que indicarle explícitamente dónde están los JAR de las aplicaciones web.

### 8.1 Archivo .vscode/settings.json

En la raíz del workspace HOSPITAL, crear (o editar) el archivo `.vscode/settings.json` con una configuración similar a:

```json
{
  "java.project.referencedLibraries": [
    "HOSPITAL_2/WebRoot/WEB-INF/lib/**/*.jar",
    "AGP/WebRoot/WEB-INF/lib/**/*.jar",
    "AGH/WebRoot/WEB-INF/lib/**/*.jar",
    "AGI/WebRoot/WEB-INF/lib/**/*.jar",
    "PROVEEDORES/WebRoot/WEB-INF/lib/**/*.jar",
    "WS-HOSPITAL/WebRoot/WEB-INF/lib/**/*.jar"
  ]
}
```

Puntos clave:

- Cada patrón `**/*.jar` hace que VS Code agregue todos los JAR de la carpeta `WEB-INF/lib` (y subcarpetas) al
  classpath del proyecto Java.
- En particular, se incluye el JAR de JSF (`javax.faces-2.2.13.jar`), que contiene la clase `SelectItem` y el resto de
  las APIs usadas por HOSPITAL_2.

### 8.2 Recargar el workspace de Java en VS Code

Después de guardar `settings.json`:

1. Pulsar `Ctrl+Shift+P`.
2. Ejecutar: `Preferences: Open Workspace Settings (JSON)` si se quiere ajustar la configuración desde la UI.
3. Verificar/ajustar los patrones en `java.project.referencedLibraries` según las rutas reales del workspace.
4. Volver a `Ctrl+Shift+P` y ejecutar: `Java: Clean Java Language Server Workspace`.
5. Elegir **Restart and delete** cuando lo pida.

Tras el reinicio del Java Language Server, VS Code tomará los JAR de `WEB-INF/lib` igual que hacía Eclipse y deberían
desaparecer los errores de compilación JSF en el editor.
