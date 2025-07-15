# Index 
##Creation (gen_rectangle1)

1. [gen_rectangle1.hdev](#gen_rectangle1)
2. [adapt_pattern.hdev](#adapt_pattern)
3. 
---

<a name="gen_rectangle1" />

# gen_rectangle1.hdev

이미지에서 특정 범위 안의 밝기에 있는 객체들 사각형 표시

```python
read_image (Image, 'letters')
threshold (Image, Region, 0, 100)
closing_rectangle1 (Region, RegionClosing, 9, 5)
connection (RegionClosing, ConnectedRegions)
smallest_rectangle1 (ConnectedRegions, Row1, Column1, Row2, Column2)
gen_rectangle1 (Rectangles, Row1, Column1, Row2, Column2)
dev_set_draw ('margin')
dev_set_colored (6)
dev_display (Image)
dev_display (Rectangles)
```
### 1. read_image( : Image : FileName : )
- 지정된 이미지 파일을 background 저장소에서 읽어와 이미지를 생성
- ⚠️ FileName에는 1개 이상 전달 가능, 2개 이상 전달 시 image object tuple 반환

### 2. threshold(Image : Region : MinGray, MaxGray : )
- 이미지의 픽셀 범위가 MinGray <=, MaxGray >= 인 이진 영역 Region을 생성
- ⚠️ 정수 유형의 image 입력 시, 부동소수점 값 잘림

### 3. closing_rectangle1(Region : RegionClosing : Width, Height : )
- Region 영역에서 dilation_rectangle1을 실행 한 후 erosion_rectangle1을 실행.
- dilation_rectangle1 : 영역을 확장
- erosion_rectangle1 : 영역을 침식
- ⚠️ 각 입력 Region에 개별적으로 적용. 서로 다른 영역 사이의 간격을 닫으려면 합칩합을 먼저 호출 필요
- ⚠️ Width, Height 가 홀수여야 함. 아니면 더 큰 양으로 침식됨.

### 4. connection(Region : ConnectedRegions : : )
- 이진 영역 Region 내의 연결된 컴포넌트를 각각 개별적인 서브영역(Regions) 으로 분리

### 5. smallest_rectangle1 (Regions : : : Row1, Column1, Row2, Column2)
- Regions 영역의 주변 사각형(좌표 축에 평행하는)을 계산 하고 모서리픽셀( Row1 , Column1 , Row2 , Column2 )의 좌표로 표현
- ⚠️ 빈 영역의 경우 Row1 , Column1 , Row2 , Column2가 모두 0으로 혼동 유발

### 6. gen_rectangle1( : Rectangle : Row1, Column1, Row2, Column2 : )
- 왼쪽 위 모서리( Row1 , Column1 )와 오른쪽 아래 모서리( Row2 , Column2 ) 로 표현되는 좌표축에 평행한 하나 이상의 사각형을 생성
- ⚠️ 왼쪽 모서리 ( 0, 0 )

#### 7. dev_set_draw ( : : DrawMode : )
- 영역 채우기 모드
- ⚠️ DrawMode : fill ( 영역이 다 채워짐 ), margin ( 윤곽선만 표시 )

#### 8. dev_set_colored( : : NumColors : )
- 색상 표시
- ⚠️ 기본 : 6

#### 9. dev_display(Object : : : )
- 활성 그래픽 창에 아이콘 객체( 이미지, 영역 등 ) 표시

---

<a name="adapt_pattern.hdev " />

# adapt_pattern

이미지에서 패턴을 찾고, 상황에 따라 이미지를 업데이트하는 적응형 패턴 매칭 

```python
dev_update_off ()
dev_close_window ()
* 
* --------------------------------------------------------------------------------------------
* Online일때는 파일에서 이미지 읽어오고 Offline 일때는 'GigE Vision' 표준 카메라 사용해 읽어옴.
* Online/ Offline 둘다 해상도와 인터페이스 등은 카메라 환경과 동일하게 설정
* 현재 소스는 Online이므로 'File' 에서 이미지 읽어오고 'card/card/*.img'에 위치해 있는 폴더 경로에
* 1번 이미지 부터 2번 이미지 까지 불러오고
* AcqHandle로 이미지 제어할 핸들 변수 설정함.
*---------------------------------------------------------------------------------------------
Online := false
if (Online)
    open_framegrabber ('GigEVision', 0, 0, 0, 0, 0, 0, 'progressive', -1, 'default', -1, 'false', 'default', 'default', 0, 0, AcqHandle)
else
    open_framegrabber ('File', 1, 1, 0, 0, 0, 0, 'default', -1, 'default', -1, 'false', 'card/card', '', 1, 2, AcqHandle)
endif
* Grab and display an image
grab_image (Image, AcqHandle)
get_image_size (Image, Width, Height)
dev_open_window (0, 0, Width, Height, 'black', WindowHandle)
dev_display (Image)
dev_set_color ('green')
dev_set_draw ('margin')
dev_set_line_width (3)
*---------------------------------------------------------------------------------------------
* 0번 ~ 100 번 까지의 이미지를 수집해
* 창에 순차적으로 표시함.
*---------------------------------------------------------------------------------------------
if (Online)
    for Index := 0 to 100 by 1
        grab_image (Image, AcqHandle)
        dev_display (Image)
    endfor
endif
*-------------------------------------------------------------------------------------------
* Online일때는 마우스로 사각형 그려서 영역 선택,
* Offline일때는 미리 정의된 좌표값 으로 영역 선택 후 템플릿 생성
*-------------------------------------------------------------------------------------------
if (Online)
    draw_rectangle1 (WindowHandle, Row1, Column1, Row2, Column2)
    gen_rectangle1 (Rectangle, Row1, Column1, Row2, Column2)
    dev_display (Rectangle)
    reduce_domain (Image, Rectangle, ImageReduced)
    create_template (ImageReduced, 5, 4, 'sort', 'original', TemplateID)
else
    Row1 := 164.5
    Row2 := 196.5
    Column1 := 152.5
    Column2 := 229.5
    gen_rectangle1 (Rectangle, Row1, Column1, Row2, Column2)
    dev_display (Rectangle)
    reduce_domain (Image, Rectangle, ImageReduced)
    create_template (ImageReduced, 5, 4, 'sort', 'original', TemplateID)
endif

disp_message (WindowHandle, 'Initial template for pattern matching', 'window', 10, 10, 'black', 'true')
disp_continue_message (WindowHandle, 'black', 'true')
stop ()

* --------------------------------------------------------
* 템플릿 크기 지정
* --------------------------------------------------------

TemplateHeight2 := (Row2 - Row1) / 2.0
TemplateWidth2 := (Column2 - Column1) / 2.0
*-------------------------------------------------------------------------------------------
* Online일때는 마우스로 사각형 그려서 영역 선택,
* Offline일때는 미리 정의된 좌표값 으로 영역 선택 후 템플릿 생성
*-------------------------------------------------------------------------------------------

for Index := 0 to 80 by 1
    if (Online)
        grab_image_async (Image, AcqHandle, -1)
    else
        grab_image (Image, AcqHandle)
    endif
    dev_display (Image)
    * 
    * Adapt the template to the size of the image
    adapt_template (Image, TemplateID)
    * 
    * Search all matches of the template in the image and
    * return all points showing an error smaller than 20

    fast_match_mg (Image, Matches, TemplateID, 20, 3)
    area_center (Matches, Area, Row, Column)
    if (Area > 0)
        dilation_circle (Matches, MergedMatches, 3.5)
        connection (MergedMatches, ConnMatches)
        add_channels (ConnMatches, Image, ImageMatches)
        * 
        * Search the best match of the template and display
        * the found template
        best_match (ImageMatches, TemplateID, 20, 'true', Row, Column, Error)
        NumMatches := |Row|
        BestMatchIndex := -1
        BestMatchError := 255
        for I := 0 to NumMatches - 1 by 1
            if (Error[I] < 255)
                disp_message (WindowHandle, 'Template found', 'window', 10, 10, 'black', 'true')
                disp_rectangle1 (WindowHandle, Row[I] - TemplateHeight2, Column[I] - TemplateWidth2, Row[I] + TemplateHeight2, Column[I] + TemplateWidth2)
                BestMatchIndex := I
                BestMatchError := Error[I]
            endif
        endfor
        * 
        * Adapt the pattern
        * --------------------------------------------------------
        * Create a new template for the matching if the gray value
        * difference of the last best match is smaller than 15
        if (BestMatchIndex >= 0)
            if (BestMatchError < 15)
                clear_template (TemplateID)
                gen_rectangle1 (Rectangle, Row[BestMatchIndex] - TemplateHeight2, Column[BestMatchIndex] - TemplateWidth2, Row[BestMatchIndex] + TemplateHeight2, Column[BestMatchIndex] + TemplateWidth2)
                reduce_domain (Image, Rectangle, ImageReduced)
                create_template (ImageReduced, 5, 4, 'sort', 'original', TemplateID)
            endif
        endif
    endif
endfor
clear_template (TemplateID)
close_framegrabber (AcqHandle)

```
#### 1. dev_update_window( : : DisplayMode : )
- 자동 화면 업데이트 차단
- ⚠️ 기본 : off

#### 2. dev_close_window( : : : )
- 모든 열려 있는 HALCON 디스플레이 윈도우 종료

### 3. open_framegrabber ( : : 이름 , 수평 해상도 , 수직 해상도 , 이미지 너비 , 이미지 높이 , 시작 행 , 시작 열 , 필드 , 채널당 비트 수 , 색상 공간 , 일반 , 외부 트리거 , 카메라 유형 , 장치 , 포트 , 라인 입력 : AcqHandle )
- 선택된 이미지 수집 장치를 열고 구성


### 4. grab_image( : Image : AcqHandle : )
- open_framegrabber를 통해 AcqHandled로 지정된 이미지를 수집

### 5
. draw_rectangle1( : : WindowHandle : Row1, Column1, Row2, Column2)
- 창에서 만든 좌표측과 평행한 사각형에 대해 매개 변수를 변환
- ⚠️ 마우스 왼쪽 버튼을 눌러 사각형 모서리를 지정해야 함. 버튼을 누른 상태에서는 사각형을 원하는 방향으로 끌어다 놓을 수 있음.
- ⚠️ 생성된 가각형의 가운데를 클릭하면 이동가능하고 한쪽 면을 클릭하면 해당 명과 수직인 방향으로 사각형의 크리가 조정됨.
- ⚠️ 마우스 오른쪽 버튼을 누르면 종료

### 6. gen_rectangle1( : Rectangle : Row1, Column1, Row2, Column2 : )
- 왼쪽 위 모서리( Row1 , Column1 )와 오른쪽 아래 모서리( Row2 , Column2 ) 로 표현되는 좌표축에 평행한 하나 이상의 사각형을 생성
- ⚠️ 왼쪽 모서리 ( 0, 0 )

### 7. reduce_domain(Image, Region : ImageReduced : : )
- 이미지를 지정된 Region 영역으로 축소함
- ⚠️ 새로운 정의 영역은 이전 정의 영역과 지정된 영역의 교집합으로 계산됨.
- ⚠️ 새로운 영역은 지정된 영역의 부분집합이 될 수 있음.

### 8. create_template(Template : : FirstError, NumLevel, Optimize, GrayValues : TemplateID)
- Region 영역으로 매칭용 템플릿 (TemplateID) 생성
사용자에게 템플릿이 설정되었음을 안내하는 메시지를 표시

## 9. disp_message()
- 일반 메시지 출력 (화면 좌측 상단)

## 10. disp_continue_message
- "계속하려면 클릭하세요" 메시지와 유사

### 11. adapt_template ( 이미지 : : TemplateID :)
- 이미지 자동 보정

### 12. fast_match_mg(Image : Matches : TemplateID, MaxError, NumLevel : )




---
