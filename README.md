# git-conventions

Plugin de [Claude Code](https://code.claude.com/docs) que incluye la skill **`conventional-commits`**.

## Qué hace

Cada vez que Claude crea un commit, redacta un mensaje de commit o sugiere uno, la skill le indica que:

- Use el formato [Conventional Commits](https://www.conventionalcommits.org/): `<type>(<scope>): <descripción>`, con tipos `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore` y `revert`.
- Escriba la descripción y el cuerpo **en español** (el tipo sigue en inglés, porque es una palabra clave de la especificación): `feat(api): agrega endpoint de reportes`.
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

## Actualizaciones

Claude Code guarda una copia del plugin en caché y solo descarga una nueva cuando cambia el campo `version` de `.claude-plugin/plugin.json`. Si se hace push sin subir la versión, nadie recibe el cambio.

**Para quien lo mantiene:** en cada cambio de la skill, sube `version` (`1.1.0` → `1.2.0`) y haz push a `main`.

**Para quien lo instaló:** la actualización automática viene desactivada en marketplaces de terceros. Hay dos formas de recibir la nueva versión:

- **Automática (recomendado):** en Claude Code, `/plugin` → **Marketplaces** → `git-conventions` → **Enable auto-update**. Se actualiza solo en segundo plano.
- **Manual:**
  ```
  /plugin marketplace update git-conventions
  ```
  o desde el shell:
  ```bash
  claude plugin marketplace update git-conventions
  claude plugin update git-conventions@git-conventions
  ```

Después, reinicia Claude Code para que cargue la nueva versión.

## Uso con OpenCode

[OpenCode](https://opencode.ai) también lee skills en formato `SKILL.md`, pero no instala plugins de Claude Code, así que se instala solo la skill:

**macOS / Linux**
```bash
mkdir -p ~/.config/opencode/skills/conventional-commits
curl -fsSL https://raw.githubusercontent.com/BrandonAlvarez1220/conventional-commits/main/skills/conventional-commits/SKILL.md \
  -o ~/.config/opencode/skills/conventional-commits/SKILL.md
```

**Windows (PowerShell)**
```powershell
$dir = "$HOME\.config\opencode\skills\conventional-commits"
New-Item -ItemType Directory -Force -Path $dir | Out-Null
Invoke-WebRequest https://raw.githubusercontent.com/BrandonAlvarez1220/conventional-commits/main/skills/conventional-commits/SKILL.md -OutFile "$dir\SKILL.md"
```

- Para un solo proyecto, usa `.opencode/skills/conventional-commits/SKILL.md` dentro del repo en lugar de la ruta global.
- Para actualizar, vuelve a ejecutar el mismo comando (descarga la última versión de `main`).
- OpenCode también busca en `~/.claude/skills/`. Si usas ambas herramientas, no copies la skill ahí además de instalar el plugin, o Claude Code la cargará dos veces.

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
