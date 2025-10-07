# MediNavi AI 🏥🤖

AI-powered research assistant for medicine & nutrition (Korea-Japan-US-EU)

## 📋 개요

MediNavi AI는 의학 및 영양학 분야의 AI 기반 연구 어시스턴트입니다. 다국어 지원을 통해 한국, 일본, 미국, 유럽 지역의 의료 및 영양 정보를 제공합니다.

## ✨ 주요 기능

### 🔍 의료 정보 검색
- 질병, 증상, 치료법 등 의료 정보 검색
- 다국어 의료 용어 번역
- 신뢰도 기반 검색 결과 제공

### 💊 약물 상호작용 검사
- 복용 중인 약물 간 상호작용 확인
- 심각도별 상호작용 분류
- 음식과의 상호작용 포함

### 🏥 건강 상태 분석
- 증상 기반 건강 상태 분석
- 가능한 질환 및 위험 요인 제시
- 응급도 평가 및 권장사항 제공

### 🥗 영양 성분 분석
- 음식의 영양 성분 상세 분석
- 일일 권장량 대비 영양소 함량
- 개인 맞춤 영양 권장사항

### 🌍 다국어 지원
- 🇰🇷 한국어 (ko)
- 🇯🇵 일본어 (ja)
- 🇺🇸 영어 (en)
- 🇩🇪 독일어 (de)
- 🇫🇷 프랑스어 (fr)
- 🇪🇸 스페인어 (es)
- 🇮🇹 이탈리아어 (it)

## 🚀 빠른 시작

### 설치

```bash
# 저장소 클론
git clone https://github.com/qpwo125749-spec/medinavi-ai.git
cd medinavi-ai

# 가상환경 생성 및 활성화
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 의존성 설치
pip install -r requirements.txt

# 환경 설정
cp env.example .env
# .env 파일을 편집하여 필요한 설정을 변경하세요
```

### 실행

#### 로컬 개발
```bash
# 개발 서버 실행
python -m uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# 또는
python run.py
```

#### Docker 사용
```bash
# Docker Compose로 실행
docker-compose up -d

# 로그 확인
docker-compose logs -f

# 중지
docker-compose down
```

### API 문서

서버 실행 후 다음 URL에서 API 문서를 확인할 수 있습니다:
- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc
- **Health Check**: http://localhost:8000/health

## 📚 API 엔드포인트

### 의료 정보 검색
```
POST /api/v1/medical/search
```

### 약물 상호작용 검사
```
POST /api/v1/medical/drug-interactions
```

### 건강 상태 분석
```
POST /api/v1/medical/health-analysis
```

### 영양 성분 분석
```
POST /api/v1/nutrition/analyze
```

### 음식 데이터베이스 검색
```
POST /api/v1/nutrition/food-database/search
```

## 🧪 테스트

```bash
# 모든 테스트 실행
pytest tests/ -v

# 특정 테스트 실행
pytest tests/test_medical_api.py -v
pytest tests/test_nutrition_api.py -v
pytest tests/test_validators.py -v
pytest tests/test_cache.py -v
pytest tests/test_config.py -v

# 커버리지 포함
pytest tests/ --cov=app --cov-report=html
```

## 📁 프로젝트 구조

```
medinavi-ai/
├── app/
│   ├── api/
│   │   └── v1/
│   │       ├── medical.py          # 의료 API 엔드포인트
│   │       └── nutrition.py        # 영양 API 엔드포인트
│   ├── core/
│   │   └── config.py              # 설정 (pydantic-settings)
│   ├── models/
│   │   ├── medical.py             # 의료 데이터 모델
│   │   └── nutrition.py           # 영양 데이터 모델
│   ├── services/
│   │   ├── medical_service.py     # 의료 서비스
│   │   └── nutrition_service.py   # 영양 서비스
│   ├── utils/
│   │   ├── cache.py               # 캐싱 시스템
│   │   ├── http_client.py         # HTTP 클라이언트 (타임아웃/재시도)
│   │   ├── logger.py              # 로깅 시스템
│   │   ├── translator.py          # 다국어 번역
│   │   └── validators.py          # 입력 검증
│   └── main.py                    # FastAPI 애플리케이션
├── tests/                         # 테스트 파일
│   ├── test_medical_api.py
│   ├── test_nutrition_api.py
│   ├── test_validators.py
│   ├── test_cache.py
│   └── test_config.py
├── docs/                          # 문서
│   ├── API_EXAMPLES.md           # API 사용 예시
│   └── DEPLOYMENT.md             # 배포 가이드
├── Dockerfile                     # Docker 이미지 정의
├── docker-compose.yml            # Docker Compose 설정
├── .dockerignore                 # Docker 빌드 제외 파일
├── env.example                   # 환경 변수 예시
├── requirements.txt              # Python 의존성
├── pyproject.toml               # 프로젝트 설정
└── README.md                    # 프로젝트 문서
```

