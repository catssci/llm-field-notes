# 002 — Gemini 이미지 캡셔닝 최적화

4만 건/시간 규모의 상품 이미지 캡셔닝을 목표로, 이미지 전달 방식·해상도·리사이즈·비동기 처리를 단계별로 실험한 기록.

모델: `gemini-2.5-flash-lite` (Vertex AI)

## 실험 결과 요약

### Phase 1 — URL 접근 가능성
| 방식 | 결과 |
|------|------|
| 외부 CDN HTTP URL | 간헐적 성공, 운영 불가 |
| GCS CDN HTTP URL (IP) | 실패 |
| GCS URI (`gs://`) | **성공** — 유일하게 신뢰 가능 |

### Phase 2 — base64 vs GCS URI 속도
| 방식 | 평균 처리 시간 |
|------|--------------|
| base64 (다운로드 + API) | 2.281s |
| GCS URI (API만) | 1.468s (**35.6% 단축**) |

### Phase 3 — `media_resolution` 토큰 절감
| 설정 | 입력 토큰 | HIGH 대비 절감 | 월 비용 (2,880만 건) |
|------|----------|--------------|-------------------|
| HIGH (기본값) | 1,312 | — | $3,779 |
| MEDIUM | 280 | 78.7% | $806 |
| LOW | 88 | 93.3% | $253 |

### Phase 4 — 이미지 리사이즈 (384px 임계값)
Gemini는 양변이 모두 384px 이하일 때 258 토큰 고정. 그 이상은 768px 타일 분할로 토큰 급증.

| 크기 | 토큰 수 | 절감율 |
|------|--------|-------|
| 원본 (800px) | 1,312 | — |
| 480px (현재 CDN) | 1,312 | 0% |
| **384px (권장)** | **280** | **78.7%** |

### Phase 5 — 동기 vs 비동기 처리
| 방식 | 5건 처리 시간 |
|------|-------------|
| Sync (순차) | 11.1s |
| Async (`client.aio`) | 3.1s (**3.6배 빠름**) |

4만 건/시간(초당 11건) 달성을 위해 `client.aio` 도입 필수.

## 권장 운영 설정

```
이미지 전달: GCS URI (gs://)
해상도:     media_resolution=MEDIA_RESOLUTION_MEDIUM
리사이즈:   384px 이하
호출 방식:  client.aio (비동기) + Semaphore 동시성 제어
```

## 실행 방법

```bash
pip install google-genai google-cloud-storage requests pillow python-dotenv

gcloud auth application-default login
```

루트 `.env`에 아래 값이 설정되어 있어야 한다.

```
GCP_PROJECT_ID=...
GCP_LOCATION=...
GCS_BUCKET=...
TEST_DOWNLOAD_URL=...
TEST_GCS_URI=...
```

이후 `gemini_image_optimization.ipynb`를 순서대로 실행.
