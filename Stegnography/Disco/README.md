# Writeup

## Disco
[Link](https://battle.cookiearena.org/challenges/stenography/0x0) bài lab.

FLAG format: CHH{XXXX}

### Công cụ
1. [CyberChef](https://gchq.github.io/CyberChef/)
2. Audacity, Sonic Visualiser

### Bắt đầu

Đầu tiên mình mở file lên rồi thêm Spectrogram để xem thì không phát hiện được gì. Đọc kỹ lại đề bài thấy phần đầu của file có chứa thông tin nên mình đã quyết định phóng to nó ra thì thấy 1 đoạn mã trong như là mã nhị phân.

![audacity](https://github.com/GnolV/CookieArenaCTF/blob/4e4e291bb150c90d37c0bd355fa9120878517c5b/Stegnography/Disco/audacity1.png)

Sau khi ngồi chuyển đoạn mã đó về dạng 0 1, mình thấy có gì đó rất lạ. Đoạn mã có tổng cộng 105 ký tự, nghĩa là không thể chia thành các đoạn 8 ký tự để giải mã được. Nhưng 105 lại chia hết cho 7, vậy thì ta sẽ chia đoạn mã đó thành các đoạn con có 7 ký tự rồi thêm 0 vào đầu mỗi đoạn. 

Giải mã xong để lấy được flag rồi submit thôi! 

![completed](https://github.com/GnolV/CookieArenaCTF/blob/14e9535b8fd1007651d04a5aa3396045235a671b/Stegnography/Disco/completed.png)

PS: Nhớ để ý FLAG format của bài khi submit đấy.
