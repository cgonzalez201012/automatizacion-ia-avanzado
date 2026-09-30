# Automatización x IA - Avanzado | Proyecto integrador

**Alumno:** Cristian González
**Checkpoint 1:** Agente base y motor de razonamiento (Módulo 1)
**Archivo:** 'm1/checkpoint1_cristian_gonzalez.json'

## Caso de uso

Agente de Triage de Data Governance para un cliente del sector retail. Recibe pedidos sin estructura (incidentes de calidad de datos, altas de maestros, consultas de proceso, pedidos de reporte), los clasifica, los registra y escala los de prioridad alta.

## Arquitectura del flujo

```
Chat Trigger → AI Agent (Tools Agent) → Gmail (log de auditoría)
                  ├─ Anthropic Chat Model   (conexión lateral)
                  └─ Google Sheets Tool     (conexión lateral)
```

- **Trigger:** Chat Trigger.
- **AI Agent (Tools Agent):** conectado a Anthropic Chat Model (Claude Sonnet 4.5, temperatura 0.2).
- **Guardrail de iteraciones:** Max Iterations = 6 y Return Intermediate Steps activado.
- **System Message:** modular (Rol, Ámbito, Objetivo, Reglas y Escalamiento), con acciones prohibidas explícitas y sin lenguaje inclusivo.
- **Tool (conexión lateral):** Google Sheets (append). Registra cada caso como una fila. Su descripción detalla cuándo debe usarla el agente de forma autónoma y cuándo no. No hay nodos de acción secuenciales después del agente, salvo la notificación final.
- **Observabilidad:** nodo final de Gmail que envía "Tarea completada" con la respuesta del agente y el log de herramientas usadas (entrada y resultado).

## Cómo importarlo

1. En n8n: menú ⋯ → Import from File → seleccionar el JSON.
2. Reasignar las credenciales de Anthropic, Google Sheets y Gmail (no se incluyen por seguridad).
3. Crear una planilla de Google Sheets con estos encabezados en la fila 1:
   `fecha_hora, solicitante, tipo_caso, dominio_dato, descripcion, prioridad, accion_recomendada, escalamiento`
4. Elegir la planilla en el nodo de Google Sheets y el email destino en el nodo de Gmail.

## Pruebas realizadas

- **Prioridad alta:** el caso se registra en la planilla y se escala al Líder de Consultoría.
- **Prioridad baja:** se registra sin escalamiento.
- **Pedido fuera de ámbito:** el agente lo rechaza y no registra nada.
- **Saludo:** responde sin usar la herramienta (decisión autónoma del agente).

En todas las corridas los nodos quedaron en verde y llegó el mail de auditoría.

## Evolución del proyecto

Este flujo es la base del proyecto integrador. En cada módulo se importa el JSON anterior y se agregan solo los nodos nuevos: multi-agente (M2), memoria (M3), integraciones (M4), RAG (M5), voz (M6), hasta el proyecto final (M11).
