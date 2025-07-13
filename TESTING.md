# 🧪 ON-AIR-mate 백엔드 테스트 가이드

> 개발, 배포, 운영 단계별 테스트 방법과 품질 보증 가이드

## 📋 목차

1. [🔍 개발 환경 테스트](#-개발-환경-테스트)
2. [🚀 배포 전 테스트](#-배포-전-테스트)
3. [🌐 프로덕션 테스트](#-프로덕션-테스트)
4. [🔄 자동화된 테스트](#-자동화된-테스트)
5. [📊 성능 테스트](#-성능-테스트)
6. [🛡️ 보안 테스트](#️-보안-테스트)
7. [🆘 장애 복구 테스트](#-장애-복구-테스트)

---

## 🔍 개발 환경 테스트

### **로컬 개발 서버 테스트**

#### 1. 기본 서버 시작 테스트
```bash
# 개발 서버 시작
npm run dev

# 예상 출력:
# Server running at http://localhost:3000
# API Docs available at http://localhost:3000/api-docs
# Health check at http://localhost:3000/health

# ✅ 성공 기준: 3000번 포트에서 정상 실행
```

#### 2. 헬스체크 테스트
```bash
# 로컬 헬스체크
curl http://localhost:3000/health

# ✅ 예상 응답:
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2025-01-15T10:30:00.000Z",
    "uptime": 25.123,
    "environment": "development"
  },
  "error": null
}
```

#### 3. API 문서 접근 테스트
```bash
# Swagger UI 접근 확인
curl -I http://localhost:3000/api-docs

# ✅ 예상 응답: HTTP/1.1 200 OK
# 브라우저에서도 확인: http://localhost:3000/api-docs
```

#### 4. 데이터베이스 연결 테스트
```bash
# Prisma 연결 확인
npx prisma db pull

# ✅ 성공 시: 스키마 파일 업데이트
# ❌ 실패 시: 연결 정보 확인 필요

# 데이터베이스 스키마 확인
npx prisma studio
# 브라우저에서 http://localhost:5555 접근 가능
```

### **코드 품질 테스트**

#### 1. 린팅 테스트
```bash
# ESLint 체크
npm run lint

# ✅ 성공 기준: 에러 0개
# ❌ 실패 시: 에러 수정 후 재실행
```

#### 2. 포맷팅 테스트
```bash
# Prettier 체크
npm run check

# ✅ 성공 기준: 모든 파일 포맷팅 일치
# ❌ 실패 시: npm run format 실행
```

#### 3. TypeScript 컴파일 테스트
```bash
# 빌드 테스트
npm run build

# ✅ 성공 기준: dist/ 폴더 생성, 에러 없음
# 생성 파일 확인
ls -la dist/
```

---

## 🚀 배포 전 테스트

### **배포 전 필수 체크리스트**

```bash
# 🔍 1단계: 로컬 빌드 확인
npm run format    # 코드 포맷팅
npm run build     # TypeScript 컴파일
npm start         # 프로덕션 모드 실행

# 🔍 2단계: 환경변수 확인
grep -E "(DATABASE_URL|JWT_SECRET)" .env
# ✅ 모든 필수 환경변수 설정 확인

# 🔍 3단계: Git 상태 확인
git status        # 변경사항 확인
git log --oneline -5  # 최근 커밋 확인
```

### **커밋 전 테스트**

```bash
# 자동화된 프리커밋 체크
npm run format && npm run build && echo "✅ 커밋 준비 완료"

# 개별 확인
npm run format    # ✅ 코드 포맷팅
npm run check     # ✅ 포맷팅 검증
npm run build     # ✅ 빌드 성공
```

### **GitHub Actions 로컬 테스트**

```bash
# Act를 사용한 로컬 GitHub Actions 테스트 (선택사항)
# Act 설치: https://github.com/nektos/act

# 워크플로우 로컬 실행
act -j test

# 전체 워크플로우 시뮬레이션
act push
```

---

## 🌐 프로덕션 테스트

### **배포 후 즉시 테스트**

#### 1. 서버 접속 테스트
```bash
# 외부 접속 테스트
curl -I http://15.164.176.168:3000/health

# ✅ 예상 응답: HTTP/1.1 200 OK
# ❌ 실패 시: PM2 상태 확인 필요
```

#### 2. PM2 프로세스 상태 확인
```bash
# EC2 접속
ssh -i your-key.pem ec2-user@15.164.176.168

# PM2 상태 확인
pm2 status

# ✅ 예상 출력:
┌─────┬──────────────────┬─────────────┬──────┬───────┬──────────┬─────────┐
│ id  │ name             │ mode        │ ↺    │ status│ cpu      │ memory  │
├─────┼──────────────────┼─────────────┼──────┼───────┼──────────┼─────────┤
│ 0   │ onairmate-api    │ fork        │ 0    │ online│ 0%       │ 45.2mb  │
└─────┴──────────────────┴─────────────┴──────┴───────┴──────────┴─────────┘
```

#### 3. 애플리케이션 로그 확인
```bash
# 실시간 로그 확인
pm2 logs onairmate-api --lines 50

# ✅ 정상 로그 예시:
# Server running at http://localhost:3000
# API Docs available at http://localhost:3000/api-docs
# Health check at http://localhost:3000/health

# ❌ 에러 로그 확인
pm2 logs onairmate-api --err
```

### **기능별 테스트**

#### 1. 헬스체크 엔드포인트
```bash
# 외부에서 헬스체크
curl -X GET http://15.164.176.168:3000/health \
  -H "Content-Type: application/json" | jq

# ✅ 예상 응답:
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2025-01-15T10:30:00.000Z",
    "uptime": 1234.567,
    "environment": "production"
  }
}
```

#### 2. API 문서 접근
```bash
# Swagger UI 접근 테스트
curl -I http://15.164.176.168:3000/api-docs

# 브라우저 테스트
# http://15.164.176.168:3000/api-docs 접속 확인
```

#### 3. CORS 테스트
```bash
# CORS 헤더 확인
curl -H "Origin: https://onairmate.vercel.app" \
     -H "Access-Control-Request-Method: POST" \
     -H "Access-Control-Request-Headers: X-Requested-With" \
     -X OPTIONS \
     http://15.164.176.168:3000/api/test

# ✅ 예상 헤더:
# Access-Control-Allow-Origin: https://onairmate.vercel.app
# Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
```

#### 4. 인증 엔드포인트 테스트 (개발 완료 시)
```bash
# JWT 토큰 없이 보호된 엔드포인트 접근
curl -X GET http://15.164.176.168:3000/protected

# ✅ 예상 응답: 401 Unauthorized
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "인증이 필요합니다."
  }
}
```

---

## 🔄 자동화된 테스트

### **GitHub Actions 테스트 확인**

#### 1. 워크플로우 실행 확인
```bash
# GitHub에서 Actions 탭 확인
# 최근 워크플로우 상태: ✅ 성공 / ❌ 실패

# 로컬에서 워크플로우 로그 확인 방법:
# 1. GitHub Actions 탭 클릭
# 2. 최근 워크플로우 클릭
# 3. 각 단계별 로그 확인
```

#### 2. 배포 성공 확인 단계
```yaml
# 워크플로우 성공 시 자동 실행되는 검증
1. ✅ ESLint 검사 통과
2. ✅ TypeScript 빌드 성공
3. ✅ EC2 SSH 접속 성공
4. ✅ Git pull 성공
5. ✅ npm ci 성공
6. ✅ npm run build 성공
7. ✅ PM2 재시작 성공
8. ✅ 헬스체크 응답 확인
```

### **배포 실패 시 롤백 테스트**

```bash
# 수동 롤백 프로세스
ssh -i your-key.pem ec2-user@15.164.176.168

# 이전 커밋으로 롤백
cd /home/ec2-user/on-air-mate
git log --oneline -5
git reset --hard PREVIOUS_COMMIT_HASH
npm run build
pm2 restart onairmate-api

# 롤백 확인
curl http://localhost:3000/health
```

---

## 📊 성능 테스트

### **기본 성능 측정**

#### 1. 응답 시간 테스트
```bash
# 헬스체크 응답 시간 측정
curl -w "응답시간: %{time_total}초\n" -o /dev/null -s http://15.164.176.168:3000/health

# ✅ 목표: 1초 이내
# ⚠️ 주의: 3초 이상 시 성능 이슈
```

#### 2. 동시 접속 테스트
```bash
# Apache Bench 사용 (설치 필요)
sudo yum install httpd-tools -y  # Amazon Linux

# 동시 접속 테스트 (10명이 100회 요청)
ab -n 100 -c 10 http://15.164.176.168:3000/health

# ✅ 목표 지표:
# - Time per request: < 1000ms
# - Failed requests: 0
# - Requests per second: > 50
```

#### 3. 메모리 사용량 모니터링
```bash
# PM2 메모리 모니터링
pm2 monit

# 시스템 메모리 확인
free -h

# ✅ 정상 범위:
# - 애플리케이션 메모리: < 200MB
# - 시스템 메모리 사용률: < 80%
```

### **장기 안정성 테스트**

```bash
# 24시간 연속 모니터링 스크립트
#!/bin/bash
# health_monitor.sh

while true; do
  RESPONSE=$(curl -s -w "%{http_code}" http://15.164.176.168:3000/health)
  TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')
  
  if [[ "$RESPONSE" == *"200" ]]; then
    echo "[$TIMESTAMP] ✅ Health Check OK"
  else
    echo "[$TIMESTAMP] ❌ Health Check FAILED: $RESPONSE"
  fi
  
  sleep 60  # 1분마다 체크
done
```

---

## 🛡️ 보안 테스트

### **기본 보안 검증**

#### 1. 포트 스캔 테스트
```bash
# 열린 포트 확인
nmap -p 1-1000 15.164.176.168

# ✅ 예상 결과:
# 22/tcp  open  ssh
# 3000/tcp open  ntop-http

# ❌ 불필요한 포트가 열려있으면 보안그룹 수정 필요
```

#### 2. SSL/TLS 테스트 (HTTPS 적용 시)
```bash
# SSL 인증서 확인 (HTTPS 설정 후)
openssl s_client -connect yourdomain.com:443 -servername yourdomain.com

# SSL Labs 테스트
# https://www.ssllabs.com/ssltest/
```

#### 3. 헤더 보안 검증
```bash
# 보안 헤더 확인
curl -I http://15.164.176.168:3000/

# ✅ 확인해야 할 헤더:
# X-Content-Type-Options: nosniff
# X-Frame-Options: DENY
# X-XSS-Protection: 1; mode=block
```

### **취약점 스캔**

```bash
# npm 보안 감사
npm audit

# ✅ 목표: 0 vulnerabilities
# ❌ 취약점 발견 시:
npm audit fix

# 수동 보안 업데이트
npm update
```

---

## 🆘 장애 복구 테스트

### **장애 시뮬레이션 테스트**

#### 1. 프로세스 강제 종료 테스트
```bash
# PM2 프로세스 강제 종료
pm2 stop onairmate-api

# 외부 접속 확인 (실패해야 정상)
curl http://15.164.176.168:3000/health
# ❌ 예상: Connection refused

# 자동 재시작 테스트
pm2 start onairmate-api

# 복구 확인
curl http://15.164.176.168:3000/health
# ✅ 예상: 정상 응답
```

#### 2. 서버 재부팅 테스트
```bash
# 서버 재부팅
sudo reboot

# 재접속 후 자동 시작 확인
ssh -i your-key.pem ec2-user@15.164.176.168
pm2 status

# ✅ 예상: onairmate-api가 자동으로 online 상태
```

#### 3. 메모리 부족 시뮬레이션
```bash
# 메모리 사용량 급증 시뮬레이션 (개발환경만)
# stress 도구 설치
sudo yum install stress -y

# 메모리 스트레스 테스트 (주의!)
stress --vm 1 --vm-bytes 512M --timeout 60s

# PM2 메모리 재시작 확인
pm2 logs onairmate-api
```

### **데이터베이스 연결 장애 테스트**

```bash
# 잘못된 DB 정보로 테스트 (개발환경)
export DATABASE_URL="mysql://wrong:info@localhost:3306/wrong"
npm start

# ✅ 예상: 적절한 에러 메시지와 함께 실행 실패
# ❌ 서버가 시작되면 에러 핸들링 개선 필요
```

---

## 📋 테스트 체크리스트

### **🔍 개발 시 필수 테스트**
- [ ] `npm run dev` 정상 실행
- [ ] `npm run format` 통과
- [ ] `npm run build` 성공
- [ ] 로컬 헬스체크 정상 응답
- [ ] API 문서 접근 가능
- [ ] 데이터베이스 연결 확인

### **🚀 배포 전 필수 테스트**
- [ ] 모든 개발 테스트 통과
- [ ] 환경변수 완전성 확인
- [ ] Git 상태 정리 (unstaged 변경사항 없음)
- [ ] 커밋 메시지 컨벤션 준수
- [ ] 프로덕션 빌드 테스트

### **🌐 배포 후 필수 테스트**
- [ ] GitHub Actions 워크플로우 성공
- [ ] 외부 헬스체크 정상 응답
- [ ] PM2 프로세스 정상 상태
- [ ] 애플리케이션 로그 확인
- [ ] API 문서 외부 접근 가능
- [ ] 메모리/CPU 사용량 정상 범위

### **🔄 정기 테스트 (주간)**
- [ ] 성능 테스트 (응답시간, 동시접속)
- [ ] 보안 스캔 (npm audit, 포트 스캔)
- [ ] 장애 복구 테스트 (프로세스 재시작)
- [ ] 로그 로테이션 확인
- [ ] 자동 백업 동작 확인

---

## 🎯 테스트 자동화 스크립트

### **종합 테스트 스크립트**

```bash
#!/bin/bash
# comprehensive_test.sh

echo "🧪 ON-AIR-mate 종합 테스트 시작"

# 색상 정의
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'

# 테스트 함수
test_command() {
    echo -e "${YELLOW}테스트 중: $1${NC}"
    if eval $2; then
        echo -e "${GREEN}✅ $1 성공${NC}"
        return 0
    else
        echo -e "${RED}❌ $1 실패${NC}"
        return 1
    fi
}

# 개발 환경 테스트
echo "🔍 개발 환경 테스트"
test_command "코드 포맷팅" "npm run format"
test_command "TypeScript 빌드" "npm run build"
test_command "로컬 서버 시작" "timeout 10s npm start &"
sleep 5
test_command "로컬 헬스체크" "curl -f http://localhost:3000/health"

# 프로덕션 테스트 (서버 주소 변경 필요)
echo "🌐 프로덕션 테스트"
test_command "외부 헬스체크" "curl -f http://15.164.176.168:3000/health"
test_command "API 문서 접근" "curl -f -I http://15.164.176.168:3000/api-docs"

# 보안 테스트
echo "🛡️ 보안 테스트"
test_command "npm 보안 감사" "npm audit --audit-level=high"

echo "🎉 테스트 완료!"
```

### **CI/CD 테스트 스크립트**

```bash
#!/bin/bash
# ci_test.sh

# GitHub Actions에서 사용할 테스트 스크립트
set -e

echo "🤖 CI/CD 테스트 시작"

# 1. 코드 품질 검사
npm run check
npm run lint

# 2. 빌드 테스트
npm run build

# 3. 기본 기능 테스트
npm start &
SERVER_PID=$!
sleep 10

# 헬스체크 테스트
curl -f http://localhost:3000/health || {
    echo "❌ 헬스체크 실패"
    kill $SERVER_PID
    exit 1
}

# 서버 종료
kill $SERVER_PID

echo "✅ 모든 CI/CD 테스트 통과"
```

---

## 🚨 테스트 실패 시 대응

### **빌드 실패**
1. TypeScript 에러 확인 및 수정
2. 의존성 충돌 해결 (`npm install`)
3. 환경변수 설정 확인

### **배포 실패**
1. GitHub Actions 로그 확인
2. SSH 키 및 권한 확인
3. EC2 상태 및 디스크 공간 확인

### **성능 저하**
1. PM2 메모리 사용량 확인
2. 데이터베이스 연결 풀 최적화
3. 불필요한 프로세스 정리

---

**📞 문제 발생 시**: README.md의 트러블슈팅 섹션 참조
**📚 추가 문서**: TROUBLESHOOTING.md
**🔧 운영 가이드**: PM2 명령어 및 AWS 콘솔 활용

**Happy Testing! 🧪✨**