# Reference: i18next Official Documentation

Este documento centraliza los recursos oficiales de i18next e i18next-cli relevantes para la skill `react-i18next-engineer`.

El agente debe consultar estas fuentes cuando necesite resolver dudas sobre configuración, APIs, hooks de React, extracción de claves o sincronización de diccionarios.

---

## Documentación oficial

- **Sitio oficial i18next**: https://www.i18next.com/
- **CLI oficial (i18next-cli)**: https://github.com/i18next/i18next-cli

---

## APIs principales de i18next en React

### Hook `useTranslation`

Uso estándar en componentes funcionales React:

```typescript
import { useTranslation } from 'react-i18next'

const MyComponent = () => {
  const { t, i18n } = useTranslation()

  return <h1>{t('home.title', 'Bienvenido')}</h1>
}
```

- `t(key, defaultValue)` — traduce una clave
- `t(key, defaultValue, options)` — traduce con interpolación o pluralización
- `i18n.changeLanguage(lang)` — cambia el idioma activo

### `Trans` component

Para traducir JSX con elementos embebidos:

```tsx
import { Trans } from 'react-i18next'

<Trans i18nKey="userGuide.terms">
  Lee los <a href="/terms">términos</a> antes de continuar.
</Trans>
```

### Acceso fuera de componentes React

Cuando `t()` se usa en lógica de negocio (servicios, utilidades):

```typescript
import i18n from './i18n'

const message = i18n.t('errors.notFound', 'Recurso no encontrado')
```

---

## Interpolación y pluralización

### Interpolación dinámica

```typescript
t('cart.items', 'Hay {{count}} artículos', { count: items.length })
t('welcome', 'Hola {{name}}, tienes {{count}} mensajes', { name, count })
```

### Pluralización

i18next gestiona plurales automáticamente con el parámetro `count`:

```json
{
  "item_one": "{{count}} artículo",
  "item_other": "{{count}} artículos"
}
```

```typescript
t('item', { count: 1 })  // → "1 artículo"
t('item', { count: 5 })  // → "5 artículos"
```

---

## i18next-cli — Extracción y sincronización

Repositorio: https://github.com/i18next/i18next-cli

### Comandos del ciclo de vida

```bash
# 1. Extraer claves del código fuente al idioma base
npx i18next-cli extract

# 2. Sincronizar claves nuevas a idiomas secundarios
npx i18next-cli sync
```

### Configuración (`i18next.config.ts`)

```typescript
import { defineConfig } from 'i18next-cli'

export default defineConfig({
  locales: ['es-ES', 'en-US', 'fr-FR'],
  extract: {
    input: ['src/**/*.{ts,tsx,js,jsx}'],
    output: 'public/locales/{{language}}/{{namespace}}.json',
    defaultNamespace: 'translation',
    options: {
      keySeparator: '.',
      nsSeparator: ':',
      func: {
        list: ['t', 'i18n.t'],
        extensions: ['.js', '.jsx', '.ts', '.tsx']
      }
    }
  }
})
```

### Versiones validadas (instalación estable)

```bash
npm install i18next@25.8.0 react-i18next i18next-http-backend@3.0.2 i18next-browser-languagedetector@8.2.0 --save
npm install i18next-cli@1.5.11 --save-dev
```

---

## Estructura de archivos esperada

```
public/locales/
  es-ES/
    translation.json    ← idioma base (generado por i18next-cli extract)
  en-US/
    translation.json    ← idioma secundario (sincronizado por i18next-cli sync)
  fr-FR/
    translation.json
src/
  i18n.ts               ← inicializador de i18next
i18next.config.ts       ← configuración del CLI
```

---

## Inicialización runtime (`i18n.ts`)

```typescript
import i18n from 'i18next'
import { initReactI18next } from 'react-i18next'
import Backend from 'i18next-http-backend'
import LanguageDetector from 'i18next-browser-languagedetector'

i18n
  .use(Backend)
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    lng: 'es-ES',
    fallbackLng: 'es-ES',
    supportedLngs: ['es-ES', 'en-US'],
    load: 'currentOnly',
    ns: ['translation'],
    defaultNS: 'translation',
    debug: false,
    interpolation: { escapeValue: false },
    backend: {
      loadPath: '/locales/{{lng}}/{{ns}}.json'
    }
  })

export default i18n
```

---

## Errores comunes y soluciones

| Error | Causa | Solución |
|-------|-------|----------|
| `TS2304: Cannot find name 't'` | `useTranslation` no importado | Añadir `import { useTranslation } from 'react-i18next'` |
| `TS2307: Cannot find module 'i18next'` | Paquete no instalado | `npm install i18next react-i18next` |
| `t() called outside component` | Uso de hook fuera de React | Usar `i18n.t()` con la instancia global |
| Claves no detectadas por CLI | Concatenación de strings | Usar siempre claves literales en `t()` |
| Diccionarios no cargados en runtime | `i18n.ts` no importado en entrypoint | Añadir `import './i18n'` en `main.ts`/`index.ts` |
