<details>
<summary>Skill metadata</summary>

---
name: dotnet-resx-architect
description: Architect for ASP.NET Core MVC backend localization using strongly typed RESX files. Use this skill to configure, audit, refactor or maintain the complete .NET localization system with Strongly Typed Resources, multi-culture support and compile-time safety.
---

</details>

# Agent Skill: dotnet-resx-architect (Strongly Typed RESX)

Este agente es un arquitecto experto en localización backend para ASP.NET Core MVC mediante archivos de recursos (.resx) fuertemente tipados.

Su objetivo es mantener un sistema:
- Autónomo
- Centralizado
- Seguro
- Determinista
- Fuerte en tiempo de compilación (Strongly Typed Resources)

Permite acceso estático desde C# y Razor mediante la clase generada `Traducciones`.
Este sistema es completamente independiente del frontend.

---

# FASE 0 — Protocolo de Diagnóstico Obligatorio

Antes de modificar cualquier archivo, el agente debe ejecutar esta validación estructural.

## 0.1 Localización del Proyecto

1. Identificar el archivo `.csproj` principal.
2. Determinar la raíz del backend.
3. Verificar existencia de la carpeta `/Resources`.
4. Si no existe:
   - Solicitar confirmación antes de crearla.
   - Proponer estructura estándar.

Nunca crear archivos de recursos fuera del backend.

## 0.2 Validación del Recurso Maestro

Confirmar existencia de:
- `Resources/Traducciones.resx`
- `Resources/Traducciones.Designer.cs`

Validar propiedades críticas:
- **Build Action:** `Embedded Resource`
- **Custom Tool:** `ResXFileCodeGenerator`
- **Access Modifier:** `Public`

Si alguna propiedad no es correcta:
- Corregir configuración.
- Forzar regeneración del Designer.

Si el Designer no se regenera:
- Indicar posible problema en Custom Tool.

## 0.3 Validación de Namespace

1. Detectar namespace raíz del proyecto.
2. Confirmar:

En `_ViewImports.cshtml`:
```csharp
@using Proyecto.Namespace.Resources
````

En backend:
````csharp
using Proyecto.Namespace.Resources;
````

Si falta → agregarlo.

## 0.4 Estrategia de Localización

Detectar si el proyecto usa:

- Recurso compartido global (modelo recomendado)
- Estructura jerárquica por áreas

Si el proyecto no define estrategia explícita, aplicar el modelo definido en `References/dotnet-resx-spec.md`.

Nunca mezclar estrategias sin confirmación.

---

# FASE 1 — Gestión de Infraestructura RESX

## 1.1 Recurso Maestro

Toda nueva clave se crea primero en: `Traducciones.resx` (cultura base: es-ES)

Nunca crear claves directamente en culturas secundarias.

## 1.2 Gestión de Culturas

Archivos secundarios válidos:
- Traducciones.va-ES-valencia.resx
- Traducciones.ca-ES.resx
- Traducciones.gl-ES.resx
- Traducciones.eu-ES.resx
- Traducciones.en-US.resx
- etc.

Reglas:
- Solo el maestro tiene `.Designer.cs`
- Las culturas secundarias NO deben generar clase
- Mantener mismo nombre exacto de clave
- No depender del fallback cultural para suplir claves ausentes

## 1.3 Propagación Obligatoria

Cuando se crea una clave:
1. Añadir al maestro.
2. Replicar en todos los `.resx` existentes.
3. Si no hay traducción:
	- Dejar valor vacío.
	- Nunca omitir la clave.

Antes de finalizar:
- Verificar que todas las culturas contienen la misma lista de claves.

## 1.4 Validación Anti-Duplicados

Antes de crear una clave:
- Buscar coincidencias semánticas.
- Evitar duplicar mensajes existentes.
- Priorizar reutilización de claves comunes.

---

# FASE 2 — Implementación y Auto-Healing

## 2.1 Razor (.cshtml)

Obligatorio:
- Sustituir texto plano por `@Traducciones.Clave_Semantica`
- No usar ViewBag para textos
- No usar strings inline
- Validar `@using Namespace.Resources`

Ejemplo correcto:
```csharp
<label>@Traducciones.Login_RememberMe</label>
````

## 2.2 Backend (.cs)

Obligatorio:
- Sustituir strings hardcodeados
- Usar acceso estático
- Verificar using

Ejemplo correcto:
```csharp
return BadRequest(Traducciones.Cart_Register_Error);
````

Nunca usar:
```csharp
return BadRequest("Error al registrar");
````

### 2.3 Auto-Healing de Compilación

Si aparece error `CS0103`:

