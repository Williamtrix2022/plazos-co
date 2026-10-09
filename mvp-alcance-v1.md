# Plazos.co — Alcance del MVP v1

Fecha: 8 de octubre de 2026 · Base: retroalimentación del amigo abogado y decisiones de William.
Este documento sirve de entrada para `docs/constitution.md` y las specs 001–005.

---

## 1. Decisiones tomadas

| # | Observación del abogado | Decisión |
|---|---|---|
| 1 | A los juzgados no les sirve (lo resuelve la secretaría); a empresas y abogados sí | **MVP para empresas y entidades** que *reciben* requerimientos. Los abogados independientes quedan para la v2. |
| 2 | Las empresas dejan vencer los derechos de petición y terminan contestando tutelas | El MVP maneja **tutela + derecho de petición + desacato**, con las reglas de plazo en una tabla configurable. |
| 3 | Organizar por prioridad: qué contestar primero | **Bandeja con semáforo** ordenada por urgencia. |
| 4 | La IA debe saber de qué trata el documento y qué área lo resuelve, sin contestar | **Clasificación y enrutamiento con IA**, siempre confirmado por una persona. |
| 5 | Responsabilidad si a la empresa se le vence un plazo y culpa a la herramienta | **Trazabilidad completa** + confirmación humana + términos y condiciones (la cláusula la redacta un abogado). |
| 6 | Consultar automáticamente el estado de los procesos (Rama Judicial) | **Arquitectura de conectores lista; integración en la v2.** |

## 2. Qué hace el MVP

1. **Organización y áreas.** La empresa registra sus áreas (nombre, funciones, temas que atiende y responsable) y sus usuarios. Roles: administrador, coordinador jurídico y responsable de área.
2. **Recepción.** Se sube el PDF de un derecho de petición, auto de tutela o requerimiento de desacato.
3. **La IA analiza el documento.** Extrae los datos (radicado, tipo, partes, juzgado o peticionario, fechas, plazo indicado), identifica el tema, hace un resumen de 3 líneas y **sugiere un área**, con un nivel de confianza y una justificación.
4. **Confirmación humana.** El coordinador revisa los datos, **confirma la fecha límite** y asigna el área. Sin esta confirmación el requerimiento no entra a la bandeja.
5. **Bandeja con semáforo.** Rojo si está vencido o vence hoy; naranja si quedan 1 o 2 días hábiles; amarillo si quedan de 3 a 5; verde si quedan más de 5. Se puede filtrar por área, tipo y responsable.
6. **Alertas escalonadas.** Correo a los 5, 2 y 1 días hábiles del vencimiento. Si el responsable no marca el requerimiento como "en gestión", la alerta **se escala** al coordinador.
7. **Ciclo de vida y vinculación.** Estados: recibido → asignado → en gestión → respondido → cerrado. Una tutela se puede **vincular** al derecho de petición que la originó, y un desacato a su tutela.
8. **Bitácora de auditoría.** Registro que no se puede editar: quién cargó, qué propuso la IA, quién confirmó qué fecha, qué alertas se enviaron, a quién y cuándo, y cada cambio de estado.
9. **Panel.** Cuántos requerimientos hay vencidos, en riesgo y al día, desglosados por área y por tipo. Incluye la tasa de respuesta a tiempo.

## 3. Lo que NO hace el MVP

- No redacta respuestas ni da concepto jurídico.
- No consulta la Rama Judicial (eso es la v2, con conectores).
- No se conecta con el correo de la empresa (la carga es manual; recibir por correo queda para la v2).
- No incluye el módulo para abogados independientes (cartera de procesos), que es la v2.

## 4. Reglas de plazos (tabla configurable — **validar con el abogado**)

