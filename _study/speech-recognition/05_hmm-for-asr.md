---
layout: page
title: HMM을 이용한 음성 인식
permalink: /study/speech-recognition/05_hmm-for-asr/
toc: true
toc_label: "목차"
toc_icon: "book"
date: 2025-05-04 12:04:00 +0900
---

# HMM을 이용한 음성 인식 (HMM for ASR)

**작성자**: 오범석 (서울과학기술대학교 인공지능응용학과)
**날짜**: 2023년 11월 26일
**자료 출처**: "6. Speech recognition (HMM for ASR I).pdf", "7. Speech recognition (HMM for ASR II).pdf" 발췌

## 1. GMM-HMM 모델 개요

* **정의**: GMM-HMM 모델은 음성 인식(ASR)에서 음향 모델링을 위해 널리 사용되는 통계 모델입니다. 시간적 특성을 모델링하는 HMM과 각 상태의 특징 벡터 분포를 모델링하는 GMM을 결합합니다.
* **HMM의 세 가지 주요 문제**:
    * **평가 (Evaluation)**: 주어진 모델 $\boldsymbol{\theta}$과 관찰 $\boldsymbol{O}$에 대해 $P(\boldsymbol{O} | \boldsymbol{\theta})$ 계산. (알고리즘: 전방 알고리즘)
    * **디코딩 (Decoding)**: 주어진 모델 $\boldsymbol{\theta}$과 관찰 $\boldsymbol{O}$에 대해 최적의 은닉 상태 시퀀스 $\boldsymbol{S}^*$ 찾기. (알고리즘: 비터비 알고리즘)
    * **학습 (Learning)**: 주어진 관찰 데이터 $\boldsymbol{O}$ 집합으로부터 최적의 모델 파라미터 $\boldsymbol{\theta}^*$ 추정. (알고리즘: Baum-Welch 알고리즘)
* **GMM-HMM 파라미터 $\boldsymbol{\theta} = \{\boldsymbol{A}, \boldsymbol{B}, \boldsymbol{\pi}\}$**:
    * $\boldsymbol{A}$: 상태 전이 확률 행렬.
    * $\boldsymbol{B}$: 방출 확률 분포 (GMM으로 모델링).
    * $\boldsymbol{\pi}$: 초기 상태 확률 분포.

## 2. ASR 시스템의 구성 요소

* **음성 신호 처리 (Speech Signal Processing)**
* **특징 추출 (Feature Extraction)**
* **음향 모델 (Acoustic Model)**: $P(x|w)$ 또는 $P(x|p)$ 모델링 (GMM-HMM 담당).
* **발음 모델 (Pronunciation Model)**: $P(p|w)$ 모델링 (단어 → 음소/상태 시퀀스).
* **언어 모델 (Language Model)**: $P(w)$ 모델링 (단어 시퀀스 확률).

## 3. ASR by GMM-HMM: 목표 및 기본 원리

* **최종 목표**: 입력 음성 $x$ (또는 특징 시퀀스 $O$)에 대해 가장 확률 높은 단어 시퀀스 $W^*$ 찾기.
    $W^* = \arg \max_W P(W|O)$
* **베이즈 규칙 적용 및 전개**:
    $W^* = \arg \max_W \frac{P(O|W) P(W)}{P(O)} = \arg \max_W P(O|W) P(W)$
    * $P(O|W)$: 음향 모델 우도 (Acoustic Model Likelihood)
    * $P(W)$: 언어 모델 사전 확률 (Language Model Prior)
* **세부 모델 통합**: 상태/음소 시퀀스 $p$를 고려하여 전개.
    $W^* = \arg \max_W \sum_p P(O|p) P(p|W) P(W)$
* **실제 구현 (Viterbi 근사)**: 계산 효율성을 위해 합계($\sum$) 대신 최댓값($\max$) 사용.
    $W^* \approx \arg \max_W \max_p P(O|p) P(p|W) P(W)$

## 4. HMM for ASR (음향 모델 구성)

* **음소 모델링**: 각 음소(phoneme)는 보통 3개의 HMM 상태로 모델링됨.
    > "In speech recognition, a phoneme is usually modeled by three states"
* **단어 모델링**: 단어는 해당 음소 HMM들의 연결로 표현됨 (예: "sits" → /s/-/i/-/t/-/s/).
* **문장 모델링**: 문장은 단어 HMM들의 연결로 구성 (단어 사이에 침묵(silence) HMM 포함 가능).
* **GMM 역할**: 각 HMM 상태 $j$에서의 방출 확률 $P(O_t|q_t=j, \theta)$을 GMM으로 모델링.
    > "State probability estimated using GMM"
* **결합 확률 계산**: 관찰 시퀀스 $O$와 상태 시퀀스 $S$의 결합 확률 $P(O, S|\theta)$는 전이 확률과 방출 확률의 곱으로 계산됨.
* **음소 레벨 확률**: $P(O|p, \theta) = \sum_{S \in p} P(O, S|\theta)$. 계산 비용 문제로 Viterbi 또는 Forward-Backward 알고리즘 사용.

## 5. GMM-HMM 음향 모델 학습 (Learning)

* **목표**: 관찰 데이터로부터 GMM-HMM 파라미터 $\theta$ 학습 (HMM 학습 문제).
    * 파라미터: $a_{i,j}^p$ (전이 확률), $w_m^{p,j}$ (GMM 가중치), $\mu_m^{p,j}$ (GMM 평균), $\Sigma_m^{p,j}$ (GMM 공분산).
