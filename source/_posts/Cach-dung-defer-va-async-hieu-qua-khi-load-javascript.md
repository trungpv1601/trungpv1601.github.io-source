---
title: Cách dùng defer và async hiệu quả khi load javascript
date: 2018-10-28 11:25:07
tags:
- javascript
- js
---

Khi load javascript trong trang HTML, bạn cần cẩn thận nếu không muốn làm ảnh hưởng tới tốc độ load trang. Tuỳ vào chỗ bạn đặt thẻ `<script>` (trong thẻ `<head>` hay ở cuối trang) sẽ đều ảnh hưởng tới thời gian tải trang.

Bình thường bạn sẽ dùng đoạn code bên dưới để load file js
```
<script src="script.js"></script>
```

Bất cứ khi nào HTML parser tìm thấy dòng code trên nó sẽ tải xuốn và thự thi script đó.

Mỗi lần load như vậy thì HTML trong trang sẽ được phân tích cho nên nó tốn rất nhiều thời gian và ảnh hưởng rất lớn tới việc load trang.

Nếu việc load script mất nhiều thời gian hơn, ví dụ khi mạng chậm thì việc chờ load xong script thì phần còn lại của HTML trên page mới được render thì người dùng sẽ nhìn thấy một trang trống không. Việc này rất khó chịu với việc trải nghiệm.

---

## 1. Vị trí đặt thẻ `<script>`

Khi bạn học về HTML thì sẽ được dạy đặt thẻ `<script>` trong thẻ `<head>` như vầy:

```
<html>
  <head>
    <title>Title</title>
    <script src="script.js"></script>
  </head>
  <body>
    ...
  </body>
</html>
```

Như đã phân tích ở trên, mỗi lần gặp thẻ `<script>` thì nó phải load và thực thi xong. Thì phần `<body>` mới được load.

Điều này không tốt vì phải chờ khi vào trang. Điều này cũng có thể giải quyết bằng cách đưa nó xuống cuối trước thẻ `</body>`.

Làm như vậy thì sẽ tối ưu rất nhiều trong việc load trang. HTML sẽ được load hết rồi sau đó javascript mới load.

Cách này là cách tốt nhất để để tối ưu việc load trang dành cho các trình duyệt cũ không hỗ trợ chức năng `async` và `defer`.

## 2. ASYNC và DEFER

async và defer là 2 thuộc tính boolean. Chúng được dùng như sau:

```
<script async src="script.js"></script>
```

```
<script defer src="script.js"></script>
```

Hai thuộc tính này chỉ hiểu khi ta đặt trong thẻ `<head>`. Nếu đặt trong `<body>` sẽ không có tác dụng.

## 3. So sánh hiệu suất

### a. Không async và defer, đặt trong `<head>`

Hình bên dưới mô tả việc load script không dùng async và defer. Được đặt trong thẻ `<head>`.

{% asset_img without-defer-async-head.png %}

Như các bạn thấy thì sẽ mất khoảng thời gian chờ cho đến khi js được load xong thì HTML mới được load tiếp.

### b. Không async và defer, đặt trong `<body>`

Hình bên dưới mô tả việc load script không dùng async và defer. Được đặt trên thẻ đóng `</body>`.

{% asset_img without-defer-async-body.png %}

Việc load sẽ không bị chờ script được load và thực thi. Khi đó thì trang sẽ đuọc hiển thị ngay khi truy cập và không hiển thị trang trắng như ví dụ trước.

### c. Dùng async, đặt trong `<head>`

Hình bên dưới mô tả việc load script dùng async. Được đặt trong thẻ `<head>`.

{% asset_img with-async.png %}

Script được tải xuống đồng bộ và chỉ chờ khi script thực thi.

### d. Dùng defer, đặt trong `<head>`

Hình bên dưới mô tả việc load script dùng defer. Được đặt trong thẻ `<head>`.

{% asset_img with-defer.png %}

Script sẽ được tải xuống đồng bộ với quá trình parse HTML và chỉ thực khi khi HTML đuọc parse xong.

Việc này cũng giống như việc ta dùng cách đưa tất cả `<script>` xuống trước thẻ đóng `</body>`.

Nhưng chỉ khác là nó đã được tải xuống trước đó rồi và không phải mất thời gian tải nữa mà thực thi luôn.

So cool! 🏆

## 4. Nguồn
- [EFFICIENTLY LOAD JAVASCRIPT WITH DEFER AND ASYNC](https://flaviocopes.com/javascript-async-defer/)