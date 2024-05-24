# Writeup

## Split
[Link](https://battle.cookiearena.org/challenges/stenography/split) bài lab.

### Công cụ sử dụng
1. Exiftool, Binwalk, Zsteg, Python3
2. [Hexed](https://hexed.it/)
3. [Dcode](https://www.dcode.fr/)
4. [Steganography online](https://stylesuxx.github.io/steganography/)

### Bắt đầu
Đầu tiên, mình sử dụng exiftool để xem thông tin ảnh.

![Screenshot](https://github.com/GnolV/CookieCTF/blob/cba8eefecded05ea62339876d1fdc297db3cbe49/Stegnography/Split/exiftool.png)

Không có gì đặc biệt cả.

Đến lượt Binwalk xem sao.

![Screenshot](https://github.com/GnolV/CookieCTF/blob/cba8eefecded05ea62339876d1fdc297db3cbe49/Stegnography/Split/binwalk.png)

Bên trong có 1 file nén RAR. Trích xuất nó ra ta được 1 file tên 29645.rar, giải nén tiếp được 1 file khác tên canva không rõ định dạng. Đưa nó nên trang [Hexed.it](https://hexed.it/) xem sao.

![Screenshot](https://github.com/GnolV/CookieCTF/blob/cba8eefecded05ea62339876d1fdc297db3cbe49/Stegnography/Split/Hex_canva.png)

Header của file bị sai, phải là Rar! mới đúng. Sửa lại rồi tải về và đổi tên thành canva.rar. Sau khi giải nén, ta được 3 file canvas.png.001, canvas.png.002, canvas.png.003.

Trong đó, canvas.png.001 là 1 file ảnh png nhưng chỉ có 1 phần. 2 file còn lại trông giống dữ liệu ảnh nhưng không có header.

Loay hoay một lúc thì mình nhớ ra bài này tên là "Split" tức là 3 file này là của 1 ảnh bị tách ra. Vậy thì tiến hành nối chúng lại với nhau thôi.

Mình sử dụng python với module base64 để chuyển ảnh về chuỗi cho dễ nối lại:

```python3
import base64

img = open("img_name", "rb")
img_data = img.read()
img_base64 = base64.b64encode(img_data)

fo = open("image.txt", "wb")
fo.write(img_base64)
```

Sau khi nối lại xong thì ta chuyển định dạng từ base64 thành ảnh:

```python3
import base64

fr = open("image.txt", "r")
img_base64 = fr.read()

img_data = base64.b64decode(img_base64)
fo = open("combined_image.png", "wb")
fo.write(img_data)
```

Sau khi nối xong ta sẽ được ảnh sau

![Screenshot](https://github.com/GnolV/CookieCTF/blob/cba8eefecded05ea62339876d1fdc297db3cbe49/Stegnography/Split/combined_image.png)

Sau khi sử dụng Exiftool và Binwalk, mình xác định bên trong ảnh không còn chứa file nào. Bây giờ mình vào trang [Steganography online](https://stylesuxx.github.io/steganography/) để tìm kiếm dữ liệu ẩn trong ảnh.

![Screenshot](https://github.com/GnolV/CookieCTF/blob/cba8eefecded05ea62339876d1fdc297db3cbe49/Stegnography/Split/stegno_online.png)

Đây rồi! 

Cái đoạn ký tự kia chính là ngôn ngữ [Brainfuck](https://en.wikipedia.org/wiki/Brainfuck). Tới đây mình vào trang [Dcode](https://www.dcode.fr/brainfuck-language) để giải mã đoạn ký tự đó. Ban đầu mình dùng công cụ Zsteg nhưng không hiểu sao nó không lấy được đầy đủ đoạn ký tự đó.

![Screenshot](https://github.com/GnolV/CookieCTF/blob/cba8eefecded05ea62339876d1fdc297db3cbe49/Stegnography/Split/dcode.png)

Vậy là đã lấy được flag, submit thôi.

![Screenshot](https://github.com/GnolV/CookieCTF/blob/cba8eefecded05ea62339876d1fdc297db3cbe49/Stegnography/Split/completed.png)
