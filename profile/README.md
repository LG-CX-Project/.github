# LG 스타일러 프로젝트 — 상상의 Nㅏ래

> "비울수록 선명해지는 미니멀리즘을 위한 LG 스타일러"

![팀 사진](./team.jpg)

**1팀 · 상상의 Nㅏ래**

---

## 프로젝트 개요

북미 미니멀리스트를 타겟으로 **소형 LG 스타일러의 신규 성장 동력**을 발굴하는 데이터 기반 CX 전략 프로젝트입니다.

국내 소형 스타일러 시장이 정체(3벌 모델 점유율 2022년 20% → 2024년 7%)되는 상황에서, 북미 의류관리기 시장(전 세계 33~40% 점유, 2035년 약 22억 3,000만 달러 예상)의 구조적 성장세를 기반으로 새로운 고객과 서비스 가치를 정의했습니다.

**핵심 난제**: "소형 스타일러의 성장세가 정체되어 있다."

---

## 리포지토리 구조

```
LG_CX_Project/
├── review_text_embed/               # 분석 파이프라인 메인 패키지
│   ├── review_text_csv_stepwise/    # CLI + 서비스 모듈 (Python 패키지)
│   │   ├── cli.py                   # 명령행 진입점
│   │   ├── config.py                # .env 기반 설정 로더
│   │   ├── steps.py                 # 단계 오케스트레이션
│   │   └── services/
│   │       ├── normalize.py         # 입력 스키마 정리 및 분석용 텍스트 생성
│   │       ├── adfilter.py          # 광고성 문서 필터링
│   │       ├── aifilter.py          # 영어 AI 생성 텍스트 필터링
│   │       ├── preprocess.py        # 일반 텍스트 전처리
│   │       ├── pos.py               # 한국어 POS 태깅
│   │       ├── sentiment.py         # 감성 분석
│   │       ├── vectorize.py         # 임베딩 / 벡터 생성
│   │       ├── actor_cluster.py     # Actor 군집화
│   │       ├── lda_action.py        # Action 토픽 분석 (LDA)
│   │       ├── opportunity.py       # 중요도 / 만족도 / 기회영역 계산
│   │       ├── runpod.py            # RunPod 엔드포인트 호출 유틸
│   │       └── stopwords.py         # 불용어 처리
│   ├── notebooks/
│   │   ├── 01_preprocess_en.ipynb         # 영어 전처리
│   │   ├── 01_preprocess_ko.ipynb         # 한국어 전처리
│   │   ├── 02_analysis.ipynb              # 기본 분석
│   │   ├── 03_topic_modeling.ipynb        # 토픽 모델링 (기본)
│   │   ├── 03_topic_modeling_ctfidf.ipynb # c-TF-IDF 기반 토픽 모델링
│   │   ├── 03_topic_modeling_hdbscan.ipynb# HDBSCAN 토픽 모델링
│   │   ├── 04_action_analysis_ctfidf.ipynb
│   │   ├── 04_action_analysis_hdbscan.ipynb
│   │   ├── 04_action_analysis_kmeans.ipynb
│   │   ├── 06_action_analysis_digital_minimalism.ipynb
│   │   ├── 06_action_analysis_fashion_closet.ipynb
│   │   ├── 06_action_analysis_consumption_philosophy.ipynb
│   │   ├── 06_action_analysis_living_space.ipynb
│   │   └── pipeline_full.ipynb
│   ├── docs/
│   │   ├── COLUMN_MAPPING.md
│   │   ├── EXECUTION_GUIDE.md
│   │   ├── METHOD_AND_MODEL_RATIONALE.md  # 모델 및 방법론 선택 근거
│   │   ├── MODEL_GUIDE.md
│   │   ├── PIPELINE_NOTEBOOK_GUIDE.md
│   │   ├── RUNPOD_GUIDE.md
│   │   ├── topic_modeling.md
│   │   └── topic_modeling_notebooks.md
│   ├── data/
│   │   ├── ko-stopwords.csv               # 한국어 불용어 사전
│   │   └── SentiWord_info.json            # 한국어 감성 사전
│   ├── examples/input/                    # 샘플 입력 데이터
│   ├── output/                            # 노트북 실행 결과물
│   └── requirements.txt
│
├── runpod-review-adfilter-hf/     # RunPod 워커: 영어 스팸/광고 필터
├── runpod-review-aifilter-hf/     # RunPod 워커: 영어 AI 생성 텍스트 탐지
├── runpod-review-embedding-hf/    # RunPod 워커: 텍스트 임베딩
├── runpod-review-koadfilter-hf/   # RunPod 워커: 한국어 광고 필터
├── runpod-review-sentiment-hf/    # RunPod 워커: 한국어 감성 분석
├── runpod-review-stopword-hf/     # RunPod 워커: 한국어 불용어 제거
├── runpod-topic-modeling-hf/      # RunPod 워커: 토픽 모델링 v1
└── runpod-topic-modeling2-hf/     # RunPod 워커: 토픽 모델링 v2
```

