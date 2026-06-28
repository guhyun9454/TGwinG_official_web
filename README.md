# T.G.winG 공식 웹

T.G.winG 동아리의 공식 웹사이트. 'winG(날개)' 브랜딩과 컴퓨터공학 컨셉을 녹인 홈페이지.

개발 방식: MVP 골격을 먼저 구축한 뒤 브랜치 → PR로 기능을 채운다.
협업 규칙은 [CONTRIBUTING.md](./CONTRIBUTING.md), 기술·운영 결정의 근거와 대안은 [docs/DECISIONS.md](./docs/DECISIONS.md) 참고.

## 기술 스택

| 영역 | 스택 |
|------|------|
| 프레임워크 | **Next.js** (App Router) + **TypeScript** — 프론트 + API 단일 |
| DB | **PostgreSQL** + ORM (Drizzle 또는 Prisma) |
| 인증 | **Auth.js** (NextAuth) + GitHub OAuth — GitHub Org 멤버십으로 멤버 판정 |
| 파일 저장 | **Cloudflare R2** (S3 호환 SDK) — 스터디 자료 등 |
| 개발 단계 배포 | **Vercel** |
| 최종 호스팅 | **쿠러그(KU LUG) 자체 서버** (Docker) — 다음 학기 이전 |

> 별도 백엔드(Spring 등) 없이 **Next.js 하나로** 운영합니다. 기여자가 매학기 바뀌는 동아리에서
> 프론트/백 분리는 세팅·유지 부담을 2배로 늘리기 때문입니다. 분리하지 않는 이유와 대안 비교는
> [docs/DECISIONS.md](./docs/DECISIONS.md) 1·2번 참고.

## 포터빌리티 원칙 (중요)

최종 호스팅이 **쿠러그 자체 서버**이므로, 특정 클라우드에 종속(lock-in)되지 않게 짭니다.
이 원칙만 지키면 이전 = 재작성이 아니라 `docker compose up` + 환경변수 교체로 끝납니다.

- **DB**: 순수 Postgres만 사용 (Supabase Auth/RLS 등 비표준 기능 ❌) → 커넥션 문자열만 교체
- **파일**: S3 호환 SDK (`@aws-sdk/client-s3`) → R2 ↔ 쿠러그 MinIO 교체 가능
- **설정**: 전부 `.env` (하드코딩 금지, **비밀키 커밋 금지** — `.env.example`만 올림)
- **배포**: `output: 'standalone'` + Dockerfile 준비

## 프로젝트 구조

```
TGwinG_official_web/
├── app/          # Next.js App Router (페이지 + API Routes)
├── components/   # 공용 컴포넌트
├── lib/          # DB · 인증 · 스토리지 클라이언트
├── db/           # 스키마 · 마이그레이션 (ORM)
├── public/       # 정적 에셋
└── .github/      # PR / 이슈 템플릿
```

## 가용성 / failover

쿠러그 이전 후에도 Vercel 배포를 살려둬 **수동 failover** 대기소로 쓴다. 쿠러그 다운 시 DNS만 Vercel로 돌리면 복구.
자동 failover는 만들지 않는다(유지보수 부담). 즉시 failover가 필요하면 DB·파일을 매니지드(Supabase/Neon + R2)로 유지해 양쪽이 같은 데이터를 보게 한다. 근거는 [docs/DECISIONS.md](./docs/DECISIONS.md) 15번.

## 주요 페이지

**Public (누구나)**
- 홈 / 랜딩 (스크롤형, winG 날개 애니메이션)
- 프로젝트 아카이브 (카드형, 기수·스택·수상 필터)
- 기술 블로그 아카이브 (외부 아티클 링크 + OG 썸네일)

**Private (멤버만 — GitHub Org 멤버십 기반)**
- 스터디 자료 공유 (파일 업로드)
- 스터디 / 팀 모집
- 공지 / 일정
- 관리자 페이지

## 로컬 실행

```bash
npm install
cp .env.example .env.local   # 값 채우기 (DB·GitHub OAuth·R2)
npm run dev
```

> 아직 골격 구축 단계입니다. 위 명령은 초기 세팅 완료 후 동작합니다.

## 작업 관리

세부 작업은 **GitHub Issues + Projects(칸반 보드)**로 관리합니다.
전체 로드맵은 [docs/ROADMAP.md](./docs/ROADMAP.md)를 참고하세요.

## 기여하기

이 프로젝트는 PR 기반으로 협업합니다. 처음이라면 [CONTRIBUTING.md](./CONTRIBUTING.md)를 반드시 확인하세요.
