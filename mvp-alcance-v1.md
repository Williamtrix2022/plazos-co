# Plazos.co — Alcance del MVP v1

Fecha: 10 de octubre de 2026 · Última revisión: retroalimentación detallada del abogado (v2).
Este documento sirve de entrada para `docs/constitution.md` y las specs 001–005.

---

## 1. Decisiones tomadas

| # | Observación | Decisión |
|---|---|---|
| 1 | A los juzgados no les sirve; a empresas y abogados sí | **MVP para empresas y entidades** que *reciben* requerimientos. Abogados independientes en la v2. |
| 2 | Las empresas dejan vencer peticiones y terminan contestando tutelas | El MVP maneja **tutela + derecho de petición + desacato**, con reglas de plazo configurables y versionadas. |
| 3 | Organizar por prioridad | **Bandeja con semáforo** ordenada por urgencia de la obligación más próxima. |
| 4 | La IA debe saber de qué trata el documento y qué área lo resuelve, sin contestar | **Clasificación y enrutamiento con IA**, siempre confirmado por una persona. |
| 5 | Responsabilidad si se vence un plazo | **Trazabilidad completa** + confirmación humana + bitácora inmutable + T&C (cláusula redactada por abogado). |
| 6 | Consultar automáticamente el estado de los procesos | **Arquitectura de conectores lista; integración en la v2.** |
| 7 | Una tutela puede tener varios eventos con plazos independientes | **Modelo de obligaciones**: cada requerimiento tiene N eventos con plazo propio. El semáforo se calcula sobre la obligación más urgente. |
| 8 | La llegada del correo no siempre inicia el plazo | **Separación explícita** de recepción → notificación → evento inicial del cómputo → fecha sugerida → fecha confirmada. |
| 9 | Las reglas jurídicas cambian | **Reglas versionadas** con vigencia, aprobación jurídica y trazabilidad. Nunca se sobrescriben reglas ya usadas. |

## 2. Qué hace el MVP

1. **Organización y áreas.** La empresa registra sus áreas (nombre, descripción, temas, tipos de requerimiento que atiende, responsable y suplente) y sus usuarios. Roles: administrador, coordinador jurídico y responsable de área. Las áreas se configuran por organización, nunca hardcodeadas.
2. **Recepción.** Se sube el PDF de un derecho de petición, auto de tutela o requerimiento de desacato. Se registra: fecha y hora de recepción, medio, remitente y archivo original con hash SHA-256.
3. **La IA analiza el documento.** Extrae los datos (radicado, tipo, subtipo, partes, juzgado o peticionario, fechas, plazo indicado en el documento), identifica el tema, hace un resumen de 3 líneas y **sugiere un área** con nivel de confianza y justificación. Si detecta múltiples actuaciones con plazos (e.g., auto de tutela con plazo para contestar y plazo para aportar documentos), las propone como obligaciones separadas.
4. **Confirmación humana.** El coordinador revisa los datos extraídos, confirma o corrige: el tipo de actuación, el evento que inicia el cómputo (que puede ser distinto a la fecha de recepción), la regla de cómputo aplicable, la fecha límite y el área asignada. Sin esta confirmación el requerimiento no entra a la bandeja. Si hay datos ambiguos o confianza < 0.7, queda en "revisión manual".
5. **Motor de plazos con 5 capas.** Para cada obligación: (a) regla general configurada → (b) plazo identificado en el documento → (c) fecha sugerida por el motor → (d) fecha confirmada por el coordinador → (e) fecha modificada posterior (sin borrar la anterior, con motivo registrado).
6. **Bandeja con semáforo.** Rojo si está vencido o vence hoy; naranja si quedan 1 o 2 días hábiles; amarillo si quedan de 3 a 5; verde si quedan más de 5. Se calcula sobre la obligación más urgente de cada requerimiento. Se puede filtrar por área, tipo, responsable y estado.
7. **Alertas escalonadas.** Correo a los 5, 2 y 1 días hábiles del vencimiento de cada obligación. Si el responsable no marca como "en gestión", la alerta **se escala** al coordinador. Se registra: alerta programada, envío intentado, resultado del proveedor. Se distingue envío de entrega.
8. **Ciclo de vida y vinculación.** Estados: recibido → asignado → en gestión → respondido → cerrado. Una tutela se puede **vincular** al derecho de petición que la originó, y un desacato a su tutela. Cada obligación dentro de un requerimiento tiene su propio estado.
9. **Bitácora de auditoría.** Registro append-only: quién cargó, hash del documento, qué propuso la IA, quién confirmó qué fecha (con la regla aplicada), qué alertas se enviaron, con qué resultado, a quién, cuándo, y cada cambio de estado. Timestamps en UTC. Las correcciones de fechas se agregan como eventos nuevos sin borrar los anteriores.
10. **Panel.** Cuántos requerimientos (y obligaciones) hay vencidos, en riesgo y al día, desglosados por área y por tipo. Tasa de respuesta a tiempo.

