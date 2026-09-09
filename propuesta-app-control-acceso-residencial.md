# Propuesta Técnica: Aplicación Móvil para Gestión y Control de Acceso Residencial

## 1. Resumen Ejecutivo

Se propone el desarrollo de una aplicación móvil integral —con app nativa/híbrida para residentes, app para guardias de seguridad y panel web de administración— orientada a digitalizar y centralizar el control de acceso de un fraccionamiento residencial. El sistema busca tres objetivos principales:

- **Seguridad**: trazabilidad completa de quién entra, quién sale, cuándo y por qué.
- **Eficiencia operativa**: reducir tiempos de espera en casetas y eliminar el registro manual en papel.
- **Experiencia del residente**: autogestión de visitas, vehículos y comunicación con la administración desde el celular.

La solución se compone de tres frentes: **App Residente** (iOS/Android), **App Guardia** (tablet/celular en caseta) y **Panel de Administración** (web), todos conectados a un backend central en la nube.

---

## 2. Funcionalidades Clave

### 2.1 Gestión de Acceso de Residentes

- Credencial digital única por residente (perfil vinculado a domicilio/lote).
- Validación en caseta mediante:
  - **Código QR dinámico** en la app (con rotación periódica para evitar capturas de pantalla reutilizadas).
  - **Tag RFID/NFC** físico como respaldo, vinculado al mismo perfil.
  - Búsqueda manual por nombre/domicilio como último recurso, con registro de excepción.
- Registro automático de cada entrada/salida (timestamp, punto de acceso, guardia en turno).
- Modo "acceso rápido" offline en caseta ante caídas de conectividad (ver sección de arquitectura).

### 2.2 Control de Acceso para Visitantes y Personal Temporal

- **Pre-registro de visitas** desde la app del residente:
  - Nombre del visitante, motivo, fecha/hora de validez (única vez, rango horario, o recurrente para personal de servicio).
  - Generación de **código QR o folio numérico** de un solo uso o con ventana de validez definida.
  - Posibilidad de compartir el código directamente al visitante (WhatsApp, SMS, link).
- **Notificaciones push en tiempo real** al residente:
  - Al momento en que el visitante llega a la caseta (con opción de aprobar/rechazar en el momento si no hubo pre-registro).
  - Al momento en que el visitante sale del fraccionamiento.
- **Registro de datos en caseta**: nombre, identificación (foto de INE/credencial opcional), motivo de visita, hora de entrada/salida y placas del vehículo si aplica.
- Categorías diferenciadas: visita personal, proveedor/servicio, delivery, personal doméstico recurrente — cada una con flujo y retención de datos ajustado.

### 2.3 Registro y Gestión de Vehículos

- Alta de vehículos autorizados por residente: placas, marca/modelo, color, número de tarjetón/etiqueta.
- Asociación de múltiples vehículos por domicilio (propietarios, familiares, empleados domésticos).
- Registro temporal de vehículos de visitantes al momento del ingreso (placas, descripción), vinculado al folio de la visita.
- Alertas automáticas de vehículo no reconocido o placa reportada (p. ej. robo, veto).
- Soporte opcional a futuro para lectura automática de placas (ANPR) en accesos vehiculares de alto tráfico.

### 2.4 Estadísticas e Informes Operativos

- Dashboard administrativo con métricas de:
  - Flujos de entrada/salida por hora, día y punto de acceso.
  - Picos de actividad (horas pico de visitas, entregas, salida/entrada de residentes).
  - Duración promedio de visitas por categoría.
  - Comparativos históricos (semana/mes/temporada).
- **Detección de anomalías**: visitas fuera de horario habitual, accesos repetidos de un mismo visitante en periodos cortos, vehículos no reconocidos, folios usados fuera de su ventana de validez.
- Exportación de reportes (PDF/Excel) para juntas vecinales o auditorías de seguridad.
- Reportes por guardia/turno para control de desempeño operativo.