| Tipo | Plazo base | Cómo se cuenta | Fuente de referencia |
|---|---|---|---|
| Derecho de petición general | 15 días | Hábiles, desde la recepción | Ley 1755 de 2015, art. 14 |
| Petición de documentos o información | 10 días | Hábiles | Ley 1755 de 2015, art. 14 |
| Petición de consulta | 30 días | Hábiles | Ley 1755 de 2015, art. 14 |
| Tutela: contestar el auto admisorio | El que fije el juez | Según el auto (días u horas) | Decreto 2591 de 1991 |
| Tutela: cumplir el fallo | El que fije el fallo | Según el fallo | Decreto 2591 de 1991, art. 27 |
| Tutela: impugnar el fallo | 3 días | Desde la notificación | Decreto 2591 de 1991, art. 31 |
| Desacato: responder el requerimiento | El que fije el juez | Según el auto | Decreto 2591 de 1991, art. 52 |

- **Prórroga del derecho de petición:** debe informarse antes del vencimiento y no puede superar el doble del plazo inicial (art. 14, parágrafo). El sistema permite registrarla y la guarda en la bitácora.
- **Días hábiles:** se excluyen sábados, domingos y festivos de Colombia (incluidos los trasladados al lunes por la Ley Emiliani).

## 5. Cómo funciona la IA (reglas)

- **Entrada:** el texto del PDF + el catálogo de áreas de la empresa (con sus descripciones y temas).
- **Salida estructurada (JSON):** tipo, datos extraídos, tema, resumen, área sugerida, confianza (0 a 1), justificación y campos dudosos.
- **Si la confianza es menor a 0,7,** el documento queda marcado como "revisión manual" y no se sugiere ningún área.
- **La IA nunca fija la fecha límite definitiva:** propone una y una persona la confirma.
- **Aprendizaje:** cuando una persona corrige el área, la corrección se guarda como ejemplo para las siguientes clasificaciones.
- **Datos:** para desarrollo y demostración solo se usan documentos ficticios. Se envía al modelo solo el texto necesario.

## 6. Protección ante responsabilidad

- **Técnica (se construye en el MVP):** bitácora de auditoría, confirmación humana de fechas, alertas múltiples con escalamiento, pruebas automáticas del cálculo de días hábiles y un reporte exportable de la bitácora de cada requerimiento.
- **Contractual (antes de vender):** términos y condiciones con limitación de responsabilidad. La plataforma es una herramienta de apoyo y el control de los términos sigue siendo del cliente. Esa cláusula la redacta un abogado.
- **Aviso visible en la aplicación:** "Las fechas sugeridas deben ser verificadas por el usuario".

## 7. Modelo de datos (preparado para crecer)

```
Organizacion (tipo: empresa | firma)        ← multi-tenant desde el día 1
 ├─ Area (nombre, descripcion, temas, responsable)
 ├─ Usuario (rol)
 ├─ ReglaPlazo (tipo_requerimiento, dias, unidad, base_de_conteo)
 └─ Requerimiento (tipo, radicado, partes, area, estado, fecha_recepcion, fecha_limite_confirmada)
     ├─ Documento (pdf, texto_extraido, analisis_ia)
     ├─ Evento (bitácora: quién, qué, cuándo)
     ├─ Alerta (programada, enviada, escalada)
     └─ vinculado_a → Requerimiento (petición → tutela → desacato)
Conector (interfaz)  →  v2: CPNU / Samai / correo entrante
```

## 8. Ruta hacia la v2 (escalabilidad)

- **Módulo de abogados:** cartera de procesos propios, usando el mismo modelo con `Organizacion.tipo = firma`.
- **Conectores:** consulta de procesos (prueba de concepto con CPNU, después de revisar sus términos de uso; luego Samai/SIUGJ) y recepción automática desde el correo.
- **Más tipos de requerimiento** (quejas, reclamos, otras PQRSD): se agregan filas a la tabla `ReglaPlazo`, sin cambiar el código.

## 9. Preguntas pendientes para el abogado

1. ¿La tabla de plazos de la sección 4 está correcta? ¿Falta alguno (por ejemplo, la consulta del desacato o las peticiones entre autoridades)?
2. En una tutela, ¿el plazo empieza a contar desde el recibo del correo o desde otra fecha?
3. ¿Qué áreas suele tener una entidad como la Unidad para las Víctimas, para usarlas en los datos de demostración?
4. ¿Qué debería contener el reporte de la bitácora para que sirva como prueba?
