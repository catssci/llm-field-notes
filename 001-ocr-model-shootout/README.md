# 001 — 상품 상세 이미지 OCR 모델 성능·비용 비교

이커머스 상품 상세 이미지에서 텍스트를 추출할 때 어떤 모델이 가장 좋은 선택인지 비교 분석한 실험.

## 비교 대상

| 모델 | 종류 |
|------|------|
| Gemini 2.5 Flash-Lite | Vertex AI (멀티모달) |
| Gemini 2.5 Flash | Vertex AI (멀티모달) |
| Gemini 2.5 Pro | Vertex AI (멀티모달) |
| Cloud Vision API | Google OCR 전용 서비스 |

## 평가 항목

- **정확도**: CER (문자 오류율), WER (단어 오류율)
- **속도**: 모델별 응답 Latency
- **비용**: 입력/출력 토큰 기준 월 예상 비용 시뮬레이션

## 실행 방법

```bash
# 의존성 설치
pip install google-genai google-cloud-vision jiwer Pillow pandas matplotlib seaborn tqdm python-dotenv

# GCP 인증 (Vertex AI / Cloud Vision 공통)
gcloud auth application-default login
```

루트 `.env`에 아래 값이 설정되어 있어야 한다.

```
GCP_PROJECT_ID=...
GCP_LOCATION=...
```

이후 `OCR_모델_성능_비용_비교.ipynb`를 순서대로 실행.

## 노트북 구성

1. 환경 설정 및 라이브러리 임포트
2. 클라이언트 초기화
3. 테스트 이미지 로드
4. Gemini 계열 OCR 추출
5. Cloud Vision API OCR 추출
6. 정량 지표 측정 (CER / WER)
7. Latency 비교
8. 비용 계산 및 시뮬레이션
9. 종합 비교 시각화
10. 결과 저장
