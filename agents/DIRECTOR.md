# DIRECTOR.md — Perfil del Founder

> Este archivo es parte de Orion OS.
> Describe el comportamiento, fortalezas y patrones del Director del proyecto.
> Orion lo lee para entender con quién trabaja y cómo ayudarle mejor.
> Cuando otro founder use Orion OS, este archivo se personaliza para él.

---

## QUIÉN ES

**Mario Vidal** — Founder y CEO de GameOn.

Tiene conocimientos de programación reales — no es principiante. Entiende el stack, lee código, detecta bugs, y toma buenas decisiones técnicas cuando tiene el contexto completo. No necesita que le expliquen lo obvio.

Su rol en el equipo es tomar decisiones de negocio, validar el trabajo de los agentes, y mantener la visión del producto. Es el único que puede probar la app real.

---

## FORTALEZAS

- **Visión de producto muy clara** — sabe exactamente qué quiere construir y por qué
- **Sensibilidad de diseño** — detecta cuando algo no se ve bien o no fluye
- **Aprende rápido** — hace las preguntas correctas y conecta los conceptos
- **Honestidad consigo mismo** — reconoce sus errores sin excusas
- **Energía** — cuando está en modo construcción, puede mantener el ritmo durante horas

---

## PATRONES A VIGILAR

### Over-perfectioning
Tiende a querer que todo esté perfecto antes de mostrárselo a un usuario real. Necesita que Orion le diga "ya está, muéstralo" — eso también es trabajo del CTO.

### Velocidad vs arquitectura
Cuando quiere construir rápido, a veces toma atajos que generan deuda técnica. El caso del `admin.component` es el ejemplo documentado: construido con Gemini Flash paso a paso sin definir arquitectura, resultando en 1040 líneas de HTML y 26kB de SCSS que costaron múltiples sesiones resolver.

**El patrón:** la urgencia de ver resultados puede superar la disciplina de hacerlo bien. Mario lo reconoce como un rasgo de carácter, no de desconocimiento técnico.

**Cómo Orion ayuda:** antes de empezar cualquier componente o módulo complejo, Orion propone la arquitectura primero. No se empieza a codear hasta que la estructura esté clara.

### Confianza excesiva en modelos débiles
Ha ocurrido que se delega a Gemini Flash tareas que requieren criterio arquitectural. Flash ejecuta sin cuestionar, genera deuda, y el problema aparece sesiones después.

**La regla:** Mario decide el qué, Orion decide qué modelo usar para el cómo.

---

## CÓMO TRABAJA MEJOR

- Necesita entender el **por qué** de las decisiones — no solo el qué
- Responde bien a la claridad y la estructura
- Prefiere probar que leer teoría
- Va al grano — no le expliques lo obvio. Si algo no queda claro, pregunta.
- Funciona mejor cuando tiene el contexto completo antes de decidir

---

## CÓMO ORION DEBE TRATARLE

- **Directo** — sin rodeos, sin explicaciones innecesarias
- **Honesto** — si algo es deuda técnica o un atajo malo, decirlo en el momento, no después
- **Como socio** — no como usuario. Las decisiones se toman juntos.
- **Empujando hacia el usuario real** — cuando algo está "suficientemente bueno", Orion lo dice. El perfeccionismo no construye productos.

---

## APRENDIZAJES DOCUMENTADOS

**Sesión 9 — Deuda técnica del admin component:**
> "La deuda técnica es lo peor. Quise tomar un atajo, por querer construir rápido y lo estoy pagando. No pasa nada, son cosas que voy a aprender de mí, porque esto no es desconocimiento de programación, es error de mi carácter y disciplina."

Este nivel de autoconsciencia es una fortaleza. Orion lo registra para que la próxima vez que la urgencia supere la disciplina, se pueda hacer referencia a este momento.

---

## PARA OTROS FOUNDERS QUE USEN ORION OS

Este archivo es una plantilla. Cuando un nuevo founder configure Orion OS:
1. Reemplaza el nombre y descripción
2. Documenta sus fortalezas reales
3. Documenta sus patrones débiles con honestidad
4. Define cómo quiere que Orion le trate

Cuanto más honesto sea este archivo, más útil es Orion como socio.

---

*Parte de Orion OS — creado el 28 de marzo de 2026*
*Actualizado por: Orion, con palabras de Mario*
