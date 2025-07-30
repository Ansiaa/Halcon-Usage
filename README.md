# Halcon-Usage

```python
mean_image(Image : ImageMean : MaskWidth, MaskHeight)
```
이미지의 평균 필터링으로 노이즈를 줄이고, 경계를 부드럽게 만듬

| 파라미터         | 설명                    |
| ------------ | --------------------- |
| `Image`      | 입력 이미지                |
| `ImageMean`  | 출력 이미지 (평균 필터링 후)     |
| `MaskWidth`  | 필터의 가로 크기 (양수, 홀수 권장) |
| `MaskHeight` | 필터의 세로 크기 (양수, 홀수 권장) |

---

```python
dyn_threshold(Image, ImageMean, RegionDynThresh, Offset, LightDark)
```
동적 이진화(Dynamic Thresholding) 를 수행. 
조명 변화나 배경이 불균일한 이미지에서 국소적으로 임계값을 적용해 객체를 분할할 때 매우 유용

| 파라미터              | 설명                                     |
| ----------------- | -------------------------------------- |
| `Image`           | 입력 이미지 (원본)                            |
| `ImageMean`       | 비교 기준이 되는 평균 이미지 (보통 `mean_image`로 생성) |
| `RegionDynThresh` | 출력: 이진화된 영역 (region)                   |
| `Offset`          | 차이 임계값. 이 값보다 차이가 크면 객체로 간주            |
| `LightDark`       | `'light'`, `'dark'`, `'equal'` 중 하나    |

---

```python
erosion_circle(Region, RegionErosion, Radius)
```
모폴로지 연산 중 침식(Erosion) 을 수행하는 함수.
바이너리 영역(region)의 가장자리를 둥근 형태로 줄여주는 역할을 하며, 
주로 노이즈 제거, 객체 분리, 얇은 선 제거 등에 사용


| 파라미터            | 설명                                    |
| --------------- | ------------------------------------- |
| `Region`        | 입력 영역 (Region, 바이너리 이미지 영역)           |
| `RegionErosion` | 출력: 침식된 영역                            |
| `Radius`        | 침식에 사용할 원(circle)의 반지름 (픽셀 단위, 양의 실수) |

---

```python
dilation_circle(Region, RegionDilation, Radius)
```
모폴로지 팽창 연산(Dilation) 중 하나로, 객체의 외곽을 원형 구조 요소를 사용하여 확장

객체 크게 만들거나, 구멍을 메우거나, 객체 간 연결을 유도할때 유용

| 파라미터             | 설명                      |
| ---------------- | ----------------------- |
| `Region`         | 입력 Region (바이너리 객체)     |
| `RegionDilation` | 출력: 팽창된 Region          |
| `Radius`         | 팽창에 사용되는 원의 반지름 (픽셀 단위) |

---

```python
closing_circle (Regions, RegionClosing, Radius)
```
형태학적 클로징(morphological closing)**을 수행
이미지나 바이너리 영역의 작은 구멍을 메우고, 끊긴 경계를 연결하는 데 매우 유용

| 파라미터            | 설명                   |
| --------------- | -------------------- |
| `Regions`       | 입력 바이너리 영역 (HObject) |
| `RegionClosing` | 결과 영역                |
| `Radius`        | 구조 요소의 반지름 (실수형)     |

## 작동 방식 (형태학적 클로징)
1. 팽창 (Dilation)

2. 수축 (Erosion)

둘 다 원형 구조 요소(disk-shaped structuring element) 를 사용하며, Radius에 따라 크기가 결정됩니다.


### 수식

closing(A) = erosion(dilation(A, B), B)

A는 입력 바이너리 영역

B는 반지름이 Radius인 원형 구조 요소

## 효과
1. 작은 구멍을 채움 (예: 노이즈나 결함)

2. 가까운 객체들이 연결됨 (예: 점자 도트가 너무 가까이 있을 때 하나로 인식)
---

```python
opening_circle (Regions, RegionOpening, Radius)
```
closing_circle과 쌍을 이루는 형태학적(morphological) 연산.
두 연산 모두 객체의 형상(structure)을 조작하여 잡음을 제거하거나, 객체를 정리하는 데 사용

| 파라미터            | 설명                   |
| --------------- | -------------------- |
| `Regions`       | 입력 이진 영역 (바이너리 객체)   |
| `RegionOpening` | 결과로 생성될 영역           |
| `Radius`        | 구조 요소의 반지름 (원형, 실수형) |

## 작동 방식 (형태학적 클로징)
1. 수축 (Erosion)

2. 팽창 (Dilation)

