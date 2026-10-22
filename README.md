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
| [gsudo](https://github.com/gerardog/gsudo) | `gerardog.gsudo` | `sudo` for Windows |
| [WinLibs (MinGW)](https://winlibs.com/) | `BrechtSanders.WinLibs.POSIX.UCRT` | C/C++ toolchain (`gcc`, `cmake`, …) |
| [NVM for Windows](https://github.com/coreybutler/nvm-windows) | `CoreyButler.NVMforWindows` | Node.js version manager |

Install them all in one go:

```powershell
winget install JanDeDobbeleer.OhMyPosh Neovim.Neovim ajeetdsouza.zoxide junegunn.fzf 7zip.7zip cURL.cURL jqlang.jq gerardog.gsudo BrechtSanders.WinLibs.POSIX.UCRT CoreyButler.NVMforWindows
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

## What's customized

- Theme based on craftzdog's `takuya.omp.json`, with a different segment color scheme and the old `z` module replaced by `zoxide`
- Aliases: `vim` → `nvim`, `g` → `git`, `ll` → `ls`, `grep` → `findstr`, `touch` → `ni`, `clear` → `cls`, `ifconfig` → `ipconfig`, `tig`/`less` → Git for Windows' bundled binaries
- PSReadLine in Emacs edit mode, history-based predictions, no bell, `Ctrl+d` deletes the character under the cursor
- PSFzf bound to `Ctrl+f` (file search) and `Ctrl+r` (reverse history search)
- A `which` function
- `$env:Path` additions for Oh My Posh and `~/.local/bin`

## License

[MIT](LICENSE)
