# Hooks CLI

O Hooks CLI recebe webhooks no localhost e gerencia recursos do [Hooks](https://hooks.arttecs.com).

## Instalação

macOS e Linux, em amd64 ou arm64, na sua conta e sem sudo:

```sh
curl -fsSL https://github.com/arttecs/hooks-releases/releases/latest/download/install.sh | sh
```

Windows, em amd64 ou arm64, com PowerShell 5.1 ou pwsh:

```powershell
irm https://github.com/arttecs/hooks-releases/releases/latest/download/install.ps1 | iex
```

O destino padrão é `~/.local/bin` no macOS/Linux ou
`%LOCALAPPDATA%\Programs\hooks` no Windows. `HOOKS_VERSION` escolhe uma versão
SemVer com `v`; sem ela, usa Latest. `HOOKS_INSTALL_DIR` escolhe uma pasta
absoluta e protegida, fora de diretórios temporários, de build ou cache.
Defina as variáveis no processo que executa o script. Exemplos, substituindo
`vX.Y.Z` por uma versão publicada:

```sh
curl -fsSL https://github.com/arttecs/hooks-releases/releases/latest/download/install.sh | HOOKS_VERSION=vX.Y.Z sh
curl -fsSL https://github.com/arttecs/hooks-releases/releases/latest/download/install.sh | HOOKS_INSTALL_DIR="$HOME/.local/bin" sh
```

```powershell
$env:HOOKS_VERSION = 'vX.Y.Z'
$env:HOOKS_INSTALL_DIR = Join-Path $env:LOCALAPPDATA 'Programs\hooks'
irm https://github.com/arttecs/hooks-releases/releases/latest/download/install.ps1 | iex
```

No macOS/Linux, adicione ao shell a linha de PATH indicada pelo instalador.
No Windows, ele acrescenta a pasta ao PATH do usuário; abra um novo terminal.
Se reinstalar com serviço instalado, siga a indicação `hooks service install`.
Se o Windows informar que o arquivo está em uso, feche o serviço de login ou
o daemon do hooks: rode `hooks service uninstall` pelo executável instalado ou
pare o processo hooks que mantém o arquivo aberto. Reinstale e depois rode
`hooks service install` para restaurar o serviço.

### Download manual

Baixe o pacote do sistema (`darwin`, `linux` ou `windows`) e arquitetura
(`amd64` ou `arm64`) e o `SHA256SUMS` da
[mesma release](https://github.com/arttecs/hooks-releases/releases).
Calcule o SHA-256 do pacote:

- macOS: `shasum -a 256 ARQUIVO`.
- Linux: `sha256sum ARQUIVO`.
- PowerShell: `Get-FileHash -Algorithm SHA256 ARQUIVO`.

Substitua `ARQUIVO` pelo pacote baixado. Compare o resultado com a entrada de
mesmo nome em `SHA256SUMS` antes de extrair. Coloque `hooks` com permissão de
execução, ou `hooks.exe` e `hooks-background.exe` juntos, numa pasta protegida
no PATH. No macOS, se o arquivo baixado pelo navegador estiver em quarentena,
rode `xattr -d com.apple.quarantine hooks` na pasta do executável extraído.
A primeira instalação confia em HTTPS e no checksum da mesma origem.

## Usar e atualizar

```sh
hooks auth login
hooks tunnels add web --port 3000
hooks update --help
hooks update check
hooks update apply
hooks update status
```

`hooks update` reúne os comandos de atualização com assinatura Ed25519
verificada por chaves compiladas. Com processo em segundo plano ativo, use
`hooks update apply --restart` para autorizar a interrupção e retomar os túneis.
`hooks update auto enable` permite patches estáveis da mesma major/minor;
`hooks update auto disable` desliga a política. Se um candidato pendente retornar
`update_busy` ou `update_replay` após um novo manifesto, desabilite a política
para abandoná-lo, confira o estado e tente a atualização novamente.

## Desinstalação

Antes de mover ou apagar o executável instalado, rode por ele:

```sh
hooks service uninstall
hooks auth logout --profile NAME
```

Repita o logout para cada perfil. Ele remove a credencial local, sem revogar o
token no servidor. Apague `hooks` ou os dois `.exe` e os artefatos dessa
instalação: `.hooks-install-owner.json`, `.hooks-maintenance.lock`,
`.hooks-use.lock`, `.hooks-previous/` e `.hooks-update-*/`.
Remova configuração e estado:

- macOS: `~/Library/Application Support/hooks/`.
- Linux: `${XDG_CONFIG_HOME:-$HOME/.config}/hooks/` e, se usado,
  `$XDG_RUNTIME_DIR/hooks-<32hex>`.
- Windows: `%APPDATA%\hooks\` e `%LOCALAPPDATA%\hooks\<32hex>\runtime`.

Com configuração alternativa, passe `--config-file CAMINHO` nos comandos e
remova apenas os arquivos do CLI na pasta correspondente. `<32hex>` identifica
a configuração, não é texto literal. Retire a linha de PATH do shell ou a
entrada da pasta no PATH do usuário do Windows.

Este repositório distribui binários e instaladores. O código-fonte não é público.
