# Features (inner_rectangle1)

---

<a name="inner_rectangle1" />

# inner_rectangle1.hdev

나뭇결 이미지(wood_knots)에서 옹이(knot)가 없는 직사각형 영역을 자동으로 찾아내는 데 사용. 
주로 옹이가 없는 부분만 잘라내어 고품질 목재를 확보하는 작업


```python
* This example program shows how to use inner_rectangle1 to find knot-free
* rectangles in a piece of wood.  Typically, the piece of wood is then cut along
* the rectangles to obtain knot-free pieces of wood.
dev_update_window ('off')
dev_update_var ('off')
dev_update_pc ('off')
read_image (Image, 'wood_knots')
get_image_size (Image, Width, Height)
dev_close_window ()
dev_open_window (0, 0, Width, Height, 'black', WindowHandle)
* Set the thresholds on the size of the rectangular parts of the piece of wood.
MinAreaSize := 10000
MinWidth := 50
MinHeight := 50
dev_display (Image)
```

### wood_knots image
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/9674162c-d8c6-4948-aae0-36a513482e3c" />

---

```python
mean_image (Image, ThresholdImage, 31, 31)
dyn_threshold (Image, ThresholdImage, RegionDynThresh, 20, 'dark')
dev_display (RegionDynThresh)
```
### Display 'ThresholdImage' 
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/9b78fe03-c10f-4513-aac7-43330a3a5d4e" />

### Display 'RegionDynThresh' 
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/884c9428-e9b2-46e4-9790-040d639016d3" />

---

```python
erosion_circle (RegionDynThresh, RegionErosion, 1.5)
connection (RegionErosion, ConnectedRegions)
```
### Display 'RegionErosion' 
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/884c9428-e9b2-46e4-9790-040d639016d3" />

### Display 'ConnectedRegions' 
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/6aac9553-9db3-47c5-a121-649dbee2979a" />

---

```python
select_shape (ConnectedRegions, KnobRegions, 'area', 'and', 20, 200)
dilation_circle (KnobRegions, KnobRegionsDilated, 5.5)
union1 (KnobRegionsDilated, KnobRegionsUnion)
```
### Display 'KnobRegions' 
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/a15fa5e9-ebbc-472a-9025-30128e806be7" />

### Display 'KnobRegionsDilated' 
<img width="320" height="242" alt="Image" src="https://github.com/user-attachments/assets/93897f6b-f563-4a72-964c-8286ae9e1f25" />

### Display 'KnobRegionsUnion' 
<img width="322" height="242" alt="Image" src="https://github.com/user-attachments/assets/451dda6c-826b-4fd7-891b-90b7b33de3ce" />

---

```python
get_domain (Image, Domain)
difference (Domain, KnobRegionsUnion, KnobFreeRegion)
* Find inner rectangles as long as they fullfil the size restrictions and add them
* to the tuple of knot-free rectangles.
area_center (KnobFreeRegion, Area, Row, Column)
gen_empty_obj (KnobFreeRectangles)
```

### Display 'Domain' 
<img width="320" height="242" alt="Image" src="https://github.com/user-attachments/assets/44ad969b-1abd-4560-98a2-b1348db3fc4d" />

### Display 'KnobFreeRectangles'
<img width="320" height="242" alt="Image" src="https://github.com/user-attachments/assets/cf3f6f91-6184-4a33-885d-3c4f88a4cdae" />

---

```python
while (Area > MinAreaSize)
    inner_rectangle1 (KnobFreeRegion, Row1, Column1, Row2, Column2)
    RectangleHeight := Row2 - Row1
    RectangleWidth := Column2 - Column1
    if (RectangleHeight > MinHeight and RectangleWidth > MinWidth)
        gen_rectangle1 (Rectangle, Row1, Column1, Row2, Column2)
        concat_obj (KnobFreeRectangles, Rectangle, KnobFreeRectangles)
        difference (KnobFreeRegion, Rectangle, KnobFreeRegion)
        area_center (KnobFreeRegion, Area, Row, Column)
    else
        break
    endif
endwhile
dev_set_draw ('margin')
dev_set_line_width (2)
dev_display (Image)
dev_set_colored (12)
dev_display (KnobFreeRectangles)

```

### Display 'KnobFreeRectangles' 
<img width="326" height="245" alt="Image" src="https://github.com/user-attachments/assets/aa3350f3-d681-48eb-ad6a-aa1e8c1a971d" />

--
