## 소개

ON-AIR-mate Backend(node.js) 레포지토리입니다.

<!-- ## 주요 기능

-
-
- -->

## 기술 스택

- **Language**: TypeScript
- **Runtime**: Node.js
- **Framework**: Express.js
- **ORM**: Prisma

### **데이터베이스**

- MySQL

### **개발 도구**

- **Linting**: ESLint
- **Formatting**: Prettier

## 프로젝트 구조

```
.
├── .github/
├── .vscode/           # VS Code 설정
├── prisma/
│   └── schema.prisma
├── src/
│   ├── auth/          # 인증 관련
│   ├── middleware/    # 미들웨어
│   ├── utils/         # 유틸리티 함수
│   └── app.ts         # 메인 애플리케이션
├── .env.example       # 환경변수 예시
├── .prettierrc.json   # Prettier 설정
├── eslint.config.js   # ESLint 설정
├── package.json
└── tsconfig.json
```

## 🚀 프로젝트 실행

### **1. 레포지토리 클론 및 초기 설정**

```bash
# 1. 레포지토리 클론
git clone https://github.com/ON-AIR-mate/Node.js.git
cd Node.js

# 2. 패키지 설치
npm install

# 3. 환경변수 설정
cp .env.example .env
# .env 파일을 열어서 필요한 값들을 설정하세요
```

### **2. 개발 서버 실행**

```bash
# 개발 서버 시작 (파일 변경 시 자동 재시작)
npm run dev
```

서버가 정상 실행되면:
- **API 서버**: http://localhost:3000
- **Swagger 문서**: http://localhost:3000/api-docs

### **3. 코드 품질 관리**

```bash
# 코드 포매팅 + ESLint 수정 (한 번에!)
npm run format

# 코드 체크만 (수정하지 않음)
npm run check

# ESLint만 실행
npm run lint

# TypeScript 컴파일
npm run build

# 프로덕션 실행
npm start
```

## 📝 개발 가이드

### **VS Code 설정 (권장)**

프로젝트에 VS Code 설정이 포함되어 있어 **파일 저장 시 자동으로 포매팅**됩니다:

1. **Prettier 확장 설치**: `esbenp.prettier-vscode`
2. **ESLint 확장 설치**: `dbaeumer.vscode-eslint`
3. 파일 저장 시 자동 포매팅 적용 ✨

### **코드 작성 전 필수 체크리스트**

```bash
# 1. 최신 코드 가져오기
git pull origin main

# 2. 의존성 업데이트 확인
npm install

# 3. 코드 품질 확인
npm run format

# 4. 개발 서버 실행
npm run dev
```

### **커밋 전 필수 체크리스트**

```bash
# 1. 코드 포매팅 및 린팅
npm run format

# 2. 타입 체크 및 빌드 확인
npm run build

# 3. 서버 정상 동작 확인
npm run dev
```

## 🛠️ 추가 설정

### **환경변수 (.env)**

```env
# 서버 설정
PORT=3000

# JWT 설정
JWT_SECRET=your_jwt_secret_here

# 데이터베이스 설정
DATABASE_URL="mysql://root:password@localhost:3306/on-air-mate"
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=password
DB_NAME=on-air-mate
DB_PORT=3306
```

### **데이터베이스 마이그레이션**

```bash
# Prisma 마이그레이션 실행
npm run db:migrate

# 또는 수동으로
npx prisma migrate dev --name migration_name
```

## 📋 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `npm install` | 의존성 설치 |
| `npm run dev` | 개발 서버 실행 |
| `npm run format` | 코드 포매팅 + ESLint 수정 |
| `npm run check` | 코드 품질 검사 (수정 안함) |
| `npm run lint` | ESLint만 실행 |
| `npm run build` | TypeScript 컴파일 |
| `npm start` | 프로덕션 실행 |

## 코드 컨벤션

### 🧑‍💻 **코드 포맷**

- 들여쓰기는 space 2로 설정
- single quote(작은 따옴표) 사용
- **자동 포매팅**: `npm run format` 실행!

### 📜 **TypeScript 관련**

- ON AIR mate Node.js에서는 typescript를 사용
- 특정한 상황 외에는 type보다 `interface`를 사용해주세요.

### 🌳 브랜치 전략

- **GitHub Flow 방식**
    
    ```sql
    main
     │
     ├───➤ feature/login-api
     │         │
     │         └───(PR & Merge)───➤
     │
     ├───➤ fix/header-overlap
     │         │
     │         └───(PR & Merge)───➤
     │
     ├───➤ refactor/user-model
     │         │
     │         └───(PR & Merge)───➤
     │
     ├───➤ docs/readme-update
     │         │
     │         └───(PR & Merge)───➤
     │
     └───➤ hotfix/image-crash
               │
               └───(PR & Merge)───➤
    ```
    
    1. `main` 브랜치에서 분기
    2. `feature/`, `fix/`, `hotfix/` 등 각자 브랜치를 생성해서 작업
    3. Pull Request 생성 및 코드 리뷰 진행
    4. 리뷰 승인 후 `main`에 머지
    5. 배포 가능 상태 유지

### **브랜치 명 규칙**

| 유형 | 접두어 | 예시 |
| --- | --- | --- |
| 기능 추가 | `feature/` | `feature/signup-form` |
| 버그 수정 | `fix/` | `fix/login-error` |
| 문서 변경 | `docs/` | `docs/readme-update` |
| 리팩토링 | `refactor/` | `refactor/user-service` |
| 테스트 관련 | `test/` | `test/login-validation` |
| 기타 작업 | `chore/` | `chore/webpack-config` |

### 📏 **Naming(네이밍 규칙)**

- **`변수`**는 camelCase 사용
- **`상수`**는 대문자  + SNAKE_CASE 사용
- 가급적이면 약어를 피해주세요!
- 페이지 등 정의할 땐 **`function`, 그 외는 `화살표 함수`를 사용해주세요.**

### **📌 commit 하기**

- **commit 형식**
    - [feat]: 새로운 기능 추가
    - [fix]: 에러, 버그 수정
    - [docs]: 문서 수정, README 수정
    - [refactor]: 코드 리펙토링
    - [test]: 테스트 코드, 리펙토링 테스트 코드 추가
    - [chore]: 기타 작업(약간의 코드 수정, 빌드 업무 수정, 패키지 매니저 수정 등..)

```bash
git add 파일명
git commit -m "[feat(닉네임)] 새로운 기능 추가"
git push origin 브랜치명
```

## ⚠️ 주의사항

1. **코드 작성 전 반드시 `npm run format` 실행**
2. **커밋 전 `npm run build` 로 빌드 확인**  
3. **환경변수 파일 (.env) 는 절대 커밋하지 말 것**
4. **의존성 추가 시 팀원들에게 알림**

## 🆘 문제 해결

### 자주 발생하는 문제들

**1. 모듈을 찾을 수 없다는 오류**
```bash
npm install
```

**2. 포매팅이 안 맞는다는 오류**
```bash
npm run format
```

**3. TypeScript 컴파일 오류**
```bash
npm run build
```

**4. 서버가 시작되지 않음**
- `.env` 파일 설정 확인
- 포트 3000번이 사용 중인지 확인

