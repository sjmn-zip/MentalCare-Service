# 🌿 MindCare AI — 대화형 정서지지 챗봇

> 감정 분류기와 위기 탐지 게이트를 결합한 안전 우선(safety-first) 멘탈케어 챗봇

[![Python](https://img.shields.io/badge/Python-3.11-blue.svg)]()
[![FastAPI](https://img.shields.io/badge/FastAPI-0.11x-green.svg)]()
[![Status](https://img.shields.io/badge/Status-WIP-yellow.svg)]()
[![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)]()

---

## 📌 프로젝트 개요

MindCare AI는 사용자의 감정 상태를 분석하고 정서적 지지를 제공하는 대화형 챗봇입니다.
단순히 LLM에 응답을 위임하는 것이 아니라, **직접 학습한 감정 분류기**와 **위기 탐지 게이트**를 앞단에 두어
위험 신호를 놓치지 않는 구조를 목표로 합니다.

### 핵심 설계 원칙

| 원칙 | 설명 |
|------|------|
| **Safety-First** | LLM이 위기 상황을 단독 판단하지 않음. 별도 분류기가 먼저 필터링 |
| **Hybrid Engine** | 대화 생성은 LLM API, 감정/위기 판단은 직접 학습 모델 |
| **Observable** | 모든 세션의 감정 변화를 로깅하여 추후 대시보드 확장 가능 |

> ⚠️ **면책 조항**: 본 프로젝트는 학습 및 포트폴리오 목적의 프로토타입입니다.
> 전문적인 의료·심리 상담을 대체하지 않으며, 위기 상황 시 전문기관(자살예방상담전화 109) 연결을 안내합니다.

---

## 🏗️ 시스템 아키텍처

```
                    ┌─────────────────┐
   사용자 입력 ───→ │  위기 탐지 게이트  │ ──위험──→ 전문기관 안내 + 대화 중단
                    └────────┬────────┘
                             │ 안전
                    ┌────────▼────────┐
                    │   감정 분류기     │ ──→ 감정 라벨 (예: 불안, 우울, 분노)
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  LLM 응답 생성   │ ──→ 감정 라벨을 프롬프트에 주입
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  감정 로그 저장   │ ──→ PostgreSQL (세션별 시계열)
                    └─────────────────┘
```

### 컴포넌트 역할 분리

| 컴포넌트 | 구현 방식 | 비고 |
|---------|----------|------|
| 대화 생성 | LLM API (Claude / GPT) | 프롬프트 엔지니어링 |
| 감정 분류 | KoBERT 파인튜닝 | **직접 학습** (DS 핵심) |
| 위기 탐지 | 규칙 + 분류기 | 자살/자해 신호 탐지 |
| 상태 추적 | SQL + 규칙 | 세션별 감정 변화 |

---

## 🛠️ 기술 스택

| 분류 | 기술 |
|------|------|
| Language | Python 3.11 |
| Backend | FastAPI |
| ML / NLP | HuggingFace Transformers (KoBERT / KcELECTRA) |
| Database | PostgreSQL |
| LLM | Anthropic Claude API |
| Frontend | Streamlit (MVP) |

---

## 📂 프로젝트 구조

```
mindcare-ai/
├── app/
│   ├── main.py              # FastAPI 엔트리포인트
│   ├── core/
│   │   ├── crisis_gate.py   # 위기 탐지 게이트
│   │   ├── emotion.py       # 감정 분류 추론
│   │   └── llm.py           # LLM 응답 생성
│   ├── models/              # 학습된 모델 가중치
│   └── db/
│       └── schema.sql       # DB 스키마
├── ml/
│   ├── train_emotion.py     # 감정 분류기 학습 스크립트
│   └── notebooks/           # 데이터 탐색 / 실험
├── data/                    # 데이터셋 (gitignore)
├── tests/
├── requirements.txt
└── README.md
```

---

## 📊 데이터셋

| 데이터셋 | 용도 | 출처 |
|---------|------|------|
| AI Hub 감성대화 말뭉치 | 감정 분류 학습 | AI Hub (60개 감정 라벨) |
| AI Hub 웰니스 대화 | 상담 톤 / 프롬프트 | AI Hub |
| KOTE | 한국어 감정 태깅 | GitHub 공개 (50개 라벨) |

---

## 🗺️ 로드맵 (방학 프로젝트)

- [ ] **Phase 0** — 환경 세팅 & 파이썬 재학습 (가상환경, 기본 문법)
- [ ] **Phase 1** — 감정 분류기 학습 (AI Hub 데이터 + KoBERT)
- [ ] **Phase 2** — 위기 탐지 게이트 구현 (규칙 → 분류기)
- [ ] **Phase 3** — LLM 연동 & 프롬프트 설계
- [ ] **Phase 4** — FastAPI 백엔드 통합
- [ ] **Phase 5** — Streamlit MVP & 감정 로깅
- [ ] **Phase 6** — 데모 배포 & 문서화

---

## 🚀 실행 방법

```bash
# 1. 가상환경 생성 및 활성화
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 2. 의존성 설치
pip install -r requirements.txt

# 3. 환경 변수 설정 (.env)
# ANTHROPIC_API_KEY=your_key
# DATABASE_URL=postgresql://...

# 4. 서버 실행
uvicorn app.main:app --reload
```

---

## 📝 학습 기록

> 파이썬 재학습 과정과 모델 실험 결과는 [Notion 기술 블로그](#)에 정리합니다.

---

## 📄 License

MIT License

---

## 👤 Author

산업IT공학(ITM) | 데이터 사이언스 & AI 플랫폼 개발 지향

