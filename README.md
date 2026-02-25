# Deploy-Aimind-Frontend (아이마음 웹 클라이언트)

**아이마음**은 보호자·상담사가 아동의 HTP(나무·집·사람) 그림과 그림일기를 업로드하면, AI가 그림을 분석·해석하고 T-Score와 심리 요약을 제공하는 **아동 심리 지원 플랫폼**입니다.  
이 저장소는 그중 **웹 클라이언트**(Next.js 16)를 담당하며, HTP 그림 분석, 그림일기 OCR, 커뮤니티, 마이페이지 등 화면을 제공합니다.

---

## 데모

- **웹 데모**: [http://43.203.135.230.nip.io:3000/](http://43.203.135.230.nip.io:3000/) (발표·시연용)

---

## 아이마음 프로젝트 구성

아이마음은 4개 저장소로 구성됩니다. 이 저장소(Frontend)가 사용자 진입점입니다.

```
[사용자] → Frontend(3000) → Backend(8000) → Aimodels(8080) / OCR(8090)
```

| 저장소 | 역할 | 기본 포트 |
|--------|------|-----------|
| **Deploy-Aimind-Frontend** (본 저장소) | 웹 UI (로그인, 그림 분석, 그림일기 OCR, 커뮤니티, 마이페이지) | 3000 |
| **Deploy-Aimind-Backend** | REST API (인증, 사용자/아동, 분석·OCR 저장, 커뮤니티), AiModels·OCR 프록시 | 8000 |
| **Deploy-Aimind-Aimodels** | HTP 그림 분석·해석·T-Score·챗봇 (YOLO + RAG + Gemini) | 8080 |
| **Deploy-Aimind-OCR** | 그림일기 이미지 → 텍스트 추출 (VLM + Gemini) | 8090 |

**전체 서비스 실행 순서 (로컬):**  
1) Backend → 2) Aimodels → 3) OCR → 4) Frontend.  
Backend `.env`에 `AIMODELS_BASE_URL=http://localhost:8080`, `OCR_BASE_URL=http://localhost:8090` 설정.  
Frontend `NEXT_PUBLIC_API_BASE_URL`, `NEXT_PUBLIC_OCR_BASE_URL`, `NEXT_PUBLIC_AIMODELS_BASE_URL`를 같은 주소로 맞추면 됩니다.

**누구를 위한 서비스인가요?**  
- **보호자**: 자녀의 HTP 그림·그림일기를 업로드해 AI 해석과 T-Score, 추천 사항 확인  
- **상담사·교육기관**: 아동 심리 지원 시 참고 자료로 활용  
- **개발자**: 그림 분석·OCR·RAG 파이프라인 참고 및 확장

---

## 기능

- **메인/소개**: 랜딩, 솔루션 소개
- **인증**: 로그인, 회원가입, 카카오·구글 OAuth 콜백
- **그림 분석**: HTP 4장 업로드 → 분석·해석 결과·T-Score·PDF 리포트
- **그림일기 OCR**: 그림일기 이미지 업로드 → OCR 결과 표시 (스트리밍 지원)
- **커뮤니티**: 게시글 목록/상세/작성/수정, 댓글, 좋아요, 북마크
- **상담/지원**: 상담 안내, 지역아동센터·심리상담·발달재활 기관 데이터 (JSON)
- **마이페이지**: 설정, 프로필

---

## 기술 스택

- **Next.js 16**, **React 19**
- **Tailwind CSS 4**, **Radix UI**, **shadcn/ui** 스타일 컴포넌트
- **React Hook Form**, **Zod** (폼·검증)
- **Recharts**, **jsPDF**, **@react-pdf/renderer**, **html2canvas** (차트·PDF·캡처)
- **date-fns**, **lucide-react**, **next-themes** (날짜·아이콘·다크모드)
- **ai** (Vercel AI SDK), **sonner** (토스트)

---

## 디렉터리 구조

| 경로 | 설명 |
|------|------|
| `app/` | App Router 페이지·레이아웃 |
| `app/page.tsx` | 메인 |
| `app/login/`, `app/signup/` | 로그인, 회원가입 |
| `app/login/kakao-callback/`, `app/login/google-callback/` | OAuth 콜백 |
| `app/analysis/` | 그림 분석 (업로드·분석 중·결과·PDF) |
| `app/diary-ocr/` | 그림일기 OCR |
| `app/community/` | 커뮤니티 목록·상세·글쓰기 |
| `app/counseling/` | 상담 안내 |
| `app/mypage/`, `app/mypage/settings/` | 마이페이지, 설정 |
| `app/solutions/` | 솔루션 소개 |
| `app/api/` | API 라우트 (openapi-fetch, image-proxy, ocr, kakao-map-key 등) |
| `components/` | 공통·UI 컴포넌트 (video-hero, ui/* 등) |
| `lib/` | 유틸 (utils.ts 등) |
| `hooks/` | use-toast, use-mobile 등 |
| `styles/globals.css` | 전역 스타일 |
| `json/` | 지역아동센터·심리상담·발달재활 기관 통합 JSON 데이터 |
| `public/` | 정적 자산 (이미지, favicon 등) |

---

## 사전 요구사항

- **Node.js 20+** (LTS 권장)
- **npm** 또는 **pnpm** (프로젝트에 `pnpm-lock.yaml` 포함)

---

## 빌드 및 실행

### 로컬 개발

```bash
npm install
# 또는 pnpm install (pnpm-lock.yaml 사용 시)
npm run dev
```

개발 서버는 기본적으로 **http://localhost:3000** 에서 실행됩니다.

### 프로덕션 빌드·실행

```bash
npm run build
npm run start
```

### Docker (Dockerfile 제공 시)

```bash
docker build -t aimind-frontend .
docker run -p 3000:3000 aimind-frontend
```

- Next.js standalone 출력으로 프로덕션 최적화
- `node:20-alpine` 기반 경량 이미지, `nextjs` 사용자로 비 root 실행

---

## 환경 변수

빌드 시 또는 런타임에 아래 **`NEXT_PUBLIC_*`** 변수를 설정합니다. `.env.local` 또는 배포 환경에 넣으면 됩니다.

| 변수 | 설명 | 기본값(미설정 시) |
|------|------|-------------------|
| `NEXT_PUBLIC_API_BASE_URL` | 백엔드 API 주소 | `http://localhost:8000` |
| `NEXT_PUBLIC_OCR_BASE_URL` | 그림일기 OCR 서비스 주소 | `http://localhost:8090` |
| `NEXT_PUBLIC_AIMODELS_BASE_URL` | AiModels 서비스 주소 (그림 분석·챗봇) | `http://localhost:8080` |
| `NEXT_PUBLIC_KAKAO_MAP_APP_KEY` 등 | 카카오맵 API 키 (상담 지도 등). 코드에서 여러 키 이름 후보 사용 | — |

상담 지도용 카카오맵 키는 `app/api/kakao-map-key/route.ts`에서 후보 변수명을 확인할 수 있습니다.

---

## 참고

- **json/** 폴더: 지역아동센터·아동 심리상담·발달재활 기관 등 전국/지역별 통합 JSON 데이터 (지도·상담 안내 연동).

---

## 라이선스 및 기여

라이선스는 본 저장소의 `LICENSE` 파일을 참고해 주세요. 버그 제보·기능 제안·Pull Request는 이 저장소의 이슈/PR로 남겨 주시면 됩니다.
