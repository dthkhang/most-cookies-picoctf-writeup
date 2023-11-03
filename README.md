# [MOST COOKIES - PICOCTF (WRITE UP)](https://play.picoctf.org/practice/challenge/177?category=1&page=2&solved=0)
TAG: **picoCTF 2021** , **Web Exploitation**

Đề bài cho chúng ta 1 Link và 1 File Server.py, nếu để ý, ta sẽ thấy 4 keyword: encryption, flask, sesion, cookie

<img width="547" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 37 10" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/7e093dfb-bb1a-4488-9ba5-f5ae9d56705b">


Ngày khi truy cập vào link, ta sẽ được Flask server cấp 1 session như sau:

<img width="583" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 40 04" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/bbd3cd75-d1df-4471-a0c1-94b54913efba">


Ok để dùng ChatGPT để "encryption" thử xem ra gì nào :> 

<img width="750" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 42 34" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/331c7c7e-071d-4a7c-bd20-4494ee8720da">


Ahhh, ở đây ta nhận được 1 chuỗi json có key:value = "very_auth": "blank".

Mà "blank" = null, thử nghĩ xem tại sao là "blank" nhỉ? 

À ô input chưa có nhập gì hết trơn nè, nhập bậy bạ thử, ở đây tui nhập "admin" xem nó ra gì.

<img width="377" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 51 14" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/3d2bb8e2-2ae7-463e-9357-770a79379cdb">

<img width="371" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 51 41" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/c6db1cb1-03fd-4379-82e4-9fa9fb3822d7">


Cái "admin" tui nhập không hợp lệ rồi, mà cái placeholder="snickerdoodle" giống tên con chó quá ta, chắc có ý đồ nè, nhập thử xem sao

<img width="390" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 53 54" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/bc43a9bf-3ed0-4947-b9e3-0251673835f7">
<img width="593" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 56 13" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/87639d29-a269-495e-adb4-aa5f4cab967f">


Đúng luôn, để check coi cái session nó ra cái gì

<img width="531" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 00 21" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/4e9f33c7-b692-48cf-a1b0-5f4931d33b98"><img width="662" alt="Ảnh chụp Màn hình 2023-11-03 lúc 14 59 47" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/4d481328-3796-47c3-9166-22e93ad5860e">

À thì ra là cái value trong ô input được dùng để cấp session cho user, mà nãy tui nhập "admin" mà không được, suy ra Flask server có 1 secret list để cấp session, mà "admin" không có trong secret list nên không hợp lệ, và ngược lại thì chắc chắn tên con chó đó có trong secret list.

Giờ check qua code thử.

Đoán như thần luôn, vừa vào soucre server.py là bị đập vào mắt bởi 1 list cookie_names, mà cái item đầu tiên là tên con chó nãy trong placeholder input luôn (snickerdoodle)

<img width="529" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 06 02" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/dc16700a-15b6-4c63-8ef7-a23cc694d3fe">

Lướt xuống tí nữa mới thấp mình đúng thiệt :))

Đoạn này đọc cái IF là hiểu liền :)) chỉ có những item có trong list cookie_names mới được cấp session thôi :>

<img width="563" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 13 13" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/29dab135-2b90-4631-b3dd-0a747775eef5">

Nhưng mấu chốt là ở đây, tất cả tinh tuý trời đất để pass cái challenges là ở đâyyyyy

Đọc sơ là biết nếu check == "admin" thì pass :> 

Vậy thì chỉ cần thêm "admin" vào list cookie_names, rồi nhập admin vào input, lấy flag là Game over òi :> 

<img width="595" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 16 47" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/d5d30932-1c5d-4071-9a72-9d8778e58f0d">

Nhưng mà đời hong có như mơ dâu bạn ơi :> dễ như vậy thì đâu được gọi là challenges hehe

Mình chỉ sửa được soucre code hoy, chứ đâu có push được lên server đâu mà pass dễ dàng như vậy.

ĐẶT VẤN ĐỀ:

 - Server chỉ cấp session cho khi value input in item list
   
 - Route /dislay thì cần sessison == "admin" để pass
   
TÓM LẠI:

 - Làm sao value của session thành admin để pass

Sau 30s tìm kiếm thì tui tìm được 1 thư viên "Flask-Unsign", còn dùng làm gì thì Google nha, dài lắm rồi ~~

Trong Flask-Unsign có 1 câu lệnh dùng để fake cái cookie theo ý mình mà vẫn secret key của Flask server

<img width="752" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 42 24" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/28ad0a74-a21d-4486-badb-6a467ae62fa0">

Nhưng secret key là gì? Ai biết -.- 

Check lại code thử nhá

<img width="458" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 47 49" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/7f2f51bc-55b3-4740-bcef-84a2f6ad8424">

Oki, giờ thì đã biết secret key lấy random trong list :> 

Giờ thì lưu tất cả item trong list vào 1 file, đặt gì cũng được (ex: cookie.txt)

Đến phần tui thích nhất nè :> ta sẽ Brute Force để tìm secret key

Dùng cái này để Brute Force nha, còn cách dùng thì Google :>

**flask-unsign --wordlist cookie.txt --unsign --cookie 'eyJ2ZXJ5X2F1dGgiOiJibGFuayJ9.ZUS0mA.2tFVycHhO5gyPg0iBUUVc_j91HI' --no-literal-eval**

<img width="1015" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 55 04" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/e115da66-409f-4eb5-b5c8-b18b115270ca">

Sau khi có được secret key thì ta tiến hành fake cookie :>

Câu lệnh như sau:

**flask-unsign --sign --cookie "{'very_auth':'admin'}" --secret 'fortune'**

<img width="697" alt="Ảnh chụp Màn hình 2023-11-03 lúc 15 59 18" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/1a84da0b-81bd-41c6-91f6-2fa7d0bf31ce">

Giờ thì edit value session lại và Enter :> 
Bùmm ~ đây là kết quả

<img width="745" alt="Ảnh chụp Màn hình 2023-11-03 lúc 16 00 42" src="https://github.com/dthkhang/most-cookies-picoctf-writeup/assets/98313915/e236a50e-27da-4ff4-bd72-a0f60064987a">


