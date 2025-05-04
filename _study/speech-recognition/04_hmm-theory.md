---
layout: page
title: HMM 이론
permalink: /study/speech-recognition/04_hmm-theory/
toc: true
toc_label: "목차"
toc_icon: "book"
date: 2025-05-04 12:03:00 +0900
---

# HMM 이론 (HMM Theory)

# 은닉 마르코프 모델 (HMM) 개요 및 응용 요약

## 1. 개요: HMM 기본 개념

* **정의**: 은닉 마르코프 모델(HMM)은 순차 데이터를 모델링하는 확률 모델입니다. 관찰 불가능한(은닉된) 상태 시퀀스가 관찰 가능한 결과 시퀀스를 생성하는 과정을 모델링하는 데 유용합니다.
* **선행 개념**: 순차 데이터/프로세스, 마르코프 프로세스(미래 상태가 현재 상태에만 의존).
* **주요 내용**: HMM 구조, 3가지 주요 문제(평가, 디코딩, 학습) 및 해결 알고리즘.

## 2. 핵심 개념 및 주제

* **순차 데이터 및 프로세스**: 시간/순서에 따라 변하는 데이터 시퀀스 ($\boldsymbol{x} = (x_1, \dots, x_T)$) 및 상태 변화 모델링.
* **마르코프 프로세스**: 현재 상태가 주어지면 과거 상태와 무관하게 미래 상태가 결정됨 (마르코프 가정). 1차 마르코프 모델이 일반적.
* **은닉 마르코프 모델 (HMM)**:
    * **두 종류의 시퀀스**: 관찰 불가능한 은닉 상태 시퀀스 ($\boldsymbol{S} = (s_1, \dots, s_T)$)와 관찰 가능한 결과 시퀀스 ($\boldsymbol{O} = (o_1, \dots, o_T)$).
    * **구조**: 은닉 상태는 마르코프 과정을 따르고, 관찰 결과는 현재 은닉 상태에 의존.
    * **예시**: 활동 기반 날씨 추정, 혼잡도 기반 날씨 추정, 점수 기반 등급 추정.
* **HMM 매개변수** $\boldsymbol{\theta} = \{\boldsymbol{A}, \boldsymbol{B}, \boldsymbol{\pi}\}$:
    * **상태 전이 확률 행렬 $\boldsymbol{A}$**: $a_{ij} = P(q_{t+1} = S_j | q_t = S_i)$ (상태 $i$에서 $j$로 전이할 확률). $\sum_j a_{ij} = 1$.
    * **방출 확률 행렬 $\boldsymbol{B}$**: $b_i(v_k) = P(O_t = v_k | q_t = S_i)$ (상태 $i$에서 관찰 $v_k$가 나올 확률). $\sum_k b_i(v_k) = 1$.
    * **초기 상태 확률 $\boldsymbol{\pi}$**: $\pi_i = P(q_1 = S_i)$ (시작 상태가 $i$일 확률). $\sum_i \pi_i = 1$.
* **HMM의 세 가지 주요 문제**:
    * **평가 (Evaluation)**: 주어진 모델 $\boldsymbol{\theta}$과 관찰 $\boldsymbol{O}$에 대해 $P(\boldsymbol{O} | \boldsymbol{\theta})$ 계산. (알고리즘: **전방 알고리즘**)
    * **디코딩 (Decoding)**: 주어진 모델 $\boldsymbol{\theta}$과 관찰 $\boldsymbol{O}$에 대해 가장 가능성 있는 은닉 상태 시퀀스 $\boldsymbol{S}^*$ 찾기. (알고리즘: **비터비 알고리즘**)
    * **학습 (Learning)**: 주어진 관찰 데이터 $\boldsymbol{O}$ (또는 여러 시퀀스)로부터 모델 파라미터 $\boldsymbol{\theta}^*$ 추정. (알고리즘: **Baum-Welch 알고리즘**)

## 3. 주요 알고리즘 세부 사항

* **평가 문제: 전방 알고리즘 (Forward Algorithm)**
    * **목표**: $P(\boldsymbol{O} | \boldsymbol{\theta})$ 계산.
    * **핵심**: 동적 계획법 사용. $\alpha_t(i) = P(O_1, \dots, O_t, q_t = S_i | \boldsymbol{\theta})$.
    * **초기화**: $\alpha_1(i) = \pi_i b_i(O_1)$
    * **재귀**: $\alpha_t(j) = \left[ \sum_{i=1}^N \alpha_{t-1}(i) a_{ij} \right] b_j(O_t)$
    * **종료**: $P(\boldsymbol{O} | \boldsymbol{\theta}) = \sum_{i=1}^N \alpha_T(i)$
