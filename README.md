# Trabajo Practico Integrador Organización Empresarial - Sosa Carlos Ezequiel
Módulo centralizado de gestión logística y redistribución de stock para HABiTAR (DILCAR S.A.). TPI de Organización Empresarial, UTN - TUP a Distancia.
# Módulo Centralizado de Gestión Logística y Redistribución de Stock – Habitar / DILCAR S.A.

Trabajo Práctico Integrador – Organización Empresarial
Tecnicatura Universitaria en Programación a Distancia – Universidad Tecnológica Nacional (UTN)

| | |
|---|---|
| **Tablero ágil** | [GitHub Projects](https://github.com/users/Carlosesosa/projects/1/views/1) |
| **Repositorio** | [GitHub](https://github.com/Carlosesosa/Trabajo-Practico-Integrador---Organizaci-n-Empresarial---Sosa-Carlos-Ezequiel.git) |
| **Docente titular** | Prof. Gabriela Martínez |
| **Docentes adjuntos** | Prof. Carolina Bruno, Prof. Mario Raúl López, Prof. Andrea Ramos |
| **Fecha de entrega** | 1 de noviembre de 2026 |

## Integrantes

- Carlos Ezequiel Sosa

## Descripción del sistema

Habitar (DILCAR S.A.) es una cadena comercial de productos para el hogar con 37 sucursales en Chaco, Corrientes, Formosa, Misiones y Santa Fe. Hoy cada sucursal realiza sus propios pedidos de mercadería según lo que considera necesario, lo que puede generar sobrestock de productos con poca demanda local mientras otra sucursal podría venderlos con mayor facilidad.

El proyecto propone un **módulo centralizado de gestión logística y redistribución de stock** que:

- Centraliza en una base de datos las existencias, ventas y movimientos de mercadería de todas las sucursales.
- Analiza periódicamente la rotación de cada producto y genera **alertas de baja rotación** (regla inicial: 30 días sin ventas, configurable por categoría).
- Busca sucursales con demanda y stock insuficiente, y **recomienda traslados** con la cantidad estimada.
- Deja la decisión final en manos del personal autorizado de Logística (la alerta nunca mueve mercadería de forma automática).
- Registra todo el ciclo del traslado (recomendado, autorizado, en transporte, recibido, con incidencia, rechazado) para garantizar trazabilidad.

**Objetivo general:** diseñar, desarrollar e implementar el módulo, con posibilidad de integración vía API con los sistemas existentes de venta e inventario, para optimizar la rotación de mercadería entre las 37 sucursales, reducir el capital inmovilizado y apoyar la toma de decisiones en datos históricos de demanda.

### Módulos de la solución

1. Gestión de productos y sucursales
2. Consulta y actualización de stock
3. Análisis de ventas y rotación
4. Alertas y recomendaciones
5. Reportes para el área de Logística

### Entidades principales

`PRODUCTO`, `SUCURSAL`, `STOCK`, `VENTA`, `MOVIMIENTO_MERCADERIA`, `ALERTA`, `USUARIO`.

## Modelado del proceso (BPMN)

Diagrama del proceso de redistribución de mercadería entre sucursales, con Pools y Lanes:

![Diagrama BPMN](.....)

**Flujo estándar:** el sistema analiza ventas y stock → detecta un producto que supera el plazo sin ventas → genera la alerta → busca una sucursal candidata → recomienda el traslado → Logística revisa y autoriza → se registra el envío y la mercadería queda en tránsito → la sucursal destino confirma la recepción → se actualiza el stock de ambas sucursales.

**Compuertas de decisión (excepciones):**

| Compuerta | Si la respuesta es "No" |
|---|---|
| ¿Supera el plazo sin ventas? | El producto no genera alerta y se vuelve a evaluar en el siguiente ciclo. |
| ¿Existe sucursal candidata? | La alerta queda registrada sin traslado recomendado, para revisión manual. |
| ¿Logística autoriza el traslado? | El movimiento pasa a estado *Rechazado* y se registra quién lo decidió. |
| ¿Coinciden cantidad enviada y recibida? | Se registra una incidencia (*Con incidencia*) para su revisión. |

## Historias de usuario

Formato: **Como** [usuario], **quiero** [funcionalidad], **para** [beneficio].

### HU-01 – Alertas de baja rotación

**Como** responsable de Logística, **quiero** recibir alertas de los productos que superan el plazo configurado sin registrar ventas en una sucursal, **para** evaluar su traslado antes de que la mercadería quede inmovilizada.

**Criterios de aceptación**
- El sistema analiza ventas y stock de forma periódica y automática.
- Se genera una alerta cuando un producto con stock mayor a cero supera `dias_limite_sin_venta` (valor inicial: 30 días) sin ventas en esa sucursal.
- La alerta muestra producto, sucursal, días sin venta, motivo y fecha de generación.
- Las alertas nuevas quedan en estado *Pendiente* y no se duplican para el mismo producto y sucursal mientras sigan abiertas.

### HU-02 – Revisión y autorización de traslados

**Como** responsable de Logística, **quiero** revisar las recomendaciones de traslado y autorizarlas o rechazarlas, **para** decidir la redistribución considerando compromisos de venta y costos de transporte, sin que el sistema mueva mercadería por sí solo.

**Criterios de aceptación**
- Cada recomendación indica producto, sucursal de origen, sucursal de destino y cantidad sugerida.
- Solo los usuarios con rol *Logística* pueden autorizar o rechazar.
- Al autorizar o rechazar, el sistema registra el usuario, la fecha y el nuevo estado.
- El sistema valida que origen y destino sean distintos y que la cantidad sea mayor que cero y no supere el stock del origen.

### HU-03 – Confirmación de recepción

**Como** encargado de sucursal, **quiero** confirmar la recepción de la mercadería indicando las unidades realmente recibidas, **para** mantener el stock actualizado y dejar registrada cualquier diferencia con lo enviado.

**Criterios de aceptación**
- La sucursal destino puede confirmar solo los traslados en estado *En transporte* dirigidos a ella.
- Al confirmar, se actualiza el stock del destino con la cantidad recibida.
- Si `cantidad_recibida` difiere de `cantidad_enviada`, el traslado pasa a *Con incidencia* para su revisión.
- Queda historial de cada movimiento consultable por Logística.

## Tablero ágil

Las historias se gestionan en el tablero: [.....]

| Historia | Estado actual |
|---|---|
| HU-01 – Alertas de baja rotación | ..... |
| HU-02 – Revisión y autorización de traslados | ..... |
| HU-03 – Confirmación de recepción | ..... |

Columnas del tablero: **Por Hacer → En Progreso → Listo**. Cada tarjeta se vincula a su Issue del repositorio y cada Pull Request referencia el Issue correspondiente (por ejemplo, `Closes #1`).

## Indicadores de control y estándares de desempeño

Indicadores para la auditoría técnica del sistema. Los primeros cuatro derivan de las metas de la Entrega N°2.

| Indicador | Fórmula / medición | Estándar (meta) | Frecuencia | Fuente |
|---|---|---|---|---|
| Tiempo de respuesta de consultas de stock | % de consultas por API resueltas en menos de 2 s | ≥ 95 % | Por prueba de carga y mensual | Logs de la API |
| Redistribución de mercadería inmovilizada | Productos con más de 30 días sin ventas redistribuidos ÷ total de productos en esa condición | ≥ 35 % en los primeros 6 meses | Mensual | Tablas ALERTA y MOVIMIENTO_MERCADERIA |
| Capacidad de procesamiento | Registros diarios de transacciones sincronizados sin error | Hasta 100.000 por día | Diaria | Logs de sincronización |
| Cumplimiento del plazo de desarrollo | Semanas reales ÷ semanas planificadas | 16 semanas hábiles | Semanal | Diagrama de Gantt y tablero |
| Tasa de incidencias en recepción | Traslados *Con incidencia* ÷ traslados recibidos | A definir tras el piloto (referencia inicial: ≤ 5 %) | Mensual | MOVIMIENTO_MERCADERIA |
| Tiempo de gestión de alertas | Tiempo promedio entre generación de la alerta y su resolución | A definir tras el piloto | Semanal | ALERTA |
| Trazabilidad de autorizaciones | Traslados autorizados con `id_usuario_autoriza` registrado | 100 % | Mensual | MOVIMIENTO_MERCADERIA |
| Integridad de stock | Registros con `cantidad_disponible` negativa o duplicados de producto + sucursal | 0 | Diaria | Tabla STOCK |

## Flujo de trabajo con Git

**Ramas**
- `main`: versión estable y entregable. No se trabaja directamente sobre ella.
- Ramas secundarias por tarea, con prefijo según el tipo: `docs/`, `feature/`, `fix/`. Ejemplos: `docs/bpmn-proceso`, `docs/historias-usuario`, `docs/indicadores-control`.

**Mensajes de commit** (formato `tipo: descripción en imperativo`)
- `docs: agrega diagrama BPMN del proceso de redistribución`
- `docs: redacta historias de usuario HU-01 a HU-03 con criterios de aceptación`
- `docs: incorpora tabla de indicadores de control`
- `fix: corrige descripción del estado Con incidencia en el diccionario`

**Pull Requests**
1. Crear la rama desde `main`, hacer commits pequeños y descriptivos.
2. Abrir un Pull Request hacia `main` indicando qué cambia y a qué historia se vincula.
3. Un par revisa y deja comentarios; se resuelven los comentarios antes de aprobar.
4. Se hace el merge y se elimina la rama.

## Estructura del repositorio

```
.
├── README.md
├── entrega-n4/   Documento consolidado
└── bpmn/        Diagrama del proceso
```

## Declaración de Uso de IA

> **Nota para completar:** esta sección debe reflejar lo que realmente hiciste. Reemplazá los textos entre corchetes con tus prompts reales y las correcciones concretas que hiciste; la consigna evalúa justamente esa justificación crítica.

### Herramientas utilizadas

| Entrega | Herramienta | Uso |
|---|---|---|
| N°1 y N°2 | ChatGPT | Organizar ideas, mejorar la redacción, estructurar las respuestas y el diagrama de Gantt. |
| N°3 | Claude (Anthropic) | Organizar y dar formato al documento, revisar la redacción y trabajar sobre el diccionario de datos. |
| N°4 | Claude (Anthropic) | Organizar la redacción del archivo README.md y la consolidación del documento. |

### Prompts utilizados y su justificación

| Prompt (resumen) | Por qué lo formulé así | Qué resultado obtuve |
|---|---|---|
| ..... | ..... | ..... |
| ..... | .....|..... |
| .....| ..... | ..... |

### Correcciones humanas sobre los resultados de la IA

Ejemplos de puntos a revisar y, si aplican, a declarar (confirmá cuáles hiciste realmente):

- La problemática logística surge de mi experiencia laboral en Habitar; la IA no la identificó.
- La referencia comparativa con Cetrogar proviene de información que obtuve a través de un familiar, no de la IA.


### Responsabilidad

Revisé y verifiqué el contenido final de este repositorio y asumo la responsabilidad por la versión presentada. Las herramientas de IA se usaron como apoyo, no como reemplazo del análisis propio.

## Bibliografía

- HABiTAR. (s.f.). *Quiénes somos*. https://habitar.com.ar/quienes-somos
- HABiTAR. (s.f.). *Sucursales*. https://habitar.com.ar/sucursales
- HABiTAR. (s.f.). *Cómo comprar online*. https://habitar.com.ar/como-comprar
- Sosa, C. E. (2026). *Trabajo Práctico Integrador, Entregas N°1, N°2 y N°3*. Organización Empresarial, Tecnicatura Universitaria en Programación, UTN.
