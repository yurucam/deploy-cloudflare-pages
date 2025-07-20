# Deploy Cloudflare Pages Action

Cloudflare Pages에 Node.js 프로젝트를 자동으로 빌드하고 배포하는 GitHub Action입니다.

## 기능

- Node.js 환경 자동 설정 (`.nvmrc` 파일 기반)
- 의존성 캐싱으로 빌드 시간 단축
- 자동 빌드 및 Cloudflare Pages 배포
- 재사용 가능한 composite action

## 사용법

```yaml
name: Deploy to Cloudflare Pages

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Deploy to Cloudflare Pages
        uses: yurucam/deploy-cloudflare-pages@main
        with:
          project-name: 'my-project'
          working-directory: 'frontend'
          output-directory: 'dist'
          account-id: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          api-token: ${{ secrets.CLOUDFLARE_API_TOKEN }}
```

## 입력 매개변수

| 매개변수 | 필수 | 타입 | 설명 |
|---------|------|------|------|
| `project-name` | ✅ | string | Cloudflare Pages 프로젝트 이름 |
| `working-directory` | ✅ | string | 프로젝트의 작업 디렉토리 경로 |
| `output-directory` | ✅ | string | 빌드된 파일들이 위치한 출력 디렉토리 경로 |
| `account-id` | ✅ | string | Cloudflare 계정 ID |
| `api-token` | ✅ | string | Cloudflare API 토큰 |

## 필수 조건

1. **Node.js 프로젝트**: `package.json`과 `package-lock.json` 파일이 있어야 합니다.
2. **Node 버전 파일**: 작업 디렉토리에 `.nvmrc` 파일이 있어야 합니다.
3. **빌드 스크립트**: `package.json`에 `build` 스크립트가 정의되어 있어야 합니다.
4. **Cloudflare 설정**: 
   - Cloudflare 계정 ID
   - 적절한 권한을 가진 API 토큰

## Secrets 설정

GitHub 저장소의 Settings > Secrets and variables > Actions에서 다음 secrets을 설정하세요:

- `CLOUDFLARE_ACCOUNT_ID`: Cloudflare 대시보드에서 확인할 수 있는 계정 ID
- `CLOUDFLARE_API_TOKEN`: Cloudflare Pages 배포 권한을 가진 API 토큰

## 예제 프로젝트 구조

```
my-project/
├── .nvmrc                 # Node.js 버전 (예: 18.17.0)
├── package.json           # npm 의존성 및 빌드 스크립트
├── package-lock.json      # 의존성 잠금 파일
├── src/                   # 소스 코드
└── dist/                  # 빌드 출력 디렉토리
```

## 작동 방식

1. **Node.js 설정**: `.nvmrc` 파일을 기반으로 Node.js 환경을 설정합니다.
2. **의존성 캐싱**: `package-lock.json` 해시를 기반으로 `node_modules`를 캐싱합니다.
3. **의존성 설치**: 캐시가 없는 경우에만 `npm ci`를 실행합니다.
4. **프로젝트 빌드**: `npm run build` 명령을 실행합니다.
5. **Cloudflare Pages 배포**: Wrangler를 사용하여 빌드된 파일을 배포합니다.

## 문제 해결

### 일반적인 오류

- **Node 버전 오류**: `.nvmrc` 파일이 작업 디렉토리에 있는지 확인하세요.
- **빌드 실패**: `package.json`에 `build` 스크립트가 정의되어 있는지 확인하세요.
- **배포 실패**: Cloudflare API 토큰과 계정 ID가 올바른지 확인하세요.

### 디버깅

GitHub Actions 로그에서 각 단계의 상세한 출력을 확인할 수 있습니다.

## 라이선스

이 프로젝트는 [MIT 라이선스](./LICENSE) 하에 배포됩니다.

## 기여

버그 리포트나 기능 제안은 GitHub Issues를 통해 제출해 주세요.