- Verificar clave en maestro.
- Confirmar regeneración de Designer.
- Validar Access Modifier = Public.
- Confirmar using correcto.
- Verificar que el namespace coincide.

Si falla alguno → corregir antes de continuar.

---

# FASE 3 — Estándar de Naming

## 3.1 Regla Obligatoria

Formato: `Modulo_Submodulo_Elemento_Accion` 

Ejemplos válidos:
- Login_RememberMe
- Cart_Register_Error
- Common_Save_Success
- User_Profile_Update_Error

## 3.2 Reglas de Naming

- PascalCase por segmento
- Separador único `_`
- Descriptivo
- Orientado a búsqueda

## 3.3 Prohibido

- Msg1
- Label1
- TextError
- Key_01
- Abreviaturas ambiguas

---

# FASE 4 — Protección de Placeholders (Crítico)

Los *placeholders* son tokens dinámicos que se reemplazan en tiempo de ejecución.  
**Nunca deben traducirse, modificarse, renumerarse ni eliminarse.**

Elementos protegidos:
- `{0}`, `{1}`
- `{0:N2}`, `{0:dd/MM/yyyy}`
- `{UserName}`
- `{OrderId}`
- Cualquier patrón `{Propiedad}`

Antes de aceptar traducción:
1. Extraer placeholders del texto base.
2. Extraer placeholders del texto traducido.
3. Comparar.
4. Si no coinciden exactamente → bloquear salida.

---

# FASE 5 — Normativa Lingüística (Alta Prioridad)

Aplicar estrictamente:
- Valenciano (AVL)
- Catalán (IEC)
- Gallego (RAG)
- Euskera Batua
- Profesional neutro para otros idiomas

Seguir siempre: `References/dotnet-resx-spec.md`

Nunca mezclar normativas.

---

# FASE 6 — Traducciones Dinámicas y Pluralización

Nunca usar patrón: 
````csharp
"Tienes {0} mensaje(s)"
````

Usar separación de claves:
- Items_Count_Zero
- Items_Count_One
- Items_Count_Many

Si el idioma lo requiere:
- Permitir reordenación gramatical.
- Mantener placeholders intactos.

---

# FASE 7 — Aislamiento Total

Este sistema:
- No consulta JSON frontend
- No sincroniza con i18next
- No reutiliza claves del front
- No comparte naming automáticamente
- Es completamente autónomo

Cualquier integración cross-layer requiere confirmación explícita.

---

# Activadores Recomendados

Localización completa:
> "Localiza este módulo usando el sistema strongly typed RESX"

Auditoría:
> "Audita este controlador y elimina texto hardcodeado"

Traducción regional:
> "Traduce es-ES a va-ES siguiendo normativa AVL"

Migración:
> "Convierte este controlador legacy a sistema strongly typed"

---

# Criterios de Calidad

Una ejecución correcta debe:
- No romper compilación
- No generar claves duplicadas
- Mantener coherencia semántica
- Respetar placeholders
- Mantener simetría entre culturas
- No alterar configuración del proyecto
- Generar código limpio y mantenible

---

# FASE 8 — CHECKLIST FINAL AUTOMÁTICO (Obligatorio)

Antes de dar por finalizada cualquier tarea de localización, el agente debe ejecutar esta validación completa.

## Infraestructura

[ ] Existe `Traducciones.resx`  
[ ] Existe `Traducciones.Designer.cs`  
[ ] Build Action = Embedded Resource  
[ ] Custom Tool = ResXFileCodeGenerator  
[ ] Access Modifier = Public  

## Consistencia de Claves

[ ] Todas las nuevas claves están en el maestro  
[ ] Todas las culturas contienen exactamente las mismas claves  
[ ] No existen claves duplicadas  
[ ] No se han creado claves innecesarias  

## Placeholders

[ ] Los placeholders del maestro y las traducciones coinciden exactamente  
[ ] No se han traducido nombres internos  
[ ] No se ha alterado numeración  
[ ] No se han eliminado formatos (`:N2`, `:dd/MM/yyyy`, etc.)  

## Código

[ ] No quedan textos hardcodeados en Razor  
[ ] No quedan strings hardcodeados en backend  
[ ] El `using Namespace.Resources` es correcto  
[ ] No se rompe compilación (CS0103, CS0117, etc.)  

## Arquitectura

[ ] No se ha mezclado con frontend  
[ ] No se han modificado configuraciones no relacionadas  
[ ] Se mantiene estrategia SharedResource (si aplica)  


Si algún punto falla: Corregir antes de finalizar la respuesta.

El agente no puede finalizar si algún punto no está validado.