둘 다 원형 구조 요소(disk-shaped structuring element) 를 사용하며, Radius에 따라 크기가 결정됩니다.

### 수식

opening(A) = dilation(erosion(A, B), B)

A는 입력 바이너리 영역

B는 반지름이 Radius인 원형 구조 요소

## 효과
1. **작은 객체(노이즈)**를 제거

2. 경계를 부드럽게 만듦

3. 객체 간에 붙어 있던 것들을 분리할 수 있음 (반지름에 따라)

---
✅ closing_circle vs opening_circle

| 항목    | `closing_circle` | `opening_circle` |
| ----- | ---------------- | ---------------- |
| 연산 순서 | 팽창 → 수축          | 수축 → 팽창          |
| 용도    | 끊긴 부분 연결, 구멍 메우기 | 잡음 제거, 객체 분리     |
| 효과    | 객체 크기 약간 커짐      | 객체 크기 약간 작아짐     |
| 사용 목적 | 연결 및 내부 결함 보정    | 정제 및 노이즈 제거      |

---

```python
smallest_circle (Regions, Row, Column, Radius)
```
 바이너리 영역(Region) 각각에 대해 그 영역을 완전히 포함하는 
 가장 작은 원(minimum enclosing circle)을 계산해주는 연산

 | 파라미터      | 설명                                 |
| --------- | ---------------------------------- |
| `Regions` | 입력 바이너리 영역(HObject), 일반적으로 연결된 객체들 |
| `Row`     | 결과 원의 중심 Y 좌표 (행 좌표)               |
| `Column`  | 결과 원의 중심 X 좌표 (열 좌표)               |
| `Radius`  | 결과 원의 반지름                          |

---

```python
gen_circle_contour_xld (Circle : Row, Column, Radius, StartPhi, EndPhi, PointOrder, Resolution)
```
원을 외곽선(컨투어) 형태로 생성하는 함수
이는 점, 반지름, 방향 등 다양한 속성을 기반으로 시각화용 또는 분석용 원형 경계선을 생성할 수 있게 해줌.


| 파라미터         | 설명                                        |
| ------------ | ----------------------------------------- |
| `Circle`     | 생성될 XLD 컨투어 객체 (출력)                       |
| `Row`        | 원의 중심의 Y좌표 (행 좌표)                         |
| `Column`     | 원의 중심의 X좌표 (열 좌표)                         |
| `Radius`     | 원의 반지름                                    |
| `StartPhi`   | 원호 시작 각도 (라디안)                            |
| `EndPhi`     | 원호 종료 각도 (라디안)                            |
| `PointOrder` | 'positive' (시계 반대 방향), 'negative' (시계 방향) |
| `Resolution` | 곡선을 구성할 포인트 간 거리 (작을수록 더 매끄러운 원 생성)       |

---

```python
connection(Regions, ConnectedRegions)

```
이진 이미지(Region) 내에서 연결된 객체(connected components) 를 개별적으로 분리

| 파라미터               | 설명                                  |
| ------------------ | ----------------------------------- |
| `Regions`          | 입력 Region (여러 객체가 하나로 합쳐진 상태일 수 있음) |
| `ConnectedRegions` | 출력: 연결된 각 객체를 분리한 Region 배열         |

---

```python
select_shape(Regions, SelectedRegions, Features, Operation, Min, Max)

```

 Region(이진 객체 집합) 중에서 형태학적 속성(면적, 길이, 너비 등)을 기준으로 객체를 선별하는 데 사용
 
즉, connection으로 분리된 객체들 중에서 조건에 맞는 객체만 필터링할 때 사용

| 파라미터              | 설명                                                           |
| ----------------- | ------------------------------------------------------------ |
| `Regions`         | 입력 Region 객체들 (보통 `connection`의 결과)                          |
| `SelectedRegions` | 출력: 조건을 만족하는 Region 객체들                                      |
| `Features`        | 기준이 되는 형태학적 속성 이름 (예: `'area'`, `'height'`, `'circularity'`) |
| `Operation`       | 비교 연산자 (예: `'and'`, `'or'`, `'=<'`, `'>='`)                  |
| `Min`, `Max`      | 선택 기준 범위 (숫자)                                                |

 ---

```python
union1(Regions, RegionUnion)
```

하나의 Region 객체 내에 포함된 여러 개의 개별 영역을 하나로 합치는 연산자

즉, 여러 객체를 하나의 큰 Region으로 병합할 때 사용

| 파라미터          | 설명                            |
| ------------- | ----------------------------- |
| `Regions`     | 입력 Region 객체 (여러 개의 객체 포함 가능) |
| `RegionUnion` | 출력: 병합된 단일 Region             |

 ---

