# 프로젝트: lotto-generator

App Router, Supabase, Tailwind CSS v4를 사용하는 Next.js 16 / React 19 로또 번호 생성 웹 애플리케이션입니다.
매주 토요일 GitHub Actions로 동행복권 API에서 당첨번호를 자동 수집하고,
역대 통계 기반의 다양한 모드로 로또 번호 조합을 생성합니다.

## 코드 스타일

- TypeScript strict 모드 사용, `any` 타입 금지
- `default export` 대신 `named export` 사용
  - 단, Next.js 특수 파일(`page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`)은 `default export` 사용 (프레임워크 요구사항)
- CSS: Tailwind 유틸리티 클래스 사용, 커스텀 CSS 파일 금지
- `cn()` 유틸리티로 className 병합 (`clsx` + `tailwind-merge`)
- 컴포넌트 파일명: PascalCase (예: `LottoCard.tsx`)
- 유틸리티 파일명: camelCase (예: `lottoGenerator.ts`)
- 상수: UPPER_SNAKE_CASE
- Props 타입: `type` 키워드로 컴포넌트 바로 위에 정의 (`React.FC` 사용 금지)
- 커스텀 훅: `use` + 대문자 시작, 반환 타입 명시
- `'use client'` 디렉티브는 최대한 leaf 컴포넌트에만 배치
- `import type` 강제 사용 (`@typescript-eslint/consistent-type-imports`)
- `??` 연산자 사용 (`||` 금지, `@typescript-eslint/prefer-nullish-coalescing`)

## 명령어

- `npm run dev`: 개발 서버 시작 (포트 3000)
- `npm run build`: 프로덕션 빌드
- `npm run lint`: ESLint 검사
- `npm run lint:fix`: ESLint 자동 수정
- `npm run type-check`: TypeScript 타입 검사
- `npm run format`: Prettier 포매팅
- `npm run test`: Jest 단위 테스트 실행
- `npm run test:watch`: Jest 워치 모드
- `npm run test:coverage`: Jest 커버리지 리포트
- `npm run test:ci`: CI용 Jest 실행 (coverage + runInBand)
- `npm run test:e2e`: Playwright E2E 테스트
- `npm run test:e2e:ui`: Playwright UI 모드
- `npm run validate`: type-check + lint + test:ci 전체 검증

## 테스트 규칙

### 필수 원칙
- **모든 함수 작성 시 테스트 코드 동시 작성** (TDD 지향)
- 순수 함수(유틸, 생성 로직): **단위 테스트** (`__tests__/*.test.ts`)
- 커스텀 훅: **훅 테스트** (`renderHook` 사용)
- UI 컴포넌트: **컴포넌트 테스트** (`*.test.tsx`)
- 사용자 플로우: **E2E 테스트** (`e2e/*.spec.ts`)

### 커버리지 기준
- 전체: Lines 80% / Branches 70% / Functions 80%
- `lib/generator/`: Lines 90% / Branches 85% / Functions 90%

### 테스트 작성 패턴
```typescript
// AAA 패턴 필수
describe('함수명 또는 컴포넌트명', () => {
  describe('조건/시나리오', () => {
    it('~을/를 한다', () => {
      // Arrange
      // Act
      // Assert
    });
  });
});
```

### RTL 쿼리 우선순위
`getByRole` > `getByLabelText` > `getByText` > `getByTestId` (최후 수단)

### 모킹 규칙
- HTTP API 호출: **MSW** 사용 (`__mocks__/handlers.ts`)
- 내부 모듈/전역 객체: `jest.mock` 사용

## 아키텍처

- `/app`: Next.js App Router 페이지 및 레이아웃
- `/app/api`: API 라우트 (Supabase 연동)
- `/components/ui`: 재사용 가능한 UI 컴포넌트
- `/lib/generator`: 번호 생성 로직 (4가지 모드)
- `/lib/supabase`: Supabase 클라이언트 및 쿼리
- `/hooks`: 커스텀 훅
- `/scripts`: GitHub Actions용 크론 스크립트 (Python)
- `/e2e`: Playwright E2E 테스트
- `/__mocks__`: MSW 핸들러
- `/.github/workflows`: GitHub Actions 워크플로우

## 기술 스택

- **프레임워크**: Next.js 16 (App Router)
- **런타임**: React 19
- **언어**: TypeScript 5 (strict)
- **스타일**: Tailwind CSS v4
- **데이터베이스**: Supabase (PostgreSQL)
- **배포**: Vercel
- **크론**: GitHub Actions (매주 토요일 21:00 KST)
- **외부 API**: 동행복권 공식 API
- **테스트**: Jest + React Testing Library + Playwright
- **린팅**: ESLint 9 (Flat Config) + Prettier

## 번호 생성 모드

1. **순수 랜덤**: Math.random() 기반 균등 무작위
2. **통계 필터**: 합계(100~175), 홀짝 균형, 연속번호 제한, 구간 분산 필터
3. **가중치**: 역대 출현 빈도 기반 가중치 적용
4. **비인기 조합**: 32~45 번호 2개 이상 포함, 인기 번호 과집중 배제

## 중요 사항

- `.env.local` 파일은 절대 커밋하지 마세요
- Supabase 키는 환경변수로만 관리 (`NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`)
- GitHub Actions secrets에 Supabase 키 등록 필요
- 동행복권 API: `https://www.dhlottery.co.kr/common.do?method=getLottoNumber&drwNo={회차번호}`
- 자세한 기획 내용: `docs/PLAN.md`
- 작업 태스크 목록: `docs/TASK.md`