---

## 데이터 수집

북미 미니멀리스트 커뮤니티를 중심으로 총 **347,275건**의 텍스트 데이터를 수집했습니다.

### 플랫폼 및 규모

| 플랫폼 | 키워드 | 수집량 |
|--------|--------|-------:|
| Reddit | minimalist | 10,339 |
| Reddit | simpleliving | 40,500 |
| Reddit | minimalism | 28,488 |
| Reddit | declutter | 14,279 |
| Reddit | capsulwardrobe | 14,279 |
| Reddit | extrememinimalism | 5,867 |
| YouTube | minimal clothes / home / live 외 | 347,215 |
| Threads | minimal cloth | 4,075 |
| **합계** | | **347,275** |

### 초기 토픽 탐색 (크롤링 키워드 도출용)

수집 전 HDBSCAN + TF-IDF로 사전 토픽을 탐색하여 크롤링 키워드를 결정했습니다.

| 토픽 | 핵심 키워드 | 문서 수 |
|------|------------|--------:|
| Topic 12 | wear, cloth, shirt, wardrobe, pant | 1,784 |
| Topic 41 | minimalism, minimalist, lifestyle, space | 1,087 |
| Topic 17 | phone, app, delete, smartphone, social media | 1,108 |
| Topic 30 | space, house, room, small, storage | 466 |

---

## 데이터 처리 파이프라인

```
원본 수집 (347,215건)
    │
    ▼  스팸·광고 제거 — roshana1s/spam-message-classifier
    │  영어 필터링 (langdetect) + 정규화 + 중복 제거 + 단문(50자↓) 제거
    │
(277,772건)
    │
    ▼  AI 생성 텍스트 제거 — fakespot-ai/roberta-base-ai-text-detection-v1
    │
(220,492건)
    │
    ▼  jinaai/jina-embeddings-v3 임베딩 (RunPod GPU)
       HDBSCAN 반복 노이즈 제거 (34 토픽에서 정지 → 미니멀리즘 무관 토픽 제거)
    │
(76,109건)  ← 최종 분석 데이터
```

### 전처리 단계별 도구

| 단계 | 모델 / 도구 | 역할 |
|------|------------|------|
| 광고 제거 (영어) | `roshana1s/spam-message-classifier` | 홍보·광고성 텍스트 제거 |
| AI 텍스트 제거 | `fakespot-ai/roberta-base-ai-text-detection-v1` | AI 봇 생성 게시물 제거 |
| 광고 제거 (한국어) | `happyalex12/kobert-ad-filter` | 한국어 협찬·광고 문장 제거 |
| 언어 필터 | `langdetect` | 영어 게시물만 유지 |
| 의미 불용어 제거 | `BM-K/KoSimCSE-roberta-multitask` | 문맥 기반 중요 토큰만 선별 |

---

## 데이터 분석

### 전체 분석 파이프라인

```
Vectorization
 jinaai/jina-embeddings-v3 (max 8192 tokens, 512차원, RunPod 호출)
    │
    ▼
Cluster Analysis
 1단계: HDBSCAN — 밀도 기반, 노이즈(-1) 자동 분류, 반복 정제
 2단계: KMeans  — 정제 데이터 균일 파티셔닝 → 최종 4 클러스터
    │
    ▼
TF-IDF / C-TF-IDF
 유니그램 + 바이그램(1,2)으로 클러스터별 차별 키워드 추출
    │
    ▼
Actor-based LDA Modeling
 각 Actor 내부에서 Action 단위 행동 구조화
    │
    ▼
Sentiment Analysis
 cardiffnlp/twitter-roberta-base-sentiment-latest
 (SNS 구어체·축약어·감탄문 특화)
    │
    ▼
Opportunity Score = Importance × (1 − Satisfaction)
```

### 임베딩 모델: `jinaai/jina-embeddings-v3`

| 항목 | 값 |
|------|-----|
| 최대 입력 토큰 | 8,192 |
| 출력 차원 | 512 |
| 선택 이유 | 짧은 댓글과 긴 경험담 혼재 처리, 문장 의미 기반 벡터화, 맥락에 따른 동일 단어 구분 |
| 비교 대상 | TF-IDF / Doc2Vec (단어 중심, 맥락 구분 불가) |

### 클러스터링 전략

**1단계 — HDBSCAN (노이즈 제거)**

