# Reference: i18next Official Documentation

Este documento centraliza los recursos oficiales de i18next relevantes para la skill `json-i18n-translator`.

El agente debe consultar estas fuentes cuando necesite resolver dudas sobre sintaxis de claves, reglas de pluralización, formato de variables o estructura de archivos de traducción.

---

## Documentación oficial

- **Sitio oficial**: https://www.i18next.com/
- **CLI oficial (i18next-cli)**: https://github.com/i18next/i18next-cli

---

## Conceptos clave del estándar i18next

### Interpolación de variables

i18next usa dobles llaves para variables dinámicas:

```json
{
  "welcome": "Bienvenido, {{name}}",
  "itemCount": "Tienes {{count}} artículos"
}
```

Las variables `{{variable}}` **nunca deben traducirse ni modificarse**.

### Pluralización

i18next utiliza sufijos estándar basados en las reglas CLDR (Unicode Common Locale Data Repository):

| Sufijo | Uso |
|--------|-----|
| `_zero` | Cero elementos (algunos idiomas) |
| `_one` | Singular |
| `_two` | Dual (árabe, etc.) |
| `_few` | Pocos (eslavo, árabe, etc.) |
| `_many` | Muchos (eslavo, árabe, etc.) |
| `_other` | Plural general (por defecto) |

Ejemplo completo:
```json
{
  "item_one": "{{count}} artículo",
  "item_other": "{{count}} artículos"
}
```

El sufijo forma parte de la clave y **nunca debe modificarse**.

### Contexto

i18next permite variantes contextuales mediante el sufijo `_context`:

```json
{
  "friend_male": "Mi amigo",
  "friend_female": "Mi amiga"
}
```

Los sufijos contextuales **nunca deben modificarse**.

### Namespaces

Los archivos de traducción se organizan por namespaces. El namespace por defecto es `translation`.

Ejemplo de estructura:
```
locales/
  es-ES/
    translation.json
    validation.json
  en-US/
    translation.json
    validation.json
```

### Anidamiento de claves

i18next soporta claves anidadas mediante separadores:

```json
{
  "user": {
    "login": {
      "title": "Iniciar sesión",
      "button": "Entrar"
    }
  }
}
```

Equivalente plano (con separador `.`):
```json
{
  "user.login.title": "Iniciar sesión",
  "user.login.button": "Entrar"
}
```

La estructura del archivo **debe preservarse exactamente** durante la traducción.

---

## i18next-cli

Herramienta de línea de comandos para extracción y sincronización de claves.

Repositorio: https://github.com/i18next/i18next-cli

### Comandos principales

```bash
# Extraer claves del código fuente
npx i18next-cli extract

# Sincronizar idiomas secundarios con el idioma base
npx i18next-cli sync
```

### Configuración (`i18next.config.ts`)

```typescript
import { defineConfig } from 'i18next-cli'

export default defineConfig({
  locales: ['es-ES', 'en-US'],
  extract: {
    input: ['src/**/*.{ts,tsx,js,jsx}'],
    output: 'public/locales/{{language}}/{{namespace}}.json'
  }
})
```

---

## Restricciones críticas para traducción de JSON

El agente **nunca debe** modificar:

1. Las claves del JSON (`"user.login.title"`)
2. Las variables de interpolación (`{{user}}`, `{{count}}`, `{{date}}`)
3. Los sufijos de pluralización (`_one`, `_other`, `_zero`, `_few`, `_many`)
4. Los sufijos de contexto (`_male`, `_female`)
5. Las etiquetas HTML embebidas (`<b>`, `</b>`, `<br>`)
6. Los saltos de línea (`\n`)

Solo se traducen los **valores** de las claves.
