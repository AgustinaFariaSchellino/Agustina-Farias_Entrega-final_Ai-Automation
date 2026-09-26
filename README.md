# Asesor Financiero IA — Ecosistema Autónomo de Conciliación y Control Presupuestario

> **Proyecto Final Integrador — AI Automation**  
> **Estudiante:** Agustina Farías  
> **Orquestador Principal:** n8n  
> **Base de Datos & Memoria:** Airtable  
> **Motor Cognitivo (LLMs):** Anthropic Claude 3.5 Sonnet & OpenAI GPT-4o-mini  
> **Canales de Interacción:** Gmail (Ingesta & Documentación) + Slack (HITL & Alertas)

---

## 📌 Descripción del Proyecto

**Asesor Financiero IA** es un ecosistema autónomo de negocio diseñado de extremo a extremo para resolver la conciliación, auditoría y control de consumos bancarios. 

El sistema escucha extractos y resúmenes de tarjetas de crédito vía Gmail, procesa la lectura densa y extracción estructurada con IA, categoriza los gastos de forma autónoma y recurre a un bucle de **Human-in-the-Loop (HITL)** interactivo en Slack cuando detecta conceptos nuevos o dudosos. Una vez conciliados los datos en una base relacional en Airtable, audita los techos de gasto por categoría y genera dinámicamente **ideas semilla de ahorro financiero** personalizadas.

---

## 🛠️ Stack Tecnológico Integrado

| Componente | Herramienta | Función en el Ecosistema |
| :--- | :--- | :--- |
| **Orquestador** | `n8n` | Orquestación lógica, routers, manejo de errores y sub-workflows. |
| **Base de Datos** | `Airtable` | Cerebro relacional (`credit_card_statements`, `expenses`, `budget_categories`, `system_logs`). |
| **Lectura Densa & Asesoría** | `Claude 3.5 Sonnet` | Extracción de transacciones complejas, análisis de desvíos y generación de ideas semilla de ahorro con Prompt Caching. |
| **Normalización & Triage** | `GPT-4o-mini` | Clasificación rápida, normalización de strings a valores numéricos y sanitización de payloads. |
| **Canal de Entrada** | `Gmail` | Disparador por evento (*From now on*) con filtro anti auto-replies. |
| **Canal HITL & Alertas** | `Slack API` | Interacción en tiempo real para validación humana de categorías y alertas críticas. |

---

## 🏗️ Arquitectura del Flujo

1. **Trigger Inteligente:** Ingesta de extractos mediante el nodo `Gmail Trigger` filtrado por asunto y remitente (evitando bucles infinitos y auto-respuestas).
2. **Sanitización & Triage:** Nodo `Code / Set` para minimización de datos (GDPR) extrayendo solo metadatos y cuerpo esencial en `snake_case`.
3. **Extracción Estructurada:** Claude 3.5 Sonnet extrae fecha, comercio, moneda original, monto consolidado en ARS, cuotas y categoría sugerida.
4. **Bifurcación Lógica & HITL:**
   - **Categoría conocida (`ai_categorized`):** Se impacta directamente en la tabla `expenses` de Airtable.
   - **Categoría desconocida (`pending_classification`):** El flujo se pausa y envía una tarjeta interactiva a Slack (`#aprobaciones-gastos`). Al confirmar el usuario, se actualiza el registro como `human_approved`.
5. **Cálculo de Desvío & Generación Semilla:** 
   - Se recalculan los techos en `budget_categories`.
   - Si el gasto supera el 80% o excede el límite, se dispara la generación de una recomendación estratégica de ahorro mediante un prompt con directriz paso a paso.
6. **Alerta de Salida:** Notificación ejecutiva en Slack (`#alertas-presupuesto`) con opción de guardar la recomendación en la lista diaria del usuario.

---

## 🛡️ Seguridad, Resiliencia y Gobernanza

- **Minimización de Datos:** No se transfieren payloads HTML crudos ni datos bancarios sensibles no indispensables.
- **Error Handling:** Ramas de contingencia con directivas de reintento (`Break` / 3 intentos a intervalos exponenciales) y volcado automático de fallos en la tabla `system_logs`.
- **Filtros Anti-Loop:** Detección estricta de encabezados `Auto-reply`, `Out of Office` y casillas `no-reply@`.
- **Tipado Estricto:** Validación matemática determinista de números vs. números en filtros de presupuesto para evitar concatenaciones de strings.

---

## 📂 Contenido del Repositorio

- `diagrama_arquitectura.pdf`: Diagrama visual completo del ecosistema con simbología formal.
- `manual_operativo_y_costos.pdf`: Documentación de tablas relacionales, esquemas JSON y matriz de optimización financiera (Tokens / Prompt Caching / Batches).
- `workflow_asesor_financiero.json`: Blueprint exportado del flujo listo para importar en n8n.
- `/evidencias`: Capturas de pantalla de ejecuciones exitosas, bifurcaciones de filtros, HITL en Slack y respuesta de Error Handling.

---

## 🔗 Enlaces Operativos

- **Dashboard de Control Ejecutivo (Airtable Shared View):** [Enlace Público en Modo Lectura](TU_ENLACE_AQUI)
- **Video Demostrativo (3 min):** [Enlace al Video Demo](TU_ENLACE_AQUI)