- 밀도 기반 비지도 학습으로 토픽 수 사전 정의 불필요
- 초기 파라미터: `min_cluster_size=1000`, `min_samples=10`
- 노이즈 비율이 낮아질 때까지 `min_cluster_size` 축소 + `min_samples` 증가 반복
- 중간에 UMAP(`n_components=5`, `n_neighbors=15`, `min_dist=0.0`)으로 차원 축소 → 미세 주제 경계 명확화
- 최종 34 토픽에서 정지, 미니멀리즘 무관 토픽 제거

**2단계 — KMeans (최종 클러스터 확정)**

- 정제된 데이터에 균일 파티셔닝 적용
- 모든 문서를 빠짐없이 클러스터에 할당
- **최종 4개 클러스터 확정**

### 4대 Actor (C-TF-IDF 바이그램 기반)

| Actor | 주제 | 대표 키워드 |
|-------|------|------------|
| Actor 0 | 디지털 미니멀리즘 | light phone, dumb phone, social media, doom scrolling |
| Actor 1 | 의류·옷장 | capsule wardrobe, laundry, donate clothes, closet |
| Actor 2 | 소비 철학 | thrift store, quality, save money, second hand |
| Actor 3 | 주거 공간 | living room, square feet, storage space, furniture |

### Actor-based LDA: Action 세그멘테이션

| Actor | Action | 핵심 키워드 |
|-------|--------|------------|
| Actor 0 | 디지털 경량화형 | app drawer, delete apps, deleted facebook |
| Actor 0 | 대체기기형 | light phone, dumb phone, flip phone |
| Actor 1 | 정리·처분·세탁형 | clean clothes, laundry basket, donate clothes |
| Actor 1 | 캡슐 워드로브형 | capsule wardrobe, personal style, wear black |
| Actor 1 | 옷장세탁 존 분리형 | clothes closet, laundry room |
| Actor 2 | 장기착용형 | good quality, high quality, investment piece |
| Actor 2 | 필요 중심형 | save money, waste money |
| Actor 2 | 재유통 소비형 | thrift store, local buy/sell, donate |
| Actor 3 | 공간 청소형 | cleaning home, easy clean, take space |
| Actor 3 | 다용도 최적화형 | piece furniture, kitchen counter |
| Actor 3 | 옷장 존 분리형 | clothes room, care home |

### 감성 분석 모델

| 언어 | 모델 | 선택 이유 |
|------|------|----------|
| 영어 | `cardiffnlp/twitter-roberta-base-sentiment-latest` | Reddit·Threads 구어체·축약어 특화, 문맥 기반 극성 추론 |
| 한국어 | `Copycats/koelectra-base-v3-generalized-sentiment-analysis` | 도메인 비특화, 생활 경험담 전반 커버 |

### 기회영역 도출

```
Opportunity Score = Importance × (1 − Satisfaction)
```

중요도는 높으나 만족도가 낮은 3대 미충족 영역:

| 기회 영역 | 대표 Pain Point |
|-----------|----------------|
| 옷장 비움 | 정리 기준 부재, 죄책감, 비움 유지 어려움 |
| 디지털 절제 | 생활 효율 저하, 불편함과 절제 사이 갈등 |
| 소형 주거 | 공간 활용 최적화, 피로·답답함 |

---

## RunPod 서버리스 워커

GPU가 필요한 모델 추론은 RunPod Serverless 워커로 분리 배포합니다.

| 디렉터리 | 모델 | 역할 |
|----------|------|------|
| `runpod-review-adfilter-hf` | `roshana1s/spam-message-classifier` | 영어 스팸·광고 필터 |
| `runpod-review-aifilter-hf` | `fakespot-ai/roberta-base-ai-text-detection-v1` | 영어 AI 생성 텍스트 탐지 |
| `runpod-review-embedding-hf` | `nlpai-lab/KURE-v1` | 텍스트 임베딩 (1024차원) |
| `runpod-review-koadfilter-hf` | `happyalex12/kobert-ad-filter` | 한국어 광고 필터 |
| `runpod-review-sentiment-hf` | `Copycats/koelectra-base-v3-generalized-sentiment-analysis` | 한국어 감성 분석 |
| `runpod-review-stopword-hf` | `BM-K/KoSimCSE-roberta-multitask` | 문맥 기반 의미 불용어 제거 |
| `runpod-topic-modeling-hf` | jina-embeddings-v3 + HDBSCAN | 토픽 모델링 v1 |
| `runpod-topic-modeling2-hf` | jina-embeddings-v3 + KMeans | 토픽 모델링 v2 |

모든 워커는 단일 텍스트 및 배치 입력을 지원하며, `Dockerfile`로 컨테이너 배포합니다.

---

