# jlc

KiCad 회로도에 JLC PCB 부품 라이브러리(심볼·풋프린트·3D)를 바로 꽂아 넣는 CLI.

소스는 비공개. 이 레포는 **일반 사용자 배포 채널**(릴리스 바이너리 + 설치 스크립트)입니다.

---

## 설치 (한 줄)

### macOS · Linux

터미널에 붙여넣기:

```sh
curl -fsSL https://github.com/orderlycode/jlc-cli-releases/releases/latest/download/install.sh | bash
```

- 플랫폼 감지 → 맞는 바이너리 다운로드 → `/usr/local/bin/jlc` 또는 `~/.local/bin/jlc`에 설치
- macOS는 자동으로 Gatekeeper 격리 플래그 제거 및 ad-hoc 재서명
- `~/.local/bin`에 설치됐고 PATH에 없다면 스크립트가 안내

### Windows (PowerShell)

```powershell
irm https://github.com/orderlycode/jlc-cli-releases/releases/latest/download/install.ps1 | iex
```

- `%LOCALAPPDATA%\Programs\jlc\jlc.exe`에 설치
- 사용자 PATH 자동 등록 (새 터미널에서 반영)
- SmartScreen Mark-of-the-Web 자동 해제

### 특정 버전 설치

```sh
# macOS / Linux
curl -fsSL https://github.com/orderlycode/jlc-cli-releases/releases/latest/download/install.sh | bash -s -- --version v0.1.1

# Windows
& ([scriptblock]::Create((irm https://github.com/orderlycode/jlc-cli-releases/releases/latest/download/install.ps1))) -Version v0.1.1
```

### 제거

- macOS / Linux: `rm /usr/local/bin/jlc` 또는 `rm ~/.local/bin/jlc`
- Windows: `Remove-Item "$env:LOCALAPPDATA\Programs\jlc\jlc.exe"` (PATH 정리는 수동)

---

## 사용

KiCad 프로젝트 폴더(예: `my-board/`) 안에서 실행합니다. 생성되는 라이브러리는 폴더 이름을 따서 `my-board.kicad_sym`, `my-board.pretty/`, `my-board.3dshapes/`로 떨어지므로 KiCad 프로젝트가 바로 인식합니다.

### 단일 부품 추가

LCSC 부품 ID를 알고 있을 때:

```sh
cd ~/project/my-board
jlc C5360631
```

결과:
- `my-board.kicad_sym` — 심볼 라이브러리 생성·갱신
- `my-board.pretty/…kicad_mod` — 풋프린트
- `my-board.3dshapes/…{step,wrl}` — 3D 모델
- 현재 폴더의 모든 `*.kicad_sch`에 `hide-pin` 정리 적용

### 회로도 전체 검증/자동 할당

인자 없이 실행하면 모든 `.kicad_sch`를 훑어서:

```sh
cd ~/project/my-board
jlc
```

1. `Footprint`가 비어있는 R/C/L 심볼을 찾아 → 스펙으로 JLC 부품 자동 검색
2. 재고 10,000개 이상인 후보가 3개 이상이면 **그 중 최저가로 자동 선정**
3. 그렇지 않으면 후보 목록을 보여주고 사용자가 직접 선택
4. `LCSC Part` 속성만 있고 `Footprint` 없는 심볼도 채워줌
5. 심볼의 Footprint / Description / Manufacturer / Datasheet / LCSC Part 속성을 모두 자동 입력

### 기존 파일 덮어쓰기

같은 LCSC ID를 다시 받으면 덮어쓸지 묻습니다:

```
? my-board.kicad_sym 이미 있습니다. 덮어쓸까요? (Use arrow keys)
❯ 아니오 (스킵)
  예 (이번만)
  모두 (세션 내 자동)
```

"모두"를 고르면 이 실행 내에서 이후 충돌은 자동 덮어쓰기.

---

## 환경변수

| 변수 | 기본값 | 설명 |
|------|--------|------|
| `JLC_API_URL` | production Cloud Run | jlc-api 베이스 URL. 스테이징 테스트 시 덮어쓰기 |
| `JLC_NO_UPDATE_CHECK` | (unset) | `1`이면 GitHub 업데이트 체크 건너뜀 |

---

## 릴리스 자산

각 릴리스에 올라가는 파일:

| 파일 | 용도 |
|------|------|
| `install.sh` | macOS/Linux 한 줄 설치 스크립트 |
| `install.ps1` | Windows PowerShell 설치 스크립트 |
| `jlc-0.1.x-arm64.pkg` | macOS Apple Silicon 인스톨러 |
| `jlc-0.1.x-x86_64.pkg` | macOS Intel 인스톨러 |
| `jlc-0.1.x.msi` | Windows 인스톨러 |
| `jlc-bun-darwin-arm64` | macOS Apple Silicon 바이너리 (수동 배치용) |
| `jlc-bun-darwin-x64` | macOS Intel 바이너리 |
| `jlc-bun-linux-x64` | Linux x64 바이너리 |
| `jlc-bun-windows-x64.exe` | Windows x64 바이너리 |

일반 사용자는 **install.sh / install.ps1 한 줄**이면 충분합니다. 인스톨러(.pkg/.msi)는 IT팀이 MDM으로 배포할 때 쓰고, 직접 바이너리는 PATH 커스터마이징이 필요한 개발자용.

---

## 문제 해결

**macOS: "식별되지 않은 개발자" 경고**
- 원라이너 설치 스크립트를 쓰면 자동 해결됩니다 (`xattr` 제거 + ad-hoc 재서명)
- 수동 배치한 경우: `xattr -c /usr/local/bin/jlc` 한 번 실행

**Windows: SmartScreen "PC를 보호했습니다" 경고**
- 원라이너 설치 스크립트가 `Unblock-File`로 자동 처리
- 수동 배치한 경우: 파일 속성 → "차단 해제" 체크

**jlc 명령을 찾을 수 없음**
- macOS/Linux: 설치 스크립트 출력에 PATH 안내가 있습니다. `~/.local/bin`이면 `export PATH="$HOME/.local/bin:$PATH"`를 `~/.zshrc` 또는 `~/.bashrc`에 추가
- Windows: PowerShell을 새로 열면 반영됩니다

**API 서버에 연결 실패**
- 기본적으로 `https://ocp-jlc-api-275606197168.asia-northeast3.run.app`을 호출합니다
- 회사 방화벽 안이면 IT에 이 도메인 허용 요청, 또는 `JLC_API_URL`로 대체 URL 지정

**커스텀 API 서버 사용**
```sh
JLC_API_URL=https://my-api.example.com jlc C5360631
```

---

## 버전 & 업데이트

```sh
jlc --version
```

새 버전이 나오면 `jlc` 실행 시(최근 24시간 내 체크 안 했을 때) 알림이 뜹니다. 업데이트는 원라이너 설치를 다시 실행하면 됩니다.
