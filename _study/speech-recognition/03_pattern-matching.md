---
layout: page
title: 패턴 매칭
permalink: /study/speech-recognition/03_pattern-matching/
toc: true
toc_label: "목차"
toc_icon: "book"
date: 2025-05-04 12:02:00 +0900
---

# 음성 인식: 템플릿 매칭 및 통계 모델링 요약


## 주요 내용 및 핵심 아이디어

### 1. 음성 인식 기본 구조 및 역사

* **기본 구조**: 음성 신호 처리 → 특징 추출 → 인식 (특징 추출이 어려운 단계로 간주됨)
* **역사**:
    * **1970년대 초**: 시간 변화 문제를 해결하기 위해 **동적 시간 워핑 (Dynamic Time Warping, DTW)** 도입.
    * **1970년대 중반**: 템플릿 매칭의 한계를 극복하기 위해 통계적 방법인 **은닉 마르코프 모델 (Hidden Markov Model, HMM)** 도입.

### 2. 템플릿 매칭 기반 음성 인식

* **개념**: 입력 음성을 미리 저장된 템플릿(프로토타입)과 비교하여 가장 유사한 것을 찾는 방식.
* **유사성 측정**: 초기에는 유클리드 거리 사용.
    $d_{dist_{p,q}} = \sqrt{\sum_{d=0}^{D-1} (M_{p,d} - M_{q,d})^2}$
* **문제점**: 발성 속도 차이 등 **시간적 변이**로 인해 단순 거리 측정은 효과적이지 않음.
* **해결책 (DTW)**: 동적 계획법 기반의 DTW를 사용하여 시간 축을 비선형적으로 정렬하여 최적의 매칭 경로 탐색.
    * **DTW 거리 계산 (누적 비용)**:
        $cost_{dist_{p,q}}(n,m) = \min \begin{cases} cost_{dist_{p,q}}(n-1, m) + dist_{dist_{p,q}}(n,m) \\ cost_{dist_{p,q}}(n-1, m-1) + 2 \cdot dist_{dist_{p,q}}(n,m) \\ cost_{dist_{p,q}}(n, m-1) + dist_{dist_{p,q}}(n,m) \end{cases}$
    * **정규화**: $DTWdist_{p,q} = \frac{1}{N+M} cost_{dist_{p,q}}(N-1, M-1)$
* **템플릿 매칭의 한계**:
    * 미리 등록된 단어만 인식 가능.
    * 화자, 환경 등 다양한 음성 변이에 취약.
    * ➡️ 1970년대 중반 이후 HMM 등 통계적 모델로 대체됨.

### 3. 통계 모델링 기반 음성 인식

* **개념**: 입력 음향 데이터 $x$가 주어졌을 때, 가장 확률이 높은 단어 시퀀스 $\hat{w}$를 찾는 문제. (베이즈 정리 활용)
    $\hat{w} = \arg \max_w P(w|x) = \arg \max_w \frac{P(x|w) P(w)}{P(x)} = \arg \max_w P(x|w) P(w)$
    * $P(x|w)$: **음향 모델 (Acoustic Model)** - 단어 $w$가 주어졌을 때 음향 $x$가 관측될 확률.
    * $P(w)$: **언어 모델 (Language Model)** - 단어 시퀀스 $w$가 나타날 확률.
* **문제점 및 해결책**:
    * **문제 1 (단어 기반 모델링의 비현실성)**: 모든 단어(파생어 포함)를 모델링하기 어려움.
        * **해결책**: 단어를 더 작은 단위인 **음소(phoneme)**로 분할하여 모델링.
        * 음소 모델링 시: $\arg \max_w P(x|w) P(w) \approx \arg \max_w \sum_p P(x|p) P(p|w) P(w)$
    * **문제 2 (음성 데이터 변이 처리)**: 다양한 변이(화자, 환경 등) 처리의 어려움.
        * **해결책**: **GMM-HMM**을 사용하여 음향 모델($P(x|p)$)을 효과적으로 훈련.

### 4. GMM-HMM 개요

* **핵심 개념**: 유사한 음향 특징을 가진 음성 데이터는 특정 음소의 분포 영역 내에 위치할 가능성이 높다는 아이디어 활용.
    > "In case an unseen phoneme falls within this region, it is reasonable (plausible) to assign the label /a/"
