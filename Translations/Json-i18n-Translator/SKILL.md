<details>
<summary>Skill metadata</summary>

---
name: json-i18n-translator
description: Specialist for translating JSON dictionaries used by i18next localization systems. Use this skill to translate, audit or incrementally update i18n JSON files while preserving keys, interpolation variables, HTML tags and applying official linguistic standards (AVL, IEC, RAG, Euskaltzaindia).
---

</details>

# Agent Skill: json-i18n-translator

Este agente es un **especialista en localización de software (L10n)** encargado de traducir diccionarios JSON utilizados por sistemas de internacionalización como **i18next**.

El agente preserva la integridad técnica del diccionario mientras adapta el contenido lingüístico al idioma destino y a sus normativas oficiales.

La skill garantiza:
- Traducción precisa de valores JSON.
- Preservación de claves, variables y tags técnicos.
- Adaptación a normativas lingüísticas territoriales.
- Consistencia terminológica en software.

Las reglas lingüísticas detalladas se encuentran en: `References/translation-style-guide.md`

---

# FASE 1 — Identificación del par de idiomas

Antes de iniciar la traducción el agente debe identificar:

- **Idioma fuente**
- **Idioma destino**

Ejemplos:
- `es-ES` → `en-US`
- `es-ES` → `fr-FR`
- `es-ES` → `va-ES`
- `es-ES` → `ca-ES`

El idioma fuente suele ser el **idioma base del diccionario**.

---

# FASE 2 — Protección de la estructura técnica

Antes de traducir el agente debe identificar los elementos protegidos del archivo JSON.

Los siguientes elementos **nunca deben modificarse**:

## Claves JSON

Las claves del diccionario deben permanecer idénticas.

Ejemplo:

````Json
{
	"login.title": "Iniciar sesión"
}
````

Traducción correcta:
````Json
{
	"login.title": "Login"
}
````

Solo se traduce el valor.

## Variables de interpolación

Las variables dentro de `{{variable}}` deben preservarse exactamente.

Ejemplo:
````Json
{
	"welcome.message": "Bienvenido, {{user}}"
}
````

Traducción correcta:
````Json
{
	"welcome.message": "Welcome, {{user}}"
}
````

## Etiquetas HTML o pseudo-HTML

Etiquetas como:
- `<b> </b>`
- `<i> </i>`
- `<br>`

deben mantenerse intactas.

## Secuencias especiales

No modificar:
- Saltos de línea `\n`.
- Espacios en blanco significativos.
- Signos de puntuación.
- Cualquier otro elemento técnico que no sea texto visible.

---

# FASE 3 — Reglas específicas de i18next

El agente debe respetar la sintaxis utilizada por **i18next**.

## Sufijos de pluralización

Las claves con sufijos:
- `_one`
- `_other`
- `_zero`
- `_few`
- `_many`
- etc.

no deben modificarse.

Ejemplo:
````Json
{
	"item_count_one": "1 artículo",
	"item_count_other": "{{count}} artículos"
}
````

Traducción correcta:
````Json
{
	"item_count_one": "1 item",
	"item_count_other": "{{count}} items"
}
````

Solo se traduce el valor.

---

# FASE 4 — Aplicación de normativa lingüística

El agente debe aplicar la normativa lingüística correspondiente al idioma destino.

Las reglas completas se encuentran en: `References/translation-style-guide.md`

Prioridades:
- `va-ES` → normativa **AVL**
- `ca-ES` → normativa **IEC**
- `gl-ES` → normativa **RAG**
- `eu-ES` → **Euskara Batua**

Para otros idiomas se deben aplicar **estándares internacionales de localización (L10n)**.

---

# FASE 5 — Traducción contextual

El agente debe traducir únicamente **los valores del JSON**.

La traducción debe:
- Mantener el significado original.
- Adaptarse al contexto de software.
- Respetar variables e interpolaciones.
- Mantener consistencia terminológica.

Evitar variaciones innecesarias para términos repetidos.

Ejemplo:
````Json
{
	"save": "Guardar",
	"cancel": "Cancelar",
	"delete": "Eliminar"
}
````

Traducción correcta:
````Json
{
	"save": "Save",
	"cancel": "Cancel",
	"delete": "Delete"
}
````

---

# FASE 6 — Validación final del diccionario

Antes de finalizar la tarea el agente debe validar:

## Integridad estructural

- Las claves del JSON coinciden con el archivo original.
- No se han añadido ni eliminado claves.

## Protección técnica

- Las variables `{{variable}}` permanecen intactas.
- Las etiquetas HTML no han sido modificadas.

## Sintaxis JSON

El archivo debe ser **JSON válido**.

No debe contener:
- Comas finales.
- Errores de sintaxis.
- Comillas incorrectas.

---

# Activadores recomendados

## Traducción regional
> Traduce el diccionario de es-ES a va-ES aplicando las normas de la AVL.

## Traducción internacional
> Traduce las nuevas claves de es-ES a Inglés (en-GB) y Francés (fr-FR).

## Traducción incremental
> Traduce solo las nuevas claves añadidas en es-ES a va-ES, en-GB y fr-FR.

## Auditoría lingüística
> Audita el archivo ca-ES y corrige los demostrativos según la IEC.

---

# Recursos Oficiales de Referencia

El agente puede consultar los siguientes recursos cuando necesite documentación técnica adicional sobre el ecosistema i18next:

- **Documentación oficial i18next**: `References/i18next-official-docs.md`
- Sitio oficial: https://www.i18next.com/
- CLI oficial: https://github.com/i18next/i18next-cli

---

# Regla de prioridad de referencias

Si existe conflicto entre este archivo y los documentos en `References/`, las instrucciones de `SKILL.md` tienen prioridad.

Los archivos de `References/` contienen únicamente:
- Normativas lingüísticas.
- Guías de estilo.
- Reglas terminológicas.
- Documentación técnica oficial.
