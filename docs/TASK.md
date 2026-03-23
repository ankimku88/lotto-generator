# 작업 태스크 목록 (TASK LIST)

> 상태: ⬜ 대기 | 🔄 진행중 | ✅ 완료 | ⛔ 블로킹

---

## Phase 1 - MVP 세팅

### 1-1. 프로젝트 초기화
| 상태 | 작업 | 설명 |
|------|------|------|
| ✅ | Next.js 15 프로젝트 생성 | TypeScript + Tailwind + App Router |
| ✅ | CLAUDE.md 작성 | 프로젝트 가이드 문서 |
| ✅ | docs/PLAN.md 작성 | 기획서 |
| ✅ | docs/TASK.md 작성 | 태스크 목록 |
| ⬜ | GitHub 원격 저장소 연결 | https://github.com/ankimku88/lotto-generator |
| ⬜ | .env.local 파일 생성 | Supabase 키 설정 (커밋 금지) |
| ⬜ | .gitignore 확인 | .env.local 포함 여부 확인 |

### 1-2. Supabase 세팅
| 상태 | 작업 | 설명 |
|------|------|------|
| ⬜ | Supabase 프로젝트 생성 | app.supabase.com |
| ⬜ | DB 스키마 생성 | lotto_results 테이블, number_frequency 뷰 |
| ⬜ | Supabase 클라이언트 설정 | /lib/supabase/client.ts |
| ⬜ | 환경변수 연결 확인 | NEXT_PUBLIC_SUPABASE_URL, ANON_KEY |

### 1-3. 번호 생성 로직 (핵심)
| 상태 | 작업 | 파일 위치 |
|------|------|----------|
| ⬜ | 유틸리티 함수 작성 | /lib/generator/utils.ts |
| ⬜ | 모드 1: 순수 랜덤 | /lib/generator/random.ts |
| ⬜ | 모드 2: 통계 필터 | /lib/generator/filtered.ts |
| ⬜ | 모드 3: 가중치 | /lib/generator/weighted.ts |
| ⬜ | 모드 4: 비인기 조합 | /lib/generator/unpopular.ts |
| ⬜ | 생성기 통합 진입점 | /lib/generator/index.ts |

### 1-4. UI 컴포넌트
| 상태 | 작업 | 파일 위치 |
|------|------|----------|
| ⬜ | 메인 레이아웃 | /app/layout.tsx 수정 |
| ⬜ | 메인 페이지 | /app/page.tsx |
| ⬜ | 모드 선택 탭 컴포넌트 | /components/ui/ModeSelector.tsx |
| ⬜ | 번호 카드 컴포넌트 | /components/ui/LottoCard.tsx |
| ⬜ | 번호 볼 컴포넌트 | /components/ui/LottoBall.tsx |
| ⬜ | 생성 버튼 컴포넌트 | /components/ui/GenerateButton.tsx |
| ⬜ | 조합 분석 컴포넌트 | /components/ui/CombinationInfo.tsx |

---

## Phase 2 - 데이터 자동화

### 2-1. 데이터 수집 스크립트
| 상태 | 작업 | 파일 위치 |
|------|------|----------|
| ⬜ | Python 스크립트 작성 | /scripts/fetch_lotto.py |
| ⬜ | 동행복권 API 연동 | 회차별 당첨번호 파싱 |
| ⬜ | Supabase 저장 로직 | supabase-py 라이브러리 사용 |
| ⬜ | 초기 데이터 일괄 적재 | 1회~최신 회차 전체 |

### 2-2. GitHub Actions 크론
| 상태 | 작업 | 파일 위치 |
|------|------|----------|
| ⬜ | 워크플로우 파일 작성 | /.github/workflows/update_lotto.yml |
| ⬜ | 크론 스케줄 설정 | 매주 토요일 21:00 KST (UTC 12:00) |
| ⬜ | GitHub Secrets 등록 | SUPABASE_URL, SUPABASE_SERVICE_KEY |
| ⬜ | 워크플로우 테스트 | 수동 트리거로 검증 |

### 2-3. 가중치 자동 반영
| 상태 | 작업 | 설명 |
|------|------|------|
| ⬜ | 번호별 출현 빈도 API | /app/api/weights/route.ts |
| ⬜ | 프론트 가중치 로드 | 앱 시작 시 Supabase에서 fetch |
| ⬜ | 가중치 캐싱 | 하루 1회 갱신 (SWR or React Query) |

---

## Phase 3 - 통계 & UX

### 3-1. 통계 대시보드
| 상태 | 작업 | 파일 위치 |
|------|------|----------|
| ⬜ | 통계 페이지 | /app/stats/page.tsx |
| ⬜ | 번호별 빈도 차트 | /components/ui/FrequencyChart.tsx |
| ⬜ | 합계 분포 차트 | /components/ui/SumDistribution.tsx |
| ⬜ | 홀짝 비율 차트 | /components/ui/OddEvenChart.tsx |

### 3-2. UX 고도화
| 상태 | 작업 | 설명 |
|------|------|------|
| ⬜ | 생성 이력 저장 | 로컬스토리지 기반 |
| ⬜ | 이력 페이지 | /app/history/page.tsx |
| ⬜ | 복사 기능 | 번호 클립보드 복사 |
| ⬜ | 반응형 UI | 모바일 최적화 |

### 3-3. 배포
| 상태 | 작업 | 설명 |
|------|------|------|
| ⬜ | Vercel 프로젝트 생성 | GitHub 저장소 연결 |
| ⬜ | Vercel 환경변수 설정 | Supabase 키 등록 |
| ⬜ | 도메인 확인 | lotto-generator.vercel.app |
| ⬜ | 프로덕션 배포 확인 | 빌드 에러 없음 확인 |

---

## Phase 4 - 고도화 (추후)

| 상태 | 작업 | 설명 |
|------|------|------|
| ⬜ | 당첨 회차 비교 기능 | 생성 번호 vs 역대 당첨 비교 |
| ⬜ | PWA 지원 | 홈 화면 추가, 오프라인 지원 |
| ⬜ | 다크모드 | 시스템 테마 연동 |
| ⬜ | 최신 회차 당첨번호 표시 | 메인 화면 상단 표시 |

---

## 환경변수 목록

```
# .env.local (커밋 금지)
NEXT_PUBLIC_SUPABASE_URL=https://xxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJxxxxxx

# GitHub Actions Secrets
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_SERVICE_KEY=eyJxxxxxx  # service_role 키 (쓰기 권한)
```

---

## 외부 서비스 설정 체크리스트

- [ ] Supabase 프로젝트 생성 및 키 발급
- [ ] GitHub Secrets에 Supabase 키 등록
- [ ] Vercel 프로젝트 생성 및 환경변수 등록
- [ ] GitHub 저장소 public 설정 (GitHub Actions 무료 사용)
