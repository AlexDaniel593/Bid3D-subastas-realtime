# Bid 3D - Plataforma de Subastas en Tiempo Real con Catálogo 3D

> **Universidad de las Fuerzas Armadas ESPE**
>
> **Asignatura:** Construcción y Evolución de Software (NRC: 36846)  
> **Docente:** Ing. Mayra Alvarez  
> **Desarrolladores:** Kevin Amaguaña (`kfamaguana@espe.edu.ec`) & Daniel Guamán (`adguaman4@espe.edu.ec`)  
> **Periodo:** Octubre 2026 – Febrero 2027

---

## Stack Tecnológico

![NodeJS](https://img.shields.io/badge/Node.js-v20+-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=for-the-badge&logo=nestjs&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Three.js](https://img.shields.io/badge/Three.js-000000?style=for-the-badge&logo=three.js&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Socket.io](https://img.shields.io/badge/Socket.io-010101?style=for-the-badge&logo=socketdotio&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)
![Telegram Bot](https://img.shields.io/badge/Telegram_Bot-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)

---

## Descripción del Proyecto

**Bid 3D** es una plataforma web inmersiva de subastas en tiempo real diseñada para optimizar los procesos de compra-venta mediante un entorno transparente, automatizado y seguro. Como principal factor diferenciador, la plataforma integra un **catálogo interactivo de modelos 3D** (`.gltf` / `.glb`), lo que permite a los compradores inspeccionar visual y tridimensionalmente los productos antes y durante el proceso de oferta, aumentando la confianza y reduciendo el riesgo de disputas.

## Características Principales y Novedad

* **Visualizador 3D Interactivo:** Renderizado en el navegador en tiempo real para examinar detalles físicos del producto antes de ofertar.
* **Pujas en Tiempo Real:** Actualización instantánea de ofertas y temporizador bidireccional mediante WebSockets (sin refrescar la página).
* **Autenticación y Seguridad Avanzada:** Acceso basado en roles (**Proveedor**, **Comprador**, **Administrador**) reforzado con Autenticación de Doble Factor (2FA).
* **Cierre Automático y Adjudicación:** Finalización automatizada por temporizador en backend con retención/liberación de saldo simulado y emisión inmediata del **Acta de Adjudicación en PDF**.
* **Historial Auditable Trazable:** Registro inmutable de cada puja, timestamp y balance transaccional para auditorías.

## Tecnologías y Módulos del Sistema

| Módulo | Tecnologías / Herramientas | Descripción |
| :--- | :--- | :--- |
| **Backend** | `Node.js` • `NestJS` • `TypeScript` | Servidor modular, gestión de endpoints y lógica de negocio. |
| **Tiempo Real** | `Socket.io` • `WebSockets` | Sincronización instantánea de pujas y temporizador. |
| **Visualización 3D** | `Three.js` • `@react-three/fiber` | Renderizado WebGL de archivos `.gltf` / `.glb` en el cliente. |
| **Persistencia** | `PostgreSQL` • `Prisma ORM` | Control estricto de transacciones ACID para pujas y saldos. |
| **Tareas de Cierre**| `Redis` • `BullMQ` | Gestión precisa de temporizadores en segundo plano. |
| **DevOps / CI-CD** | `GitHub Actions` • `Telegram Bot` | Compilación automatizada, suite de tests y alertas inmediatas. |

## Reglas de Negocio Clave

1. **Gestión de Ofertas Simultáneas:**
   * En caso de recibir dos pujas por el mismo monto en el mismo milisegundo, la base de datos aplica bloqueo pesimista/transaccional (ACID): **se registra la oferta con el timestamp de llegada al servidor más antiguo**.

2. **Retención y Liberación de Saldo Simulado:**
   * Al realizar una puja activa, el monto ofertado es **retenido temporalmente** del saldo disponible del comprador.
   * Si otro comprador supera la oferta, el saldo del postor anterior se **libera automáticamente de forma inmediata**.

3. **Cierre Automático:**
   * El cronómetro de la subasta está sincronizado en el servidor mediante **Redis**. Una vez finalizado el tiempo, no se aceptan más conexiones ni pujas, procediendo al bloqueo y generación del acta.

## Alcance y Restricciones

### Incluido
* Autenticación segura (2FA + JWT).
* Catálogo interactivo 3D.
* Motor de ofertas en tiempo real.
* Cierre automático y generación de actas en PDF.

### Fuera de Alcance
* Pasarelas de pago bancario real (únicamente se utiliza saldo simulado).
* Envíos o logística de entrega física de productos.
* Firma electrónica con validez legal notarial.
* Soporte multi-idioma (disponible exclusivamente en español).

## Guía de Instalación y Ejecución Local

### Prerrequisitos
* **Node.js** `>= 20.0.0`
* **npm** `>= 10.0.0`
* **PostgreSQL** `>= 15.0`
* **Redis** `>= 7.0`

### Pasos de Configuración

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/tu-usuario/bid3d.git
   cd bid3d
   ```

2. **Instalar dependencias:**
   ```bash
   npm install
   ```

3. **Configurar variables de entorno (`.env`):**
   Crea un archivo `.env` en la raíz del proyecto tomando como base `.env.example`:
   ```env
   PORT=3000
   DATABASE_URL="postgresql://usuario:password@localhost:5432/bid3d_db?schema=public"
   REDIS_URL="redis://localhost:6379"
   JWT_SECRET="super_secreto_jwt"
   ```

4. **Ejecutar migraciones de Base de Datos:**
   ```bash
   npx prisma migrate dev
   ```

5. **Iniciar en entorno de desarrollo:**
   ```bash
   npm run start:dev
   ```

6. **Ejecutar suite de pruebas:**
   ```bash
   npm test
   ```

## Flujo de Git y CI/CD

El repositorio sigue una arquitectura estricta de protección de ramas:

* **`dev`:** Rama para desarrollo activo y commits diarios del equipo.
* **`test`:** Rama de validación e integración continua. Solo recibe cambios a través de Pull Requests desde `dev`. Ejecuta automáticamente el linter, build y pruebas.
* **`main`:** Rama de producción. Recibe fusiones automáticas (*auto-merge*) desde `test` únicamente cuando todas las pruebas unitarias e integraciones se completan con éxito.

*Cualquier fallo durante la fase de integración enviará un reporte detallado del error directamente al canal de Telegram del equipo.*

## Documentación Adicional

La documentación extendida del proyecto se encuentra en el directorio `/docs`:
* 📄 [`docs/proyecto_bid3d_especificaciones.md`](docs/proyecto_bid3d_especificaciones.md): Especificaciones de requerimientos y arquitectura.
* 📄 [`docs/manual_workflows_cicd.md`](docs/manual_workflows_cicd.md): Manual detallado de CI/CD, GitHub Rulesets y alertas de Telegram.

## Licencia

Este proyecto se desarrolla con fines académicos bajo la licencia [MIT](LICENSE).