* **평가 문제: 후방 알고리즘 (Backward Algorithm)**
    * **목표**: $P(\boldsymbol{O} | \boldsymbol{\theta})$ 계산 (다른 방식), Baum-Welch에서 사용됨.
    * **핵심**: $\beta_t(i) = P(O_{t+1}, \dots, O_T | q_t = S_i, \boldsymbol{\theta})$.
    * **초기화**: $\beta_T(i) = 1$
    * **재귀**: $\beta_t(i) = \sum_{j=1}^N a_{ij} b_j(O_{t+1}) \beta_{t+1}(j)$
    * **결합**: $P(\boldsymbol{O} | \boldsymbol{\theta}) = \sum_{i=1}^N \alpha_t(i) \beta_t(i)$ (임의의 $t$에 대해).
* **디코딩 문제: 비터비 알고리즘 (Viterbi Algorithm)**
    * **목표**: 가장 가능성 있는 은닉 상태 시퀀스 $\boldsymbol{S}^* = \arg \max_{\boldsymbol{S}} P(\boldsymbol{S} | \boldsymbol{O}, \boldsymbol{\theta})$ 찾기.
    * **핵심**: 동적 계획법 사용. $v_t(j)$는 시간 $t$에 상태 $j$에서 끝나는 가장 확률 높은 경로의 확률. $\tau_t(j)$는 해당 경로의 이전 상태 저장.
    * **초기화**: $v_1(j) = \pi_j b_j(O_1)$
    * **재귀**: $v_t(j) = \max_{1 \le i \le N} [v_{t-1}(i) a_{ij}] b_j(O_t)$, $\tau_t(j) = \arg \max_{1 \le i \le N} [v_{t-1}(i) a_{ij}]$
    * **종료 및 백트래킹**: $P(\boldsymbol{S}^*) = \max_{1 \le i \le N} v_T(i)$, $\hat{q}_T = \arg \max_{1 \le i \le N} v_T(i)$. $\tau$를 사용하여 $\hat{q}_{T-1}, \dots, \hat{q}_1$ 역추적.
* **학습 문제: Baum-Welch 알고리즘 (전방-후방 알고리즘)**
    * **목표**: 관찰 데이터로부터 HMM 파라미터 $\boldsymbol{\theta}^*$ 추정 (EM 알고리즘의 특수 형태).
    * **핵심**: E-단계와 M-단계를 반복하여 가능도 $P(\boldsymbol{O} | \boldsymbol{\theta})$를 최대화.
    * **E-단계 (Expectation)**: 현재 $\boldsymbol{\theta}$로 $\alpha_t(i)$, $\beta_t(i)$ 계산 후, 다음 두 확률 계산.
        * $\gamma_t(i) = P(q_t = S_i | \boldsymbol{O}, \boldsymbol{\theta})$ (시간 $t$에 상태 $i$일 확률)
        * $\xi_t(i, j) = P(q_t = S_i, q_{t+1} = S_j | \boldsymbol{O}, \boldsymbol{\theta})$ (시간 $t$에 $i$, $t+1$에 $j$일 확률)
    * **M-단계 (Maximization)**: $\gamma_t(i)$, $\xi_t(i, j)$를 사용하여 파라미터 $\boldsymbol{\theta}_{new}$ 업데이트 (기대 횟수 기반).
        * $\pi_i^{new} = \gamma_1(i)$
        * $a_{ij}^{new} = \frac{\sum_{t=1}^{T-1} \xi_t(i, j)}{\sum_{t=1}^{T-1} \gamma_t(i)}$
        * $b_j(v_k)^{new} = \frac{\sum_{t=1, O_t=v_k}^T \gamma_t(j)}{\sum_{t=1}^T \gamma_t(j)}$
    * **반복**: $\boldsymbol{\theta} \leftarrow \boldsymbol{\theta}_{new}$ 후 E-단계, M-단계 반복 (수렴 시까지).

## 4. 요약 및 결론

* HMM은 은닉된 상태와 관찰 가능한 결과를 연결하여 순차 데이터를 모델링하는 강력한 도구입니다.
* HMM의 실제 적용을 위해서는 **평가, 디코딩, 학습**의 세 가지 핵심 문제를 해결해야함.
* **전방/후방, 비터비, Baum-Welch 알고리즘**은 동적 계획법 및 EM 알고리즘을 기반으로 이 문제들을 효율적으로 해결하며, 음성 인식 등 다양한 분야에서 HMM 활용의 기반을 제공한다.
* 

