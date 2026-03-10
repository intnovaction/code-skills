# Reference: i18n Infrastructure & Refactoring Protocol

Este documento define la **especificación técnica completa** del sistema de internacionalización utilizado por la Skill **React-i18next-Engineer**.

El documento incluye:

- instalación de dependencias
- configuración runtime de i18next
- configuración de i18next-cli
- protocolo de refactorización de código
- ciclo de extracción de traducciones
- protocolo de auto-sanación (Auto-Healing)

Este archivo funciona como **documentación técnica de referencia** utilizada por el agente durante la configuración y mantenimiento del sistema de localización.

---

# 1. Infraestructura de Internacionalización

Esta sección describe la **configuración base de i18next** necesaria para habilitar internacionalización en aplicaciones React o Preact.

El agente debe inyectar rutas dinámicas en los placeholders:
- `{{SRC_PATH}}`
- `{{ROOT_PATH}}`
- `{{STATIC_PATH}}`
- `{{LOCALES_REL_PATH}}`
- `{{LOCALES_ARRAY}}`


Estas rutas se detectan automáticamente durante la **Fase de Descubrimiento del proyecto** definida en `SKILL.md`.

# 2. Plantillas de configuración dinámica

## Idioma Fuente (Base Locale)

El primer idioma definido en `LOCALES_ARRAY` se considera el **idioma fuente**.

Todas las claves nuevas generadas por `i18next-cli extract` se crearán inicialmente en ese idioma.

Ejemplo: `["es-ES","ca-ES","va-ES"]`.
Idioma base: `es-ES`.

Esto evita **bugs en sincronización**.

## Regla Crítica de Idempotencia

El agente nunca debe sobrescribir archivos de configuración existentes sin verificar previamente su contenido.

Si el archivo ya existe (`i18n.ts` o `i18next.config.ts`) el agente debe:

1. Analizar la configuración existente.
2. Aplicar solo las modificaciones necesarias.
3. Mantener cualquier configuración personalizada del proyecto.

# 3. Instalación de Dependencias

Los comandos deben ejecutarse en el directorio raíz del frontend donde exista el archivo: `package.json`.

El agente debe seleccionar una de las dos estrategias siguientes.

## Instalación con versiones validadas (Recomendado)

Utilizar cuando el proyecto requiere **estabilidad máxima**.

```powershell
npm install i18next@25.8.0 react-i18next i18next-http-backend@3.0.2 i18next-browser-languagedetector@8.2.0 --save
npm install i18next-cli@1.5.11 --save-dev
````

## Instalación de últimas versiones (Rolling Release)

Solo utilizar si el proyecto permite dependencias **bleeding-edge**.

```powershell
npm install i18next react-i18next i18next-http-backend i18next-browser-languagedetector --save
npm install i18next-cli --save-dev
````

Si aparecen errores de tipos TypeScript tras usar esta estrategia, el agente debe realizar **rollback automático a la instalación con versiones validadas**.

# 4. Configuración Runtime de i18next

Archivo: `{{SRC_PATH}}/i18n.ts`.

Este archivo inicializa **i18next** y lo integra con **React**.
```typescript
import i18n from 'i18next'
import { initReactI18next } from 'react-i18next'
import Backend from 'i18next-http-backend'

i18n
  .use(Backend)
  .use(initReactI18next)
  .init({
    lng: 'es-ES',
    fallbackLng: 'es-ES',
    supportedLngs: {{LOCALES_ARRAY}},
    load: 'currentOnly',
    ns: ['translation'],
    defaultNS: 'translation',
    debug: false,
    interpolation: {
      escapeValue: false
    },
    backend: {
      loadPath: `${process.env.PUBLIC_URL || ''}/{{LOCALES_REL_PATH}}/{{lng}}/{{ns}}.json`,
      parse: (data: string) => {
        const json = JSON.parse(data)
        return json.translation ?? json
      }
    }
  })

export default i18n;
````

## Registro del inicializador

El archivo debe importarse en el **entrypoint principal del frontend**.

Ejemplo:
```typescript
import "./i18n";
// O también
import i18n from './i18n';
````

