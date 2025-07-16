# Index 
## Features (gen_rectangle1)

1. [inner_circle.hdev](#inner_circle)
2. [gen_circle.hdev](#gen_circle)
3. 
---

<a name="inner_circle" />

# inner_circle.hdev

이미지에서 특정 범위 안의 밝기에 있는 객체들 사각형 표시

```python
read_image (Image, 'fabrik')
dev_close_window ()
dev_open_window (0, 0, 512, 512, 'black', WindowID)
dev_set_color ('white')
dev_set_draw ('fill')
regiongrowing (Image, Regions, 1, 1, 3, 500)
inner_circle (Regions, Row, Column, Radius)
dev_set_color ('red')
disp_circle (WindowID, Row, Column, Radius)
```
### 1. read_image( : Image : FileName : )
- 지정된 이미지 파일을 background 저장소에서 읽어와 이미지를 생성
- ⚠️ FileName에는 1개 이상 전달 가능, 2개 이상 전달 시 image object tuple 반환

#### 2. dev_close_window( : : : )
- close : 모든 열려 있는 HALCON 디스플레이 윈도우 종료

#### 3. dev_open_window( : : Row, Column, Width, Height, Background : WindowHandle)
- 원하는 좌표(Row, Column)에서 크기 (width,height) 만큼의 창을 열어 배경색을 지정하고 추가로 Handle 변수도 지정하는 함수

#### 4. dev_set_color( : : ColorName : )
- 색상 지정

#### 5. dev_set_draw ( : : DrawMode : )
- 영역 채우기 모드
- ⚠️ DrawMode : fill ( 영역이 다 채워짐 ), margin ( 윤곽선만 표시 )

### 6. regiongrowing(Image : Regions : Row, Column, Tolerance, MinSize : )
- Region Growing 기법 : 유사한 속성을 가진 픽셀들을 하나의 영역으로 묶는 세분화(segmentation) 기법
- Region Growing 절차 :
  1) 시드 포인트(Seed Point): 알고리즘이 시작할 초기 픽셀들을 지정. 지정한 픽셀은 기준점이 됨
  2) 성장 조건(Growth Criteria): 이웃한 픽셀이 기존 영역에 포함될지 여부를 판단하는 조건. 보통은 픽셀 간 그레이 값 차이, 또는 색상 차이 등이 사용
  3) 확장: 조건을 만족하는 주변 픽셀들을 기존 영역에 추가하고, 이 과정을 반복.
  4) 정지 조건: 더 이상 성장 조건을 만족하는 픽셀이 없을 때까지 반복.
- 원하는 좌표 위치에 픽셀 간 그레이 값 차이를 조건으로 설정해 확장해 세분화 한 기법

### 7. inner_circle(Regions : : : Row, Column, Radius)
- Region 영역에 Row, Column의 최대의 원을 그려서 반지름과 좌푤를 구

#### 8. disp_circle( : : WindowHandle, Row, Column, Radius : )


---
<a name="gen_circle" />

# gen_circle.hdev
```python
read_image (Image, 'fabrik')
dev_set_colored (6)
regiongrowing (Image, Regions, 1, 1, 3, 1000)
dev_set_color ('white')
inner_circle (Regions, Row, Column, Radius)
gen_circle (Circle, Row, Column, Radius)
```
### 1. read_image( : Image : FileName : )
- 지정된 이미지 파일을 background 저장소에서 읽어와 이미지를 생성
- ⚠️ FileName에는 1개 이상 전달 가능, 2개 이상 전달 시 image object tuple 반환

