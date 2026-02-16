# AiMind Frontend (Docker)

AiMind 웹 클라이언트 **Next.js 16** 애플리케이션의 Docker 빌드용 폴더입니다.

## 기술 스택

- **Next.js 16**, React 19
- **Tailwind CSS**, Radix UI
- **React Hook Form**, Zod
- **Recharts**, jsPDF, html2canvas 등 (차트·PDF·캡처)

## 빌드 및 실행

```bash
# 이미지 빌드 (multi-stage: deps → builder → runner)
docker build -t aimind-frontend .

# 실행 (포트 3000)
docker run -p 3000:3000 aimind-frontend
```

## Docker 빌드 특징

- **Standalone** 출력으로 프로덕션 최적화
- `node:20-alpine` 기반 경량 이미지
- `nextjs` 사용자로 비 root 실행

## 로컬 개발

```bash
npm install
npm run dev   # 개발 서버 (기본 http://localhost:3000)
npm run build
npm run start # 프로덕션
```

## 환경 변수

백엔드/OCR/AI 모델 API URL 등은 빌드 시 또는 런타임에 `NEXT_PUBLIC_*` 형태로 설정합니다.
