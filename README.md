# HCM-KS26-CNTT1_NhapMonCNTT_Session18_BTVN3
# BÀI 3: XỬ LÝ LỖI BỐ CỤC GIAO DIỆN HỆ THỐNG THEO DÕI XE

**Họ và tên:** Phạm Thành Đạo  
**Lớp:** HCM-KS26-CNTT1  
**Môn học:** Nhập môn CNTT  
**Session:** 16 – Ex03

---

# PHẦN 1. PHÂN TÍCH

## 1.1. Xác định phần tử bị lỗi bằng Inspect Element

Khi giao diện "Theo dõi lộ trình xe" bị chồng lấn, cần sử dụng Chrome DevTools để xác định chính xác phần tử HTML gây ra lỗi.

Thực hiện theo các bước:

**Bước 1:** Mở trang web hệ thống theo dõi xe.

**Bước 2:** Nhấn `F12` hoặc tổ hợp `Command + Option + I` trên macOS để mở Chrome DevTools.

**Bước 3:** Chọn công cụ Select Element (biểu tượng mũi tên trong ô vuông) hoặc nhấn `Command + Shift + C`.

**Bước 4:** Di chuyển chuột đến khung "Thông tin tài xế" đang đè lên khung "Danh sách trạm", sau đó nhấp chuột để chọn phần tử.

**Bước 5:** Quan sát tab Elements và cây DOM Tree để xác định thẻ HTML tương ứng.

Ví dụ cấu trúc HTML minh họa:

```html
<div class="tracking-container">
    <div class="driver-info">
        <h3>Thông tin tài xế</h3>
    </div>

    <div class="station-list">
        <h3>Danh sách trạm</h3>
    </div>
</div>
```

Trong tình huống này, phần tử cần kiểm tra đầu tiên là:

```css
.driver-info
```

Đồng thời cần kiểm tra phần tử cha `.tracking-container` và phần tử `.station-list` để xác định nguyên nhân chồng lấn.

**Lưu ý:** Đây là cấu trúc HTML giả định để minh họa. Khi thực hiện trên website thực tế, phải sử dụng đúng tên thẻ và class hiển thị trong DOM Tree.

## 1.2. Phân tích CSS Box Model

CSS Box Model là mô hình mô tả cách trình duyệt tính toán kích thước và khoảng không gian mà một phần tử chiếm trên trang web.

Box Model gồm 4 lớp:

1. **Content:** Vùng chứa nội dung như văn bản, hình ảnh hoặc thông tin tài xế.
2. **Padding:** Khoảng cách từ nội dung đến đường viền.
3. **Border:** Đường viền bao quanh nội dung và Padding.
4. **Margin:** Khoảng cách bên ngoài đường viền, giúp tách phần tử với các thành phần khác.

### Sơ đồ CSS Box Model

```text
+-------------------------------------------+
|                  MARGIN                   |
|   +-----------------------------------+   |
|   |              BORDER              |   |
|   |   +---------------------------+   |   |
|   |   |          PADDING          |   |   |
|   |   |   +-------------------+   |   |   |
|   |   |   |                   |   |   |   |
|   |   |   |      CONTENT      |   |   |   |
|   |   |   |                   |   |   |   |
|   |   |   +-------------------+   |   |   |
|   |   +---------------------------+   |   |
|   +-----------------------------------+   |
+-------------------------------------------+
```

### 1.3. Kiểm tra kích thước trong tab Computed

Sau khi chọn phần tử `.driver-info`:

1. Chọn tab **Computed** trong DevTools.
2. Cuộn xuống phần **Box Model**.
3. Quan sát các thông số Content, Padding, Border và Margin.
4. Kiểm tra các giá trị Width, Height và Position.
5. Đối chiếu với phần tử `.station-list` để xác định nguyên nhân chồng lấn.

Ví dụ minh họa thông số hiện tại:

| Thuộc tính | Giá trị giả định | Nhận xét |
|---|---:|---|
| Width | 600px | Khung khá rộng |
| Height | 200px | Chiều cao nội dung |
| Padding | 20px | Khoảng cách bên trong |
| Border | 1px | Đường viền |
| Margin | 0px | Không có khoảng cách bên ngoài |
| Position | absolute | Phần tử bị đưa khỏi luồng bố cục thông thường |

Sơ đồ Box Model hiện tại:

```text
.driver-info
Position: absolute

+--------------------------------------+
|             Margin: 0px              |
|  +--------------------------------+  |
|  |          Border: 1px            |  |
|  |  +--------------------------+  |  |
|  |  |       Padding: 20px      |  |  |
|  |  |  +--------------------+  |  |  |
|  |  |  |                    |  |  |  |
|  |  |  |  Content           |  |  |  |
|  |  |  |  600px x 200px     |  |  |  |
|  |  |  +--------------------+  |  |  |
|  |  +--------------------------+  |  |
|  +--------------------------------+  |
+--------------------------------------+
```

**Nhận xét:** Nếu phần tử `.driver-info` đang sử dụng `position: absolute`, nó có thể không còn chiếm vị trí trong luồng bố cục thông thường. Khi đó, phần tử `.station-list` có thể xuất hiện tại cùng khu vực và bị khung thông tin tài xế che lên.

