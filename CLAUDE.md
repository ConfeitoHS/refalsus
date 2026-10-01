# refalsus

로컬 경로: `desktop/code/refalsus`

같은 로직의 hue 치환 셰이더가 두 벌 있다.

- `HueReplace.fx`: ReShade FX
- `obs_hue_replace.shader`: obs-shaderfilter (`mainImage` 형식)

## 공통 동작

- **마스크**: RGB→HSV 변환 후, target hue와의 원형 hue 거리(부호 포함)로 마스크를 만든다.
  hue range와 softness는 `smoothstep`으로 적용하고, 채도·밝기 하한으로 무채색과 어두운 픽셀을 제외한다.
- **치환**: 결과 hue는 `new_hue + 부호 있는 hue 거리`로 계산해 그라데이션을 유지한다.
  여기에 채도·밝기 스케일을 곱한다.
- **영역 제한**: 꼭짓점 4개로 정의한 임의의 사각형.
  - 수평 반직선 교차 횟수로 안팎 판정
  - 변까지의 최소 거리로 부호 거리를 계산해 feather 적용
  - 거리는 화면 비율로 보정 (ReShade: `BUFFER_ASPECT_RATIO`, OBS: `uv_size`)
  - invert 옵션
- **디버그**: Show mask(흑백 마스크), Show region(영역 붉은 오버레이 + 노란 테두리)

## 규칙

- 두 파일의 파라미터 이름·의미·기본값을 **항상 동기화**한다.
  한쪽을 수정하면 반드시 다른 쪽에도 같은 변경을 반영한다.
