---
title: Nextjs 구조
date: 2024-09-08
time: 07:38
tags: []
---

# Nextjs 구조

NextJS 웹앱의 권장 폴더 구조는 다음과 같습니다:

```
my-nextjs-app/
├── app/
│   ├── api/
│   ├── (routes)/
│   ├── layout.tsx
│   └── page.tsx
├── components/
├── lib/
├── public/
├── styles/
├── types/
├── utils/
├── .env.local
├── .gitignore
├── next.config.js
├── package.json
└── tsconfig.json
```

주요 폴더 및 파일의 설명:

- `app/`: 라우팅 및 페이지 컴포넌트
- `api/`: API 라우트
- `(routes)/`: 그룹화된 라우트
- `components/`: 재사용 가능한 UI 컴포넌트
- `lib/`: 외부 라이브러리 및 API
- `public/`: 정적 파일
- `styles/`: 전역 스타일 및 CSS 모듈
- `types/`: TypeScript 타입 정의
- `utils/`: 유틸리티 함수

이 구조는 NextJS 13 이상의 App Router를 기반으로 합니다. 프로젝트의 규모와 요구사항에 따라 조정할 수 있습니다.
