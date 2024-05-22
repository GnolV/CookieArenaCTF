# Writeup

## Can you see me
[Link](https://battle.cookiearena.org/challenges/stenography/can-you-see-me) bài lab.

Writeup được viết dựa trên writeup của n9tr4o trên forum Cookie Arena [link](https://forum.cookiearena.org/t/stenography-can-you-see-me/61).

Format FLAG: ECH{XXXX}

### Công cụ
1. Exiftool, Python3,
2. [CyberChef](https://gchq.github.io/CyberChef)
3. [Aperi'Solve](https://www.aperisolve.com/)
4. [tomeko.net](https://tomeko.net/index.php?lang=en)

### Bắt đầu
Trước tiên, ta tải ảnh lên Aperi'Solve để phân tích ảnh.

![img](https://github.com/GnolV/CookieCTF/blob/6f43a63ef608e5d15281cfff777157fdaf540e0f/Stegnography/Can%20you%20see%20me/aperi1.png)

Ở đây, ta có thể thấy 1 dòng comment đặc biệt:
```
y0u_c4n_n0t_cr4ck_th1s_f1l3_w1th_th3_d3f4ut_w0rdl1st_0n_k4l1
```

Để ý nó.

Tiếp theo ở tool PngCheck, xuất hiện thông báo lỗi:
```
invalid chunk name "" (00 4E 00 47)
```

Phân tích ảnh về dạng Hex sử dụng CyberChef với Recipe là "To Hex" và để "16 bytes per line" rồi tìm đoạn hex trên.

![img](https://github.com/GnolV/CookieCTF/blob/6f43a63ef608e5d15281cfff777157fdaf540e0f/Stegnography/Can%20you%20see%20me/cyberchef1.png)

Đây là Header của file ảnh PNG nhưng có gì đó không đúng lắm. Kéo đến cuối ta thấy nó kết thúc ở 82. 

![img](https://github.com/GnolV/CookieCTF/blob/6f43a63ef608e5d15281cfff777157fdaf540e0f/Stegnography/Can%20you%20see%20me/cyberchef2.png)

Chuyển về "0 bytes per line" copy lại rồi lưu về máy.

![img](https://github.com/GnolV/CookieCTF/blob/6f43a63ef608e5d15281cfff777157fdaf540e0f/Stegnography/Can%20you%20see%20me/hex.png)

Có vẻ là bị chèn byte 00 vào giữa các byte. VIết code python để xử lý dữ liệu.

```python
with open("hex.txt", "r") as file:
    line = file.read() #Đọc dữ liệu trong file
    line = line.split() #Chuyển về danh sách các byte
    filted_line = [line[i] for i in range(len(line)) if i%2!=0] #Chỉ lấy các byte ở vị trí lẻ

with open("true_hex.txt", "w") as f:
    for i in filted_line:
        f.write(i + " ") #Lưu dữ liệu đã xử lý vào file mới
```

Xử lý xong ta được 1 file mới, đưa file này lên CyberChef với Recipe "Render Image" có "Input format" là Hex, ta sẽ được 1 file ảnh png.

![img](https://github.com/GnolV/CookieCTF/blob/6f43a63ef608e5d15281cfff777157fdaf540e0f/Stegnography/Can%20you%20see%20me/cyberchef3.png)

Tải ảnh về máy rồi đưa lên Aperi'Solve để phân tích.

![img](https://github.com/GnolV/CookieCTF/blob/6f43a63ef608e5d15281cfff777157fdaf540e0f/Stegnography/Can%20you%20see%20me/aperi2.png)

Không có gì đặc biệt khi phân tích bằng các công cụ nhưng khi phân tích thành dải màu RGB thì chỉ có màu Blue là trông khác biệt với Red và Green. Tới đây ta dùng module PIL của python để phân tích và lấy dữ liệu của dải màu Blue

```python
from PIL import Image

img = Image.open("pic2.png") #Thay tên ảnh bằng tên ảnh mà bạn lưu
pixel_access = img.load()

with open("result.txt", "w") as res:
    for y in range(img.height):
        for x in range(img.width):
            r,g,b = pixel_access[x,y] #Đọc thông số RGB
            res.write(f"{b} ") #Lưu dữ liệu vào file
```

Mở file lưu dữ liệu màu Blue lên

![img](https://github.com/GnolV/CookieCTF/blob/02a6c1c488edf13ad97e2b989e99d5b4dc4275c5/Stegnography/Can%20you%20see%20me/result.png)

Lại bị chèn byte 0 rồi, dùng file cũ để xử lý rồi lưu ra file mới. Đưa file mới này lên CyberChef phân tích. Sau khi thử decode về các định dạng khác ở trong thẻ "Data format" thì ở thẻ "From Decimal" công cụ tự động nhận diện đây là file zip. 

![img](https://github.com/GnolV/CookieCTF/blob/02a6c1c488edf13ad97e2b989e99d5b4dc4275c5/Stegnography/Can%20you%20see%20me/cyberchef4.png)

Tải về và giải nén ra thấy yêu cầu mật khẩu. Còn nhớ dòng comment đặc biệt ta tìm thấy khi mới phân tích file không? Đó chính là mật khẩu giải nén. Giải nén xong ta lấy được flag của bài lab.

![img](https://github.com/GnolV/CookieCTF/blob/02a6c1c488edf13ad97e2b989e99d5b4dc4275c5/Stegnography/Can%20you%20see%20me/flag.png)

Submit thôi!!!

![img](https://github.com/GnolV/CookieCTF/blob/02a6c1c488edf13ad97e2b989e99d5b4dc4275c5/Stegnography/Can%20you%20see%20me/completed.png)

PS: nhớ để ý format flag để không bị sai.
