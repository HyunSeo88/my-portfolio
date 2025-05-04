---
layout: page
title: 특징 추출
permalink: /study/speech-recognition/02_features/
toc: true
toc_label: "목차"
toc_icon: "book"
date: 2025-05-04 12:01:00 +0900
---

# 음성 인식 특징 추출: MFCC (Mel-Frequency Cepstral Coefficients) 요약


## 주요 주제

* **음성 신호 전처리 (Preprocessing)**: 불필요한 성분 제거 및 특정 특성 강조.
* **신호 필터링 (Signal Filtering)**: 특정 주파수 대역 처리 (필터 뱅크).
* **멜 주파수 켑스트럼 계수 (MFCC)**: 인간 청각 특성을 반영한 스펙트럼 특징 추출.

## 핵심 아이디어 및 사실

### 1. 음성 인식 기본 단계

* **인지 단계 (Perception Stage)**: 음성 신호 처리 및 특징 추출.
* **인식 단계 (Recognition Stage)**: 추출된 특징과 음성 모델을 사용하여 텍스트 변환.

### 2. 전처리 (Preprocessing)

* **디더링 (Dithering)**: 무음 구간 스펙트로그램 계산 시 음의 무한대 값 방지를 위해 미세 노이즈 추가.
    > "Dithering is to add a very small noise value to speech waves"
* **DC 성분 제거 (DC Component Elimination)**: 신호의 평균값(DC 오프셋)을 제거하여 0 중심으로 맞춤. 푸리에 변환 오류 방지 및 스펙트럼 분석 정확도 향상.
    > $dc\_cut(f[n]) = f[n] - \frac{1}{N} \sum_{n=0}^{N-1} f[n]$
* **프리엠퍼시스 (Pre-emphasis)**: 고주파 성분 증폭. 스펙트럼 균형 맞추고, 수치 문제 회피 및 SNR 개선. ($\alpha \approx 0.97$ 사용).
    > "To amplify the high frequency components... balance the frequency spectrum..."

### 3. 스펙트로그램 (Spectrogram)

* 시간에 따른 주파수 스펙트럼 변화 시각화.
* 로그 계산 시 0 값으로 인한 음의 무한대 발생 가능성 (디더링으로 방지).
* 어두운 영역은 스펙트럼 피크(**포먼트, Formant**)를 나타냄. 포먼트와 그 변화는 소리 식별에 중요.
    > "Dark regions indicate peaks (formants) in the spectrum"
    > "Sounds can be identified much better by the Formants and by their transitions"

### 4. 인간 청각 시스템 (Human Auditory System)

* 가청 주파수 범위: 20Hz ~ 20,000Hz.
* 고주파 변화에 덜 민감함.
    > "Human's auditory system is less sensitive to changes at higher frequencies!"

### 5. 필터 뱅크 (Filter Bank)

* 여러 대역 통과 필터(Bandpass Filter)의 배열. 신호 차원 축소에 사용.
* 선형 필터 뱅크: 주파수 대역을 선형적으로 분할.

### 6. 멜 스케일 (Mel Scale)

* 인간이 인지하는 음높이(Pitch)의 지각적 척도.
* 변환 공식: $mel(f) = 2595 \log_{10}(1 + f/700)$
* 낮은 주파수 대역에 더 민감한 인간의 청각 특성을 반영.

### 7. 멜 필터 뱅크 (Mel Filter Bank)

* 멜 스케일 기반 필터 뱅크. 인간 청각 특성 반영 설계.
    > "This reflects the characteristics of humans' auditory system"
* 낮은 주파수 영역에 더 많은 필터를 배치.
* 출력에 로그 스케일을 적용하는 것이 일반적 ($FBANK_l = \log(b_{mel, l})$).

### 8. MFCC (Mel Frequency Cepstral Coefficients)

* 음성 신호 특징 추출 핵심 기법.
* 멜 필터 뱅크 특징에 **켑스트럼 분석**을 적용하여 얻음.
* 신호의 저주파 성분, 즉 **스펙트럼 엔벨로프(Spectral Envelope)** 정보를 포함.
    > "Contains the low frequency component (i.e., signal envelop) of the signal"

### 9. 켑스트럼 분석 (Cepstral Analysis)

* 스펙트럼에서 스펙트럼 엔벨로프와 세부 정보(미세 구조)를 분리하는 기법.
* 계산: 로그 스펙트럼에 역 푸리에 변환(IFFT 또는 DCT) 적용.
    > "Take the FFT of the spectrum! An FFT on spectrum referred to as Inverse FFT" (log domain에서)
* 결과 축은 **의사 주파수 (Pseudo-frequency)**.
* 켑스트럼의 저주파 영역 = 스펙트럼 엔벨로프. 음성 인식 특징으로 널리 사용됨.
    > "represents the spectral envelope and is widely used as feature for speech recognition."

### 10. MFCC 계산 과정

1.  **프레임화 (Framing) 및 윈도잉 (Windowing)**
2.  **FFT**: 각 프레임의 스펙트럼 계산.
3.  **멜 필터 뱅크 적용**: 멜 스케일 스펙트럼 계산.
4.  **로그 스케일 변환**: $\log(\text{Mel Spectrum})$.
5.  **DCT (Discrete Cosine Transform)**: 켑스트럼 계수 계산 → MFCC 특징.
    > "Discrete cosine transform (DCT) is commonly used to compute the MFCC features"

### 11. 스펙트로그램 vs. 켑스트로그램

* **스펙트로그램 (Spectrogram)**: 시간-주파수 에너지 분포.
* **켑스트로그램 (Cepstrogram)**: 시간-켑스트럼(의사 주파수) 분포.