## 🔧 개발

### 주요 기능

#### 🔒 보안
- CORS 설정 및 출처 제한
- Rate Limiting (분당 요청 제한)
- 입력 검증 및 정제
- 안전한 SECRET_KEY 관리

#### ⚡ 성능 최적화
- LRU 캐싱 시스템
- HTTP 요청 타임아웃 및 재시도
- 비동기 처리
- 워커 프로세스 설정

#### 📊 로깅 및 모니터링
- 구조화된 JSON 로깅
- 요청/응답 로깅
- Health Check 엔드포인트
- 캐시 통계

#### 🌐 국제화
- 7개 언어 지원
- 에러 메시지 국제화
- 의료 용어 번역

### 코드 포맷팅
```bash
# Black으로 코드 포맷팅
black app/ tests/

# isort로 import 정렬
isort app/ tests/
```

### 린팅
```bash
# flake8로 코드 검사
flake8 app/ tests/
```

### 환경 변수

주요 환경 변수는 `env.example` 파일을 참조하세요.

```bash
# 개발 환경
DEBUG=true
LOG_LEVEL=DEBUG
ENVIRONMENT=development

# 프로덕션 환경
DEBUG=false
LOG_LEVEL=WARNING
ENVIRONMENT=production
SECRET_KEY=<강력한-랜덤-키>
```

## 🛠️ 운영/모니터링

- **관측 가능성 가이드**: `docs/OBSERVABILITY.md`
- **운영 점검 체크리스트**: `docs/OPERATIONS_CHECKLIST.md`
- **배포 가이드**: `docs/DEPLOYMENT.md`

## ☁️ Deta Space 배포

### 1) Space CLI 로그인 및 배포

```bash
# Deta Space CLI 설치 후
space login

# 리포 루트에서
space push
```

루트의 `Spacefile`이 다음 마이크로를 정의합니다:

```yaml
v: 0
micros:
  - name: api
    src: .
    engine: python3.11
    primary: true
    run: uvicorn app.main:app --host 0.0.0.0 --port 8000
```

### 2) Environment 변수 설정 (Space → Settings → Environment)

- `API_KEY`
- `ALLOWED_ORIGINS` 또는 `CORS_ORIGINS`
- `SENTRY_DSN` (선택)
- `RATE_LIMIT_GLOBAL`, `RATE_LIMIT_HEAVY` (선택)
- MongoDB 사용 시:
  - `MONGODB_URI`
  - `MONGODB_DB` (예: `medinavi`)
  - `MONGODB_COLLECTION` (예: `pubmed`)
- 기타 선택값: `CONTACT_EMAIL`, `NCBI_API_KEY`

`.env.example`를 참고하여 필요한 값을 설정하세요.

### 3) 배포 검증

- Health: `GET /health` → 200 및 상태 JSON
- Metrics: `GET /metrics` → Prometheus 텍스트 메트릭
- Docs: `GET /docs` → Swagger UI 로드

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

## 🤝 기여

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📞 연락처

- **개발자**: qpwo125749-spec
- **이메일**: qpwo125749@example.com

## ⚠️ 면책 조항

이 서비스에서 제공하는 정보는 의학적 조언이 아닙니다. 정확한 진단과 치료를 위해서는 반드시 의료 전문가와 상담하시기 바랍니다.
