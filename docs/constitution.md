# Constitución — Plazos.co

Principios innegociables. Toda spec, plan y tarea debe cumplirlos.

1. **La spec manda.** Nada se implementa si no está en la spec activa. Si falta una decisión, se para y se pregunta. Un cambio va primero a la spec, luego al plan, luego al código.

2. **Lógica separada de interfaz.** Los cálculos de negocio (días hábiles, plazos, semáforos, alertas) son funciones puras que reciben la fecha "hoy" como parámetro. No dependen de la base de datos, del framework ni de la petición HTTP. Viven en módulos propios (`core/`).

3. **Tests como puerta.** La lógica de negocio se prueba con pytest (backend) y Vitest (frontend). Prohibido fusionar un PR con tests en rojo. Cada requisito funcional (RF) tiene al menos un test que lo cubre.

4. **Los plazos no se adivinan.** Un requerimiento puede tener varias **obligaciones** con plazos independientes (`Obligacion`). Cada fecha límite pasa por cinco capas: regla configurada → plazo del documento → fecha sugerida por el motor → fecha confirmada por una persona → fecha modificada posterior (sin borrar la anterior). La tabla de reglas (`ReglaPlazo`) es versionada: nunca se sobrescribe una regla ya usada; se crea una nueva versión con nueva vigencia. La fecha de recepción, la fecha de notificación y el evento que inicia el cómputo son campos separados. La IA propone; una persona confirma antes de que algo entre a la bandeja.

5. **Los datos del usuario son sagrados.** Solo se usan documentos sintéticos o anonimizados en desarrollo y demos (Ley 1581 de 2012). La bitácora de auditoría es inmutable: solo se agregan registros, nunca se editan ni se borran. Cada organización solo ve sus propios datos (multi-tenant estricto).

6. **Un stack, sin atajos.** Backend en Python/FastAPI, frontend en TypeScript/Next.js, base de datos en PostgreSQL. No se agregan frameworks, ORMs ni librerías de UI adicionales sin una decisión documentada en un ADR. Las dependencias se justifican.

7. **Idioma.** Código (variables, funciones, clases, commits) en inglés. Interfaz de usuario, documentación y specs en español.

---

## Roles del MVP

- **Administrador:** configura la organización, las áreas y los usuarios.
- **Coordinador jurídico:** revisa las propuestas de la IA, confirma fechas y asigna áreas.
- **Responsable de área:** atiende los requerimientos de su bandeja.

## Historias de usuario

### Organización y áreas
- HU-1. Como administrador, quiero registrar mi organización y sus áreas con sus temas y responsables, para que la IA sepa a quién enrutar cada requerimiento.
- HU-2. Como administrador, quiero crear usuarios con roles (coordinador, responsable), para que cada persona vea solo lo que le corresponde.

### Recepción
- HU-3. Como coordinador, quiero subir el PDF de un requerimiento y que el sistema registre la fecha y hora de recepción, el medio y el hash del documento, para que el sistema lo analice automáticamente y quede trazabilidad desde el minuto cero.

### Análisis con IA
- HU-4. Como coordinador, quiero que la IA me diga de qué tipo es el documento, quién lo envía, de qué trata, qué área debería atenderlo y, si detecta múltiples actuaciones con plazo, las proponga como obligaciones separadas, para no tener que leer 20 páginas antes de asignarlo.

### Confirmación humana
- HU-5. Como coordinador, quiero revisar los datos que la IA extrajo, confirmar o corregir el evento que inicia el cómputo, la regla aplicable y la fecha límite de cada obligación, para que ningún plazo entre a la bandeja sin verificación humana.

### Bandeja con semáforo
- HU-6. Como responsable de área, quiero ver mis requerimientos pendientes ordenados por la urgencia de su obligación más próxima, con colores (rojo, naranja, amarillo, verde), para saber qué contestar primero.
- HU-7. Como coordinador, quiero filtrar la bandeja por área, tipo, responsable y estado, para tener visibilidad de toda la organización.

### Alertas escalonadas
- HU-8. Como responsable de área, quiero recibir un correo cuando me queden 5, 2 y 1 días hábiles para el vencimiento de cada obligación, para no dejar vencer un plazo.
- HU-9. Como coordinador, quiero que me escalen si un responsable no marca una obligación como "en gestión" después de la segunda alerta, para intervenir a tiempo.

### Ciclo de vida y vinculación
- HU-10. Como coordinador, quiero vincular una tutela al derecho de petición que la originó, y un desacato a su tutela, para ver la trazabilidad completa de la cadena.
- HU-11. Como responsable, quiero marcar cada obligación de un requerimiento como "en gestión" y luego "respondido", para que la bandeja refleje el avance real por plazo.

### Auditoría
- HU-12. Como administrador, quiero ver la bitácora completa de un requerimiento (quién cargó, qué propuso la IA, quién confirmó, qué alertas se enviaron), para demostrar que la organización fue diligente.

### Panel
- HU-13. Como coordinador, quiero ver cuántos requerimientos y obligaciones hay vencidos, en riesgo y al día, desglosados por área y tipo, con tasa de respuesta a tiempo, para tomar decisiones con datos.

## Nota sobre las specs

Cada spec (en `specs/NNN-nombre/`) usará la notación EARS para los requisitos funcionales:

- **Evento:** CUANDO `<evento>`, EL SISTEMA `<respuesta>`.
- **Condición no deseada:** SI `<condición>`, ENTONCES EL SISTEMA `<respuesta>`.
- **Estado:** MIENTRAS `<estado>`, EL SISTEMA `<comportamiento>`.
- **Permanente:** EL SISTEMA `<comportamiento permanente>`.

Cada RF debe ser verificable: nada de "rápido", "intuitivo" o "bonito" sin un criterio medible.