* **학습 알고리즘**: **Baum-Welch 알고리즘** (EM 알고리즘의 일종) 사용. 상태 시퀀스와 GMM 혼합 요소가 관찰되지 않기 때문.
    > "We learn the parameters using Baum-Welch algorithm (i.e., EM algorithm)"
* **과정**:
    * **E-단계**: 현재 파라미터 $\theta'$로 은닉 변수(상태, GMM 요소)의 기댓값 계산 (전방/후방 확률 사용).
    * **M-단계**: 계산된 기댓값을 사용하여 가능도를 최대화하는 새로운 파라미터 $\theta$ 추정.
    * **반복**: 수렴할 때까지 E-단계와 M-단계를 반복.

## 6. 언어 모델 (Language Model)

* **역할**: 단어 시퀀스의 확률 $P(W)$를 모델링하여 문법/의미적으로 자연스러운 시퀀스 선택 지원.
* **N-gram 모델**: 현재 단어의 확률이 이전 $N-1$개 단어에만 의존한다고 가정.
    $P(W) \approx \prod_{k=1}^{|W|} P(w_k | w_{k-N+1}, \dots, w_{k-1})$
    * 예: Unigram(N=1), Bigram(N=2), Trigram(N=3).
* **확률 추정 (MLE)**: 훈련 데이터에서의 빈도수 기반.
    $P(w_i | w_{i-1}) = \frac{count(w_{i-1}, w_i)}{count(w_{i-1})}$ (Bigram 예시)
* **장점**: 계산 가능, 모든 시퀀스 커버, 다양한 정보 인코딩, 계산 단순성 등.
* **실용적 고려사항**: Trigram이 일반적, 로그 확률 사용(언더플로우 방지, 계산 효율).

## 7. 디코딩 (Decoding): 최적 단어 시퀀스 탐색

* **목표**: 학습된 모델(음향, 발음, 언어)을 사용하여 주어진 음성 $O$에 대한 최적 단어 시퀀스 $W^*$ 찾기.
* **복잡성**: 가능한 상태 시퀀스와 단어 시퀀스의 수가 매우 많아 탐색 공간이 방대함.
* **핵심 알고리즘: Viterbi 디코딩**:
    * HMM의 디코딩 문제 해결 알고리즘을 ASR에 맞게 확장.
    * 동적 계획법 기반으로 최적 경로 탐색.
    * 시간 $t$, 상태 $j$까지의 최대 로그 확률 $\delta_t(j)$와 이전 상태 $\psi_t(j)$ 저장.
* **음향 모델과 언어 모델 통합 (로그 공간)**:
    $\log P(O, W) = \log P(O|W) + \log P(W)$
    * Viterbi 탐색 중 각 경로의 누적 점수에 로그 음향 확률과 로그 언어 모델 확률을 더함.
* **언어 모델 가중치 (Language Model Weight/Scale Factor, $\lambda$)**:
    * 음향 점수와 언어 모델 점수 간의 스케일 차이를 보정하기 위해 사용.
    * $\log P(O, W)_{\text{scaled}} = \log P(O|W) + \lambda \log P(W)$
    * 최적 $\lambda$는 개발 세트를 통해 실험적으로 결정 (튜닝).
* **Viterbi 탐색 네트워크 (Search Network)**:
    * 디코딩 효율성을 높이기 위해 HMM 상태, 음소, 단어, 언어 모델 정보를 통합한 그래프 구조.
    * Viterbi 알고리즘은 이 네트워크 상에서 최적 경로(단어 시퀀스)를 탐색.
* **빔 서치 (Beam Search)**:
    * Viterbi 탐색의 변형으로, 계산 효율성을 위한 휴리스틱 탐색 기법.
    * 각 시간 단계에서 확률이 낮은 경로를 가지치기(pruning)하고, 확률 높은 일부 경로(빔 내 경로)만 유지하여 탐색 공간 축소.
    * 계산량 감소 효과가 크지만, 최적 경로를 놓칠 수 있는 근사 탐색.

## 8. 고립 단어 인식 (Isolated Word Recognition)

* **목표**: 주어진 음성 $\hat{O}$에 대해 어휘집 내에서 가장 확률 높은 **단일 단어** $\hat{w}$ 찾기.
    > "Find the most probable word $\hat{w}$ given the acoustic observation $\hat{x}$"
* **방법**: 각 단어에 대한 HMM 구축 후, Viterbi 알고리즘 등으로 최적 정렬(alignment)을 찾아 가장 높은 확률을 갖는 단어 선택. (연속 음성 인식보다 단순한 형태)

## 결론

GMM-HMM 모델은 음성 인식 시스템의 핵심 요소인 음향 모델링을 위한 강력한 통계적 프레임워크입니다. HMM은 음성의 시간적 구조를, GMM은 각 시점의 음향 특징 분포를 모델링합니다. Baum-Welch 알고리즘을 통해 모델 파라미터를 학습하며, 학습된 모델은 발음 모델 및 언어 모델(주로 N-gram)과 결합됩니다. 최종적으로 Viterbi 디코딩 알고리즘(빔 서치 등 효율화 기법 포함)을 사용하여 방대한 탐색 공간에서 음향 및 언어 정보를 통합하고 최적의 단어 시퀀스를 추론합니다. 언어 모델 가중치 조절은 두 정보 소스 간의 균형을 맞추는 중요한 과정입니다. 이 문서는 GMM-HMM 기반 ASR의 기본 구조, 구성 요소, 학습 및 디코딩 과정을 포괄적으로 요약합니다.
