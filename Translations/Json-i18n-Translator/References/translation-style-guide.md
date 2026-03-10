# Reference: Translation Expert Style Guide

Este documento define las **normativas lingüísticas y terminológicas** utilizadas por la skill: `json-i18n-translator`.

Su objetivo es garantizar:
- Coherencia terminológica.
- Cumplimiento de normativas lingüísticas oficiales.
- Calidad en traducciones de software.
- Consistencia entre diccionarios i18n.

Este documento se utiliza durante la **Fase de Traducción Lingüística** del agente.

---

# 1. Normativas Lingüísticas Oficiales (Alta Prioridad)

Cuando el idioma destino sea una lengua co-oficial de España, el agente debe aplicar estrictamente su normativa académica.

## 1.1 Valenciano (va-ES)

**Normativa oficial:** Acadèmia Valenciana de la Llengua (AVL).

### Demostrativos obligatorios

**Debe utilizarse:** este, esta, estos, estas.

**Prohibido usar formas catalanas:** aquest, aquesta, aquests, aquestes.

### Léxico preferente

| Forma correcta | Forma a evitar |
|---|---|
| servici | servei |
| vacacions | vacances |
| mitat | meitat |

Estas formas corresponden al **registro administrativo valenciano**.

### Morfología verbal

**Preferir formas valencianas:** nosaltres, vosaltres, etc.

**Con terminaciones típicas:** -is, -ixes, -ix (ej. nosaltres som, vosaltres sou).

### Acentuación característica

**Respetar la tonicidad valenciana:** ej. anglés, francés, café (con acento en la última sílaba).

## 1.2 Catalán (ca-ES)

**Normativa oficial:** Institut d'Estudis Catalans (IEC).

### Demostrativos

**Usar:** aquest, aquesta, aquests, aquestes.

### Léxico estándar

| Correcto |
|---|
| servei |
| vacances |
| meitat |

### Registro lingüístico

Usar:
- Registro formal.
- Estilo administrativo
- Estándar institucional de la Generalitat.

Evitar coloquialismos.

## 1.3 Gallego (gl-ES)

Normativa oficial: Real Academia Galega (RAG).

### Reglas generales

- Aplicar ortografía oficial.
- Evitar castellanismos.
- Priorizar léxico gallego genuino.

Ejemplo: usar *grazas* en lugar de *gracias*.

## 1.4 Euskera (eu-ES)

Normativa oficial: Euskaltzaindia (Real Academia de la Lengua Vasca).

### Variante obligatoria

**Usar:** Euskara Batua (Euskera unificado).

El euskera unificado garantiza comprensión en:
- Administración.
- Educación.
- Software público.

Evitar dialectos regionales.

# 2. Reglas Terminológicas para Software

Las traducciones deben adaptarse al **contexto de software**.

El agente debe mantener consistencia terminológica en toda la aplicación.

### Terminología recomendada

| Español | Inglés |
|---|---|
| usuario | user |
| configuración | settings |
| guardar | save |
| cancelar | cancel |
| iniciar sesión | sign in |
| cerrar sesión | sign out |
| contraseña | password |

Evitar traducciones literales incorrectas.

Ejemplo incorrecto: "guardar" traducido como "keep" en lugar de "save".

# 3. Adaptación Gramatical de Variables

Las variables deben mantenerse intactas: {{count}}, {{user}}, {{date}}, etc.

Pero la gramática del idioma destino debe adaptarse.

Ejemplo: en inglés, si el texto original es "Tienes {{count}} mensajes", la traducción correcta sería: "You have {{count}} message(s)" para manejar el plural correctamente.

# 4. Reglas para Plurales i18next

Los sufijos de pluralización deben respetarse:
- `_zero`
- `_one`
- `_few`
- `_many`
- `_other`

El agente **nunca debe modificar la clave**.

Ejemplo: 
```json
{
	"item_count_one": "1 artículo",
	"item_count_other": "{{count}} artículos"
}
```

Debe traducirse como:
```json
{
	"item_count_one": "1 item",
	"item_count_other": "{{count}} items"
}
```

Solo traducir el valor.

# 5. Traducciones Dinámicas (Otros Idiomas)

Para idiomas no cubiertos por normativas españolas:

Ejemplos:
- Inglés
- Francés
- Alemán
- Italiano
- Portugués

El agente debe aplicar principios internacionales de **localización (L10n)**.

### Reglas

Usar lenguaje:
- Profesional
- Neutral
- Accesible
- Claro

Evitar:
- Traducciones literales
- Estructuras sintácticas españolas
- Términos ambiguos

# 6. Restricciones Técnicas Críticas

Durante la traducción el agente debe proteger los siguientes elementos.

### Claves JSON

Nunca traducir: `user.login.title`.

### Variables

Nunca modificar: `{{user}}`, `{{date}}`, `{{count}}`, etc.

### HTML

Mantener etiquetas: `<b>`, `</b>`, `<i>`, `</i>`, saltos de línea `\n`, etc.

### Secuencias

Respetar signos de puntuación, espacios y formato del texto original. 

# 7. Consistencia Dialectal

No mezclar normativas dentro del mismo idioma.

Ejemplo incorrecto: usar "este" (AVL) y "aquest" (IEC) en el mismo archivo de traducción para valenciano.

Cada archivo debe seguir una **única normativa**.