### 2.5 Módulo de Incidencias y Seguridad

- Reporte de incidencias por residentes y guardias: emergencias, mantenimiento, vehículo mal estacionado, alarmas, disturbios, etc.
- Adjuntar **fotos/video** y **geolocalización** (o referencia a manzana/lote) del incidente.
- Flujo de estatus: Reportado → En atención → Resuelto, con historial y responsable asignado.
- Botón de pánico/SOS para guardias y residentes con notificación inmediata a administración y, si aplica, a caseta central.
- Bitácora de incidencias consultable por administración para análisis de tendencias.

### 2.6 Comunicación Interna y Avisos

- Envío de comunicados generales o segmentados (por sección/calle/torre) desde el panel de administración.
- Categorías: avisos generales, recordatorios de pago de cuotas, alertas de seguridad urgentes, mantenimiento programado (corte de agua/luz, fumigación, etc.).
- Notificaciones push + bandeja de mensajes dentro de la app con confirmación de lectura.
- Encuestas o confirmaciones rápidas (ej. "¿Asistirás a la asamblea?").

### 2.7 Roles de Usuario Diferenciados

| Rol | Permisos principales |
|---|---|
| **Administrador** | Gestión de residentes, configuración de reglas de acceso, envío de comunicados, acceso a estadísticas e informes completos, gestión de incidencias, administración de usuarios y roles. |
| **Guardia de Seguridad** | Validación de credenciales y códigos QR en caseta, registro de visitantes/vehículos, reporte de incidencias, consulta de vehículos/residentes autorizados (sin acceso a datos financieros ni configuración). |
| **Residente** | Pre-registro de visitas, gestión de sus vehículos, recepción de notificaciones y avisos, reporte de incidencias propias, consulta de historial de accesos de su domicilio. |

Se recomienda control de acceso basado en roles (**RBAC**) a nivel de API, no solo de interfaz, para evitar exposición de datos entre roles.

### 2.8 Interfaz y Experiencia de Usuario (UI/UX)

- **App Residente**: diseño simple, con acciones frecuentes (nueva visita, ver historial, reportar incidencia) accesibles en máximo 2 toques desde el home.
- **App Guardia**: interfaz optimizada para uso rápido en condiciones de campo (luz solar directa, uso con guantes, una sola mano), botones grandes, escaneo QR como acción principal, flujo mínimo de pasos para registrar una visita no planeada.
- Diseño consistente entre iOS y Android siguiendo lineamientos nativos (Human Interface Guidelines / Material Design) para sentirse "nativo" en cada plataforma.
- Modo oscuro, soporte de accesibilidad (tamaños de fuente, contraste) y localización en español como idioma base.
- Pruebas de usabilidad con guardias reales antes del despliegue, dado que son el punto crítico de adopción operativa.

### 2.9 Seguridad de Datos y Privacidad

- **Cifrado en tránsito** (TLS 1.2+) y **en reposo** (cifrado de base de datos y almacenamiento de imágenes/documentos).
- Códigos QR firmados criptográficamente y de un solo uso o con expiración corta para evitar duplicación o reenvío fraudulento.
- Autenticación robusta: login con OTP/SMS o correo, y **2FA obligatorio para administradores**.
- Principio de mínimo privilegio y RBAC aplicado a nivel de base de datos y API.
- Política de retención y anonimización de datos de visitantes (p. ej. purga automática de fotos/identificaciones tras un periodo definido, salvo incidencias abiertas).
- Cumplimiento con normativa de protección de datos aplicable (en México, **LFPDPPP** — Ley Federal de Protección de Datos Personales en Posesión de los Particulares); aviso de privacidad accesible dentro de la app y consentimiento explícito para el tratamiento de datos de visitantes.
- Auditoría (logs) de accesos administrativos a información sensible, y bitácora inmutable de eventos de acceso para fines legales/forenses.
- Respaldos automáticos periódicos y plan de recuperación ante desastres (RPO/RTO definidos).

