# Checkpoint 4 — Game Production Integrations

**AI Automation Avanzado · Coderhouse**  
**Proyecto:** Rush Rift — Game Production AI Agent  
**Autora:** Camila Liendro

## Descripción

Este checkpoint amplía el mismo proyecto integrador de los módulos anteriores. Conserva el Manager, sus Workers y la memoria por `Session_ID` en Airtable, e incorpora una rama de soporte por correo conectada con **Salesforce, Gmail y Slack**.

Cuando llega una consulta a Gmail, n8n filtra posibles respuestas automáticas, normaliza el mensaje y utiliza IA para redactar una respuesta preliminar. Luego busca al remitente en Salesforce: si existe, actualiza el contacto; si no, lo crea. Finalmente, guarda la respuesta en **Borradores de Gmail** para revisión humana y avisa por Slack. **No envía automáticamente la respuesta al remitente.**

## Integraciones

| Herramienta | Función | Autenticación |
|---|---|---|
| Salesforce | Buscar, crear y actualizar contactos | OAuth2 |
| Gmail | Recibir mensajes y guardar borradores | OAuth2 |
| Slack | Avisar al equipo en `#general` | OAuth2 |
| Airtable | Conservar la memoria del TP3 | OAuth2 |

## Controles del módulo

1. **IF anti auto-reply:** corta mensajes `Auto-reply`, `Out of office`, `Undeliverable` y remitentes `no-reply` antes de llegar a la IA.
2. **Look up antes de Create:** realiza una búsqueda por email en Salesforce para elegir entre actualizar o crear un contacto y evitar duplicados en el uso normal.
3. **Create Draft / Human-in-the-loop:** Gmail guarda un borrador que debe revisar una persona antes del envío.
4. **Set de limpieza de payload:** reduce el contenido a campos necesarios y genera una notificación breve para Slack, sin adjuntos ni objetos binarios.

## Pruebas manuales

Se comprobó en la instancia de n8n:
- Recepción de correos reales de prueba en Gmail.
- Paso de una consulta normal por la rama permitida del IF.
- Bloqueo de `Auto-reply: Fuera de oficina` por **False Branch**.
- Búsqueda de un contacto, creación en Salesforce, nueva búsqueda del mismo correo y actualización del registro existente.
- Creación de un mensaje con etiqueta `DRAFT` en Gmail.
- Publicación de una notificación resumida en Slack.
- **Prueba integral final:** un nuevo correo pasó por IA → Salesforce Create → Gmail Create Draft → Slack, con nodos ejecutados satisfactoriamente.

Las pruebas corresponden a la instancia original. El archivo público no contiene historial de ejecuciones, ni valida por sí solo el comportamiento en otra cuenta de n8n.

## Evidencias

Las siete capturas de evidencia ya están publicadas en esta misma carpeta `checkpoint4/` y se pueden abrir desde los enlaces siguientes.

| Archivo preparado | Contenido |
|---|---|
| [`01_workflow_completo.png`](./01_workflow_completo.png) | Arquitectura TP3 + integraciones TP4 |
| [`02_credenciales_oauth2.png`](./02_credenciales_oauth2.png) | Lista de Salesforce, Gmail y Slack OAuth2 |
| [`03_filtro_auto_reply_bloqueado.png`](./03_filtro_auto_reply_bloqueado.png) | False Branch del IF anti auto-reply |
| [`04_gmail_borrador_creado.png`](./04_gmail_borrador_creado.png) | Borrador confirmado por Gmail |
| [`05_notificacion_slack.png`](./05_notificacion_slack.png) | Aviso enviado a `#general` |
| [`06_prueba_integral_workflow.png`](./06_prueba_integral_workflow.png) | Prueba de extremo a extremo |
| [`07_prueba_integral_slack.png`](./07_prueba_integral_slack.png) | Notificación de la prueba integral |

## Archivo e importación

[Descargar el workflow JSON](./checkpoint4_camila_liendro.json)

1. En n8n, crear un workflow e importarlo desde **Import from File**.
2. Asignar credenciales propias para Salesforce, Gmail, Slack, Airtable y el proveedor de IA.
3. Revisar los identificadores de la base de Airtable, los dos subworkflows de los Workers y el canal de Slack, porque dependen de la instancia original.
4. Hacer pruebas con correos y contactos de prueba antes de activar el workflow.
5. Mantener desactivado el antiguo nodo de envío automático de reportes. El soporte utiliza únicamente **Gmail Draft → Create**.

Esta versión pública mantiene los **49 nodos** del flujo sin el nodo temporal `Get a contact` usado para diagnosticar Salesforce. No incluye credenciales de n8n ni claves privadas, y se entrega con `active: false`.

## Limitaciones

El borrador de IA requiere revisión: no debe asumirse que el texto generado contiene información oficial verificada. La búsqueda por email disminuye el riesgo de duplicados, pero no impone unicidad estricta frente a ejecuciones concurrentes. El filtro anti auto-reply reconoce patrones frecuentes, aunque no todos los posibles mensajes automáticos.