## 3. Lo que NO hace el MVP

- No redacta respuestas ni da concepto jurídico.
- No consulta la Rama Judicial (v2, con conectores).
- No se conecta con el correo de la empresa (carga manual; correo entrante en v2).
- No incluye el módulo para abogados independientes (v2).
- No exporta reporte PDF/JSON de la bitácora con las 11 secciones completas (P1).
- No implementa encadenamiento criptográfico de la bitácora (P2).
- No gestiona múltiples pretensiones dentro de un mismo requerimiento con áreas colaboradoras (P2).

## 4. Reglas de plazos (tabla configurable y versionada)

### 4.1. Derechos de petición (Ley 1755 de 2015, art. 14)

| Tipo de petición | Plazo base | Unidad | Evento inicial |
|---|---:|---|---|
| Petición general | 15 | Días hábiles | Recepción por la entidad |
| Petición de documentos o información | 10 | Días hábiles | Recepción por la entidad |
| Petición de consulta | 30 | Días hábiles | Recepción por la entidad |
| Petición entre autoridades (documentos/información) | 10 | Días hábiles | Recepción por la entidad (art. 30) |

**Casos especiales registrables:**
- Petición incompleta: registrar requerimiento de subsanación y su efecto en el trámite.
- Falta de competencia: registrar remisión y recepción por la autoridad competente.
- Prórroga excepcional: registrar justificación, fecha de aviso al peticionario, fecha original y nueva fecha. La prórroga no es automática.
- Atención prioritaria: marca independiente del semáforo.
- Norma especial: el coordinador marca el asunto y lo envía a revisión manual.

### 4.2. Tutelas (Decreto 2591 de 1991)

| Actuación | Referencia | Regla de producto |
|---|---|---|
| Informe/contestación al juez | Art. 19: 1 a 3 días según el auto | Extraer del auto, exigir confirmación humana. |
| Cumplir el fallo | Art. 27: sin demora; plazo del fallo | Registrar cada orden, obligado, evidencia y término si lo hay. |
| Impugnación del fallo | Art. 31: 3 días desde notificación | Registrar evidencia de notificación y validar cómputo. |
| Otras órdenes judiciales | Según la providencia | Tratar como evento independiente; revisión humana si el término no es claro. |

**Regla clave:** no existe una única fecha límite para toda una tutela. Se modelan como N obligaciones independientes.

### 4.3. Desacato (Decreto 2591 de 1991, art. 52)

Se diferencian al menos: el plazo del requerimiento dentro del incidente, la obligación de cumplir la orden original de tutela, y las actuaciones posteriores. Cada una es una obligación con plazo propio.

### 4.4. Entidad ReglaPlazo (campos mínimos)