## 분석 파이프라인 실행 방법

### 환경 설정

```bash
cd review_text_embed
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install --upgrade pip
pip install -r requirements.txt
```

### CLI 전체 실행 (로컬, 규칙 기반)

```bash
python -m review_text_csv_stepwise.cli run-all \
  --input examples/input/sample_posts.csv \
  --output-dir runs/result \
  --env-file profiles/local_lexicon.env
```

예상 출력:

```
runs/result/00_normalized.csv
runs/result/01_adfiltered.csv
runs/result/02_preprocessed.csv
runs/result/03_pos_tagged.csv
runs/result/04_sentiment.csv
```

### RunPod 기반 영어 파이프라인

```bash
python -m review_text_csv_stepwise.cli run-all \
  --input examples/input/sample_posts.csv \
  --output-dir runs/result_en \
  --env-file profiles/runpod_hf_en.env
```

### 노트북 분석 흐름

**영어 미니멀리즘 데이터**

```
01_preprocess_en.ipynb
    → 02_analysis.ipynb
    → 03_topic_modeling_hdbscan.ipynb
    → 04_action_analysis_kmeans.ipynb
    → 06_action_analysis_fashion_closet.ipynb (등 Actor별)
```

**한국어 데이터**

```
01_preprocess_ko.ipynb
    → 02_analysis.ipynb
    → 03_topic_modeling.ipynb
```

### 환경 파일 선택 기준

| 파일 | 상황 |
|------|------|
| `profiles/local_lexicon.env` | 외부 연결 없이 로컬에서 빠른 검증 |
| `profiles/runpod_hf_en.env` | 영어 광고·AI 필터 + 감성 분석을 RunPod으로 처리 |
| `profiles/runpod_hf_ko.env` | 한국어 광고 필터 + 임베딩을 RunPod으로 처리 |
| `profiles/runpod_llm.env` | 문맥 의존적 LLM 기반 분류 (비용 높음) |

---

## 핵심 서비스 — LG WA (Wear Again)

분석 결과를 기반으로 도출한 AI 옷장 관리 서비스입니다.

| 서비스 | 핵심 메커니즘 | 가치 제안 |
|--------|-------------|----------|
| **Clear Guide** | 착용 이력 + NIR 센서 섬유 마모 분석 → 남길 옷 / 보낼 옷 제안 | "감으로 하던 정리를 데이터로 결정" |
| **Routine Master** | 생활 패턴 학습 → 주간 케어 루틴 + 수납 알림 | "한 번 정리하고 끝이 아닌, 루틴으로 유지" |
| **Eco Report** | 탄소 절감·물 절약 효과 수치화 | "비움의 죄책감을 성취감으로" |
| **LG WA** | 위 세 서비스를 통합한 AI 옷장 비서 | 정리 → 관리 → 환경 가치의 선순환 |

---

## KPI

### 고객 가치 지표

- 의류 관리 결정 피로 및 시간 부담 감소
- 잦은 세탁 손상 감소 → 의류 수명 연장
- 세탁 비용 / 의류 구매 비용 / 에너지 절약

### 경영 성과 지표

| 지표 | 목표 |
|------|------|
| 미국 스타일러 보급률 | 프리미엄 가전 인지도 상승 및 유통 채널 확대 |
| 소형 스타일러 판매 비중 | 1~2인 가구 확장으로 지속 증가 |
| 전체 스타일러 판매량 | 연간 60만 대 박스권 돌파 |

---

## 기술 스택

| 분류 | 도구 |
|------|------|
| 데이터 수집 | Reddit API, YouTube Data API, Threads 크롤링 |
| 전처리 | langdetect, konlpy, kiwipiepy |
| 임베딩 | `jinaai/jina-embeddings-v3` (RunPod GPU) |
| 클러스터링 | HDBSCAN, UMAP, KMeans (scikit-learn) |
| 토픽 모델링 | LDA (gensim), TF-IDF, C-TF-IDF |
| 감성 분석 | `cardiffnlp/twitter-roberta-base-sentiment-latest`, vaderSentiment |
| LLM 보조 | EXAONE (클라우드 GPU, 키워드 정리) |
| 인프라 | RunPod Serverless, Docker |
| 시각화 | matplotlib, seaborn, pyLDAvis |

---

## 팀 소개

**1팀 · 상상의 Nㅏ래**

남극의 펭귄(수현)과 함께 상상의 날개를 펼치는 데이터 분석 팀입니다.  
소셜 미디어 텍스트 대규모 수집부터 임베딩, 클러스터링, 감성 분석, 기회영역 도출까지 End-to-End 데이터 파이프라인을 직접 설계하고 구현했습니다.

---

*LG CX 프로젝트 · 2025*
