# 프로젝트: lotto-generator

App Router, Supabase, Tailwind CSS를 사용하는 Next.js 15 로또 번호 생성 웹 애플리케이션입니다.
매주 토요일 GitHub Actions로 동행복권 API에서 당첨번호를 자동 수집하고,
역대 통계 기반의 다양한 모드로 로또 번호 조합을 생성합니다.

## 코드 스타일

- TypeScript strict 모드 사용, `any` 타입 금지
- default export 대신 named export 사용
- CSS: Tailwind 유틸리티 클래스 사용, 커스텀 CSS 파일 금지
- 컴포넌트 파일명: PascalCase (예: LottoCard.tsx)
- 유틸리티 파일명: camelCase (예: lottoGenerator.ts)
- 상수는 UPPER_SNAKE_CASE

## 명령어

- `npm run dev`: 개발 서버 시작 (포트 3000)
- `npm run build`: 프로덕션 빌드
- `npm run lint`: ESLint 검사
- `npm run type-check`: TypeScript 타입 검사

## 아키텍처

- `/app`: Next.js App Router 페이지 및 레이아웃
- `/app/api`: API 라우트 (Supabase 연동)
- `/components/ui`: 재사용 가능한 UI 컴포넌트
- `/lib`: 유틸리티 및 공유 로직
  - `/lib/generator`: 번호 생성 로직 (4가지 모드)
  - `/lib/supabase`: Supabase 클라이언트 및 쿼리
- `/scripts`: GitHub Actions용 크론 스크립트 (Python)
- `/.github/workflows`: GitHub Actions 워크플로우

## 기술 스택

- **프레임워크**: Next.js 15 (App Router)
- **언어**: TypeScript (strict)
- **스타일**: Tailwind CSS
- **데이터베이스**: Supabase (PostgreSQL)
- **배포**: Vercel
- **크론**: GitHub Actions (매주 토요일 21:00 KST)
- **외부 API**: 동행복권 공식 API

## 번호 생성 모드

1. **순수 랜덤**: Math.random() 기반 균등 무작위
2. **통계 필터**: 합계(100~175), 홀짝 균형, 연속번호, 구간 분산 필터 적용
3. **가중치**: 역대 출현 빈도 기반 가중치 적용
4. **비인기 조합**: 32~45 번호 2개 이상, 인기 번호 과집중 배제

## 중요 사항

- `.env.local` 파일은 절대 커밋하지 마세요
- Supabase 키는 환경변수로만 관리 (`NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`)
- GitHub Actions secrets에 Supabase 키 등록 필요
- 동행복권 API: `https://www.dhlottery.co.kr/common.do?method=getLottoNumber&drwNo={회차번호}`
- 자세한 기획 내용은 `docs/PLAN.md` 참고
- 작업 태스크 목록은 `docs/TASK.md` 참고
