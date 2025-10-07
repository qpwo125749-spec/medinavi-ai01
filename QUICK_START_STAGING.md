# ⚡ 스테이징 배포 빠른 시작 가이드

48시간 내 스테이징 배포를 위한 핵심 명령어 모음입니다.

## 📋 변경된 파일 목록

### 새로 추가된 파일

```
app/
├── core/
│   └── security.py                    # API 키 인증 시스템
└── utils/
    └── metrics.py                     # Prometheus 메트릭

docker/
└── nginx/
    └── conf.d/
        └── app.conf                   # Nginx 설정 (HTTPS + 보안 헤더)

scripts/
├── certbot_init.sh                    # SSL 인증서 초기 발급
├── certbot_renew.sh                   # SSL 인증서 갱신
├── generate_api_key.py                # API 키 생성
└── manage_api_keys.py                 # API 키 관리

tests/
└── load/
    ├── k6_smoke_test.js               # k6 로드 테스트
    └── README.md                      # 로드 테스트 가이드

docs/
├── DEPLOYMENT.md                      # 배포 가이드 (업데이트)
└── STAGING_DEPLOYMENT_GUIDE.md        # 스테이징 배포 상세 가이드

.env.staging.example                   # 스테이징 환경 변수 예시
docker-compose.staging.yml             # 스테이징 Docker Compose
QUICK_START_STAGING.md                 # 이 파일
```

### 수정된 파일

```
app/main.py                            # Rate limiting, 메트릭, API 키 지원
requirements.txt                       # prometheus-client, sentry-sdk 추가
```

---

## 🚀 실행 명령어 (순서대로)

### 1. 환경 준비

```bash
# 저장소 클론 (이미 완료된 경우 생략)
git clone https://github.com/qpwo125749-spec/medinavi-ai.git
cd medinavi-ai

# 환경 변수 설정
cp .env.staging.example .env.staging
nano .env.staging  # DOMAIN, CONTACT_EMAIL, SECRET_KEY 설정

# 스크립트 실행 권한 부여
chmod +x scripts/certbot_init.sh
chmod +x scripts/certbot_renew.sh
```

### 2. 서비스 시작 (HTTP)

```bash
# API와 Proxy 시작
docker compose -f docker-compose.staging.yml up -d api proxy

# 로그 확인
docker compose -f docker-compose.staging.yml logs -f

# 상태 확인
docker compose -f docker-compose.staging.yml ps
```

### 3. SSL 인증서 발급

```bash
# 자동 발급 (권장)
./scripts/certbot_init.sh api-staging.medinavi.com admin@medinavi.com

# 또는 수동 발급
sed -i "s/__DOMAIN__/api-staging.medinavi.com/g" docker/nginx/conf.d/app.conf
docker compose -f docker-compose.staging.yml run --rm certbot certonly \
  --webroot \
  --webroot-path=/var/www/certbot \
  --email admin@medinavi.com \
  --agree-tos \
  --no-eff-email \
  -d api-staging.medinavi.com
docker compose -f docker-compose.staging.yml restart proxy
```

### 4. HTTPS 접속 확인

```bash
# Health Check
curl https://api-staging.medinavi.com/health

# API 문서
curl https://api-staging.medinavi.com/docs

# 메트릭
curl https://api-staging.medinavi.com/metrics
```

### 5. API 키 생성

```bash
# 프로덕션 키 생성
python scripts/generate_api_key.py \
  --name "Production Client" \
  --rate-limit 100 \
  --expires-days 365

# 키 목록 확인
python scripts/manage_api_keys.py list
```

### 6. API 키 테스트

```bash
# API 키로 요청
curl -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{"query": "두통", "language": "ko", "max_results": 5}' \
  https://api-staging.medinavi.com/api/v1/medical/search
```

### 7. 로드 테스트

```bash
# k6 설치 (macOS)
brew install k6

# k6 설치 (Linux)
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
echo "deb https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6

# 스모크 테스트 실행 (100 RPS, 1분)
k6 run tests/load/k6_smoke_test.js \
  -e BASE_URL=https://api-staging.medinavi.com \
  -e API_KEY=YOUR_API_KEY_HERE
```

### 8. 자동 갱신 크론 설정

```bash
# 크론탭 편집
crontab -e

# 다음 라인 추가 (하루 2회: 자정과 정오)
0 0,12 * * * /path/to/medinavi-ai/scripts/certbot_renew.sh >> /var/log/certbot-renew.log 2>&1
```

---

## 🔍 주요 엔드포인트

### API 엔드포인트

| 엔드포인트 | 설명 | 인증 |
|-----------|------|------|
| `GET /` | 루트 (API 정보) | 선택 |
| `GET /health` | Health Check | 선택 |
| `GET /metrics` | Prometheus 메트릭 | 선택 |
| `GET /docs` | Swagger UI | 선택 |
| `GET /redoc` | ReDoc | 선택 |
| `POST /api/v1/medical/search` | 의료 정보 검색 | 선택 |
| `POST /api/v1/medical/drug-interactions` | 약물 상호작용 | 선택 |
| `POST /api/v1/medical/health-analysis` | 건강 상태 분석 | 선택 |
| `POST /api/v1/nutrition/analyze` | 영양 성분 분석 | 선택 |

