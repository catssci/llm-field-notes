# llm-field-notes

LLM / 멀티모달 API를 실무에 적용하면서 검증한 실험 기록. 성능·비용·처리량을 실측 데이터 기반으로 정리한다.

## 프로젝트 목록

| # | 폴더 | 주제 | 핵심 질문 |
|---|------|------|----------|
| 001 | `001-ocr-model-shootout` | 상품 이미지 OCR 모델 비교 | Gemini vs Cloud Vision — 정확도·속도·비용 |
| 002 | `002-gemini-image-at-scale` | Gemini 이미지 캡셔닝 최적화 | 전달 방식·해상도·리사이즈·비동기로 비용·속도 최적화 |

## 환경 설정

공통 환경 변수는 루트 `.env`에서 관리한다.

```bash
cp .env.example .env  # 값 채운 뒤 사용
```

```
GCP_PROJECT_ID=...
GCP_LOCATION=...
GCS_BUCKET=...
TEST_DOWNLOAD_URL=...
TEST_GCS_URI=...
```

GCP 인증은 각 노트북 실행 전 아래 명령어로 처리한다.

```bash
gcloud auth application-default login
```