Entrypoints comunes:
- `src/index.ts`
- `src/main.ts`
- `src/app.ts`
- `src/render.ts`

Si el archivo no se importa, **i18next no se inicializa en runtime**.

# 5. Configuración de i18next CLI

Archivo: `{{ROOT_PATH}}/i18next.config.ts`.

Define el comportamiento de **extracción automática de claves**.

```typescript
import { defineConfig } from 'i18next-cli'

export default defineConfig({
  locales: {{LOCALES_ARRAY}},
  extract: {
    input: ["{{SRC_PATH}}/**/*.{ts,tsx,js,jsx}"],
    output: "{{STATIC_PATH}}/locales/{{language}}/{{namespace}}.json",
    defaultNamespace: "translation",
    options: {
      keySeparator: ".",
      nsSeparator: ":",
      func: {
        list: ["t", "i18n.t"],
        extensions: [".js", ".jsx", ".ts", ".tsx"]
      },
      resource: {
        loadPath: "{{STATIC_PATH}}/locales/{{language}}/{{namespace}}.json",
        savePath: "{{STATIC_PATH}}/locales/{{language}}/{{namespace}}.json"
      }
    }
  }
});
````

## Configuración de emergencia

Si existen errores de tipado con **i18next-cli**, utilizar la versión simplificada.

```typescript
import { defineConfig } from 'i18next-cli'

export default defineConfig({
  locales: {{LOCALES_ARRAY}},
  extract: {
    input: ["{{SRC_PATH}}/**/*.{ts,tsx,js,jsx}"],
    output: "{{STATIC_PATH}}/locales/{{language}}/{{namespace}}.json",
    keySeparator: ".",
    nsSeparator: ":",
    functions: ["t", "i18n.t"]
  }
});
````

# 6. Refactorización de Código

Cuando el agente procese archivos dentro de `{{SRC_PATH}}` debe aplicar el siguiente flujo.

## Alcance de la Refactorización

Archivos que pueden modificarse:
- componentes React
- archivos `.tsx`
- archivos `.ts`
- archivos `.jsx`
- archivos `.js`

Archivos que nunca deben modificarse automáticamente:
- archivos de configuración de build (`vite.config`, `webpack.config`)
- `package-lock.json`
- `yarn.lock`
- `node_modules`
- carpetas `.git`

Esto reduce **errores semánticos del agente**.

## Análisis previo del archivo

Antes de modificar un archivo el agente debe:

1. Identificar si el archivo es un componente React.
2. Detectar si `useTranslation` ya está importado.
3. Detectar si `i18n.ts` ya está siendo utilizado.
4. Analizar si existen llamadas previas a `t()`.

Solo después debe aplicar cambios.

## Detección de texto traducible

El agente debe convertir en traducciones:
- textos visibles en la interfaz
- placeholders
- labels
- títulos
- botones
- mensajes de error
- tooltips

No deben traducirse:
- claves de API
- nombres de variables
- nombres de rutas
- identificadores técnicos

## Inyección de traducción en React

```Typescript
import { useTranslation } from 'react-i18next'

const { t } = useTranslation();
````

## Uso en archivos de lógica

```Typescript
import i18n from './i18n'

i18n.t("clave", "valor por defecto");
````

## Sustitución de texto hardcodeado

Ejemplo:
```Typescript
<h1>{t("home.title", "Bienvenido")}</h1>
````

También debe aplicarse a atributos:
```Typescript
placeholder={t("form.namePlaceholder", "Escribe tu nombre")}
````

Si el texto contiene interpolaciones dinámicas se debe usar: `t("clave", "valor {{variable}}" , { variable })`.

Ejemplo:
```Typescript
<p>{t("cart.items", "Hay {{count}} items", { count: items.length })}</p>
````

Ejemplo con múltiples interpolaciones:
```typescript
t(
  "order.createdMessage",
  "Pedido {{orderId}} creado para {{customerName}}",
  { orderId, customerName }
)
````