### Rate Limit 헤더

모든 응답에 포함:
- `X-RateLimit-Limit`: 분당 요청 제한
- `X-RateLimit-Remaining`: 남은 요청 수
- `X-RateLimit-Reset`: 리셋 시간 (Unix timestamp)

---

## 📊 모니터링

### Prometheus 메트릭

```bash
# 메트릭 확인
curl https://api-staging.medinavi.com/metrics

# 주요 메트릭
# - http_requests_total: 총 요청 수
# - http_request_duration_seconds: 응답 시간
# - http_requests_in_progress: 진행 중인 요청
# - rate_limit_exceeded_total: Rate limit 초과
# - errors_total: 에러 발생
```

### 로그 확인

```bash
# 전체 로그
docker compose -f docker-compose.staging.yml logs -f

# API 로그만
docker compose -f docker-compose.staging.yml logs -f api

# Nginx 로그만
docker compose -f docker-compose.staging.yml logs -f proxy

# 실시간 접근 로그
tail -f logs/nginx/access.log
```

---

## 🛠️ 서비스 관리

### 시작/중지/재시작

```bash
# 전체 시작
docker compose -f docker-compose.staging.yml start

# 전체 중지
docker compose -f docker-compose.staging.yml stop

# 전체 재시작
docker compose -f docker-compose.staging.yml restart

# API만 재시작
docker compose -f docker-compose.staging.yml restart api

# Proxy만 재시작
docker compose -f docker-compose.staging.yml restart proxy
```

### 업데이트

```bash
# 코드 업데이트
git pull

# 이미지 재빌드
docker compose -f docker-compose.staging.yml build api

# 서비스 재시작 (다운타임 최소화)
docker compose -f docker-compose.staging.yml up -d --no-deps --build api
```

---

## 🔐 보안 체크리스트

배포 전 확인:

- [ ] `.env.staging`에 강력한 `SECRET_KEY` 설정
- [ ] `ALLOWED_ORIGINS`에 스테이징 도메인만 포함
- [ ] DNS A 레코드 설정 완료
- [ ] 포트 80, 443만 방화벽 오픈
- [ ] SSL 인증서 발급 완료
- [ ] HTTPS 접속 확인
- [ ] Rate limiting 동작 확인
- [ ] API 키 생성 및 테스트
- [ ] 로드 테스트 통과
- [ ] 인증서 자동 갱신 크론 설정

---

## 🚨 트러블슈팅 빠른 참조

### 인증서 발급 실패
```bash
dig +short api-staging.medinavi.com  # DNS 확인
sudo netstat -tulpn | grep :80        # 포트 확인
docker compose -f docker-compose.staging.yml logs certbot  # 로그 확인
```

### 502 Bad Gateway
```bash
docker compose -f docker-compose.staging.yml ps api        # 상태 확인
docker compose -f docker-compose.staging.yml logs api      # 로그 확인
docker compose -f docker-compose.staging.yml restart api   # 재시작
```

### Rate Limit 테스트
```bash
# 연속 요청으로 rate limit 확인
for i in {1..70}; do
  curl -H "X-API-Key: YOUR_KEY" https://api-staging.medinavi.com/health
  echo " - Request $i"
done
```

---

## 📚 추가 문서

- **상세 배포 가이드**: `docs/STAGING_DEPLOYMENT_GUIDE.md`
- **전체 배포 문서**: `docs/DEPLOYMENT.md`
- **API 사용 예시**: `docs/API_EXAMPLES.md`
- **로드 테스트 가이드**: `tests/load/README.md`

---

## 🎯 성공 기준

배포가 성공적으로 완료되면:

✅ HTTPS로 접속 가능  
✅ API 문서 (`/docs`) 접근 가능  
✅ Health Check 정상 응답  
✅ 로드 테스트 통과 (100 RPS, p95 < 500ms)  
✅ Rate limiting 동작  
✅ API 키 인증 동작  
✅ 메트릭 수집 가능  
✅ 인증서 자동 갱신 설정  

---

## 🚀 다음 단계

1. **베타 사용자 온보딩**
   - API 키 발급
   - 문서 공유
   - 피드백 수집

2. **모니터링 강화**
   - Grafana Cloud 연동
   - Sentry 에러 트래킹
   - 알림 설정

3. **성능 최적화**
   - 캐시 튜닝
   - 워커 수 조정
   - 데이터베이스 연동

4. **프로덕션 준비**
   - 부하 테스트 확대
   - 백업 자동화
   - 재해 복구 계획

---

**문의사항이 있으면 GitHub Issues에 등록해주세요!**
