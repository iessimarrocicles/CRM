# Directrices e Instrucciones para Agentes de IA

Bienvenido/a al proyecto **AI Agency CRM**. Este documento define el marco de trabajo, las normas de seguridad, la arquitectura y las buenas prácticas que **todo agente de IA (y desarrollador)** debe respetar al operar en este repositorio.

---

## ⚠️ AVISO CRÍTICO: REPOSITORIO PÚBLICO EN GITHUB

> **ESTE REPOSITORIO ES 100% PÚBLICO.**  
> Cualquier archivo añadido y subido mediante `git push` quedará inmediatamente visible en Internet y será indexado por motores de búsqueda y scrapers de claves.
> 
> **BAJO NINGUNA CIRCUNSTANCIA** se debe commitear información sensible, credenciales privadas, tokens o datos reales de clientes.

---

## 1. Contexto del Proyecto

- **Propósito:** CRM a medida enfocado en agencias de Inteligencia Artificial y automatización.
- **Funcionalidades Clave:**
  - Gestión de clientes, leads y pipeline de ventas especializado en servicios de IA.
  - Seguimiento de proyectos de automatización, agentes implementados y flujos de trabajo.
  - Monitorización de consumo de tokens, presupuestos de APIs (OpenAI, Anthropic, Gemini, etc.) por cliente.
  - Integraciones con webhooks, mensajería y plataformas de automatización.

---

## 2. Pila Tecnológica & Uso de Firebase

El backend principal y servicios de datos de este proyecto están basados en **Firebase / Google Cloud**:

- **Firebase Authentication:** Gestión de acceso y roles (administradores de agencia, operadores, clientes).
- **Cloud Firestore:** Base de datos NoSQL para gestión de clientes, leads, tareas y registros de consumo.
- **Firebase Storage:** Almacenamiento seguro de entregables, contratos o activos multimedia de clientes.
- **Cloud Functions / Backend Server:** Lógica de negocio segura, webhooks y llamadas seguras a APIs de IA.

---

## 3. Protocolo Estricto de Seguridad y Secretos

### A. Gestión de Credenciales de Firebase

1. **Firebase Client SDK (Frontend):**
   - Las claves del cliente web (`apiKey`, `appId`, `projectId`) son identificadores públicos de la app, pero **NUNCA confíes en que aíslan la seguridad**.
   - **Reglas de Seguridad (Security Rules) Obligatorias:** Todo acceso a Firestore y Storage debe estar protegido por reglas de seguridad estrictas basadas en `request.auth`.
   - **NUNCA** utilices ni sugieras reglas permisivas tipo:
     ```text
     // ❌ PROHIBIDO
     match /{document=**} {
       allow read, write: if true;
     }
     ```
2. **Firebase Admin SDK (Backend / Funciones de Servidor):**
   - Requiere claves de cuenta de servicio (`serviceAccountKey.json`, `private_key`).
   - **NUNCA subas archivos de claves de servicio a Git.**
   - Estas credenciales deben inyectarse exclusivamente a través de variables de entorno de servidor o Secret Manager en producción (y `.env.local` ignorado en local).

### B. Claves de Proveedores de IA y Terceros
- Claves de OpenAI, Anthropic, Google Gemini, Stripe, etc., **NUNCA** deben incluirse en código fuente, scripts de prueba ni ejemplos directos.
- Usa siempre variables de entorno y documenta únicamente sus nombres simbólicos en `.env.example`.

### C. Archivos Prohibidos en Git
Revisa siempre antes de hacer commit que **NO** se incluyan:
- Archivos `.env`, `.env.local`, `.env.production`
- Archivos `.json` de cuentas de servicio (`*serviceAccountKey*.json`, `*firebase-adminsdk*.json`)
- Archivos con credenciales, tokens OAuth o dumps de bases de datos
- Datos reales de clientes (PII: nombres, correos reales, teléfonos o chats)

---

## 4. Flujo de Trabajo para Agentes

1. **Verificación previa a cualquier commit:**
   - Ejecuta siempre `git status` y revisa los archivos en `staged`.
   - Si detectas cualquier archivo de configuración local o clave, elimínalo del staging de inmediato.
2. **Respetar el `.gitignore`:**
   - Si creas nuevas carpetas con secretos temporales o dumps de desarrollo, añade la regla de exclusión correspondiente al `.gitignore`.
3. **Manejo de Variables de Entorno:**
   - Si se añade una nueva variable de configuración necesaria para el proyecto, agrégala con un valor ficticio en [.env.example](file:///c:/Users/Kike/Desktop/CRM/.env.example).
4. **Calidad de Código:**
   - Escribe código modular, tipado y documentado.
   - Sigue la convención de commits semánticos (`feat:`, `fix:`, `chore:`, `refactor:`, `docs:`).
   - Documenta claramente los contratos de datos de Firestore en interfaces de TypeScript o esquemas de validación (Zod, etc.).

---

## 5. Lista de Chequeo Rápida (Pre-Push Checklist)

Antes de autorizar o ejecutar un `git push` al repositorio público remoto:

- [ ] ¿`git status` muestra únicamente archivos de código o documentación sin secretos?
- [ ] ¿No hay claves de Firebase Admin ni tokens de APIs de IA en texto plano?
- [ ] ¿Todas las variables nuevas están registradas en `.env.example` con valores de muestra?
- [ ] ¿Las reglas de seguridad de Firestore no tienen permisos abiertos globales?
- [ ] ¿Los tests o comprobaciones de compilación pasan localmente?