- tipo_requerimiento, subtipo_requerimiento
- norma_fuente, articulo_fuente
- unidad_plazo (días hábiles, días calendario, horas, fecha explícita)
- cantidad_plazo (cuando aplica)
- evento_inicial (recepción, notificación, ejecutoria, fecha indicada, otro)
- regla_computo
- requiere_revision_manual (booleano)
- vigencia_desde, vigencia_hasta
- version_regla
- estado_aprobacion_juridica, aprobado_por, fecha_aprobacion

**Las reglas nunca se sobrescriben.** Si cambia una norma, se crea una nueva versión con nueva vigencia. Los expedientes activos conservan la regla con la que se calculó su plazo.

### 4.5. Días hábiles

Se excluyen sábados, domingos y festivos de Colombia (incluidos los trasladados al lunes por la Ley Emiliani, Ley 51 de 1983).

## 5. Cómo funciona la IA (reglas)

- **Entrada:** el texto del PDF + el catálogo de áreas de la empresa (descripciones, temas, tipos que atiende).
- **Salida estructurada (JSON):** tipo, subtipo, datos extraídos, actuaciones con plazo detectadas, tema, resumen, área sugerida, confianza (0 a 1), justificación y campos dudosos.
- **Si la confianza es menor a 0.7,** el documento queda marcado como "revisión manual" y no se sugiere área.
- **La IA nunca fija la fecha límite definitiva:** propone datos y cálculos; una persona confirma.
- **Cuando detecta múltiples actuaciones con plazo,** las propone como obligaciones separadas para que el coordinador las confirme individualmente.
- **Aprendizaje:** cuando una persona corrige el área o los datos, la corrección se guarda como evento separado para futuras clasificaciones.
- **Datos:** solo documentos ficticios en desarrollo y demos. Se envía al modelo solo el texto necesario.

## 6. Protección ante responsabilidad

- **Técnica (se construye en el MVP):** bitácora append-only con hashes SHA-256, confirmación humana de fechas con regla aplicada, alertas con registro de resultado real (envío ≠ entrega ≠ lectura), pruebas automáticas del cálculo de días hábiles, multi-tenant estricto.
- **Contractual (antes de vender):** T&C con limitación de responsabilidad. La plataforma es herramienta de apoyo; el control de términos sigue siendo del cliente. Cláusula redactada por abogado.
- **Aviso visible en la aplicación:** "Las fechas sugeridas deben ser verificadas por el usuario. Plazos.co no sustituye la revisión jurídica."

## 7. Modelo de datos (actualizado)

```
Organizacion (tipo: empresa | firma)              ← multi-tenant desde el día 1
 ├─ Area (nombre, descripcion, temas, tipos_requerimiento,
 │        responsable, suplente, coordinador, vigencia)
 ├─ Usuario (rol, organizacion_id)
 ├─ ReglaPlazo (tipo, subtipo, norma, articulo, unidad, cantidad,
 │              evento_inicial, regla_computo, requiere_revision_manual,
 │              vigencia_desde/hasta, version, estado_aprobacion,
 │              aprobado_por, fecha_aprobacion)
 └─ Requerimiento (tipo, radicado, partes, area, estado_general,
 │                  fecha_recepcion, medio_recepcion, hash_documento)
 │   ├─ Documento (pdf, texto_extraido, hash_sha256, analisis_ia)
 │   ├─ Obligacion (actuacion, regla_plazo_aplicada,
 │   │              fecha_recepcion_doc, fecha_notificacion_acreditada,
 │   │              evento_inicial_computo,
 │   │              fecha_inicio_computo_sugerida,
 │   │              fecha_inicio_computo_confirmada,
 │   │              fecha_limite_sugerida, fecha_limite_confirmada,
 │   │              confirmado_por, estado, semaforo)
 │   ├─ Evento (bitácora append-only: quien, que, cuando_utc, resultado)
 │   ├─ Alerta (obligacion_id, programada, enviada, resultado_proveedor,
 │   │          escalada, destinatario)
 │   └─ vinculado_a → Requerimiento (petición → tutela → desacato)
Conector (interfaz)  →  v2: CPNU / Samai / correo entrante
```

