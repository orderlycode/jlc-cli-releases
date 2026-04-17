# jlc CLI — 설치

KiCad 회로도에서 JLC PCB 부품 라이브러리를 다운로드하는 CLI.

## 설치 (한 줄)

### macOS / Linux

```sh
curl -fsSL https://github.com/orderlycode/jlc-cli-releases/releases/latest/download/install.sh | bash
```

### Windows (PowerShell)

```powershell
irm https://github.com/orderlycode/jlc-cli-releases/releases/latest/download/install.ps1 | iex
```

## 사용

```sh
jlc C5360631   # LCSC ID로 심볼+풋프린트+3D 다운로드
jlc            # cwd의 *.kicad_sch 훑어 Footprint 없는 RLC 자동 할당
jlc --help
```

## 수동 다운로드

[Latest Release](https://github.com/orderlycode/jlc-cli-releases/releases/latest)에서 플랫폼에 맞는 바이너리 직접 받기.

- macOS: `.pkg` 또는 `jlc-bun-darwin-{arm64,x64}` 바이너리
- Windows: `.msi` 또는 `jlc-bun-windows-x64.exe`
- Linux: `jlc-bun-linux-x64`

소스는 비공개이며 이 레포는 릴리스 배포 전용입니다.
