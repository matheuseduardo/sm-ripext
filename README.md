# sm-ripext (fork)

> Read this in [English](#english) below.

## Português

Este repositório é um **fork** da extensão [REST in Pawn (sm-ripext)](https://forums.alliedmods.net/showthread.php?t=298024) para SourceMod, criada originalmente por Erik Minekus ("Tsunami"). A extensão adiciona natives de HTTP e JSON para plugins SourcePawn.

### Objetivo do fork

O upstream original não estava mais compilando corretamente no CI (GitHub Actions), especificamente:

1. **Build do Windows quebrada**: o passo de instalação do AMBuild falhava no Windows com um erro de `PicklingError` / `PermissionError [WinError 5]`. Isso acontecia porque o `setup.py` do AMBuild dispara um processo via `multiprocessing` para detectar o `distutils`, e quando o `pip` instala o pacote, o `setuptools` executa esse `setup.py` via `exec()` a partir de uma string — nesse contexto, o processo filho criado no Windows não conseguia reimportar `__main__.detect_distutils`, quebrando a instalação. A correção foi trocar `pip install` por `python setup.py install`, executado diretamente no diretório do AMBuild, fazendo com que o `setup.py` rode como o módulo `__main__` de verdade.
2. **Compatibilidade com versões mais novas do SourceMod**: o pipeline de CI ainda buildava contra `1.12-dev`. O header `sm_stringhashmap.h` foi renomeado para `sm_hashmap.h` em versões mais recentes do SourceMod (a partir da 1.13). O CI e o código-fonte (`extension.h`) foram atualizados para compilar contra o SourceMod `1.13.0.7441`, mantendo o mesmo tipo `StringHashMap<std::string>`.

Com essas correções, a extensão volta a compilar (Linux e Windows) contra uma versão mais recente do SourceMod, e o pipeline de release volta a gerar os pacotes `.zip` corretamente para as duas plataformas.

### O que muda em relação ao upstream

- `.github/workflows/ci.yml`: instalação do AMBuild via `python setup.py install` (em vez da action `BSFishy/pip-action`, que dependia do fluxo de build isolado do pip); matriz de build agora usa SourceMod `1.13.0.7441`.
- `extension.h`: `#include <sm_stringhashmap.h>` → `#include <sm_hashmap.h>`.
- `smsdk_config.h`: versão da extensão atualizada para `1.4.0`, refletindo a nova compatibilidade com SourceMod 1.13.

Nenhuma lógica de negócio (natives de HTTP/JSON) foi alterada — as mudanças são exclusivamente de build e compatibilidade.

### Créditos

Todo o crédito pelo desenvolvimento original da extensão é do autor upstream, Erik Minekus ("Tsunami"). Este fork existe apenas para manter o processo de build funcional e atualizado.

---

## English

This repository is a **fork** of the [REST in Pawn (sm-ripext)](https://forums.alliedmods.net/showthread.php?t=298024) SourceMod extension, originally created by Erik Minekus ("Tsunami"). The extension provides HTTP and JSON natives for SourcePawn plugins.

### Purpose of this fork

The original upstream repository was no longer building correctly in CI (GitHub Actions), specifically:

1. **Broken Windows build**: the AMBuild installation step failed on Windows with a `PicklingError` / `PermissionError [WinError 5]`. This happened because AMBuild's `setup.py` spawns a `multiprocessing` process to detect `distutils`, and when `pip` installs the package, `setuptools` executes that `setup.py` via `exec()` from a string — in that context, the child process spawned on Windows could not re-import `__main__.detect_distutils`, breaking the install. The fix was to replace `pip install` with `python setup.py install`, run directly from the AMBuild checkout directory, so `setup.py` runs as the real `__main__` module.
2. **Compatibility with newer SourceMod versions**: the CI pipeline was still building against `1.12-dev`. The `sm_stringhashmap.h` header was renamed to `sm_hashmap.h` in more recent SourceMod versions (starting with 1.13). CI and the source code (`extension.h`) were updated to build against SourceMod `1.13.0.7441`, while keeping the same `StringHashMap<std::string>` type.

With these fixes, the extension builds again (Linux and Windows) against a newer SourceMod version, and the release pipeline correctly produces `.zip` packages for both platforms again.

### What changed compared to upstream

- `.github/workflows/ci.yml`: AMBuild is now installed via `python setup.py install` (instead of the `BSFishy/pip-action` action, which relied on pip's isolated build flow); the build matrix now targets SourceMod `1.13.0.7441`.
- `extension.h`: `#include <sm_stringhashmap.h>` → `#include <sm_hashmap.h>`.
- `smsdk_config.h`: extension version bumped to `1.4.0`, reflecting the new SourceMod 1.13 compatibility.

No business logic (HTTP/JSON natives) was changed — the changes are strictly build and compatibility related.

### Credits

All credit for the original development of this extension goes to the upstream author, Erik Minekus ("Tsunami"). This fork exists solely to keep the build process working and up to date.
