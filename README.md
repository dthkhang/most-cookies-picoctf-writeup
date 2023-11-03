# [MOST COOKIES - PICOCTF (WRITE UP)](https://play.picoctf.org/practice/challenge/177?category=1&page=2&solved=0)
TAG: **picoCTF 2021** , **Web Exploitation**

Đề bài cho chúng ta 1 Link và 1 File Server.py, nếu để ý, ta sẽ thấy 4 keyword: encryption, flask, sesion, cookie

<img width="543" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 27 13" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/ce453079-a212-46f9-90b8-fdf998c3f98e">

Ngay khi truy cập vào link, ta sẽ được Flask server cấp 1 session như sau:

<img width="580" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 27 41" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/525f38e8-bb0d-45a5-8994-bc6c9b6b8ccc">


Ok để dùng ChatGPT decode thử xem ra gì nào :> 

<img width="745" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 27 58" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/8f5cf2b1-74b7-47c1-92e4-59e0027bf0d4">


Ở đây ta nhận được 1 chuỗi json có key:value = "very_auth": "blank".

Mà "blank" = null, thử nghĩ xem tại sao là "blank" nhỉ? 

Thì do ô input chưa có nhập gì hết trơn nè, nhập bậy bạ thử, ở đây tui nhập "admin" xem nó ra gì.

<img width="377" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 28 39" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/71ffdbac-01af-4024-84df-138a903ebb9a">
<img width="369" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 28 49" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/fe555a10-c4db-422e-b9c7-9aa36107829a">

Cái "admin" tui nhập không hợp lệ, mà cái placeholder="snickerdoodle" chắc có ý đồ, nhập thử xem sao

<img width="390" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 29 21" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/98803c3f-6737-4829-bfc8-b5398fc84a49">
<img width="433" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 29 38" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/353e8c26-090f-4374-85ce-fbeb3a91bae2">

Đúng luôn, để check coi cái session nó ra cái gì

<img width="533" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 30 13" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/73ca279f-95b1-4319-943a-bce4dc1cdf65">

<img width="343" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 30 28" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/da57969d-6558-4214-adaf-08d5d5402c8b">


Thì ra là cái value trong ô input được dùng để cấp session cho user, mà nãy tui nhập "admin" mà không được, suy ra Flask server có 1 secret list để cấp session, mà "admin" không có trong secret list nên không hợp lệ, và ngược lại thì chắc chắn tên con chó đó có trong secret list.

Giờ check qua code thử.

Đoán như thần luôn, vừa vào soucre server.py là bị đập vào mắt bởi 1 list cookie_names, mà cái item đầu tiên là cái placeholder luôn (snickerdoodle)

<img width="529" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 31 00" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/73271961-d3cc-41bb-8640-0f2565f18fab">

Lướt xuống tí nữa mới thấp mình đúng thiệt :))

Đoạn này đọc cái IF là hiểu liền :)) chỉ có những item có trong list cookie_names mới được cấp session thôi :>

<img width="563" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 31 19" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/e01357e3-dac4-4ccb-9bc9-40ecd225afa9">

Nhưng mấu chốt là ở đây, tất cả tinh tuý trời đất để pass cái challenges là ở đâyyyyy

Đọc sơ là biết nếu check == "admin" thì pass :> 

Vậy thì chỉ cần thêm "admin" vào list cookie_names, rồi nhập admin vào input, lấy flag là Game over òi :> 

<img width="592" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 31 39" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/b3289140-3f95-4acd-ad1a-e7c62d433a2a">

Nhưng mà đời hong có như mơ đâu bạn ơi :> dễ như vậy thì đâu được gọi là challenges hehe

Mình chỉ sửa được soucre code hoy, chứ đâu có push được lên server đâu mà pass dễ dàng như vậy.

ĐẶT VẤN ĐỀ:

 - Server chỉ cấp session cho khi value input in item list
   
 - Route /dislay thì cần sessison == "admin" để pass
   
TÓM LẠI:

 - Làm sao mà đổi value của session thành admin để pass

Sau 30s tìm kiếm thì tui tìm được 1 thư viên "Flask-Unsign", còn dùng làm gì thì Google nha, dài lắm rồi ~~

Trong Flask-Unsign có 1 câu lệnh dùng để fake cái cookie theo ý mình mà vẫn secret key của Flask server

<img width="743" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 31 58" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/6603607d-379c-4dba-ac94-52dc6b0f9971">

Nhưng secret key là gì? Ai biết -.- 

Check lại code thử nhá

<img width="463" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 32 19" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/3641358c-34a3-477d-9119-28aa115b5dba">

Oki, giờ thì đã biết secret key lấy random trong list :> 

Giờ thì lưu tất cả item trong list vào 1 file, đặt gì cũng được (ex: cookie.txt)

Đến phần tui thích nhất nè :> ta sẽ Brute Force để tìm secret key

Dùng cái này để Brute Force nha, còn cách dùng thì Google :>

**flask-unsign --wordlist cookie.txt --unsign --cookie 'eyJ2ZXJ5X2F1dGgiOiJibGFuayJ9.ZUS0mA.2tFVycHhO5gyPg0iBUUVc_j91HI' --no-literal-eval**

<img width="830" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 32 36" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/481b9d44-28d6-4f59-af4f-aafdbf443782">

Sau khi có được secret key thì ta tiến hành fake cookie :>

Câu lệnh như sau:

**flask-unsign --sign --cookie "{'very_auth':'admin'}" --secret 'fortune'**

<img width="695" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 32 57" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/42ecaa9b-ec31-4efc-b418-cc26a66f0dc3">

Giờ thì edit value session lại và Enter :> 

Bùmm ~ đây là kết quả

<img width="750" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 33 12" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/d6f97ebb-a654-40f6-b975-6ac38207444d">




