# Documentación del Proyecto: Bid 3D
**Plataforma de Subastas en Tiempo Real con Catálogo 3D**

---

## 1. Informacion Institucional
* **Institución:** Universidad de las Fuerzas Armadas ESPE
* **Asignatura:** Construcción y Evolución de Software (NRC: 36846)
* **Docente:** Ing. Mayra Alvarez
* **Integrantes del Equipo:**
  * Kevin Amaguaña (`kfamaguana@espe.edu.ec`)
  * Daniel Guamán (`adguaman4@espe.edu.ec`)
* **Fecha de Desarrollo:** Octubre 2026 – Febrero 2027

---

## 2. Descripción General del Proyecto
**Bid 3D** es una plataforma web inmersiva de subastas en tiempo real diseñada para optimizar los procesos de compra-venta mediante un entorno transparente, automatizado y seguro. La innovación principal radica en la integración de un **catálogo interactivo 3D** (mediante formatos `.gltf` / `.glb`), permitiendo a los compradores examinar tridimensionalmente el producto antes y durante el proceso de oferta.

---

## 3. Objetivos

### 3.1 Objetivo General
Desarrollar una plataforma web de subastas en tiempo real con catálogo de modelos visuales en 3D, que optimice los procesos de compra-venta mediante un entorno transparente, automatizado y seguro para proveedores y compradores.

### 3.2 Objetivos Específicos
1. Implementar un módulo de gestión de usuarios y roles (**Proveedor**, **Comprador** y **Administrador**) con autenticación de doble factor (2FA) y control de accesos RBAC.
2. Desarrollar un motor de ofertas sincronizado en tiempo real (WebSockets / Socket.io) sin demoras perceptibles entre participantes.
3. Integrar un visor visual de modelos 3D interactivos para consolidar la confianza de la compra.
4. Automatizar el cierre de subasta al finalizar el temporizador, emitiendo el acta de adjudicación auditable y registrando la trazabilidad completa.

---

## 4. Alcance y Límites del Sistema

### 4.1 Dentro del Alcance
* Registro y autenticación segura de usuarios (JWT + 2FA).
* Catálogo de productos interactivo en 3D (`Three.js` / WebGL).
* Motor de ofertas en tiempo real con WebSockets.
* Sistema de recarga de saldo simular mediante validación de comprobantes.
* Cierre de subasta automatizado por temporizador.
* Generación de acta de adjudicación (PDF) e historial auditable.

### 4.2 Fuera del Alcance
* Integración con pasarelas de pago bancarias reales (se utiliza saldo simulado).
* Logística, envíos físicos o servicios de mensajería/courier.
* Firma electrónica con validez notarial de contratos.
* Soporte multi-idioma (únicamente español).

---

## 5. Decisiones de Arquitectura y Stack Tecnológico

| Componente | Tecnología Seleccionada | Justificación |
| :--- | :--- | :--- |
| **Backend** | **Node.js + NestJS / Express** | Mantenibilidad con TypeScript, alto rendimiento en I/O asíncrono y facilidades para WebSockets. |
| **Frontend** | **React / Vue + Three.js** | Visualización rápida y fluida de modelos 3D tridimensionales en el navegador. |
| **Tiempo Real** | **Socket.io / WebSockets** | Comunicación bidireccional inmediata para pujas instantáneas y temporizadores. |
| **Base de Datos** | **PostgreSQL + Prisma / TypeORM** | Garantía de transacciones ACID para control de saldos y manejo de concuerrencia. |
| **Gestión de Tareas / Cierre** | **Redis + BullMQ** | Planificación precisa de tareas en segundo plano para cierres de subasta exactos. |

---

## 6. Estrategia de Git y CI/CD (Rulesets de GitHub)

El repositorio **Bid 3D** implementa el siguiente flujo de ramas: `dev` ➔ `test` ➔ `main`.

```
     [dev] ---------- (Pruebas Exitosas) ---------> [test] -------- (Pull Request + 1 Review) --------> [main]
(Trabajo diario)                               (Rama Integrada)                                      (Producción)
```

### Configuración de Branch Protection / Rulesets
1. **`dev` (Desarrollo Directo):**
   * *Permisos:* Direct push permitido para el equipo.
   * *Protección:* `Block force pushes` y `Restrict deletions` activados.
2. **`test` (Rama de Integración Automatizada):**
   * *Permisos:* Direct push bloqueado (`Restrict updates` activado). No permite PRs manuales.
   * *Actualización:* Exclusiva mediante scripts/workflows automatizados cuando los tests unitarios pasen.
3. **`main` (Producción):**
   * *Permisos:* Direct push bloqueado.
   * *Requisitos:* Se exige Pull Request con **mínimo 1 aprobación** y validación de **Status Checks (CI/Tests)** en verde.

### Notificaciones del Workflow
Integración automatizada vía **GitHub Actions** hacia el canal de **Telegram** del equipo para notificar:
* Fallos en la ejecución de linter o suites de prueba.
* Pull Requests creados y listos para revisión.
* Merges exitosos sobre la rama principal `main`.

---

## 7. Políticas Mínimas de Seguridad

* **Sesión y Autenticación:** Bloqueo de cuenta tras 3 intentos fallidos de inicio de sesión. Cierre automático por inactividad a los 15 minutos.
* **Control de Acceso:** Principio de mínimo privilegio según el rol (RBAC).
* **Contraseñas:** Longitud mínima de 8 caracteres, requiriendo al menos una mayúscula, un número y un carácter especial.
* **Integridad Transaccional:** Control estricto de concurrencia a nivel de base de datos (ACID) para evitar doble consumo de saldo o pujas inválidas simultáneas.