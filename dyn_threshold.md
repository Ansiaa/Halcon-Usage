
점자 이미지에서 점자를 구성하는 도트(raised dots) 들을 추출하는 법
---

```python
dev_update_off ()
* 
* Preparation
read_image (Image, 'photometric_stereo/embossed_01')
get_image_size (Image, Width, Height)
dev_get_window (WindowHandle)
if (WindowHandle >= 0)
    dev_resize_window_fit_image (Image, 0, 0, -1, -1)
else
    dev_open_window_fit_image (Image, 0, 0, -1, -1, WindowHandle)
endif
set_display_font (WindowHandle, 16, 'mono', 'true', 'false')
dev_set_line_width (2)
* 
dev_display (Image)
Message := 'Original image'
disp_message (WindowHandle, Message, 'window', 12, 12, 'black', 'true')
disp_continue_message (WindowHandle, 'black', 'true')
stop ()
```

### embossed_01 image
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/d60e59ba-5f79-4473-b6c0-06de263b4979" />

---

```python
* 
* Use a strong mean filter on the image to blur out the braille points
mean_image (Image, ImageMean, 59, 59)
* 
dev_display (ImageMean)
Message := 'Mean filtered image'
disp_message (WindowHandle, Message, 'window', 12, 12, 'black', 'true')
disp_continue_message (WindowHandle, 'black', 'true')
stop ()
```

### ImageMean image
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/f6fb6a1d-eb68-4445-8e06-134a38220c1a" />

---

```python
* 
* Compare the original image with the blurred one and determine the
* region where the gray values of the two images differ by more than 15
dyn_threshold (Image, ImageMean, RegionDynThresh, 15, 'not_equal')
* 
dev_display (Image)
dev_display (RegionDynThresh)
Message := 'Regions segmented with dyn_threshold'
disp_message (WindowHandle, Message, 'window', 12, 12, 'black', 'true')
disp_continue_message (WindowHandle, 'black', 'true')
stop ()
```
### RegionDynThresh image
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/6a1afe26-8c14-4000-aa05-5224272386e2" />

---

```python
* 
* Post-process the found regions
closing_circle (RegionDynThresh, RegionClosing, 8.5)
opening_circle (RegionClosing, RegionOpening, 6.5)
connection (RegionOpening, ConnectedRegions)
smallest_circle (ConnectedRegions, Row, Column, Radius)
gen_circle_contour_xld (ContCircle, Row, Column, Radius, 0, 6.28318, 'positive', 1)

```

### RegionClosing image
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/dbc192b0-a45d-4d77-98b8-87966b34efa0" />

### ConnectedRegions image
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/3cae3ad8-c569-448f-b347-3dd7b885470f" />

### ContCircle image
<img width="323" height="245" alt="Image" src="https://github.com/user-attachments/assets/4b6a9a65-adf5-464f-aab9-139bd465ad98" />

---

```python
* 
* Show the results
dev_display (Image)
dev_set_color ('green')
dev_display (ContCircle)
Message := 'Results of braille segmentation after morphology'
disp_message (WindowHandle, Message, 'window', 12, 12, 'black', 'true')
```

### result image
<img width="397" height="209" alt="Image" src="https://github.com/user-attachments/assets/6560686f-82e5-4f12-b634-5ba940958c44" />

