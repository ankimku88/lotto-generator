# 로또 번호 생성기 기획서

## 1. 프로젝트 개요

| 항목 | 내용 |
|------|------|
| 프로젝트명 | lotto-generator |
| 목적 | 수학적 확률 + 역대 통계 기반 로또 번호 조합 자동 생성 |
| 대상 | 로또 번호를 체계적으로 관리하고 싶은 사용자 |
| 배포 URL | https://lotto-generator.vercel.app (예정) |
| GitHub | https://github.com/ankimku88/lotto-generator |

> **면책 고지**: 어떤 전략도 1등 확률(1/8,145,060)을 높이지 못합니다.
> 이 프로그램은 역대 패턴에 부합하는 조합을 생성하고 비인기 조합으로 당첨 시 수령금을 최적화하는 도구입니다.

---

## 2. 로또 6/45 기본 규칙

- **숫자 범위**: 1~45에서 6개 선택 (순서 무관)
- **총 경우의 수**: C(45,6) = 8,145,060가지
- **추첨**: 매주 토요일 오후 8시 45분 (MBC 생방송)
- **보너스 번호**: 6개 추첨 후 별도 1개 → 2등 판별 전용

### 당첨 등급

| 등급 | 조건 | 당첨금 | 확률 |
|------|------|--------|------|
| 1등 | 6개 일치 | 변동 (판매액 75%) | 1/8,145,060 |
| 2등 | 5개 + 보너스 | 변동 | 1/1,357,510 |
| 3등 | 5개 일치 | 변동 | 1/35,724 |
| 4등 | 4개 일치 | 고정 50,000원 | 1/733 |
| 5등 | 3개 일치 | 고정 5,000원 | 1/44.6 |

---

## 3. 핵심 기능

### 3-1. 번호 생성 (4가지 모드)

#### 모드 1: 순수 랜덤
- 1~45에서 6개 균등 무작위 선택
- 매 실행마다 다른 조합 생성

#### 모드 2: 통계 필터
역대 당첨 패턴 기반 4개 필터 적용:

| 필터 | 조건 | 근거 |
|------|------|------|
| 합계 범위 | 100 ≤ sum ≤ 175 | 역대 65~70% 회차 해당 구간 |
| 홀짝 균형 | 홀수 1~5개 (전체 홀수 또는 짝수 배제) | 6:0 또는 0:6은 2% 미만 |
| 연속 번호 | 3연속 이상 배제 | 3연속 이상 출현율 2% 미만 |
| 구간 분산 | 한 구간(10개 단위)에 4개 이상 집중 배제 | 균형 분포 권장 |

#### 모드 3: 가중치
- 역대 출현 빈도 기반 가중치 적용
- Supabase DB에서 최신 통계 반영
- 빈출 번호(34, 27, 12, 13, 33...)에 높은 선택 확률 부여

#### 모드 4: 비인기 조합
- 32~45 번호 최소 2개 강제 포함 (생일 범위 초과)
- 인기 번호(7, 3, 8) 과집중 배제
- 통계 필터 동시 적용
- 목적: 당첨 시 단독 수령 확률 극대화 → 수령금 최적화

### 3-2. 매주 자동 업데이트

- **트리거**: GitHub Actions 크론 (매주 토요일 21:00 KST)
- **동작**:
  1. 동행복권 공식 API 호출
  2. 최신 당첨번호 Supabase DB 저장
  3. 번호별 가중치 자동 재계산
- **효과**: 앱 실행 시 항상 최신 통계 반영

### 3-3. 부가 기능

| 기능 | 설명 |
|------|------|
| 멀티 게임 생성 | 1~10게임 동시 생성 (기본 5게임) |
| 조합 분석 | 합계·홀짝·구간 분포 리포트 |
| 통계 대시보드 | 번호별 출현 빈도 시각화 |
| 이력 저장 | 로컬스토리지 생성 번호 기록 |

---

## 4. 기술 스택

### 프론트엔드

| 항목 | 기술 | 선택 이유 |
|------|------|----------|
| 프레임워크 | Next.js 15 (App Router) | Vercel 최적화, SSR/CSR 유연 |
| 언어 | TypeScript (strict) | 타입 안정성 |
| 스타일 | Tailwind CSS | 빠른 UI 개발 |
| 상태 관리 | React useState/useEffect | 단순한 상태, 외부 라이브러리 불필요 |

### 백엔드 (서버리스)

| 항목 | 기술 | 선택 이유 |
|------|------|----------|
| DB | Supabase (PostgreSQL) | JSONB 지원, REST API 자동 제공, 무료 500MB |
| 크론 | GitHub Actions | 완전 무료, 저장소 통합 |
| 크론 스크립트 | Python 3.11 | 데이터 처리에 적합 |

