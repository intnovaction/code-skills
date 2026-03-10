# Copilot Skills — IntNovAction

Repositorio central de **Agent Skills** de ([IntNovAction](https://www.intnovaction.com/)). Una colección de habilidades especializadas para asistentes de IA que siguen el **estándar abierto Agent Skills** ([agentskills.io](https://agentskills.io)).

---

## ¿Qué son las Agent Skills?

Las **Agent Skills** son recursos portables basados en el sistema de archivos que proporcionan a los agentes de IA experiencia de dominio específica. Definidas en archivos `SKILL.md` con metadatos YAML, las skills se cargan bajo demanda y eliminan la necesidad de repetir el mismo contexto entre conversaciones.

> El formato fue desarrollado originalmente por Anthropic, publicado como estándar abierto y adoptado por una comunidad creciente de productos de agentes de IA.

**Principios clave:**

- **Reutilizables**: Una skill, múltiples agentes y proyectos.
- **Portables**: Funcionan en cualquier agente compatible con el estándar abierto.
- **Versionables**: Viven en git junto al código, como cualquier otro artefacto de ingeniería.
- **Progresivas**: El agente carga solo lo necesario en cada momento.

### Descubrimiento progresivo (cómo las lee el agente)

| Nivel | Qué carga | Cuándo |
|-------|-----------|--------|
| **Discovery** | `name` + `description` del frontmatter YAML | Siempre, al iniciar sesión |
| **Instructions** | Contenido completo del `SKILL.md` | Cuando detecta que la skill es relevante para la tarea |
| **Resources** | Archivos en `References/` | Cuando necesita contexto técnico adicional |

Este modelo garantiza eficiencia: el agente solo incorpora conocimiento cuando es pertinente a la intención del usuario.

---

## Agentes compatibles

El estándar Agent Skills es soportado por más de 30 herramientas de desarrollo. Las principales:

| Agente | Ruta de instalación (proyecto) | Invocación |
|--------|-------------------------------|------------|
| **GitHub Copilot** | `.copilot/skills/` | Lenguaje natural, `@workspace` |
| **Claude Code** | `.claude/skills/` | Lenguaje natural, `/nombre-skill` |
| **Cursor** | `.cursor/skills/` | `@nombre-skill` en Chat |
| **Gemini CLI** | `.gemini/skills/` | Lenguaje natural |
| **OpenAI Codex** | `.codex/skills/` | Lenguaje natural |
| **Junie (JetBrains)** | `.agent/skills/` | Automático en Agent Mode |
| **OpenHands** | `.agent/skills/` | Automático |
| **Roo Code** | `.roo/skills/` | Lenguaje natural |
| **Goose** | `.goose/skills/` | Lenguaje natural |
| **Amp** | `.amp/skills/` | Lenguaje natural |

> Para instalación global (usuario): usar el equivalente en `~/` (home directory).

---

## Estructura del repositorio

```
/[ámbito]/
 └── /[NombreSkill]/
      ├── SKILL.md           # Skill: frontmatter YAML + instrucciones del agente
      └── /References/       # Especificaciones técnicas, guías de estilo y contexto
```

- **`/[ámbito]/`**: Área de aplicación (ej. `Translations`, `Testing`, `Backend`).
- **`/[NombreSkill]/`**: Directorio dedicado a la skill.
- **`SKILL.md`**: El corazón de la skill. Contiene el frontmatter de identificación y todas las instrucciones del agente.
- **`/References/`**: Documentación técnica, especificaciones o snippets que sirven de contexto adicional.

### Formato obligatorio de SKILL.md

Cada `SKILL.md` debe envolver el frontmatter YAML en un bloque `<details>` para que GitHub no lo renderice como contenido visible:

```markdown
<details>
<summary>Skill metadata</summary>

---
name: nombre-skill-en-minusculas
description: Precise description of when and why to use this skill (max. 1024 chars)
---

</details>
```

**Reglas del estándar:**

| Campo | Regla |
|-------|-------|
| `name` | Solo minúsculas y guiones (`-`). Máximo 64 caracteres. |
| `description` | El agente usa este campo para decidir si activar la skill. Debe escribirse en **inglés** para maximizar la compatibilidad con agentes de distintos idiomas. Máximo 1024 caracteres. |

---

## Catálogo de Skills

### Translations

Skills especializadas en localización e internacionalización de software.

| Skill | Descripción |
|-------|-------------|
| **[dotnet-resx-architect](Translations/DotNet-Resx-Architect/SKILL.md)** | Arquitecto de localización backend para ASP.NET Core MVC mediante archivos `.resx` fuertemente tipados. Configura, valida y mantiene el sistema completo de forma autónoma. |
| **[json-i18n-translator](Translations/Json-i18n-Translator/SKILL.md)** | Especialista en traducción de diccionarios JSON para sistemas i18next. Preserva la integridad técnica (claves, variables, HTML) y aplica normativas lingüísticas oficiales. |
| **[react-i18next-engineer](Translations/React-i18next-Engineer/SKILL.md)** | Senior Frontend Engineer para i18n en aplicaciones React/Preact con i18next. Configura infraestructura, refactoriza texto hardcodeado y mantiene el ciclo de vida de traducciones. |

---

## Cómo usar una Skill

### Opción A — Instalación en proyecto (recomendado para equipos)

Copia la carpeta de la skill al directorio correspondiente de tu agente dentro del proyecto:

```bash
# Ejemplo para GitHub Copilot
cp -r Translations/DotNet-Resx-Architect .copilot/skills/

# Ejemplo para Claude Code
cp -r Translations/React-i18next-Engineer .claude/skills/
```

### Opción B — Instalación global (usuario)

Copia al directorio `skills` en el home del agente (`~/.copilot/skills/`, `~/.claude/skills/`, etc.).

### Invocación en el chat

Una vez instalada, hay dos formas de activar una skill:

**Invocación explícita** (cuando sabes exactamente qué skill usar):
```
/nombre-skill
```
Ejemplo: `/react-i18next-engineer`

**Invocación por lenguaje natural** (el agente selecciona la skill por su `description`):
```
"Localiza este módulo usando el sistema strongly typed RESX"

"Traduce el diccionario de es-ES a va-ES aplicando la normativa AVL"

"Configura i18n en este proyecto React para los idiomas es-ES, en-US y fr-FR"
```

> La invocación explícita por `/nombre-skill` está disponible en Claude Code. En otros agentes, consulta la columna "Invocación" de la tabla de agentes compatibles.

---

## Cómo añadir una nueva Skill

1. Crear la carpeta en el ámbito correspondiente: `/[Ámbito]/[NombreSkill]/`
2. Crear `SKILL.md` con frontmatter YAML válido (`name` + `description`).
3. Añadir archivos de referencia en `/References/` si la skill necesita documentación técnica adicional.
4. Registrar la skill en el catálogo de este README.

---

## Recursos

- Especificación oficial del estándar: [agentskills.io/specification](https://agentskills.io/specification)
- Repositorio de skills de referencia: [github.com/MicrosoftDocs/Agent-Skills](https://github.com/MicrosoftDocs/Agent-Skills)
- Skills de ejemplo Anthropic: [github.com/anthropics/skills](https://github.com/anthropics/skills)

---

*Desarrollado por el equipo de IntNovAction · Estándar abierto: [agentskills.io](https://agentskills.io)*
