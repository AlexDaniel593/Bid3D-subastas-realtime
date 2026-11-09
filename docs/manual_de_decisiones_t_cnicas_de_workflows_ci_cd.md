# Manual de Decisiones Técnicas y Arquitectura CI/CD

**Proyecto:** Bid 3D  
**Asignatura:** Construcción y Evolución de Software (NRC: 36846)  
**Institución:** Universidad de las Fuerzas Armadas ESPE  
**Integrantes:** Kevin Amaguaña (`kfamaguana@espe.edu.ec`), Daniel Guamán (`adguaman4@espe.edu.ec`)  

---

## 1. Introducción y Justificación de la Estrategia

El propósito de este manual es documentar la arquitectura del pipeline de Integración y Despliegue Continuo (CI/CD) diseñado para el proyecto **Bid 3D**. Dado que el equipo está conformado por dos desarrolladores con un cronograma acotado, la estrategia de CI/CD busca **maximizar la velocidad de entrega sin comprometer la estabilidad del sistema**, automatizando la validación sintáctica, la compilación y la ejecución de pruebas unitarias/integración.

---

## 2. Definición del Flujo de Ramas (Git Branching Model)

El proyecto adopta un modelo simplificado derivado de *Git Flow*, con tres ramas principales:

```
    [dev] ──(Pull Request)──> [test] ──(Auto-Merge al pasar CI)──> [main]
  Desarrollo               Validación                          Producción
```

1. **`dev` (Rama de Desarrollo Activo):**
   * **Propósito:** Trabajo diario, integración frecuente de funciones.
   * **Acceso:** *Push* directo permitido para el equipo.
   * **Protecciones:** Bloqueo de *Force Push* (`Block force pushes`) y prohibición de borrado de la rama (`Restrict deletions`).

2. **`test` (Rama de Validación e Integración Automática):**
   * **Propósito:** Rama de staging donde se compila el código y se ejecutan las suites de pruebas automatizadas.
   * **Acceso:** Bloqueada para *pushes* directos manuales (`Restrict updates`). Solamente acepta cambios mediante Pull Requests originados en `dev`.

3. **`main` (Rama Principal de Producción):**
   * **Propósito:** Código estable, validado y listo para despliegue.
   * **Acceso:** Bloqueada totalmente para *pushes* directos.
   * **Actualización:** Automatizada desde `test` únicamente cuando la pipeline de CI se ejecuta sin errores.

---

## 3. Arquitectura de GitHub Actions

La automatización consta de dos flujos de trabajo (*workflows*) modulares ubicados en la carpeta `.github/workflows/`:

### 3.1 Workflow 1: Verificación de Código y Diagnóstico (`ci-pr-test.yml`)
* **Desencadenante (`on`):** Creación, sincronización o re-apertura de un Pull Request hacia la rama `test`.
* **Proceso de ejecución:**
  1. Descarga el código (`checkout@v4`) y configura el entorno Node.js 20.
  2. Instala dependencias del proyecto (`npm ci`).
  3. Executa análisis estático (`npm run lint`), compilación (`npm run build`) y pruebas unitarias (`npm test`).
  4. Redirige la salida de errores hacia archivos `.log` dedicados.
* **Manejo de Errores Detallados en Telegram:**
  Si cualquiera de las fases falla, el paso `Extraer detalles del error` obtiene las últimas 20 líneas del archivo de log del comando fallido y envía un reporte detallado con bloque de código formateado al grupo de Telegram del equipo.

### 3.2 Workflow 2: Promoción Automática (Opción A: `auto-merge-main.yml`)
* **Desencadenante (`on`):** Cierre y fusión de un PR sobre la rama `test` (tras aprobar la verificación de CI).
* **Proceso de ejecución:**
  1. Configura el bot de automatización de Git.
  2. Fusiona mediante *fast-forward* o *merge commit* la rama `test` hacia `main`.
  3. Hace *push* directo a `main` e informa el éxito del despliegue en Telegram.

---

## 4. Guía de Configuración de Secretos y Variables

Para habilitar las notificaciones a Telegram, se deben configurar los siguientes **Repository Secrets** en GitHub (`Settings > Secrets and variables > Actions`):

| Nombre del Secret | Descripción | Ejemplo de Valor |
| :--- | :--- | :--- |
| `TELEGRAM_BOT_TOKEN` | Token de autenticación del bot otorgado por BotFather. | `123456789:ABCdefGhIJKlmNoPQ...` |
| `TELEGRAM_CHAT_ID` | Identificador único del grupo de Telegram del equipo. | `-1001234567890` |

---

## 5. Permisos de Workflow requeridos en GitHub

En la ruta **Settings > Actions > General > Workflow permissions**, se debe seleccionar:
* ✅ **Read and write permissions**: Permite que el bot de GitHub Actions realice el *push/merge* automático a la rama `main` al completar exitosamente la suite de integración.