### 배포 및 인프라

| 항목 | 기술 | 비용 |
|------|------|------|
| 프론트 배포 | Vercel | 무료 |
| DB | Supabase | 무료 (500MB) |
| 크론 | GitHub Actions | 무료 (public repo) |
| 외부 API | 동행복권 공식 API | 무료 |

**월 운영비: 0원**

---

## 5. 시스템 아키텍처

```
사용자 브라우저
      │
      ▼
Vercel (Next.js 프론트엔드)
├── 번호 생성 로직 (브라우저에서 JS 실행)
├── 통계 필터 / 가중치 / 비인기 조합 계산
└── Supabase REST API 호출
      │
      ▼
Supabase (PostgreSQL DB)
├── lotto_results 테이블 (역대 당첨번호)
└── number_weights 뷰 (번호별 출현 빈도)

GitHub Actions (매주 토요일 21:00 KST)
├── 동행복권 API 호출
├── 최신 당첨번호 파싱
└── Supabase DB 업데이트
```

---

## 6. DB 스키마

```sql
-- 역대 당첨번호
CREATE TABLE lotto_results (
  id          SERIAL PRIMARY KEY,
  round       INTEGER UNIQUE NOT NULL,   -- 회차
  num1        INTEGER NOT NULL,
  num2        INTEGER NOT NULL,
  num3        INTEGER NOT NULL,
  num4        INTEGER NOT NULL,
  num5        INTEGER NOT NULL,
  num6        INTEGER NOT NULL,
  bonus       INTEGER NOT NULL,          -- 보너스 번호
  prize1      BIGINT,                    -- 1등 당첨금
  winners1    INTEGER,                   -- 1등 당첨자 수
  drawn_at    DATE NOT NULL,             -- 추첨일
  created_at  TIMESTAMPTZ DEFAULT NOW()
);

-- 번호별 출현 빈도 뷰
CREATE VIEW number_frequency AS
SELECT num, COUNT(*) AS frequency
FROM (
  SELECT num1 AS num FROM lotto_results UNION ALL
  SELECT num2 FROM lotto_results UNION ALL
  SELECT num3 FROM lotto_results UNION ALL
  SELECT num4 FROM lotto_results UNION ALL
  SELECT num5 FROM lotto_results UNION ALL
  SELECT num6 FROM lotto_results
) t
GROUP BY num
ORDER BY num;
```

---

## 7. 번호 생성 로직 (핵심 알고리즘)

```typescript
// 통계 필터 검증
function isValidCombination(numbers: number[]): boolean {
  const total = numbers.reduce((a, b) => a + b, 0);
  const oddCount = numbers.filter(n => n % 2 === 1).length;
  const sorted = [...numbers].sort((a, b) => a - b);

  // 필터 1: 합계 범위
  if (total < 100 || total > 175) return false;

  // 필터 2: 홀짝 균형
  if (oddCount === 0 || oddCount === 6) return false;

  // 필터 3: 3연속 이상 배제
  let consecutive = 1;
  for (let i = 1; i < sorted.length; i++) {
    if (sorted[i] === sorted[i - 1] + 1) {
      if (++consecutive >= 3) return false;
    } else {
      consecutive = 1;
    }
  }

  // 필터 4: 구간 편중 배제 (한 구간 4개 이상 금지)
  const zones = [0, 0, 0, 0, 0];
  numbers.forEach(n => {
    if (n <= 9) zones[0]++;
    else if (n <= 19) zones[1]++;
    else if (n <= 29) zones[2]++;
    else if (n <= 39) zones[3]++;
    else zones[4]++;
  });
  if (Math.max(...zones) >= 4) return false;

  return true;
}
```

---

## 8. 개발 로드맵

### Phase 1 - MVP (1주차)
- [ ] Next.js 프로젝트 초기 세팅
- [ ] 4가지 번호 생성 모드 구현
- [ ] 기본 UI (번호 카드 디자인)
- [ ] Supabase 연동 (DB 스키마 생성)

### Phase 2 - 데이터 자동화 (2주차)
- [ ] 동행복권 API 연동 스크립트 (Python)
- [ ] GitHub Actions 크론 설정
- [ ] 역대 당첨번호 초기 데이터 적재 (1~1215회)
- [ ] 가중치 자동 계산 반영

### Phase 3 - 통계 & UX (3주차)
- [ ] 통계 대시보드 (번호별 빈도 차트)
- [ ] 조합 분석 리포트
- [ ] 생성 이력 로컬스토리지 저장
- [ ] Vercel 배포

### Phase 4 - 고도화 (4주차~)
- [ ] 당첨 회차 비교 기능
- [ ] 반응형 모바일 UI
- [ ] PWA 지원 (홈 화면 추가)
