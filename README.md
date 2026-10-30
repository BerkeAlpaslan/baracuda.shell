# ⚡ Baracuda Shell

My personal PowerShell + [Oh My Posh](https://ohmyposh.dev/) terminal setup for Windows. It started from [Takuya Matsuyama's (craftzdog)](https://github.com/craftzdog) devaslife Oh My Posh tutorial — [watch it here](https://www.youtube.com/watch?v=5-aK2_WwrmM) — (the theme is based on his `takuya.omp.json`), then got heavily customized on top: a different prompt color scheme, `zoxide` instead of the old `z` module, aliases, PSReadLine/PSFzf setup, and more.

## Requirements

| Tool | winget package ID | What it's for |
| ---- | ------------------ | -------------- |
| [Oh My Posh](https://ohmyposh.dev/) | `JanDeDobbeleer.OhMyPosh` | The prompt engine this repo configures |
| [Neovim](https://neovim.io/) | `Neovim.Neovim` | Editor (`vim` is aliased to `nvim`) |
| [zoxide](https://github.com/ajeetdsouza/zoxide) | `ajeetdsouza.zoxide` | A smarter `cd` |
| [fzf](https://github.com/junegunn/fzf) | `junegunn.fzf` | Fuzzy finder, used through PSFzf |
| [7-Zip](https://www.7-zip.org/) | `7zip.7zip` | Archive tool |
| [curl](https://curl.se/) | `cURL.cURL` | HTTP client |
| [jq](https://jqlang.org/) | `jqlang.jq` | JSON processor |
| [ripgrep](https://github.com/BurntSushi/ripgrep) | `BurntSushi.ripgrep` | `grep` is aliased to `rg` |
| [gsudo](https://github.com/gerardog/gsudo) | `gerardog.gsudo` | `sudo` for Windows |
| [WinLibs (MinGW)](https://winlibs.com/) | `BrechtSanders.WinLibs.POSIX.UCRT` | C/C++ toolchain (`gcc`, `cmake`, …) |
| [NVM for Windows](https://github.com/coreybutler/nvm-windows) | `CoreyButler.NVMforWindows` | Node.js version manager |

Install them all in one go:

```powershell
winget install JanDeDobbeleer.OhMyPosh Neovim.Neovim ajeetdsouza.zoxide junegunn.fzf 7zip.7zip cURL.cURL jqlang.jq BurntSushi.ripgrep gerardog.gsudo BrechtSanders.WinLibs.POSIX.UCRT CoreyButler.NVMforWindows
```

Also needed:

- [Git for Windows](https://git-scm.com/download/win) — provides the `less` and `tig` binaries this profile aliases to
- [PowerShell 7+](https://github.com/PowerShell/PowerShell)
- A few PowerShell modules:
  ```powershell
  Install-Module -Name Terminal-Icons -Repository PSGallery
  Install-Module -Name PSFzf -Repository PSGallery
  ```

## Files

| File | What it is | Where it goes |
| ---- | ----------- | --------------- |
| `baracuda.omp.json` | The Oh My Posh prompt theme | `%USERPROFILE%\.config\powershell\` |
| `baracuda-claude.omp.json` | Oh My Posh theme for Claude Code's status line — model, effort/thinking indicators, repo & PR info, language stack, 5h/weekly/context usage, reset countdowns | Anywhere — path is referenced from Claude Code's `settings.json` (see [below](#claude-code-status-line-optional)); kept next to `baracuda.omp.json` here |
| `user_profile.ps1` | Aliases, PSReadLine/PSFzf setup, prompt init — the actual custom config | `%USERPROFILE%\.config\powershell\` |
| `Microsoft.PowerShell_profile.ps1` | PowerShell's own profile entrypoint: sets `$env:Path` and dot-sources `user_profile.ps1` | Your `$PROFILE` path (usually `Documents\PowerShell\`) |

## Installation

1. Watch [the tutorial video](https://www.youtube.com/watch?v=5-aK2_WwrmM) this setup is based on.
2. Install the [requirements](#requirements) above.
3. Clone this repo, e.g. to `~\Documents\oh-my-posh`.
4. Copy `baracuda.omp.json` and `user_profile.ps1` into `%USERPROFILE%\.config\powershell\`.
5. Copy `Microsoft.PowerShell_profile.ps1` to your `$PROFILE` path. Check where that is with:
   ```powershell
   $PROFILE
   ```
   If the file doesn't exist yet, opening it directly (e.g. `nvim $PROFILE`) will error out. Create it first, then retry:
   ```powershell
   New-Item -Path $PROFILE -Type File -Force
   ```
6. Restart PowerShell.

> [!NOTE]
> `baracuda.omp.json` belongs in `.config\powershell\`, next to `user_profile.ps1` — **not** directly under `.config\`. `user_profile.ps1` resolves the theme path relative to its own folder (`Join-Path (Get-ScriptDirectory) 'baracuda.omp.json'`), so both files have to live side by side for the prompt to load.

## Claude Code status line (optional)

`baracuda-claude.omp.json` renders Oh My Posh's [`claude` segment](https://ohmyposh.dev/docs/segments/agents/claude) as [Claude Code](https://code.claude.com/)'s status line instead of a shell prompt. It's a multi-line layout:

- Model name, reasoning effort, extended-thinking indicator, repo/PR info, git branch, working directory, and whichever language/tool segments apply (dotnet, Java, Go, Rust, Lua, Python, Node, Angular, Docker, AWS, Azure), plus the time
- 5-hour / weekly / context-window usage percentages, color-coded from green to red
- When each rate-limit window resets, with a countdown

1. Put the theme file somewhere permanent, e.g. next to `baracuda.omp.json` in `%USERPROFILE%\.config\powershell\`.
2. Add a `statusLine` entry to Claude Code's `settings.json` (global: `%USERPROFILE%\.claude\settings.json`, or a project's `.claude\settings.json`):
   ```json
   "statusLine": {
     "type": "command",
     "command": "oh-my-posh claude --config 'C:/Users/<YourUsername>/.config/powershell/baracuda-claude.omp.json'",
     "padding": 0
   }
   ```
3. Start a new Claude Code session — the status line picks it up immediately, no restart of the shell needed.

This is independent of the PowerShell prompt setup above: Claude Code itself invokes `oh-my-posh claude`, piping its own session data (model, cost, token usage, etc.) in on stdin, so `oh-my-posh` only needs to be on `PATH` (already covered by the [requirements](#requirements)).

## What's customized

- Theme based on craftzdog's `takuya.omp.json`, with a different segment color scheme and the old `z` module replaced by `zoxide`
- Both themes auto-update from Oh My Posh's CDN every 24h (`upgrade` block) and use the current `options`/`cache` segment schema instead of the older `properties`/`cache_duration` keys
- Extra right-aligned segments on `baracuda.omp.json`: Docker, Go, Rust, Lua (alongside the existing Node, Python, Java, .NET, Angular, AWS, Azure segments), plus a reworked battery/RAM color gradient and the `exit` segment replaced by `status`
- Aliases: `vim` → `nvim`, `g` → `git`, `ll` → `ls`, `grep` → `rg`, `sudo` → `gsudo`, `touch` → `ni`, `clear` → `cls`, `ifconfig` → `ipconfig`, `tig`/`less` → Git for Windows' bundled binaries
- PSReadLine in Emacs edit mode, history-based predictions, no bell, `Ctrl+d` deletes the character under the cursor
- PSFzf bound to `Ctrl+f` (file search) and `Ctrl+r` (reverse history search)
- A `which` function
- `$env:Path` additions for Oh My Posh and `~/.local/bin`

## License

[MIT](LICENSE)