```python
get_domain(Image, Domain)
```
이미지의 유효 영역(도메인, domain) 을 얻는 데 사용

이미지 처리에세 도메인은 유효한 픽셀 영역을 의미하며, 일반적으로 이미지 전체 크기의 Region 형태로 반환됨.

| 파라미터     | 설명                    |
| -------- | --------------------- |
| `Image`  | 입력 이미지 (Gray/Color 등) |
| `Domain` | 출력: 이미지 도메인 Region    |

 ---

```python
difference(Region1, Region2, RegionDifference)
```
두 Region 간의 차집합 연산

| 파라미터               | 설명                          |
| ------------------ | --------------------------- |
| `Region1`          | 입력 Region 1 (기준 영역)         |
| `Region2`          | 입력 Region 2 (뺄 영역)          |
| `RegionDifference` | 출력: Region1에서 Region2를 뺀 영역 |

---

```python
area_center(Region, Area, Row, Column)
```
Region(객체)의 면적(area)과 무게중심(center of gravity) 좌표를 계산하는 함수
객체의 크기와 위치를 간단하게 파악할 때 매우 유용

| 파라미터     | 설명                       |
| -------- | ------------------------ |
| `Region` | 입력 Region (분석할 객체 영역)    |
| `Area`   | 출력: Region 전체 면적 (픽셀 단위) |
| `Row`    | 출력: 무게중심의 Y 좌표 (행 좌표)    |
| `Column` | 출력: 무게중심의 X 좌표 (열 좌표)    |

---

```python
gen_empty_obj(ObjectType, EmptyObject)
```

빈(비어있는) Region 또는 XLD 객체를 생성할 때 사용

| 목적              | 설명                    |
| --------------- | --------------------- |
| 빈 객체 초기화        | 객체 누적 및 병합을 위한 시작점 생성 |
| 조건에 따라 객체 생성/누적 | 루프 내에서 누적 연산 등에 사용    |
| 안전한 초기값 설정      | 빈 객체를 사용해 오류 방지       |

---

```python
concat_obj(Objects1, Objects2, ObjectsConcat)
```
여러 개의 Region 또는 XLD 객체를 하나의 객체 배열로 결합할 때 사용

즉, 각각의 객체들을 하나의 객체 배열로 "리스트처럼 묶는" 기능

단, 병합(union)은 하지 않음

| 파라미터            | 설명                                    |
| --------------- | ------------------------------------- |
| `Objects1`      | 입력 객체 1 (Region 또는 XLD 배열)            |
| `Objects2`      | 입력 객체 2                               |
| `ObjectsConcat` | 출력: `Objects1`와 `Objects2`가 결합된 객체 배열 |


---

```python
inner_rectangle1(Region, Row1, Column1, Row2, Column2)
```
Region 내부에 완전히 포함되는 최대 크기의 직사각형(축에 평행한 bounding box) 을 찾는 데 사용

즉, 주어진 Region 안에서 가장 큰 사각형을 구하는 것이 아니라, 

Region 내부에 꼭 맞게 들어가는 가장 큰 사각형을 계산

| 파라미터      | 설명                          |
| --------- | --------------------------- |
| `Region`  | 입력 Region (객체 영역)           |
| `Row1`    | 출력: 내부 사각형의 왼쪽 위 꼭짓점 행 좌표   |
| `Column1` | 출력: 내부 사각형의 왼쪽 위 꼭짓점 열 좌표   |
| `Row2`    | 출력: 내부 사각형의 오른쪽 아래 꼭짓점 행 좌표 |
| `Column2` | 출력: 내부 사각형의 오른쪽 아래 꼭짓점 열 좌표 |

---

```python
gen_rectangle1(Rectangle : Row1, Column1, Row2, Column2)
```
축에 평행한 사각형 Region을 생성

지정한 좌표만으로 간단하게 사각형을 만들 수 있어, ROI 설정이나 테스트용 마스크 생성 등에 자주 사용

| 파라미터        | 설명                          |
| ----------- | --------------------------- |
| `Rectangle` | 출력: 생성된 사각형 Region 객체       |
| `Row1`      | 사각형의 왼쪽 위 꼭짓점의 행 좌표 (Y좌표)   |
| `Column1`   | 사각형의 왼쪽 위 꼭짓점의 열 좌표 (X좌표)   |
| `Row2`      | 사각형의 오른쪽 아래 꼭짓점의 행 좌표 (Y좌표) |
| `Column2`   | 사각형의 오른쪽 아래 꼭짓점의 열 좌표 (X좌표) |


---
