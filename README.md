# bdget 🎓

Microservicio REST en **Java 17 + Spring Boot 3** para gestión de estudiantes (CRUD). Incluye tests unitarios, manejo de excepciones y persistencia vía Spring Data JPA.

Este repositorio es la **base de trabajo** para la Evaluación Parcial N°1 de Ingeniería DevOps (DOY0101) — nivel avanzado. A partir de este punto, cada equipo debe construir su propio flujo de trabajo colaborativo aplicando Git, GitHub y GitHub Actions.

---

## 🚀 Cómo levantar el proyecto localmente

Requiere JDK 17 y Maven (o usa el wrapper incluido, `./mvnw`, que no requiere Maven instalado).

```bash
./mvnw spring-boot:run
```

La API queda disponible en `http://localhost:8080/students`. La base de datos es **H2 en memoria** — no requiere instalación ni credenciales, y se reinicia cada vez que se detiene la aplicación. Mientras la app está corriendo, puedes inspeccionar los datos en `http://localhost:8080/h2-console` (JDBC URL: `jdbc:h2:mem:bdgetdb`, usuario: `sa`, sin contraseña).

Para correr los tests:
```bash
./mvnw test
```

---

## 📁 Estructura del proyecto

```
bdget/
├── .github/workflows/     → automatización CI/CD
├── src/main/java/...      → código fuente (modelo, controlador, servicio, repositorio)
├── src/test/java/...      → tests unitarios
├── src/main/resources/    → configuración (application.properties)
├── Dockerfile              → imagen de contenedor (opcional)
├── pom.xml                 → dependencias del proyecto (Maven)
└── README.md
```

>el diagrama se mantiene debido que no creamos nuevas carpetas o archivos , solo se modificaron los ya creados y por ende el diagrama se mantiene igual.

---

## 🌳 Estrategia de ramificación

> Como equipo optamos por una versión adaptada de GitFlow, usando las ramas main, develop, feature/<nombre> y hotfix/<nombre>. No incluimos la rama release/* del GitFlow original, ya que nuestro equipo es pequeño (3 personas) y no necesitamos una etapa formal de preparación/congelamiento de versión antes de pasar a producción: con develop estabilizado y probado es suficiente para fusionar directo a main.

main: código estable, listo para "producción" (evaluación).
develop: rama de integración, donde se juntan los cambios de features y hotfixes antes de pasar a main.
feature/<nombre>: una rama por funcionalidad nueva, creada desde develop y fusionada de vuelta a develop vía Pull Request.
hotfix/<nombre>: una rama por corrección urgente, creada desde develop (o main si el bug ya estaba en producción) y fusionada vía Pull Request.

Esta estrategia nos permitió trabajar en paralelo (búsqueda por nombre y validación de email) sin pisarnos el código, y aislar la corrección del bug de NullPointerException sin mezclarla con features en desarrollo.

---

## 📝 Convenciones de commits

> Usamos un formato inspirado en Conventional Commits, con prefijos que indican el tipo de cambio:

feat: — nueva funcionalidad (ej. feat: agrega endpoint de búsqueda por nombre)
fix: — corrección de errores (ej. fix: corrige NullPointerException al buscar estudiante inexistente)
chore: — tareas de mantenimiento que no afectan lógica de negocio (ej. permisos, configuración)

Elegimos este formato porque hace que el historial de commits sea fácil de leer y filtrar: cualquiera puede revisar el log y entender rápido qué tipo de cambio fue cada commit, sin tener que abrir el diff(diferencia entre dos versiones de un archivo o de código línea por línea, qué se agregó, qué se borró y qué se modificó).

---

## 🔀 Convenciones de naming de ramas

> Usamos el formato tipo/nombre-descriptivo-en-minusculas, por ejemplo:

feature/busqueda-por-nombre
feature/validacion-email
hotfix/fix-null-pointer
hotfix/fix-test-update-student

El criterio fue que el nombre describiera brevemente qué hace el cambio, sin necesidad de abrir la rama para saber de qué se trata. Esto facilita ver de un vistazo, en el listado de ramas, qué se está trabajando en cada una.

---

## 🔍 Estrategia de revisión (Pull Requests)

> Todo cambio se integró a develop (o main) mediante Pull Request, nunca con push directo. Antes de aprobar un PR revisamos:

Que los tests unitarios pasaran (validado por el workflow de GitHub Actions).
Que el código compilara sin errores.
Una revisión cruzada entre integrantes del equipo, dejando comentarios o aprobando directamente en GitHub cuando el cambio se veía correcto y no rompía funcionalidad existente.

---

## ⚙️ Automatización (CI/CD)

Este repositorio incluye un workflow en .github/workflows/main.yml que:

Ejecuta los tests unitarios (mvn test) en cada push a develop.
Compila y empaqueta el proyecto en cada Pull Request hacia main.

Durante el desarrollo tuvimos que corregir dos problemas detectados gracias al pipeline:

Permisos de ejecución del wrapper de Maven (mvnw): el workflow fallaba porque mvnw no tenía permisos de ejecución en el runner de GitHub Actions. Se corrigió agregando el permiso correspondiente.
Test desactualizado tras un cambio de comportamiento: al mejorar updateStudent para que lance una excepción (RuntimeException) en vez de retornar null cuando el estudiante no existe, el test testUpdateStudentNotExists quedó desactualizado y el CI detectó el error inmediatamente (Errors: 1 en el reporte de Maven). Se corrigió el test para usar assertThrows en vez de assertNull, alineándolo con el nuevo comportamiento esperado.

Ambos casos nos permitieron entender de primera mano por qué la automatización de CI/CD es importante: sin ella, ese tipo de errores solo se habrían detectado manualmente y más tarde, después de fusionar cambios directamente a main. Con el workflow corriendo en cada push/PR, detectamos y corregimos ambos problemas antes de llegar a main, que es justamente el rol central de un pipeline de CI/CD: dar retroalimentación rápida y automática sobre el estado del código.

---

## 🐳 Docker (opcional)

El proyecto incluye un `Dockerfile` funcional. Para construir y correr la imagen localmente:

```bash
docker build -t bdget .
docker run -p 8080:8080 bdget
```

---

## 👥 Autores

-Maximiliano Romero
-Benjamin Perez
-Bastian Bravo
