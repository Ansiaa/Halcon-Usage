
# Creation (smallest_circle)

1. [ball.hdev](#ball)

---

<a name="ball" />

# ball.hdev

반도체 패키징 공정 중 하나인 볼 본딩(Ball Bonding) 영역을 자동으로 검사하고, 각 볼의 지름(Diameter) 등을 계산하고 표시하는 작업

```python
* ball.hdev: Inspection of Ball Bonding
* 
dev_update_window ('off')
dev_close_window ()
dev_open_window (0, 0, 728, 512, 'black', WindowID)
read_image (Bond, 'die/die_03')
dev_display (Bond)
```
### Bond image
<img width="528" height="312" alt="Image" src="https://github.com/user-attachments/assets/e6c1734a-53ee-4bce-84e0-5d90db3bc85f" />

---

```python
set_display_font (WindowID, 14, 'mono', 'true', 'false')
disp_continue_message (WindowID, 'black', 'true')
stop ()
threshold (Bond, Bright, 100, 255)
```
### Display 'Bright' 
<img width="362" height="250" alt="Image" src="https://github.com/user-attachments/assets/7484efac-f74a-4720-a0ad-3a1a68cf2de5" />

---

```python
shape_trans (Bright, Die, 'rectangle2')
dev_set_color ('green')
dev_set_line_width (3)
dev_set_draw ('margin')
dev_display (Die)
disp_continue_message (WindowID, 'black', 'true')
stop ()
```
### Display 'Die' 
<img width="361" height="254" alt="Image" src="https://github.com/user-attachments/assets/801fb759-f726-467d-9721-61f8e5d1aa60" />

---
```python
reduce_domain (Bond, Die, DieGrey)
```

### Display 'DieGrey' 
<img width="362" height="251" alt="Image" src="https://github.com/user-attachments/assets/74b0f9c3-2a42-4e61-9b09-3e34772c11be" />

---
```python
threshold (DieGrey, Wires, 0, 50)
```
### Display 'Wires' 
<img width="361" height="251" alt="Image" src="https://github.com/user-attachments/assets/6070e9db-e674-4c22-ae07-6fe6788388dc" />

---

```python
fill_up_shape (Wires, WiresFilled, 'area', 1, 100)
dev_display (Bond)
dev_set_draw ('fill')
dev_set_color ('red')
dev_display (WiresFilled)
disp_continue_message (WindowID, 'black', 'true')
stop ()
```
### Display 'WiresFilled' 
<img width="362" height="251" alt="Image" src="https://github.com/user-attachments/assets/c3660d52-c9ae-4e31-b837-9a6f8dd9fd5c" />

---
```python
opening_circle (WiresFilled, Balls, 15.5)
dev_set_color ('green')
dev_display (Balls)
disp_continue_message (WindowID, 'black', 'true')
stop ()
```
### Display 'Balls' 
<img width="359" height="254" alt="Image" src="https://github.com/user-attachments/assets/69366b43-600f-45d0-8d6b-d0a2b2f55c15" />

---
```python
connection (Balls, SingleBalls)
select_shape (SingleBalls, IntermediateBalls, 'circularity', 'and', 0.85, 1.0)
```
### Display 'SingleBalls' 
<img width="359" height="250" alt="Image" src="https://github.com/user-attachments/assets/8f9d4e9b-efee-4b51-b602-c9e9c5900f71" />

### Display 'IntermediateBalls' 
<img width="359" height="250" alt="Image" src="https://github.com/user-attachments/assets/8f9d4e9b-efee-4b51-b602-c9e9c5900f71" />

---

```python
sort_region (IntermediateBalls, FinalBalls, 'first_point', 'true', 'column')
dev_display (Bond)
dev_set_colored (12)
dev_display (FinalBalls)
disp_continue_message (WindowID, 'black', 'true')
stop ()
```
### Display 'FinalBalls' 
<img width="362" height="253" alt="Image" src="https://github.com/user-attachments/assets/c348c92e-7c83-4d1d-8fdf-22b0513cdf68" />

---

```python
smallest_circle (FinalBalls, Row, Column, Radius)
NumBalls := |Radius|
Diameter := 2 * Radius
meanDiameter := sum(Diameter) / NumBalls
mimDiameter := min(Diameter)
dev_display (Bond)
disp_circle (WindowID, Row, Column, Radius)
dev_set_color ('white')
for i := 1 to NumBalls by 1
    if (fmod(i,2) == 1)
        disp_message (WindowID, 'D: ' + Diameter[i - 1], 'image', Row[i - 1] - 2.7 * Radius[i - 1], max([Column[i - 1] - 60,0]), 'white', 'false')
    else
        disp_message (WindowID, 'D: ' + Diameter[i - 1], 'image', Row[i - 1] + 1.2 * Radius[i - 1], max([Column[i - 1] - 60,0]), 'white', 'false')
    endif
endfor
* dump_window (WindowID, 'tiff_rgb', './ball')
dev_set_color ('green')
dev_update_window ('on')
disp_continue_message (WindowID, 'black', 'true')
stop ()
dev_close_window ()
```
---

# Final
<img width="359" height="254" alt="Image" src="https://github.com/user-attachments/assets/2adcfe5b-5f0c-4823-8a02-85fc61f52835" />

---