Ngoài ra, cần kiểm tra các thuộc tính `top`, `left`, `right`, `z-index` và kích thước của phần tử cha để xác định chính xác nguyên nhân.

---

# PHẦN 2. TRIỂN KHAI

## 2.1. Đề xuất thông số CSS mới

Để sửa lỗi chồng lấn, có thể đưa hai khung về luồng bố cục bình thường và sử dụng Flexbox để sắp xếp chúng cạnh nhau hoặc xuống dòng khi không đủ không gian.

Thông số CSS đề xuất:

```css
.tracking-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    align-items: flex-start;
}

.driver-info {
    position: static;
    box-sizing: border-box;
    flex: 1 1 300px;
    min-width: 0;
    margin: 0;
    padding: 20px;
    border: 1px solid #ccc;
}

.station-list {
    position: static;
    box-sizing: border-box;
    flex: 1 1 300px;
    min-width: 0;
    margin: 0;
    padding: 20px;
    border: 1px solid #ccc;
}
```

### Giải thích các thuộc tính

| Thuộc tính | Ý nghĩa |
|---|---|
| `display: flex` | Sắp xếp các phần tử con theo bố cục Flexbox |
| `flex-wrap: wrap` | Cho phép phần tử xuống dòng khi không đủ chiều rộng |
| `gap: 20px` | Tạo khoảng cách 20px giữa hai khung |
| `position: static` | Đưa phần tử về luồng bố cục thông thường |
| `box-sizing: border-box` | Tính Width và Height bao gồm Content, Padding và Border |
| `flex: 1 1 300px` | Cho phép khung co giãn, với kích thước cơ sở 300px |
| `min-width: 0` | Cho phép phần tử Flex co nhỏ hơn kích thước nội dung mặc định |
| `margin: 0` | Xóa khoảng cách ngoài không cần thiết |
| `padding: 20px` | Tạo khoảng cách bên trong khung |

## 2.2. Thực hiện sửa lỗi trực tiếp trên Chrome DevTools

Theo yêu cầu đề bài, không chỉnh sửa file mã nguồn gốc. Tất cả thay đổi chỉ thực hiện trong tab Styles.

**Bước 1:** Mở DevTools và chọn tab Elements.

**Bước 2:** Chọn phần tử cha `.tracking-container`.

**Bước 3:** Trong tab Styles, thêm các thuộc tính:

```css
display: flex;
flex-wrap: wrap;
gap: 20px;
align-items: flex-start;
```

**Bước 4:** Chọn phần tử `.driver-info`.

Nếu đang có `position: absolute`, bỏ chọn thuộc tính cũ hoặc thêm:

```css
position: static;
box-sizing: border-box;
margin: 0;
padding: 20px;
flex: 1 1 300px;
```

**Bước 5:** Chọn phần tử `.station-list` và áp dụng:

```css
position: static;
box-sizing: border-box;
margin: 0;
padding: 20px;
flex: 1 1 300px;
```

**Bước 6:** Quan sát lại giao diện để xác nhận hai khung không còn chồng lấn.

Lưu ý: Các thay đổi trong DevTools chỉ có hiệu lực tạm thời trên trình duyệt hiện tại. Khi tải lại trang, thay đổi có thể bị mất. Đây là cách phù hợp với ràng buộc không chỉnh sửa mã nguồn gốc.

## 2.3. Box Model sau khi khắc phục

Sau khi sửa, mỗi khung được đưa về luồng bố cục Flexbox, có khoảng cách rõ ràng và không còn đè lên nhau.

Sơ đồ minh họa:

```text
+---------------------------------------------+
|              TRACKING CONTAINER             |
|                                             |
|  +-------------------+  20px  +------------+ |
|  |                   |        |            | |
|  |  THÔNG TIN        |        | DANH SÁCH  | |
|  |  TÀI XẾ           |        | TRẠM       | |
|  |                   |        |            | |
|  +-------------------+        +------------+ |
|                                             |
+---------------------------------------------+
```

Kết quả mong đợi:

- Hai khung được bố trí riêng biệt.
- Nội dung không bị che khuất.
- Có khoảng cách 20px giữa hai khung.
- Khi màn hình hẹp, các khung có thể tự động xuống dòng.
- Kích thước các khung được tính toán nhất quán nhờ `box-sizing: border-box`.

---

# PHẦN 3. KIỂM THỬ

## 3.1. Kiểm thử sau khi sửa lỗi

Thực hiện các bước:

1. Chọn phần tử `.driver-info` và `.station-list`.
2. Quan sát giao diện sau khi thay đổi CSS trong Styles.
3. Kiểm tra hai khung có còn chồng lấn không.
4. Thay đổi kích thước cửa sổ trình duyệt.
5. Kiểm tra khả năng tự động xuống dòng khi màn hình hẹp.

**Kết quả mong đợi:**

