# 🎱 로또 번호 생성기 (lotto-generator)

수학적 확률 + 역대 통계 기반의 로또 6/45 번호 생성 웹 애플리케이션입니다.
매주 토요일 동행복권 API에서 당첨번호를 자동 수집하여 최신 통계를 반영합니다.

**GitHub**: https://github.com/ankimku88/lotto-generator
**배포**: Vercel (예정)

---

## 번호 생성 모드

| 모드 | 설명 |
|------|------|
| 순수 랜덤 | 1~45에서 균등 무작위 6개 선택 |
| 통계 필터 | 합계(100~175), 홀짝 균형, 연속번호 제한, 구간 분산 필터 적용 |
| 가중치 | 역대 출현 빈도 기반 가중치 적용 (최신 통계 자동 반영) |
| 비인기 조합 | 32~45 번호 2개 이상 포함, 인기 번호 과집중 배제 → 당첨 시 단독 수령 확률 최적화 |

---

## 기술 스택

| 항목 | 기술 |
|------|------|
| 프레임워크 | Next.js 16 (App Router) |
| 언어 | TypeScript 5 (strict) |
| 스타일 | Tailwind CSS v4 |
| 데이터베이스 | Supabase (PostgreSQL) |
| 배포 | Vercel |
| 크론 | GitHub Actions (매주 토요일 21:00 KST) |
| 테스트 | Jest + React Testing Library + Playwright |
| 린팅 | ESLint 9 (Flat Config) + Prettier |

---

## 개발 명령어

```bash
# 개발 서버 실행
npm run dev

# 프로덕션 빌드
npm run build

# 코드 품질 검사
npm run type-check       # TypeScript 타입 검사
npm run lint             # ESLint 검사
npm run lint:fix         # ESLint 자동 수정
npm run format           # Prettier 포매팅
npm run format:check     # Prettier 검사만

# 테스트
npm run test             # Jest 단위 테스트
npm run test:watch       # Jest 워치 모드
npm run test:coverage    # 커버리지 리포트
npm run test:ci          # CI용 실행 (coverage + runInBand)
npm run test:e2e         # Playwright E2E 테스트
npm run test:e2e:ui      # Playwright UI 모드
npm run test:e2e:report  # Playwright 결과 리포트

# 전체 검증 (타입 + 린트 + 테스트)
npm run validate
```

---

## 프로젝트 구조

```
lotto-generator/
├── app/                        # Next.js App Router
│   ├── api/                    # API 라우트 (Supabase 연동)
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   └── ui/                     # 재사용 UI 컴포넌트
├── lib/
│   ├── generator/              # 번호 생성 로직 (4가지 모드)
│   └── supabase/               # Supabase 클라이언트 및 쿼리
├── hooks/                      # 커스텀 훅
├── e2e/                        # Playwright E2E 테스트
├── scripts/                    # GitHub Actions 크론 스크립트 (Python)
├── docs/
│   ├── PLAN.md                 # 기획서 (기술스택, 알고리즘, DB 스키마)
│   └── TASK.md                 # 작업 태스크 목록
├── .github/workflows/          # GitHub Actions
├── jest.config.ts
├── playwright.config.ts
└── CLAUDE.md                   # AI 코딩 가이드
```

---

## 환경변수 설정

`.env.local` 파일을 생성하고 아래 값을 설정합니다. (커밋 금지)

```bash
NEXT_PUBLIC_SUPABASE_URL=https://xxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJxxxxxx
```

GitHub Actions Secrets에도 동일하게 등록 필요:
- `SUPABASE_URL`
- `SUPABASE_SERVICE_KEY` (service_role 키)

---

## 시스템 아키텍처

```
사용자 브라우저
      │
      ▼
Vercel (Next.js 프론트엔드 + 번호 생성 로직)
      │
      ▼
Supabase (PostgreSQL DB + REST API 자동 제공)

GitHub Actions (매주 토요일 21:00 KST)
  → 동행복권 API → Supabase DB 업데이트
```

월 운영비: **0원** (전부 무료 플랜)

---

## 문서

- [기획서](docs/PLAN.md) — 로또 규칙, 알고리즘 설계, DB 스키마, 로드맵
- [태스크 목록](docs/TASK.md) — Phase별 작업 현황
- [CLAUDE.md](CLAUDE.md) — 코드 컨벤션 및 AI 코딩 가이드