**Cambio principal vs. v1:** Obligacion es la nueva entidad que modela cada evento con plazo dentro de un requerimiento. El semáforo y las alertas se calculan por obligación, no por requerimiento.

## 8. Datos de demostración (organización ficticia)

Catálogo funcional inspirado en una entidad pública de atención a víctimas (NO es el organigrama oficial de la UARIV):

| Área | Temas | Tipos de requerimiento |
|---|---|---|
| Oficina Jurídica | Tutelas, desacatos, órdenes judiciales, impugnaciones | Providencias judiciales |
| Servicio al Ciudadano y PQRSD | Peticiones, quejas, reclamos, solicitudes de información | Derechos de petición |
| Atención Humanitaria | Asistencia humanitaria, emergencias | Peticiones y órdenes de asistencia |
| Registro e Información | Registro, novedades, certificaciones | Solicitudes de información |
| Reparación | Reparación individual o colectiva | Peticiones de reparación |
| Gestión Administrativa | Talento humano, contratación, presupuesto | Peticiones administrativas |
| Direcciones Territoriales | Atención regional | Requerimientos territoriales |

## 9. Ruta de prioridades (del abogado)

### P0 — Necesario antes de producción (ENTRA AL MVP)

- Validar jurídicamente la matriz de términos y su vigencia.
- Separar recepción, notificación, inicio del cómputo y vencimiento.
- Permitir varios eventos/obligaciones con plazos por requerimiento.
- Exigir confirmación humana del término y la fecha límite.
- Marcar casos ambiguos para revisión manual.
- Probar el cálculo de días hábiles, festivos y reglas especiales.
- Bitácora append-only con hashes SHA-256.
- Registrar resultado real de alertas (envío ≠ entrega).
- Aislar datos por organización y permisos por rol.
- Documentos ficticios siempre en desarrollo y demos.

### P1 — Primera versión comercial

- Exportación PDF + JSON/CSV del reporte de bitácora (11 secciones).
- Versionamiento formal de reglas con aprobación jurídica.
- Hashes de documentos con verificación de integridad.
- Procedimiento de corrección de fechas con motivo y evidencia.
- Retención, respaldo y recuperación.
- Pruebas para todos los casos límite de cálculo.

### P2 — Escalabilidad posterior

- Conectores con Rama Judicial (CPNU, Samai, SIUGJ).
- Correo entrante con autorización y trazabilidad.
- Módulo para firmas de abogados.
- Múltiples pretensiones con áreas colaboradoras.
- Analítica de tiempos de respuesta.
- Encadenamiento criptográfico de la bitácora.

## 10. Fuentes normativas de referencia

- Ley 1755 de 2015: derecho de petición (arts. 14, 20, 21, 30).
- Decreto 2591 de 1991: tutela (arts. 16, 19, 27, 31, 52).
- Ley 527 de 1999: mensajes de datos y admisibilidad probatoria.
- Ley 1581 de 2012: protección de datos personales.
- Ley 51 de 1983 (Ley Emiliani): festivos trasladados.

## 11. Decisiones de diseño explícitas (del abogado)

| Decisión | Respuesta |
|---|---|
| ¿La IA define el vencimiento? | No. Propone; un usuario confirma. |
| ¿Un solo vencimiento por tutela? | No. Se modelan obligaciones independientes. |
| ¿La recepción del correo inicia el plazo? | No siempre. Se registra y se valida el evento jurídico. |
| ¿Qué pasa si no se identifica el plazo? | Revisión manual; no inventar fecha. |
| ¿Las reglas se sobrescriben? | No. Se versionan con vigencia. |
| ¿La bitácora prueba el cumplimiento? | No automáticamente. Documenta eventos y evidencias. |
| ¿Expedientes reales en demos? | Nunca. Solo documentos ficticios. |
| ¿Nombres de áreas hardcodeados? | No. Configurables por organización. |