## Reutilización de claves existentes

Si una clave existente **representa exactamente el mismo significado**, el agente debe reutilizarla.

Ejemplo:

Ya existe:
```json
{
  "common.save": "Guardar"
}
````

Si aparece en otro componente el texto Guardar, el agente debe reutilizar:
```typescript
t("common.save")
````

No crear:
```typescript
t("userProfile.saveButton", "Guardar")
````

## Prevención de colisiones semánticas

Si una clave ya existe pero **su significado es distinto**, el agente debe crear una clave diferente.

Ejemplo incorrecto:
```json
{
  "user.delete": "Eliminar"
}
````
Y reutilizarla para: `Eliminar cuenta`

Correcto:
```typescript
t("user.deleteAccount", "Eliminar cuenta");
````

Las claves deben reflejar **intención semántica**, no solo el texto.

## Prevención de doble traducción

El agente nunca debe envolver expresiones que ya utilizan `t()`.

Ejemplo incorrecto:
````Typescript
t("home.title", t("home.title"))
````

Si un nodo ya contiene una llamada a `t()` debe dejarse sin modificar.

# 7. Generación de Claves

Las claves deben ser:
- Semánticas
- Jerárquicas
- En camelCase

**Formato recomendado:** `{{modulo}}.{{seccion}}.{{elemento}}`.

Ejemplo: Para `UserCard.tsx`, usar `userCard.header.title`.

Las claves nunca deben depender del texto traducido.
Las claves deben permanecer estables aunque cambie el contenido traducido.

# 8. Ciclo de Extracción de Traducciones

Después de refactorizar el código se deben ejecutar los comandos:
```Powershell
npx i18next-cli extract
npx i18next-cli sync
````

Acciones realizadas:
- Escaneo del código fuente.
- Generación de claves en el idioma base.
- Sincronización automática con otros idiomas.

# 9. Protocolo de Auto-Sanación

Error de importación: `TS2304` / `TS2307`.

Acciones:
- Verificar dependencias en `package.json`.
- Reinstalar dependencias si es necesario.
- Corregir imports incorrectos.

Error de scope: Si `t()` se usa fuera de React.

Acción:
- Usar `i18n.t()` en su lugar.

# 10. Gestión Automática de Diccionarios

Los diccionarios se almacenan en: `{{STATIC_PATH}}/locales/`.

La estructura se genera automáticamente mediante: `npx i18next-cli extract`.

# 11. Estructura de Proyecto Esperada

````
{{SRC_PATH}}/
   i18n.ts
{{ROOT_PATH}}/
   i18next.config.ts
{{STATIC_PATH}}/
   locales/
      es-ES/
         translation.json
      en-US/
         translation.json
````

# 12. Invariantes de agentes

Las siguientes reglas nunca deben violarse durante la ejecución de la Skill.

1. Nunca eliminar lógica existente del componente.
2. Nunca sobrescribir archivos de configuración sin analizar su contenido.
3. Nunca modificar hooks de React existentes.
4. Nunca eliminar comentarios del código original.
5. Nunca concatenar strings traducidos manualmente.

Si una operación viola alguna de estas reglas, el agente debe abortar la modificación y reportar el conflicto.

# 13. Criterios de Éxito

La integración se considera exitosa cuando se cumplen todas las condiciones siguientes:

1. El archivo `i18n.ts` existe y se inicializa correctamente.
2. El archivo `i18next.config.ts` existe en la raíz del proyecto frontend.
3. Los componentes React utilizan `useTranslation()` para textos visibles.
4. No existen strings hardcodeados visibles en UI.
5. Los diccionarios se han generado mediante `i18next-cli extract`.
6. Los archivos de traducción existen para todos los idiomas definidos en `LOCALES_ARRAY`.
7. La aplicación compila sin errores TypeScript.

Si alguna de estas condiciones falla, el agente debe continuar el proceso de corrección.