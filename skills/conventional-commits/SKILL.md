---
name: conventional-commits
description: Use whenever creating a git commit, writing a commit message, or suggesting one. Enforces Conventional Commits format (feat, fix, style, chore, etc.) with the description and body written in Spanish, and forbids any Claude/AI attribution or Co-Authored-By trailers.
---

# Conventional Commits

Aplica estas reglas cada vez que crees un commit, redactes un mensaje de commit o sugieras uno.

## Formato

```
<type>(<scope opcional>): <descripción corta en imperativo>

<cuerpo opcional: explica el POR QUÉ del cambio, no el qué>

<pie opcional: BREAKING CHANGE, referencias a issues>
```

- `scope` es opcional e indica la zona afectada: `feat(auth): ...`, `fix(api): ...`.
- El cuerpo va separado del título por una línea en blanco. El diff ya muestra *qué* cambió; el cuerpo explica *por qué*.

## Idioma

- **Escribe la descripción, el cuerpo y el pie en español**, aunque el código, el repo o la conversación estén en inglés.
- **El `type` va siempre en inglés** (`feat`, `fix`, `docs`...), igual que `BREAKING CHANGE`: son palabras clave de la especificación que usan las herramientas de changelog y versionado.
- El `scope` y los identificadores de código (nombres de clases, funciones, archivos) se escriben tal como aparecen en el código.
- Usa el imperativo en segunda persona: "agrega", "corrige", "elimina", "actualiza" — no "agregado", "agregando" ni "se agregó".

## Tipos permitidos

| Tipo       | Cuándo usarlo |
|------------|---------------|
| `feat`     | Nueva funcionalidad para el usuario. |
| `fix`      | Corrección de un bug. |
| `docs`     | Solo cambios de documentación. |
| `style`    | Formato, espacios, punto y coma; sin cambiar lógica. |
| `refactor` | Reestructura código sin cambiar su comportamiento ni corregir bugs. |
| `perf`     | Mejora de rendimiento. |
| `test`     | Añade o corrige tests. |
| `build`    | Sistema de build o dependencias (npm, NuGet, Maven...). |
| `ci`       | Configuración de integración continua (GitHub Actions, pipelines). |
| `chore`    | Tareas de mantenimiento que no tocan código de producción ni tests. |
| `revert`   | Revierte un commit anterior. |

## Reglas

- Título de **máximo 72 caracteres**.
- Todo en **minúsculas** (salvo nombres propios o identificadores de código).
- **Sin punto final** en el título.
- **Modo imperativo**: "agrega", "corrige", "elimina" — no "agregado", "corrigiendo", "se eliminó".
- **Un commit = un cambio lógico.** Si los cambios mezclan varios propósitos (p. ej. un fix y un refactor), propón dividirlos en commits separados antes de commitear.
- **Cambios que rompen compatibilidad**: añade `!` después del tipo/scope y un pie `BREAKING CHANGE: <explicación>`.

## Prohibido

- **NUNCA** añadir `Co-Authored-By: Claude` ni ninguna variante (`Co-authored-by: Claude ...`, `Co-Authored-By: ... <noreply@anthropic.com>`, etc.).
- **NUNCA** añadir `Generated with Claude Code`, `🤖 Generated with ...`, `Claude-Session:` ni ninguna mención a Claude, Anthropic o IA en el título, cuerpo o pie.
- El commit debe quedar **solo con la autoría del usuario** (su `git config user.name` / `user.email`). No uses `--author` ni trailers para atribuir el cambio a otra identidad.
- Antes de ejecutar `git commit`, revisa el mensaje completo y elimina cualquier línea de atribución a IA que se haya colado.

## Ejemplos

✅ **Correcto**

```
feat(cart): agrega campo de código de descuento en el checkout
```

```
fix(api): maneja customerId nulo al consultar pedidos

El endpoint devolvía 500 cuando un pedido antiguo no tenía cliente
asociado. Ahora responde 404, que es lo que espera el frontend.
```

```
refactor(auth)!: reemplaza cookies de sesión por jwt

Las cookies de sesión impedían escalar horizontalmente sin sticky
sessions.

BREAKING CHANGE: los clientes deben enviar el header Authorization
en lugar de la cookie SESSIONID.
```

❌ **Incorrecto**

```
Se corrigió el bug del login.
```
Sin tipo, sin imperativo, con mayúscula inicial y punto final.

```
feat: add export to excel

Co-Authored-By: Claude <noreply@anthropic.com>
🤖 Generated with Claude Code
```
Descripción en inglés e incluye atribución a IA. Correcto: `feat: agrega exportación a excel`, sin esas dos líneas.
