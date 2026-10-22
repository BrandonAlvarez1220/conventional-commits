# git-conventions

Plugin de [Claude Code](https://code.claude.com/docs) que incluye la skill **`conventional-commits`**.

## Qué hace

Cada vez que Claude crea un commit, redacta un mensaje de commit o sugiere uno, la skill le indica que:

- Use el formato [Conventional Commits](https://www.conventionalcommits.org/): `<type>(<scope>): <descripción>`, con tipos `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore` y `revert`.
- Respete reglas de estilo: título ≤ 72 caracteres, minúsculas, imperativo, sin punto final, un cambio lógico por commit y `!` + `BREAKING CHANGE:` para cambios incompatibles.
- **No añada** `Co-Authored-By: Claude`, `Generated with Claude Code`, `Claude-Session:` ni ninguna otra mención a IA: el commit queda solo con tu autoría.

## Por qué existe

- **Historial legible y automatizable**: Conventional Commits permite generar changelogs, calcular versiones semánticas (`feat` → minor, `fix` → patch, `!` → major) y filtrar el historial por tipo de cambio.
- **Autoría limpia**: por defecto Claude Code añade trailers de coautoría y enlaces a la sesión. En muchos equipos o repos personales se prefiere que el historial refleje solo a la persona responsable del cambio.

## Instalación

Dentro de Claude Code:

```
/plugin marketplace add BrandonAlvarez1220/conventional-commits
/plugin install git-conventions@git-conventions
```

### Alternativa desde el shell

```bash
claude plugin marketplace add BrandonAlvarez1220/conventional-commits
claude plugin install git-conventions@git-conventions
```

## Cómo probarla

1. Abre Claude Code en cualquier repo git y haz un cambio pequeño.
2. Pide: *"haz commit de estos cambios"*.
3. Revisa el resultado con `git log -1 --format=full`:
   - El título debe seguir `tipo(scope): descripción`.
   - No debe haber líneas `Co-Authored-By`, `Generated with Claude Code` ni `Claude-Session`.
   - `Author` y `Commit` deben ser tu `user.name` / `user.email`.
4. Opcional: ejecuta `/skills` (o revisa `/plugin`) para confirmar que `conventional-commits` está cargada.

Para validar el plugin localmente tras modificarlo:

```bash
claude plugin validate .
```

## Nota importante: la skill no es una garantía dura

Una skill es una **instrucción para el modelo**, no una regla que el harness imponga. Claude la sigue de forma muy consistente, pero la atribución que añade Claude Code por defecto proviene de su propia configuración, y el modelo puede recibir indicaciones contradictorias de esa configuración. Para eliminar la atribución de forma determinista, compleméntala con este ajuste en tu `settings.json` (`~/.claude/settings.json` para todos tus proyectos, o `.claude/settings.json` en un repo):

```json
{ "attribution": { "commit": "", "pr": "" } }
```

- `attribution.commit`: texto que se añade a los commits (cadena vacía = nada).
- `attribution.pr`: texto que se añade a las descripciones de pull requests.

En sesiones cloud (Claude Code en la web) o con Remote Control existe además la clave `attribution.sessionUrl`, que controla el trailer `Claude-Session:` con el enlace a la sesión. Verifica su valor y comportamiento exactos en la [documentación oficial de settings](https://code.claude.com/docs/en/settings), ya que estas claves pueden cambiar entre versiones.

También puedes añadir un hook `commit-msg` de git que rechace mensajes con esas líneas si necesitas una garantía total.

## Estructura

```
.claude-plugin/
  plugin.json        # manifiesto del plugin
  marketplace.json   # catálogo del marketplace (apunta a ./)
skills/
  conventional-commits/
    SKILL.md         # la skill
```
