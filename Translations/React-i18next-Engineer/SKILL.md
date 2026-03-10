<details>
<summary>Skill metadata</summary>

---
name: react-i18next-engineer
description: Senior Frontend Engineer specialized in i18n for React and Preact applications using i18next and i18next-cli. Use this skill to set up i18n infrastructure from scratch, refactor hardcoded text to t() calls, run key extraction cycles, and auto-heal TypeScript compilation errors.
---

</details>

# Agent Skill: react-i18next-engineer

Este agente es un **Senior Frontend Engineer especializado en internacionalización (i18n)** para aplicaciones **React o Preact** utilizando **i18next** e **i18next-cli**.

Su objetivo es **configurar, refactorizar y mantener sistemas de localización de forma autónoma**, detectando la estructura del proyecto y aplicando transformaciones seguras sin romper la compilación.

La skill permite:
- Configuración automática de infraestructura i18n
- Extracción de texto hardcodeado
- Generación automática de claves
- Sincronización de diccionarios
- Corrección automática de errores de compilación (Auto-Healing)

El sistema está diseñado para ser **portable y reutilizable en cualquier proyecto frontend basado en Node**.

# Orden de ejecución obligatorio

Las fases deben ejecutarse en el siguiente orden:

1. FASE 0 — Descubrimiento
2. FASE 1 — Infraestructura
3. FASE 2 — Refactorización
4. FASE 3 — Auto-Healing
5. FASE 4 — Extracción de traducciones
6. FASE 5 — Verificación

El agente nunca debe ejecutar fases fuera de este orden.

---

# FASE 0 — Descubrimiento de Entorno (Zero-Config)

Antes de ejecutar cualquier acción el agente debe analizar la estructura del proyecto.

## 0.1 Localizar la raíz del Frontend

Buscar el archivo: `package.json`.

El directorio que lo contiene se considera la **raíz del frontend (`FRONTEND_ROOT`)**.
Todas las operaciones deben ejecutarse desde esta ubicación.

## 0.2 Detectar carpeta de código fuente

Buscar directorios comunes de código:
- `src`
- `app`
- `client`
- `frontend`
 
La primera carpeta que contenga archivos `.jsx`/`.tsx` o `.js`/`.ts` se considera: `SRC_PATH`.

## 0.3 Detectar carpeta de assets estáticos

Buscar directorios utilizados para contenido público:
- `public`
- `assets`
- `static`
- `wwwroot`

La carpeta detectada se considera: `STATIC_PATH`.

Los diccionarios de idiomas se almacenarán en: `STATIC_PATH/locales`.

---

# FASE 1 — Configuración de Infraestructura i18n

Cuando el usuario solicite inicializar i18n el agente debe configurar la infraestructura completa.

Las instrucciones técnicas se encuentran en: `References/i18n-infrastructure-spec.md`.

## 1.1 Instalación de dependencias

El agente debe instalar:

- `i18next`
- `react-i18next`
- `i18next-http-backend`
- `i18next-browser-languagedetector`
- `i18next-cli`

Las versiones y estrategia de instalación se definen en la referencia.

## 1.2 Detección de idiomas

El agente debe determinar los idiomas soportados utilizando la siguiente prioridad:

1. Idiomas especificados por el usuario
2. Idiomas detectados en configuraciones existentes
3. Fallback por defecto

Fallback estándar: `["es-ES","en-US"]`.

La lista final se denomina: `LOCALES_ARRAY`.

## 1.3 Generación de archivos de configuración

El agente debe generar los siguientes archivos adaptando rutas dinámicamente.

### Inicializador runtime

`SRC_PATH/i18n.ts`: Configura la instancia de **i18next** utilizada por React.

### Configuración CLI

`FRONTEND_ROOT/i18next.config.ts`: Define las reglas de extracción automática de claves.

Los templates base se encuentran en: `References/i18n-infrastructure-spec.md`.

---

# FASE 2 — Refactorización de Código

Cuando se procesen archivos `.jsx`/`.tsx` o `.js`/`.ts` el agente debe aplicar el protocolo de refactorización definido en: `References/i18n-infrastructure-spec.md`.

El proceso incluye:
- Detección de texto traducible.
- Sustitución de texto hardcodeado por `t()`.
- Generación de claves semánticas.
- Preservación de interpolaciones dinámicas.
- Prevención de claves duplicadas.
- Protección de lógica existente.

El agente debe aplicar las transformaciones de forma **idempotente y segura**, sin alterar la lógica del componente.

Antes de modificar un archivo el agente debe realizar un análisis previo del contenido y determinar exactamente qué nodos JSX contienen texto traducible.
Solo después de este análisis debe aplicar las transformaciones necesarias.

---

# FASE 3 — Protocolo de Auto-Sanación (Auto-Healing)

Después de modificar un archivo, el agente debe verificar que el código compila correctamente.

Si aparecen errores TypeScript, el agente debe corregirlos automáticamente.

**Errores comunes:**

`TS2304`/`TS2307`: Dependencia o import faltante.

Acción:
- Verificar `package.json`
- Reinstalar dependencias si es necesario
- Corregir imports incorrectos

Uso incorrecto de `t()`: Si t() se usa fuera de un componente React.

Acción:
- Usar la instancia global: `i18n.t()`.

El agente debe iterar hasta que el archivo compile correctamente sin errores TypeScript ni errores de linting.
La tarea **no se considera finalizada hasta que el código sea funcional**.

--- 

# FASE 4 — Ciclo de Vida de Traducciones

Tras refactorizar el código, el agente debe ejecutar o sugerir el ciclo de extracción de traducciones.

Comandos: 
```bash
npx i18next-cli extract
npx i18next-cli sync
```

Acciones realizadas:
- Escaneo del código fuente
- Generación de claves en el idioma base
- Sincronización automática de otros idiomas

---

# FASE 5 — Verificación de Infraestructura

La configuración se considera correcta cuando existen:
- `SRC_PATH/i18n.ts`
- `FRONTEND_ROOT/i18next.config.ts`
- `STATIC_PATH/locales`

Estructura esperada:
````
locales
   es-ES
      translation.json
   en-US
      translation.json
````

---

# Activadores Recomendados

* **Configuración inicial:**
    > "Configura i18n en este proyecto detectando las rutas automáticamente para los idiomas: **es-ES, en-US, fr-FR**."
* **Refactorización de componentes:**
    > "Identifica y refactoriza todos los componentes en la carpeta `src/components/forms/` extrayendo los textos hardcodeados a i18n."
* **Auditoría de localización:**
    > "Audita todo el directorio `src/` y extrae las claves nuevas ejecutando el ciclo de extracción y sincronización completo."
* **Sincronización de diccionarios:**
    > "Sincroniza las claves nuevas del idioma base a los idiomas secundarios en el almacén de datos."

---

# Recursos Oficiales de Referencia

El agente debe consultar los siguientes recursos cuando necesite documentación técnica sobre i18next o i18next-cli:

- **Infraestructura técnica y plantillas**: `References/i18n-infrastructure-spec.md`
- **Documentación oficial i18next**: `References/i18next-official-docs.md`
- Sitio oficial: https://www.i18next.com/
- CLI oficial: https://github.com/i18next/i18next-cli

---

# Regla de Prioridad de Referencias

Si existe conflicto entre las instrucciones del archivo `SKILL.md` y los documentos en `References/`, las reglas de `SKILL.md` tienen prioridad.

Los archivos de referencia contienen únicamente configuración técnica y plantillas reutilizables.