| Nội dung kiểm thử | Kết quả mong đợi |
|---|---|
| Hai khung thông tin | Không chồng lấn |
| Khoảng cách giữa hai khung | 20px |
| Nội dung bên trong | Hiển thị đầy đủ |
| Khi thu nhỏ màn hình | Hai khung có thể xuống dòng |
| File mã nguồn gốc | Không bị chỉnh sửa |

## 3.2. Tình huống thay đổi Margin nhưng phần tử không di chuyển

Nếu đã thay đổi `margin` trong tab Styles nhưng phần tử vẫn không di chuyển, cần kiểm tra các nguyên nhân sau:

### Nguyên nhân 1: Thuộc tính CSS bị ghi đè bởi !important

Một quy tắc CSS khác có thể sử dụng `!important` để ưu tiên thuộc tính của nó.

Ví dụ:

```css
.driver-info {
    margin: 0 !important;
}
```

Trong trường hợp này, quy tắc `margin: 20px` thông thường có thể không có hiệu lực.

**Cách kiểm tra:**

- Mở tab Styles.
- Tìm thuộc tính `margin`.
- Quan sát quy tắc nào đang được áp dụng và quy tắc nào bị gạch ngang.
- Kiểm tra xem có `!important` hay không.
- Thử thay đổi giá trị ngay tại Styles để xác định quy tắc đang có hiệu lực.

### Nguyên nhân 2: Thuộc tính Position

Nếu phần tử đang sử dụng `position: absolute` hoặc `position: fixed`, vị trí của nó còn phụ thuộc vào các thuộc tính `top`, `left`, `right`, `bottom`.

Ngoài ra, Margin có thể không tạo ra kết quả di chuyển như mong muốn nếu các thuộc tính định vị đang được thiết lập.

**Cách xử lý:** Kiểm tra `position`, sau đó thử đưa phần tử về `position: static` nếu phù hợp với cấu trúc bố cục.

### Nguyên nhân 3: Phần tử đang chịu ảnh hưởng của Flexbox hoặc Grid

Nếu phần tử nằm trong một container sử dụng Flexbox hoặc Grid, vị trí của nó còn phụ thuộc vào các thuộc tính bố cục như:

- `justify-content`
- `align-items`
- `gap`
- `flex`
- `grid-template-columns`

Khi đó, cần kiểm tra cả phần tử cha thay vì chỉ chỉnh Margin của phần tử con.

### Nguyên nhân 4: Browser User-Agent Stylesheet

Trình duyệt có một lớp CSS mặc định gọi là **User-Agent Stylesheet**. Lớp này cung cấp các kiểu hiển thị mặc định cho một số phần tử HTML.

Ví dụ: các thẻ Heading có thể được trình duyệt gán Margin mặc định.

Trong DevTools:

1. Chọn phần tử cần kiểm tra.
2. Mở tab Styles.
3. Quan sát phần quy tắc CSS có nguồn từ `user agent stylesheet`.
4. Kiểm tra thuộc tính nào đang được áp dụng mặc định.

Có thể thử khai báo lại thuộc tính trong Styles để kiểm tra kết quả.

**Lưu ý quan trọng:** User-Agent Stylesheet không phải lúc nào cũng ghi đè CSS do lập trình viên khai báo. CSS của trang web thông thường có thể ghi đè kiểu mặc định của trình duyệt. Ngoài ra, thuộc tính `!important`, độ ưu tiên Selector và thứ tự khai báo cũng có thể ảnh hưởng đến kết quả.

## 3.3. Kết luận kiểm thử

Nếu Margin thay đổi nhưng phần tử không di chuyển, không nên kết luận ngay rằng Browser Engine đang ghi đè CSS.

Cần kiểm tra lần lượt:

1. Quy tắc CSS có bị gạch ngang hoặc bị ghi đè không.
2. Có thuộc tính `!important` không.
3. Phần tử có đang sử dụng `position: absolute` hoặc `fixed` không.
4. Container cha có đang sử dụng Flexbox hoặc Grid không.
5. Có quy tắc mặc định từ User-Agent Stylesheet không.

Việc kiểm tra cả phần tử cha và phần tử con sẽ giúp xác định chính xác nguyên nhân và tránh chỉnh sửa sai vị trí.

---

# KẾT LUẬN

Qua bài thực hành, có thể thấy Chrome DevTools là công cụ hữu ích để kiểm tra và xử lý lỗi giao diện mà không cần trực tiếp chỉnh sửa file mã nguồn.

- Inspect Element giúp định vị phần tử HTML trong cây DOM Tree.
- Computed giúp kiểm tra kích thước thực tế và các lớp của CSS Box Model.
- Styles cho phép thử nghiệm và điều chỉnh thuộc tính CSS trực tiếp trên trình duyệt.
- Flexbox giúp sắp xếp các thành phần giao diện một cách linh hoạt.
- Việc kiểm tra CSS Cascade, `!important`, Position và User-Agent Stylesheet giúp tìm ra nguyên nhân khi thay đổi CSS không có hiệu lực.

Đối với hệ thống theo dõi xe, giải pháp phù hợp là kiểm tra phần tử gây chồng lấn, đưa các khung về luồng bố cục phù hợp và sử dụng Flexbox để tách biệt khu vực "Thông tin tài xế" và "Danh sách trạm".
