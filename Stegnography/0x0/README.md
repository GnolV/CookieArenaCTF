# Writeup

## 0x0
[Link](https://battle.cookiearena.org/challenges/stenography/0x0) bài lab.

Format FLAG: CHH{XXXX}

### Công cụ
1. Exiftool, Python3, Pngcheck, GHex
2. [Aperi'Solve](https://www.aperisolve.com/)
3. [CyberChef](https://gchq.github.io/CyberChef/)
4. [PNG dimensions bruteforces](https://github.com/cjharris18/png-dimensions-bruteforcer)

### Bắt đầu

Đầu tiên, mình sử dụng Exiftool để xem thông tin ảnh. Không có gì bất thường cả. Tiếp theo, mình sử dụng Pngcheck thì thấy xuất hiện thông báo sau chunk IEND vẫn còn dữ liệu.

[img](https://github.com/GnolV/CookieArenaCTF/blob/a3595975803a9e90693c4562938f7f1b1e091d6a/Stegnography/0x0/pngcheck.png)

Sử dụng GHex để xem dữ liệu đó là gì.

[img](https://github.com/GnolV/CookieArenaCTF/blob/a3595975803a9e90693c4562938f7f1b1e091d6a/Stegnography/0x0/ghex1.png)

Đó là dữ liệu của 1 file ảnh png khác. Trích xuất dữ liệu đó để lấy fiel ảnh thứ 2. Ở đây, mình đưa ảnh lên CyberChef, chuyển về định dạng hexdump rồi tải file đó về. Mình mở file đó lên và thấy dữ liệu file ảnh thứ 2 bắt đầu từ dòng 38644 nên mình viết code python để lấy dữ liệu. Các bạn có thể tham khảo:

```python
with open("download.txt", "r") as file:
    lines = file.readlines()
    
with open("hidden.txt", "w") as hidden:
    for i in range(len(lines)):
        if i >= 38644:
            hidden.write(lines[i])
```

Rồi mình lại đưa lên CyberChef để chuyển về ảnh png. Ảnh tải về không xem được nên mình sử dụng Pngcheck để kiểm tra thì thấy xuất hiện thông báo kích thước ảnh bị sai "chunk IHDR at offset 0x0000c, length 13: invalid image dimensions (0x0)".

[img](https://github.com/GnolV/CookieArenaCTF/blob/a3595975803a9e90693c4562938f7f1b1e091d6a/Stegnography/0x0/pngcheck2.png)

Sau một hồi lần mò mình tìm thấy công cụ "PNG dimensions bruteforcer" trên github cho phép ta sửa lại thông tin kích thước ảnh. Sau khi sửa xong ta được ảnh:

[img](https://github.com/GnolV/CookieArenaCTF/blob/a3595975803a9e90693c4562938f7f1b1e091d6a/Stegnography/0x0/fixed.png)

Dùng Exiftool, Binwalk, Zsteg không thấy có gì lạ cả nên mình đã đưa ảnh lên Aperi'Solve để phân tích. Mình phát hiện trong số các filter màu có 1 filter cho ra 1 ảnh có chứa mã QR. 

Tải ảnh đó về rồi cắt lấy phần mã QR ra. Tuy nhiên mã QR này bị ngược màu nên mình không quét được. Mình viết code python để chuyển màu ảnh đen sang trắng và trắng sang đen:

```python
from PIL import Image

img = Image.open("image_b_2_qr.png")
pixel = img.load()

for y in range(img.height):
    for x in range(img.width):
        if pixel[x,y] == (0, 0, 0, 255):
            pixel[x,y] = (255, 255, 255, 255)
        elif pixel[x,y] == (255, 255, 255, 255):
            pixel[x,y] = (0, 0, 0, 255)

img.save("image_qr.png", format="png")
```

Mở ảnh đã sửa lên, quét mã QR, lấy flag rồi submit thôi.

[img](https://github.com/GnolV/CookieArenaCTF/blob/a3595975803a9e90693c4562938f7f1b1e091d6a/Stegnography/0x0/completed.png)
