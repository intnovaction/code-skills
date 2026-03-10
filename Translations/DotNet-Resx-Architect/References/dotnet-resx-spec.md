# Reference dotnet-resx-spec.md

Este documento define el estándar técnico y lingüístico oficial para la localización backend en proyectos .NET mediante archivos de recursos (.resx).

Este estándar es normativo.
No describe comportamiento del agente.
Define cómo debe estructurarse el sistema.

El sistema es completamente independiente de cualquier infraestructura frontend o i18n externa.

---

# 1. Arquitectura del Sistema

El modelo adoptado es:
- Recurso maestro único
- Strongly Typed Resources
- Resolución por cultura en tiempo de ejecución
- Acceso estático desde código C# y Razor

Archivo maestro obligatorio: `/Resources/Traducciones.resx`
Este archivo genera: `Traducciones.Designer.cs`

⚠ Solo el archivo maestro debe generar clase.
Los archivos de cultura secundaria NO deben generar `.Designer.cs`.

---

# 2. Formato Técnico RESX

Los archivos `.resx` deben respetar el esquema XML estándar de .NET.

Estructura válida:
```xml
<data name="Modulo_Submodulo_Elemento" xml:space="preserve">
  <value>Texto traducido según normativa</value>
</data>
````

Ejemplo:
```xml
<data name="Login_RememberMe" xml:space="preserve">
    <value>Recordarme</value>
</data>
````

---

# 3. Convención de Naming

Formato obligatorio: `Modulo_Submodulo_Elemento_Accion`

Reglas:
- PascalCase por segmento
- Separador único `_`
- Descriptivo y orientado a búsqueda
- Evitar abreviaturas ambiguas
- Evitar numeraciones genéricas (Msg1, Text1, etc.)

El naming debe reflejar intención semántica, no posición visual.

---

# 4. Estructura de Culturas

Ejemplos válidos:
- Traducciones.resx (es-ES base)
- Traducciones.ca-ES.resx
- Traducciones.va-ES-valencia.resx
- Traducciones.gl-ES.resx
- Traducciones.eu-ES.resx
- Traducciones.en-US.resx

Reglas:
- Todas las culturas deben contener exactamente las mismas claves.
- No deben eliminarse claves en culturas secundarias.
- El fallback de cultura lo gestiona automáticamente el ResourceManager de .NET según CurrentUICulture.

---

# 5. Implementación en Código

## 5.1 Razor (.cshtml)

Debe existir en `_ViewImports.cshtml`:
```csharp
@using Proyecto.Namespace.Resources
````

Uso:
````csharp
@Traducciones.Login_RememberMe
````

## 5.2 Backend (Controladores / Servicios)

````csharp
using Proyecto.Namespace.Resources;

return new AltaResponse()
{
    ErrorMsg = $"{Traducciones.Cart_Register_Error}: {ex.Message}"
};
````

El acceso siempre es estático mediante la clase generada.

---

# 6. Normativas Lingüísticas Oficiales

Las siguientes normativas son de cumplimiento obligatorio.

## 6.1 Valenciano (va-ES) - Estándar AVL
Referencia oficial: Acadèmia Valenciana de la Llengua.
- **Demostrativos:** este, esta, estos, estes
- **Prohibido:** aquest/aquesta
- **Léxico preferente:** servici, vacacions, mitat
- **Morfología verbal:** -is, -ixes, -ix
- **Acentuación valenciana:** anglés, francés, café

## 6.2 Catalán (ca-ES) - Estándar IEC
Referencia oficial: Institut d'Estudis Catalans.
- **Demostrativos:** aquest, aquesta, aquests, aquestes.
- **Léxico:** Servei, vacances, meitat.
- **Registro:** Formal administrativo estándar

## 6.3 Gallego (gl-ES) - Estándar RAG
Referencia oficial: Real Academia Galega.
- Cumplimiento ortográfico vigente
- Evitar castellanismos
- Preferir formas genuinas (ej. grazas)

## 6.4 Euskera (eu-ES) - Estándar Euskaltzaindia
Referencia oficial: Euskaltzaindia (Real Academia de la Lengua Vasca).
- Utilizar Euskara Batua
- Mantener coherencia administrativa

## 6.5 Traducciones Dinámicas (Otros Idiomas)
Para idiomas no regionales (Inglés, Francés, Alemán, etc.):
- Tono profesional
- Neutralidad cultural
- Adaptación gramatical correcta
- Reordenación permitida si el idioma lo requiere
- Respeto absoluto de placeholders

---

# 7. Placeholders y Tokens Dinámicos

Los siguientes patrones deben preservarse intactos:
- `{0}`, `{1}`
- `{0:N2}`, `{0:dd/MM/yyyy}`
- `{UserName}`
- `{OrderId}`
- Cualquier patrón `{Propiedad}`

Reglas:
1. No traducir
2. No renumerar
3. No eliminar formatos
4. No alterar capitalización

La integridad de placeholders es obligatoria para evitar errores de ejecución.