* **확률 밀도 함수 추정**: 연속적인 음성 특징(예: MFCC)을 모델링하기 위해 확률 밀도 함수(PDF) 사용. 주로 **가우시안 분포** 사용.
    * **다차원 가우시안**: $o(\mathbf{x}|\theta) = \frac{1}{(2\pi)^{D/2} |\mathbf{\Sigma}|^{1/2}} \exp(-\frac{1}{2} (\mathbf{x} - \mathbf{\mu})^T \mathbf{\Sigma}^{-1} (\mathbf{x} - \mathbf{\mu}))$
        * $\theta = (\mathbf{\mu}, \mathbf{\Sigma})$: 평균 벡터와 공분산 행렬.
    * **최적 파라미터 추정 (Maximum Likelihood Estimation)**: 훈련 데이터 $X = \{\mathbf{x}_0, \dots, \mathbf{x}_{N-1}\}$가 주어졌을 때, 가능도 $L(\theta|X) = \prod_{n=0}^{N-1} P(\mathbf{x}_n|\theta)$를 최대화하는 $\hat{\theta}$를 찾음.
        * $\hat{\mathbf{\mu}} = \frac{1}{N} \sum_{n=0}^{N-1} \mathbf{x}_n$ (샘플 평균)
        * $\hat{\mathbf{\Sigma}} = \frac{1}{N} \sum_{n=0}^{N-1} (\mathbf{x}_n - \hat{\mathbf{\mu}}) (\mathbf{x}_n - \hat{\mathbf{\mu}})^T$ (샘플 공분산)

### 5. GMM 및 EM 알고리즘

* **GMM (Gaussian Mixture Model)**: 단일 가우시안으로는 복잡한 음성 데이터 분포를 표현하기 어려움. 여러 개의 가우시안 분포를 가중치 합으로 결합하여 사용.
    $o(\mathbf{x}|\theta) = \sum_{m=0}^{M-1} w_m \mathcal{N}(\mathbf{x}; \mathbf{\mu}_m, \mathbf{\Sigma}_m)$
    * $w_m$: 혼합 계수 ($\sum w_m = 1$)
    * $\theta = \{w_m, \mathbf{\mu}_m, \mathbf{\Sigma}_m; m=0, \dots, M-1\}$: GMM 파라미터 셋.
* **EM (Expectation-Maximization) 알고리즘**: GMM 파라미터를 학습하기 위한 반복적 알고리즘. 관측 데이터($x$)와 잠재 변수($z$, 각 데이터가 어떤 가우시안에서 생성되었는지 나타냄)를 사용하여 로그 가능도의 기댓값을 최대화.
    * **E-단계 (Expectation)**: 현재 파라미터 $\theta'$를 이용하여, 각 데이터 $\mathbf{x}_n$이 각 가우시안 혼합 $m$에서 생성되었을 사후 확률 $P(z_m | \mathbf{x}_n, \theta')$ 계산.
        $P(z_m | \mathbf{x}_n, \theta') = \frac{w_m' \mathcal{N}(\mathbf{x}_n; \mathbf{\mu}_m', \mathbf{\Sigma}_m')}{\sum_{k=0}^{M-1} w_k' \mathcal{N}(\mathbf{x}_n; \mathbf{\mu}_k', \mathbf{\Sigma}_k')}$
    * **M-단계 (Maximization)**: E-단계에서 계산된 확률을 가중치로 사용하여 GMM 파라미터 $\hat{w}_m, \hat{\mathbf{\mu}}_m, \hat{\mathbf{\Sigma}}_m$ 업데이트.
        * $\hat{w}_m = \frac{1}{N} \sum_{n=0}^{N-1} P(z_m | \mathbf{x}_n, \theta')$
        * $\hat{\mathbf{\mu}}_m = \frac{\sum_{n=0}^{N-1} P(z_m | \mathbf{x}_n, \theta') \mathbf{x}_n}{\sum_{n=0}^{N-1} P(z_m | \mathbf{x}_n, \theta')}$
        * $\hat{\mathbf{\Sigma}}_m = \frac{\sum_{n=0}^{N-1} P(z_m | \mathbf{x}_n, \theta') (\mathbf{x}_n - \hat{\mathbf{\mu}}_m) (\mathbf{x}_n - \hat{\mathbf{\mu}}_m)^T}{\sum_{n=0}^{N-1} P(z_m | \mathbf{x}_n, \theta')}$
    * **반복**: $\theta' \leftarrow \hat{\theta}$로 업데이트 후, 수렴할 때까지 E-단계와 M-단계를 반복.