---

## 3. Arquitectura Tecnológica Propuesta

### 3.1 Frontend (Apps móviles)

- **Framework multiplataforma**: React Native o Flutter, para mantener una sola base de código entre iOS y Android con rendimiento cercano a nativo (recomendado especialmente por velocidad de desarrollo y mantenimiento con un solo equipo).
- Librerías nativas para escaneo QR y lectura NFC/RFID.
- Notificaciones push vía Firebase Cloud Messaging (Android) y APNs (iOS), unificadas si se usa Firebase como capa intermedia.

### 3.2 Backend

- **API REST o GraphQL** construida en Node.js (NestJS) o Python (Django/FastAPI), ambas opciones maduras para este tipo de sistemas transaccionales.
- Arquitectura modular (por dominio: accesos, visitas, vehículos, incidencias, comunicados) que facilite escalar equipos y funcionalidades de forma independiente.
- Cola de mensajería (RabbitMQ o servicios administrados como AWS SQS) para desacoplar notificaciones push, generación de reportes y procesamiento de eventos de acceso.
- Servicio de generación/validación de QR con firma criptográfica y control de expiración.

### 3.3 Base de Datos

- **Base de datos relacional** (PostgreSQL) para datos estructurados: residentes, vehículos, visitas, incidencias, roles — dada la necesidad de integridad referencial y consultas complejas para reportes.
- **Almacenamiento de objetos** (AWS S3 o equivalente) para fotos de incidencias e identificaciones de visitantes, con cifrado y URLs firmadas de acceso temporal.
- Opcional: motor de analítica (p. ej. un data warehouse ligero o vistas materializadas en PostgreSQL) para acelerar los reportes de estadísticas sin afectar la base transaccional.

### 3.4 Infraestructura y Operación

- Despliegue en la nube (AWS, GCP o Azure) con contenedores (Docker) orquestados vía Kubernetes o servicios administrados (ECS/Cloud Run) para escalabilidad horizontal.
- CI/CD para despliegues controlados y pruebas automatizadas antes de cada release.
- Monitoreo y alertas (p. ej. Datadog, Grafana + Prometheus) para disponibilidad del servicio, crítico dado que la caseta depende de la app para operar.
- **Modo offline en caseta**: cacheo local de credenciales activas y códigos QR pre-registrados válidos, con sincronización automática al restablecer conectividad, para no dejar sin acceso al fraccionamiento ante fallas de internet.

### 3.5 Integraciones Futuras (Roadmap)

- Lectura automática de placas (ANPR) en accesos vehiculares.
- Control de acceso a amenidades (alberca, salón de eventos) con el mismo esquema de credenciales.
- Integración con sistemas de videovigilancia (CCTV) para asociar eventos de acceso con clips de video.
- Pasarela de pagos para cuotas de mantenimiento dentro de la misma app.

---

## 4. Fases de Implementación Sugeridas

1. **Fase 1 – MVP**: Gestión de residentes, pre-registro de visitas con QR, validación en caseta (app guardia), notificaciones push básicas.
2. **Fase 2**: Registro de vehículos, módulo de incidencias, comunicados masivos.
3. **Fase 3**: Estadísticas avanzadas, detección de anomalías, modo offline robusto.
4. **Fase 4**: Integraciones (ANPR, CCTV, pagos) y optimizaciones basadas en uso real.

---

## 5. Conclusión

Esta propuesta plantea una solución modular que puede iniciar con un MVP funcional en pocos meses y crecer hacia un sistema de seguridad integral. La combinación de credenciales digitales, pre-registro de visitas, control vehicular y analítica de accesos permite reducir fricciones operativas en caseta, dar mayor tranquilidad a los residentes y proveer a la administración de datos accionables para la toma de decisiones de seguridad.
