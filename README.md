# LinhScript (HQL) — Ngôn ngữ lập trình tiếng Việt do Hà Quang Linh xây dựng

LinhScript (tên viết tắt **HQL**, file có đuôi `.hql`) là một ngôn ngữ lập trình thông dịch được viết bằng **Java 21**.
Cú pháp gọn như JavaScript/Python, có sẵn **28 thư viện lõi** (toán, file, mạng, database, web server, game 2D,
xuất Excel/Word/PDF, gửi email, lập lịch, socket...) nên viết được từ script nhỏ tới website hoàn chỉnh mà không phải cài thêm gì.

> Tài liệu này mô tả đúng theo **mã nguồn** trong dự án (`Interpreter.java`, `Lexer.java`...).
> Chỗ nào docs cũ trong thư mục `docs/` khác với mã nguồn thì README này theo mã nguồn (xem mục 9).

## Mục lục

1. [Cài đặt & chạy chương trình](#1-cài-đặt--chạy-chương-trình)
2. [Cú pháp cơ bản](#2-cú-pháp-cơ-bản)
3. [Hàm lõi, phương thức chuỗi / mảng / map](#3-hàm-lõi-phương-thức-chuỗi--mảng--map)
4. [Hàm, class (OOP), `@` public/private](#4-hàm-class-oop--public--private)
5. [Gọi thư viện (`use`) và gọi file khác (`import`)](#5-gọi-thư-viện-use-và-gọi-file-khác-import)
6. [Danh mục 28 thư viện](#6-danh-mục-28-thư-viện)
7. [Chi tiết từng thư viện (đầy đủ hàm + code mẫu)](#7-chi-tiết-từng-thư-viện)
8. [Tệp cấu hình `.henv`](#8-tệp-cấu-hình-henv)
9. [Lưu ý quan trọng & lỗi thường gặp](#9-lưu-ý-quan-trọng--lỗi-thường-gặp)

---

## 1. Cài đặt & chạy chương trình

Bộ cài đặt chính thức đã đóng gói sẵn toàn bộ môi trường chạy (không cần cài thêm JDK hay cấu hình thủ công).

### Bước 1: Tải bộ cài đặt
👉 **[Tải về LinhScript-Setup-1.0.0.exe (Windows 64-bit)](https://github.com)**

### Bước 2: Cài đặt vào máy
1. Mở file **`LinhScript-Setup-1.0.0.exe`** vừa tải về.
2. Tích chọn **"Add LinhScript to PATH (Khuyên dùng)"** rồi bấm **Next → Install**.
3. Chương trình sẽ tự động cài đặt gọn gàng vào thư mục `C:\Users\<Tên_Bạn>\AppData\Local\Programs\LinhScript` và cấu hình sẵn lệnh hệ thống.

### Bước 3: Chạy chương trình đầu tiên
1. Tạo một file mới tên là **`hello.hql`** với nội dung:
   ```java
   print("Xin chào LinhScript!");
   var ten = "Linh";
   print("Tôi là " + ten);
   ```
2. Mở **Command Prompt (CMD)** hoặc **PowerShell** tại thư mục chứa file và gõ lệnh:
   ```bash
   LinhScript hello.hql
   ```
---

## 2. Cú pháp cơ bản

### 2.1 Câu lệnh, ghi chú

- Kết thúc câu lệnh bằng `;` **hoặc** xuống dòng. Xuống dòng bên trong `( )` / `[ ]` không kết thúc câu lệnh nên mảng/biểu thức viết trải nhiều dòng được.
- Ghi chú: `// một dòng`, `# một dòng`, `/* nhiều dòng */`.
- Chuỗi chỉ dùng **nháy kép** `"..."`. Hỗ trợ escape (`\n`, `\t`, `\"`, `\\`).
- Chuỗi nhiều dòng (Text Block, giống Java), không cần escape, hợp để nhúng HTML/JSON:

```java
var html = """
    <h1>Xin chào</h1>
    <p>Dòng thứ hai</p>
""";
```

### 2.2 Biến và kiểu dữ liệu

```java
var a = 10;            // số (mọi số đều là số thực double; 10.0 in ra là 10)
var ten = "Linh";      // chuỗi
var dung = true;       // logic: true / false
var rong = null;       // rỗng
var ds = [1, 2, 3];    // mảng (list)
var sv = { "ten": "Linh", "tuoi": 17 };   // bảng (map) khoá-giá trị
print(sv.ten);         // truy cập khoá bằng dấu chấm
print(sv["tuoi"]);     // hoặc bằng []
ds[0] = 99;            // gán phần tử
```

Kiểu trả về của `type(x)`: `"null"`, `"string"`, `"number"`, `"bool"`, `"list"`, tên class cho object, còn map trả `"unknown"`.

### 2.3 Toán tử

| Nhóm      | Toán tử                                   |
| --------- | ----------------------------------------- |
| Số học    | `+ - * / % ^` (`^` là lũy thừa)           |
| Gán       | `= += -= *= /=`                           |
| Tăng/giảm | `i++` `i--`                               |
| So sánh   | `== != > < >= <=`                         |
| Logic     | `and` / `&&`, `or` / `\|\|`, `not` / `!`  |
| 3 ngôi    | `dieuKien ? giaTri1 : giaTri2`            |
| Khoảng    | `1 .. 5` (dùng trong `for`, **gồm cả 5**) |

`+` nối chuỗi khi một vế là chuỗi: `"Tuổi: " + 17` → `"Tuổi: 17"`.

### 2.4 Rẽ nhánh

```java
var diem = 8;
if (diem >= 9) { print("Xuất sắc"); }
else if (diem >= 7) { print("Khá"); }
else { print("Cần cố gắng"); }

print(diem >= 5 ? "Đậu" : "Rớt");

switch (diem) {
    case 10: print("Tuyệt đối"); break;
    case 9:  print("Giỏi");      break;
    default: print("Khác");
}
```

> `switch` không có `break` sẽ chạy tuột xuống case kế (giống C/Java). Đặt `default:` ở **cuối**.

### 2.5 Vòng lặp

```java
for i in 1 .. 5 { print(i); }                 // 1,2,3,4,5 (gồm 5)
for sv in ["An", "Bình", "Chi"] { print(sv); } // duyệt mảng
for c in "abc" { print(c); }                  // duyệt từng ký tự

var n = 0;
while (n < 3) { print(n); n++; }

for i in 1 .. 10 {
    if (i == 3) { continue; }   // bỏ qua lượt này
    if (i == 6) { break; }      // thoát vòng lặp
    print(i);
}
```

> HQL **không có** vòng `for (i = 0; i < n; i++)` kiểu C. Dùng `for i in 0 .. n-1`.
> Muốn lặp theo bước nhảy: `for i in range(0, 10, 2)` (cần `use hql.util;`, `range` **không gồm** số cuối).

### 2.6 Bắt lỗi

```java
try {
    throw "Có lỗi rồi";
} catch (e) {            // e là CHUỖI thông báo lỗi
    print("Bắt được: " + e);
} finally {
    print("Luôn chạy");
}
```

Lỗi không bắt sẽ in ra có **tên file, số dòng, đoạn code và đường đi các hàm** (do `BaoLoi.java`).

### 2.7 Danh sách từ khóa

`var func return class extends new import use if else while for in to switch case default break continue try catch finally throw true false null and or not`

---

## 3. Hàm lõi, phương thức chuỗi / mảng / map

### 3.1 Hàm lõi (luôn có sẵn, không cần `use`)

| Hàm                   | Công dụng                                                               | Ví dụ                     |
| --------------------- | ----------------------------------------------------------------------- | ------------------------- |
| `print(x)`            | In ra màn hình                                                          | `print("Hi")`             |
| `input([lời_nhắc])`   | Đọc bàn phím; tự đổi thành số / `true` / `false` / `null` nếu đúng dạng | `var t = input("Tuổi: ")` |
| `type(x)`             | Tên kiểu dữ liệu                                                        | `type(5)` → `"number"`    |
| `str(x)`              | Ép sang chuỗi                                                           | `str(5)` → `"5"`          |
| `num(x)`              | Ép sang số (không đổi được → `0`)                                       | `num("12")` → `12`        |
| `bool(x)`             | Ép sang logic (số khác 0 → true; chuỗi bất kỳ → true)                   | `bool(0)` → `false`       |
| `len(x)` / `size(x)`  | Độ dài chuỗi hoặc số phần tử mảng (map/khác → `0`)                      | `len("abc")` → `3`        |
| `call("tenHam", ...)` | Gọi hàm theo tên lưu trong biến                                         | `call("tinhTong", 1, 2)`  |

> `print` hoạt động bình thường; nếu bạn đã `use hql.math;` nó vẫn là cùng một hàm.

### 3.2 Phương thức của chuỗi

| Phương thức                                     | Ý nghĩa                                                   |
| ----------------------------------------------- | --------------------------------------------------------- |
| `trim()` / `strip()`                            | Bỏ khoảng trắng hai đầu                                   |
| `lstrip()` / `rstrip()`                         | Bỏ khoảng trắng bên trái / phải                           |
| `split(sep)`                                    | Cắt thành mảng (bỏ trống `sep` thì cắt theo khoảng trắng) |
| `substring(bat_dau[, ket_thuc])`                | Lấy đoạn con (vượt quá độ dài thì trả nguyên chuỗi)       |
| `toLowerCase()` / `toUpperCase()`               | Chữ thường / chữ hoa                                      |
| `indexOf(s)` / `lastIndexOf(s)`                 | Vị trí xuất hiện đầu / cuối (`-1` nếu không có)           |
| `startsWith(s)` / `endsWith(s)` / `contains(s)` | Kiểm tra, trả `true`/`false`                              |
| `replace(cu, moi)`                              | Thay mọi `cu` bằng `moi`                                  |
| `insert(vi_tri, s)`                             | Chèn chữ vào vị trí                                       |
| `removeAt(vi_tri)`                              | Xoá ký tự tại vị trí                                      |
| `containsRegex(regex)`                          | Có đoạn khớp biểu thức chính quy không                    |
| `matches(regex)`                                | Cả chuỗi khớp 100% regex không                            |

```java
var s = "  Xin chào Việt Nam  ";
print(s.trim().toUpperCase());          // XIN CHÀO VIỆT NAM
print(s.trim().split(" "));             // [Xin, chào, Việt, Nam]
print("a,b,c".replace(",", "-"));       // a-b-c
print("abc123".matches("[a-z]+[0-9]+")); // true
```

### 3.3 Phương thức của mảng

| Phương thức                                                   | Ý nghĩa                                                        |
| ------------------------------------------------------------- | -------------------------------------------------------------- |
| `push(x)` / `add(x)`                                          | Thêm vào cuối, trả số phần tử mới                              |
| `pop()`                                                       | Lấy và xoá phần tử cuối                                        |
| `set(vi_tri, x)`                                              | Đổi giá trị tại vị trí                                         |
| `insert(vi_tri, x)`                                           | Chèn vào vị trí                                                |
| `removeAt(vi_tri)`                                            | Xoá tại vị trí                                                 |
| `indexOf(x)`                                                  | Vị trí của `x` (`-1` nếu không có)                             |
| `clear()`                                                     | Xoá hết                                                        |
| `join(sep)`                                                   | Nối thành một chuỗi                                            |
| `map(tenHam)` / `filter(tenHam)` / `reduce(tenHam, khoi_tao)` | Biến đổi / lọc / gộp. Hàm callback do bạn khai báo bằng `func` |

```java
func gapDoi(x) { return x * 2; }
func chan(x) { return x % 2 == 0; }
func cong(tong, x) { return tong + x; }

var ds = [1, 2, 3, 4];
ds.push(5);
print(ds.map(gapDoi));        // [2, 4, 6, 8, 10]
print(ds.filter(chan));       // [2, 4]
print(ds.reduce(cong, 0));    // 15
print(ds.join(" - "));        // 1 - 2 - 3 - 4 - 5
```

### 3.4 Map (bảng khoá-giá trị)

```java
var sp = { "ten": "Laptop", "gia": 1200 };
print(sp.ten);            // Laptop
print(sp["gia"]);         // 1200
sp["kho"] = 5;            // thêm khoá mới
print(sp.khongCo);        // chuỗi rỗng "" (không báo lỗi)
```

Kết quả `query(...)` của database là **mảng các map**: `for dong in query("SELECT ...") { print(dong.TenSP); }`.

---

## 4. Hàm, class (OOP), `@` public/private

### 4.1 Hàm

```java
func tinhTong(a, b) {
    return a + b;
}
print(tinhTong(2, 3));    // 5
```

- Gọi hàm phải truyền **đúng số tham số** (không có tham số mặc định, không có tham số rải).
- Hàm có thể gọi đệ quy, có biến cục bộ riêng.

### 4.2 Class

```java
class DongVat {
    var @ten;                                // @ = public
    func @init(t) { this.@ten = t; }         // constructor: init hoặc @init
    func @keu() { return this.@ten + " kêu"; }
}

class Cho extends DongVat {                  // kế thừa
    func @sua() { return this.@ten + " sủa gâu gâu"; }
}

var c = new Cho("Mực");
print(c.@keu());     // Mực kêu
print(c.@sua());     // Mực sủa gâu gâu
```

**Quy tắc `@`:**

| Viết                                                | Ý nghĩa                                                    |
| --------------------------------------------------- | ---------------------------------------------------------- |
| `var @ten` / `func @ham()` trong class              | **Public**: gọi được từ bên ngoài (`obj.@ten`)             |
| `var ten` / `func ham()` (không `@`)                | **Private**: chỉ truy cập được qua `this.` bên trong class |
| `var @x` / `func @f()` / `class @Lop` ở ngoài class | **Xuất ra** khi file này được `import` vào file khác       |

Truy cập thành viên private từ ngoài sẽ báo `Lỗi OOP: ... là Private`.

---

## 5. Gọi thư viện (`use`) và gọi file khác (`import`)

### 5.1 `use` — bật thư viện

```java
use hql.math;
use hql.io;
use hql.database;
```

- Mỗi thư viện phải `use` trước khi gọi hàm của nó. Gọi hàm khi chưa `use` sẽ báo
  `Hàm không có sẵn hoặc thư viện chưa được 'use'.`
- `hql.web` và `hql.io` được tự bật khi chạy trong chế độ web.
- `use hql.math;` còn khai báo thêm các hằng đổi đơn vị: `KM_TO_MILE`, `MILE_TO_KM`, `INCH_TO_CM`, `CM_TO_INCH`, `KG_TO_POUND`, `POUND_TO_KG`, `LITRE_TO_GALLON`, `GALLON_TO_LITRE`, `METER_TO_FEET`, `FEET_TO_METER`, `CELSIUS_OFFSET`.

### 5.2 `import` — nạp file `.hql` khác

File `tienich.hql`:

```java
var @PHI_VAT = 0.1;                   // có @ → được xuất ra
var bi_mat = 123;                     // không @ → bị ẩn

func @tinhThue(gia) { return gia * @PHI_VAT; }   // có @ → xuất ra
func noiBo() { return 1; }                        // không @ → bị ẩn

class @SanPham { var @ten; func @init(t) { this.@ten = t; } }
```

File `main.hql`:

```java
import "tienich.hql";
print(@tinhThue(1000));        // 100
var sp = new @SanPham("Laptop");
```

Khi import, HQL chỉ nạp **khai báo** (`var`, `func`, `class`) và **không chạy** `print`, `if`, vòng lặp... của file kia. Chỉ những tên có `@` mới sống sót; tên không có `@` bị xoá.
Class từ file import **phải** có `@` ở tên class (`class @SanPham`), nếu không `new` sẽ báo không tìm thấy lớp.

---

## 6. Danh mục 28 thư viện

| #   | Thư viện       | Dùng để                                                                  |
| --- | -------------- | ------------------------------------------------------------------------ |
| 1   | `hql.math`     | Toán học, thống kê, ma trận, đổi đơn vị                                  |
| 2   | `hql.io`       | Đọc/ghi file, thư mục, nén zip                                           |
| 3   | `hql.net`      | HTTP GET/POST, tải file, kiểm tra mạng, server đơn giản                  |
| 4   | `hql.sys`      | Thông tin phần cứng/OS, chạy lệnh hệ thống                               |
| 5   | `hql.util`     | Sắp xếp, UUID, hash md5/sha256, base64, JSON, `sleep`, `range`           |
| 6   | `hql.ui`       | Cửa sổ Swing, hộp thoại, nút, ô nhập                                     |
| 7   | `hql.crypto`   | AES, RSA, PBKDF2, số ngẫu nhiên an toàn                                  |
| 8   | `hql.database` | SQL Server / MySQL / PostgreSQL / SQLite, giao dịch, bể kết nối          |
| 9   | `hql.time`     | Ngày giờ                                                                 |
| 10  | `hql.regex`    | Biểu thức chính quy                                                      |
| 11  | `hql.game`     | Game 2D (đồ họa, sprite, âm thanh, bản đồ lưới, isometric)               |
| 12  | `hql.app`      | Mở trang web thành cửa sổ app (Chromium nhúng JCEF), chạy tiến trình con |
| 13  | `hql.multiplayer`     | Mạng cho game: server/khách nhắn tin không chặn luồng                    |
| 14  | `hql.pc`       | Tự động hoá bàn phím, chuột, clipboard, dò pixel                         |
| 15  | `hql.logging`  | Ghi nhật ký có mức độ                                                    |
| 16  | `hql.image`    | Xử lý ảnh + mã hoá ảnh thành base64 để lưu database                      |
| 17  | `hql.chart`    | Vẽ biểu đồ ra file PNG                                                   |
| 18  | `hql.env`      | Đọc tệp `.henv`                                                          |
| 19  | `hql.config`   | Đọc/ghi file INI                                                         |
| 20  | `hql.csv`      | Đọc/ghi/lọc/sắp xếp CSV                                                  |
| 21  | `hql.xml`      | Đọc/sửa/ghi XML, XPath                                                   |
| 22  | `hql.binary`   | Byte nhị phân, hex, pack/unpack, checksum                                |
| 23  | `hql.color`    | Chuyển đổi và phân tích màu                                              |
| 24  | `hql.socket`   | TCP/UDP/WebSocket/SSL cấp thấp                                           |
| 25  | `hql.schedule` | Hẹn giờ, cron, tác vụ lặp                                                |
| 26  | `hql.export`   | Xuất XLSX/DOCX/PDF/CSV có màu sắc                                        |
| 27  | `hql.web`      | Web server, `.hqlsp`, servlet, session, cookie                           |
| 28  | `hql.mail`     | Gửi email SMTP (Gmail...)                                                |

---

## 7. Chi tiết từng thư viện

> Quy ước: `[tham_số]` là tuỳ chọn. Số luôn là số thực. Hàm "trả `null`" nghĩa là không có giá trị trả về.

### 7.1 `hql.math` — Toán học

```java
use hql.math;
```

**Hằng số:** `PI`, `E`, `TAU` (= 2π), `INF` (vô cực), `NAN`.

| Nhóm       | Hàm                                                                                                                                              |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Cơ bản     | `sqrt(x)`, `cbrt(x)`, `pow(x,y)`, `abs(x)`, `hypot(x,y)`, `isqrt(x)` (căn nguyên), `exp(x)`                                                      |
| Làm tròn   | `round(x)`, `ceil(x)`, `floor(x)`, `trunc(x)`                                                                                                    |
| Lượng giác | `sin(x)`, `cos(x)`, `tan(x)` (radian), `toRadians(độ)`, `degrees(rad)`                                                                           |
| Logarit    | `log(x)` (ln), `log10(x)`, `log2(x)`                                                                                                             |
| Tổ hợp     | `factorial(n)`, `gcd(a,b)`, `lcm(a,b)`, `comb(n,k)`, `perm(n,k)`                                                                                 |
| Tiện ích   | `max(a,b)`, `min(a,b)`, `clamp(x,min,max)`, `random(min,max)` (số nguyên trong [min,max]), `fsum(mảng)`, `isclose(a,b)`, `isnan(x)`, `isinf(x)`  |
| Thống kê   | `mean(mảng)`, `median(mảng)`, `variance(mảng)` (tổng thể), `stddev(mảng)`, `correlation(X,Y)`, `linearRegression(X,Y)` → `[độ_dốc, hệ_số_tự_do]` |
| Ma trận    | `matrixMultiply(A,B)`, `matrixTranspose(M)`, `matrixDeterminant(M)`, `matrixInverse(M)` (ma trận là mảng của mảng)                               |
| Đổi đơn vị | `unitConvert(giá_trị, từ, sang)`, `unitCompare(v1,u1,v2,u2)` → `-1/0/1`, `listUnits()`, `celsiusToFahrenheit(c)`, `fahrenheitToCelsius(f)`       |

Đơn vị hỗ trợ: `m km cm mm inch ft yard mile` · `kg g lb oz` · `l ml gal`.

```java
use hql.math;
print(sqrt(144));                      // 12
print(round(3.6));                     // 4
print(factorial(5));                   // 120
print(mean([1, 2, 3, 4]));             // 2.5
print(unitConvert(5, "km", "mile"));   // 3.1068...
var pt = linearRegression([1,2,3], [2,4,6]);
print(pt);                             // [2, 0]
var M = [[1,2],[3,4]];
print(matrixDeterminant(M));           // -2
```

### 7.2 `hql.io` — File & thư mục

```java
use hql.io;
```

| Hàm                                                                                       | Công dụng                                                                           |
| ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| `read(path)`                                                                              | Đọc cả file thành chuỗi (UTF-8)                                                     |
| `readA(path)`                                                                             | Đọc thành mảng các dòng                                                             |
| `write(path, noiDung[, noi_them])`                                                        | Ghi chuỗi. Tham số 3 là **nối thêm**, mặc định `true`; truyền `false` để **ghi đè** |
| `writeA(path, mang[, noi_them])`                                                          | Ghi mảng, mỗi phần tử một dòng (tham số 3 như trên)                                 |
| `exists(path)`                                                                            | Có tồn tại không                                                                    |
| `create(path)`                                                                            | Có dấu chấm → tạo file (tự tạo thư mục cha), ngược lại tạo thư mục                  |
| `delete(path)`                                                                            | Xoá file, hoặc xoá cả thư mục (đệ quy)                                              |
| `copy(nguon, dich)` / `move(nguon, dich)`                                                 | Sao chép / di chuyển / đổi tên                                                      |
| `fileSize(path)`, `getName(path)`, `extension(path)`, `isDir(path)`, `lastModified(path)` | Thông tin file                                                                      |
| `list(dir)`                                                                               | Tên các file/thư mục con                                                            |
| `listFull(dir)`                                                                           | Đường dẫn đầy đủ các mục con                                                        |
| `find("*.txt", [thu_muc])`                                                                | Tìm đệ quy theo đuôi (mặc định thư mục hiện tại)                                    |
| `freeSpace()`, `totalSpace()`                                                             | Dung lượng ổ đĩa, đơn vị **GB**                                                     |
| `homeDir()`, `workingDir()`                                                               | Thư mục home / thư mục đang chạy                                                    |
| `zip(thu_muc, file_zip)` / `unzip(file_zip, thu_muc)`                                     | Nén / giải nén                                                                      |

```java
use hql.io;
write("ghi_chu.txt", "Dòng 1\n", false);     // ghi đè
write("ghi_chu.txt", "Dòng 2\n");            // nối thêm
for dong in readA("ghi_chu.txt") { print(dong); }
if (exists("ghi_chu.txt")) { print(fileSize("ghi_chu.txt") + " byte"); }
print(find("*.hql", "."));
zip("du_an", "du_an.zip");
```

### 7.3 `hql.net` — Mạng Internet

```java
use hql.net;
```

| Nhóm         | Hàm                                                                                                                                                                                                     |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cấu hình     | `addHeader(ten, giaTri)`, `setCookie("a=1")`, `setTimeout(ms)`, `setProxy(host, port)`, `ignoreSSL()`                                                                                                   |
| Gửi request  | `httpGet(url)` → nội dung trả về; `httpPost(url, body)`; `download(url, duong_dan_luu)` → `true/false`; `httpPostFile(url, json, duong_dan_file[, ten_gui][, mime])` (multipart, ví dụ Discord webhook) |
| Đọc phản hồi | `getStatusCode()`, `getContentType()`, `getResponseHeader(ten)`                                                                                                                                         |
| Tiện ích     | `isOnline()`, `getIP()`, `getHost()`, `ping(host)` (ms, `-1` nếu thất bại), `checkPort(host, port)`                                                                                                     |
| Server       | `createServer(cong, "tenHamXuLy")` — hàm không tham số, giá trị `return` là nội dung trang                                                                                                              |

```java
use hql.net;
addHeader("Authorization", "Bearer TOKEN");
var kq = httpGet("https://api.example.com/data");
print(getStatusCode());
download("https://example.com/a.zip", "tai/a.zip");

func trangChu() { return "Xin chào từ HQL!"; }
createServer(8081, "trangChu");     // mở http://localhost:8081
```

### 7.4 `hql.sys` — Hệ thống

```java
use hql.sys;
```

| Nhóm         | Hàm                                                                                                                       |
| ------------ | ------------------------------------------------------------------------------------------------------------------------- |
| CPU          | `cpuModel()`, `cpuCores()`, `cpuThreads()`, `cpuSpeed()`, `cpuVendor()`                                                   |
| GPU/màn hình | `gpuModel()`, `gpuMemory()`, `gpuDriver()`, `resolution()`                                                                |
| RAM          | `totalRam()`, `freeRam()`, `usedRam()`, `ramSpeed()`                                                                      |
| Máy          | `machineModel()`, `mainboard()`, `biosVersion()`, `serialNumber()`                                                        |
| Ổ cứng       | `diskName()`, `diskStatus()`, `diskTemp()`, `diskWear()`                                                                  |
| OS           | `osName()`, `osArch()`, `userName()`, `uptime()`                                                                          |
| Điều khiển   | `exec("lệnh")` (chạy, không đợi), `execRead("lệnh")` (chạy và lấy kết quả), `exit(ma)`, `env("BIEN"[, mac_dinh])`, `gc()` |

> Một số hàm phần cứng (nhiệt độ, độ mòn ổ, serial...) dựa vào công cụ của Windows, có thể rỗng trên hệ điều hành khác.
> `exec`/`execRead` cắt lệnh theo **dấu cách** nên đường dẫn có dấu cách sẽ hỏng.

```java
use hql.sys;
print(osName() + " - " + cpuModel());
print("RAM trống: " + freeRam());
print(execRead("cmd /c echo xin chao"));
```

### 7.5 `hql.util` — Tiện ích chung

```java
use hql.util;
```

| Hàm                                  | Công dụng                                                             |
| ------------------------------------ | --------------------------------------------------------------------- |
| `sort(mang)`                         | Sắp xếp **tại chỗ**, so sánh theo **chuỗi** (nên `10` đứng trước `9`) |
| `reverse(mang)`, `shuffle(mang)`     | Đảo / xáo trộn tại chỗ                                                |
| `unique(mang)`                       | Mảng mới đã bỏ phần tử trùng                                          |
| `count(mang, x)`                     | Đếm số lần `x` xuất hiện                                              |
| `md5(s)`, `sha256(s)`                | Băm chuỗi                                                             |
| `base64Encode(s)`, `base64Decode(s)` | Mã hoá / giải mã Base64                                               |
| `uuid()`                             | Chuỗi ID ngẫu nhiên                                                   |
| `sleep(ms)`                          | Dừng chương trình                                                     |
| `range(bat_dau, ket_thuc[, buoc])`   | Mảng số, **không gồm** `ket_thuc`; bước âm để đếm ngược               |
| `toJson(x)`                          | Mảng/map → chuỗi JSON                                                 |
| `fromJson(chuoi)`                    | Chỉ đọc được **mảng đơn giản** `[a, b, c]` (tách theo dấu phẩy)       |
| `jsonEscape(s)`                      | Làm sạch chuỗi để nhét vào JSON                                       |

```java
use hql.util;
print(unique([1,2,2,3,3,3]));        // [1, 2, 3]
print(sha256("123456"));
print(base64Decode(base64Encode("Linh")));
print(range(10, 0, -3));             // [10, 7, 4, 1]
print(toJson({ "ten": "Linh", "tuoi": 17 }));
sleep(500);
```

### 7.6 `hql.ui` — Giao diện cửa sổ (Swing)

```java
use hql.ui;
```

| Nhóm            | Hàm                                                                                                                                                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hộp thoại       | `alert([tieu_de,] noi_dung)`, `confirm([tieu_de,] noi_dung)` → `true/false`, `prompt([tieu_de,] noi_dung)` → chuỗi, `chooseFile()` → đường dẫn, `chooseColor()` → `#rrggbb`                                                                     |
| Cửa sổ          | `window(tieu_de, rong, cao)`, `setLayout("grid"` hoặc `"flow")`, `show()`, `hide()`, `close()`                                                                                                                                                  |
| Thành phần      | `label(id, chu)`, `button(id, chu, "tenHam")`, `textField(id)`, `textArea(id)`, `image(id, duong_dan)`, `checkbox(id, chu)`, `radioButton(id, nhom, chu)`, `comboBox(id, mang)`, `table(id, mang_cot, mang_dong)`, `progressBar(id, phan_tram)` |
| Sự kiện/dữ liệu | `onHover(id, "tenHam")`, `onKeyPress(id, "tenHam")`, `getText(id)`, `setInterval("tenHam", ms)`, `trayInit(icon, "hamBat", "hamTat", "hamThoat")`                                                                                               |
| Kiểu dáng       | `setStyle(id, "thuoc_tinh", gia_tri)` hoặc `setStyle(id, "thuoc_tinh:gia_tri")`                                                                                                                                                                 |

Thuộc tính của `setStyle`: `color`, `bg` (mã `#rrggbb`), `font_size`, `font_weight` (`bold`/`plain`), `text`, `padding`, `border_radius`.
Hàm xử lý sự kiện là **tên hàm viết dạng chuỗi**, hàm đó không có tham số.

```java
use hql.ui;

func chao() {
    var ten = getText("o_ten");
    alert("Xin chào " + ten + "!");
}

window("Ứng dụng đầu tay", 400, 250);
setLayout("flow");
label("tieu_de", "Nhập tên của bạn:");
textField("o_ten");
button("nut", "Chào", "chao");
setStyle("nut", "bg", "#2d7ff9");
setStyle("nut", "color", "#ffffff");
show();
```

### 7.7 `hql.crypto` — Mã hoá & bảo mật

```java
use hql.crypto;
```

| Hàm                                                | Công dụng                                                                     |
| -------------------------------------------------- | ----------------------------------------------------------------------------- |
| `encrypt(du_lieu, khoa)` / `decrypt(ban_ma, khoa)` | AES (kết quả dạng Base64)                                                     |
| `generateKeys()`                                   | Tạo cặp RSA 2048-bit → `[publicKey, privateKey]` (Base64)                     |
| `sign(du_lieu, privateKey)`                        | Ký số (SHA256withRSA) → chữ ký Base64                                         |
| `verify(du_lieu, chu_ky, publicKey)`               | Kiểm chữ ký → `true/false`                                                    |
| `secureRandom(n)`                                  | Chuỗi hex ngẫu nhiên an toàn từ `n` byte (dài `2n` ký tự), dùng làm token/OTP |
| `pbkdf2(mat_khau, salt)`                           | Băm mật khẩu 65536 vòng (Base64)                                              |

> Hash `md5`, `sha256` và `base64Encode/Decode` nằm ở **`hql.util`**, không phải `hql.crypto`.

```java
use hql.crypto;
var ban_ma = encrypt("Mật khẩu bí mật", "khoa-cua-linh");
print(decrypt(ban_ma, "khoa-cua-linh"));

var cap = generateKeys();
var chu_ky = sign("hợp đồng", cap[1]);
print(verify("hợp đồng", chu_ky, cap[0]));   // true

print(pbkdf2("matkhau123", "muoi-ngau-nhien"));
```

### 7.8 `hql.database` — Cơ sở dữ liệu

```java
use hql.database;
```

**Kết nối** (trả `true`/`false`, không ném lỗi — xem lý do bằng `dbError()`):

```java
connect("sqlite", "data.db");                              // SQLite
connect("mysql", "localhost", 3306, "shop", "root", "123"); // MySQL / MariaDB
connect("postgres", "localhost", 5432, "shop", "user", "pw");
connect("sqlserver", "localhost", 1433, "GearZone", "sa", "123");
connect("localhost", 1433, "GearZone", "sa", "123");       // bỏ loại → mặc định SQL Server
connect("jdbc:sqlserver://...;databaseName=X", "user", "pw"); // chuỗi JDBC trực tiếp
connectEnv();                                              // theo tệp .henv
```

| Nhóm      | Hàm                                                                                                                                                                |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Kết nối   | `connect(...)`, `connectEnv()`, `closeDB()`, `isOpen()`, `dbError()`, `dbPoolSize(toi_da[, giu_san])` (gọi **trước** `connect`), `dbPoolStats()`                   |
| Truy vấn  | `query(sql[, [tham_so]])` → mảng map; `querySafe` (giống `query`); `queryValue(sql[, [..]])` → 1 ô đầu tiên; `fetchOne(sql[, [..]])` → 1 dòng (map)                |
| Thay đổi  | `execute(sql[, [..]])` / `executeSafe` → số dòng bị ảnh hưởng; `executeBatch(sql, [[..],[..]])` (một giao dịch); `identity()` → ID tự tăng của lần INSERT gần nhất |
| Thủ tục   | `procedure("TenProc", [tham_so])` → mảng dòng nếu thủ tục trả bảng, ngược lại `true`                                                                               |
| Giao dịch | `beginTransaction()`, `commit()`, `rollback()` (mỗi luồng một giao dịch riêng)                                                                                     |
| Khám phá  | `tables()`, `columns("bang")`                                                                                                                                      |
| Xuất      | `exportCSV(sql, "file.csv")` (ghi trực tiếp, hợp bảng lớn)                                                                                                         |

Tham số truyền bằng dấu `?` và **mảng** giá trị — chống SQL Injection:

```java
use hql.database;

if (connect("sqlite", "shop.db") == false) {
    print("Lỗi: " + dbError());
    exit(1);        // cần use hql.sys
}
execute("CREATE TABLE IF NOT EXISTS sp (id INTEGER PRIMARY KEY, ten TEXT, gia REAL)");

execute("INSERT INTO sp(ten, gia) VALUES (?, ?)", ["Laptop", 1200]);
print("ID vừa thêm: " + identity());

for dong in query("SELECT * FROM sp WHERE gia > ?", [100]) {
    print(dong.ten + " - " + dong.gia);
}
print("Số sản phẩm: " + queryValue("SELECT COUNT(*) FROM sp"));

beginTransaction();
try {
    execute("UPDATE sp SET gia = gia + 10");
    commit();
} catch (e) {
    rollback();
    print("Hoàn tác: " + e);
}
closeDB();
```

> Cần có JDBC driver tương ứng trong `lib/`. Chi tiết bể kết nối xem [mục 8](#8-tệp-cấu-hình-henv).

### 7.9 `hql.time` — Thời gian

```java
use hql.time;
```

Ngày giờ được lưu dưới dạng **chuỗi ISO** (`2026-11-22T10:30:15`).

| Hàm                                                    | Công dụng                                                  |
| ------------------------------------------------------ | ---------------------------------------------------------- |
| `now()`, `today()`, `currentTime()`                    | Bây giờ / hôm nay / giờ hiện tại                           |
| `format(ngay, mau)`                                    | Định dạng theo mẫu Java: `"dd/MM/yyyy HH:mm:ss"`           |
| `parse(chuoi, mau)`                                    | Chuỗi → ngày giờ ISO (thiếu giờ thì thêm `00:00:00`)       |
| `addMinutes(d, n)`, `addDays`, `addMonths`, `addYears` | Cộng thời gian (có thể số âm)                              |
| `daysBetween(d1, d2)`                                  | Số ngày giữa hai ngày                                      |
| `getYear(d)`, `getMonth(d)`, `getDay(d)`               | Thành phần của ngày                                        |
| `getWeekday(d)`                                        | `1` (Thứ Hai) … `7` (Chủ Nhật)                             |
| `age(ngay_sinh)`                                       | Tuổi tính theo năm                                         |
| `isWeekend(d)`, `isPast(d)`                            | Kiểm tra                                                   |
| `countdown(ngay_dich)`                                 | Đếm ngược hiển thị trên màn hình, **chặn** tới khi hết giờ |

```java
use hql.time;
print(format(now(), "dd/MM/yyyy HH:mm"));
var han = addDays(today(), 7);
print("Hạn chót: " + format(han, "dd/MM/yyyy"));
print(daysBetween("2026-01-01", "2026-12-31"));   // 364
print(age("2009-05-20"));
```

### 7.10 `hql.regex` — Biểu thức chính quy

```java
use hql.regex;
```

| Hàm                                                    | Công dụng                             |
| ------------------------------------------------------ | ------------------------------------- |
| `compile(pattern)`, `quote(chuoi)`                     | Biên dịch sẵn / thoát ký tự đặc biệt  |
| `matches(chuoi, regex)`                                | Cả chuỗi khớp 100%                    |
| `containsRegex(chuoi, regex)`                          | Có đoạn khớp                          |
| `findRegex(chuoi, regex)`                              | Vị trí khớp đầu tiên (`-1` nếu không) |
| `findAll(chuoi, regex)`                                | Mảng mọi đoạn khớp                    |
| `replaceRegex(chuoi, regex, moi)`, `replaceFirst(...)` | Thay tất cả / thay lần đầu            |
| `splitRegex(chuoi, regex)`                             | Cắt thành mảng                        |
| `group(chuoi, regex, n)`, `groups(chuoi, regex)`       | Lấy nhóm bắt `n` / mọi nhóm           |

```java
use hql.regex;
print(matches("0912345678", "0[0-9]{9}"));          // true
print(findAll("a1b22c333", "[0-9]+"));              // [1, 22, 333]
print(replaceRegex("a  b   c", "\\s+", " "));       // a b c
print(group("Mã: SP-042", "SP-([0-9]+)", 1));       // 042
```

### 7.11 `hql.pc` — Tự động hoá bàn phím / chuột

```java
use hql.pc;
```

| Hàm                                  | Công dụng                                                   |
| ------------------------------------ | ----------------------------------------------------------- |
| `pos()`                              | Toạ độ chuột `[x, y]`                                       |
| `scroll(so_nac)`                     | Lăn chuột                                                   |
| `press("phim")`, `release("phim")`   | Nhấn / nhả phím                                             |
| `combo("k1", "k2")`                  | Tổ hợp phím, ví dụ `combo("CTRL", "C")`                     |
| `findPixel("#mau", x, y, rong, cao)` | Tìm pixel đúng màu trên màn hình → `[x, y]` hoặc `[-1, -1]` |
| `getClip()`, `setClip("chu")`        | Đọc / ghi clipboard                                         |

Tên phím: `CTRL SHIFT ALT ENTER TAB ESC SPACE WIN UP DOWN LEFT RIGHT` hoặc 1 ký tự (`"A"`, `"5"`).

```java
use hql.pc;
use hql.util;
setClip("Xin chào");
sleep(1000);
combo("CTRL", "V");
print(pos());
```

### 7.12 `hql.logging` — Nhật ký

| Hàm                                                                 | Công dụng                                  |
| ------------------------------------------------------------------- | ------------------------------------------ |
| `logInfo(msg)`, `logWarning(msg)`, `logError(msg)`, `logDebug(msg)` | Ghi log kèm thời gian và mức               |
| `setLogLevel("debug"/"info"/"warning"/"error"/"off")`               | Mức tối thiểu được ghi (mặc định `info`)   |
| `setLogFile("duong/dan.log")`                                       | Ghi ra file (nối thêm) thay vì in màn hình |
| `logReset()`                                                        | Về mặc định                                |

```java
use hql.logging;
setLogLevel("debug");
logInfo("Khởi động");
logWarning("Dung lượng sắp đầy");
logError("Mất kết nối");     // in màu đỏ
```

### 7.13 `hql.image` — Xử lý ảnh & mã hoá ảnh base64

```java
use hql.image;
```

**A. Xử lý ảnh trong bộ nhớ** — `loadImage` trả về một `id` (vd `@img1`); các hàm sau **sửa trực tiếp** ảnh đó.

| Hàm                                                                      | Công dụng              |
| ------------------------------------------------------------------------ | ---------------------- |
| `loadImage(path)` → id                                                   | Nạp ảnh                |
| `saveImage(id, "png"/"jpg", path)`                                       | Lưu ảnh                |
| `imageWidth(id)`, `imageHeight(id)`                                      | Kích thước             |
| `resizeImage(id, w, h)`, `cropImage(id, x, y, w, h)`                     | Đổi cỡ / cắt           |
| `rotateImage(id, goc)`, `flipImage(id, "h"/"v")`                         | Xoay / lật             |
| `grayscaleImage(id)`, `blurImage(id, muc)`, `invertImage(id)`            | Xám / làm mờ / đảo màu |
| `getPixelColor(id, x, y)` → `#rrggbb`, `setPixelColor(id, x, y, "#mau")` | Điểm ảnh               |
| `imageToSvg(id[, path])` → đường dẫn                                     | Ảnh → SVG              |
| `svgToImage(noi_dung_svg_hoac_path)` → id                                | SVG → ảnh              |

```java
use hql.image;
var a = loadImage("logo.png");
resizeImage(a, 200, 200);
grayscaleImage(a);
saveImage(a, "png", "logo_xam.png");
```

**B. Mã hoá ảnh thành chuỗi base64** (để cất vào cột `VARCHAR(MAX)` của database):

| Hàm                                           | Công dụng                                                    |
| --------------------------------------------- | ------------------------------------------------------------ |
| `imageFileToBase64(path)`                     | File ảnh → chuỗi base64                                      |
| `imageBase64ToFile(b64, path)`                | Chuỗi → file ảnh (trả số byte)                               |
| `imageBase64Clean(b64)`                       | Bỏ tiền tố `data:image/png;base64,` và khoảng trắng          |
| `imageBase64Mime(b64)`                        | `image/png`, `image/jpeg`...                                 |
| `imageBase64Uri(b64[, mime])`                 | Dùng thẳng trong `<img src>`                                 |
| `imageBase64Size(b64)`                        | Số byte thật                                                 |
| `imageBase64Valid(b64)`                       | Có phải ảnh thật không                                       |
| `imageBase64Width(b64)`, `imageBase64Height(b64)` | Kích thước pixel                                         |
| `imageBase64Info(b64)`                        | Map `{width, height, mime, size, valid}`                     |
| `imageBase64Resize(b64, maxWidth, maxHeight)` | Thu nhỏ giữ tỉ lệ                                            |
| `imageBase64Square(b64, side)`                | Cắt giữa thành ảnh vuông (avatar)                            |

```java
use hql.image;
use hql.database;
var b64 = imageFileToBase64("avatar.png");
if (imageBase64Valid(b64)) {
    var nho = imageBase64Resize(b64, 512, 512);
    executeSafe("INSERT INTO KhoAnh(ten, du_lieu) VALUES (?, ?)", ["avatar", nho]);
}
```

Trả ảnh về trình duyệt bằng `webImage(chuoi)` (xem `hql.web`). Chi tiết thêm: `docs/HQL_ANH.md`.

### 7.14 `hql.chart` — Biểu đồ

| Hàm                                                        | Công dụng      |
| ---------------------------------------------------------- | -------------- |
| `drawBarChart(mang_Y, mang_nhan, "Tiêu đề")`               | Cột → trả `id` |
| `drawLineChart(mang_Y, mang_nhan, "Tiêu đề")`              | Đường          |
| `drawPieChart(mang_Y, mang_nhan, "Tiêu đề")`               | Tròn           |
| `drawScatterChart(mang_X, mang_Y, "Tiêu đề")`              | Phân tán       |
| `drawHistogram(mang_du_lieu, so_cot, "Tiêu đề")`           | Histogram      |
| `setChartSize(id, rong, cao)`, `setChartColor(id, "#mau")` | Cỡ và màu      |
| `chartTitle(id)`                                           | Đọc tiêu đề    |
| `saveChart(id, "file.png")`                                | Lưu thành ảnh  |

```java
use hql.chart;
var bd = drawBarChart([120, 90, 150], ["T1", "T2", "T3"], "Doanh thu quý 1");
setChartSize(bd, 800, 500);
setChartColor(bd, "#7C3AED");
saveChart(bd, "doanhthu.png");
```

### 7.15 `hql.env` — Đọc tệp `.henv`

```java
use hql.env;
```

Tệp `.henv` được tự nạp khi chạy (xem [mục 8](#8-tệp-cấu-hình-henv)). Các hàm dưới đây để đọc / kiểm tra giá trị.

| Hàm                                    | Công dụng                                                                          |
| -------------------------------------- | ---------------------------------------------------------------------------------- |
| `envGet("KHOA"[, mac_dinh])`           | Lấy giá trị (không có → `null` hoặc giá trị mặc định)                              |
| `envInt("KHOA"[, mac_dinh])`           | Lấy số nguyên (mặc định `0`)                                                       |
| `envBool("KHOA"[, mac_dinh])`          | `true` với `true/1/yes/on/y/co`, `false` với `false/0/no/off/n/khong`              |
| `envHas("KHOA")`                       | Khoá có tồn tại không                                                              |
| `envSet("KHOA", gia_tri)`              | Đặt tạm trong bộ nhớ (không ghi ra tệp)                                            |
| `envRequire("A", "B", ...)`            | Thiếu khoá nào thì **dừng chương trình** và báo đúng khoá thiếu; đủ thì trả `true` |
| `envAll()`                             | Toàn bộ khoá đã nạp                                                                |
| `envFiles()`                           | Danh sách tệp `.henv` đã nạp                                                       |
| `envLoad("duong/dan/.henv"[, ghi_de])` | Nạp thêm một tệp; truyền **thư mục** thì tự dò `.henv` trong đó                    |

```java
use hql.env;
// .henv:  DB_HOST=127.0.0.1   DB_PORT=1433   DEBUG=yes
print(envGet("DB_HOST"));                 // 127.0.0.1
print(envGet("KHONG_CO", "mặc định"));    // mặc định
print(envInt("DB_PORT", 0) + 1);          // 1434
print(envBool("DEBUG", false));           // true
envRequire("DB_HOST", "DB_PORT");         // thiếu là dừng ngay
```

### 7.16 `hql.config` — File INI

```java
use hql.config;
```

`loadConfig` trả về một **id** (vd `@config1`); các hàm sau nhận id đó làm tham số đầu. File chưa tồn tại thì tạo cấu hình rỗng. Dòng bắt đầu bằng `#` hoặc `;` là ghi chú; cặp khoá nhận cả `=` lẫn `:`.

| Hàm                                                    | Công dụng                                     |
| ------------------------------------------------------ | --------------------------------------------- |
| `loadConfig(path)` → id                                | Đọc file INI                                  |
| `saveConfig(id, path)`                                 | Ghi ra file (ghi đè)                          |
| `getConfig(id, khoa)` / `setConfig(id, khoa, gt)`      | Khoá **không thuộc section nào** (ở đầu file) |
| `hasConfigKey(id, khoa)` / `removeConfigKey(id, khoa)` | Kiểm tra / xoá khoá không thuộc section       |
| `listConfigKeys(id)`                                   | Danh sách khoá không thuộc section            |
| `getSectionConfig(id, section, khoa)`                  | Đọc khoá trong `[section]` (không có → `""`)  |
| `setSectionConfig(id, section, khoa, gt)`              | Ghi khoá trong `[section]` (tự tạo section)   |
| `listSections(id)` / `listSectionKeys(id, section)`    | Danh sách section / khoá của một section      |
| `removeSection(id, section)`                           | Xoá cả section                                |

```java
use hql.config;
use hql.io;
var c = loadConfig("app.ini");
setConfig(c, "ten", "LinhApp");
setSectionConfig(c, "Database", "host", "localhost");
setSectionConfig(c, "Database", "port", "1433");
saveConfig(c, "app.ini");
print(read("app.ini"));
// ten=LinhApp
//
// [Database]
// host=localhost
// port=1433
print(getSectionConfig(c, "Database", "port"));   // 1433
print(listSections(c));                           // [Database]
```

### 7.17 `hql.csv` — Đọc / ghi / lọc / sắp xếp CSV

```java
use hql.csv;
```

Có **hai cách** làm việc: dạng **mảng các mảng** (có cả dòng tiêu đề) hoặc dạng **bản ghi** (mảng các map, khoá là tên cột).

| Nhóm                     | Hàm                                                                                                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Đọc                      | `readCsvFile(path)` → mảng các mảng; `readDelimitedFile(path, ngan_cach)` (vd `";"`); `readCsvRecords(path)` → mảng map (dòng đầu là tiêu đề)                        |
| Ghi                      | `writeCsvFile(path, mang_cac_mang)`; `writeDelimitedFile(path, mang_cac_mang, ngan_cach)`; `writeCsvRecords(path, mang_ten_cot, mang_ban_ghi)`                       |
| Đếm                      | `csvRowCount(ds)`, `csvColumnCount(ds)`                                                                                                                              |
| Ô (dạng mảng)            | `getCsvCell(ds, dong, cot)`, `setCsvCell(ds, dong, cot, gia_tri)` (chỉ số từ `0`), `addCsvRow(ds, mang_dong)`, `removeCsvRow(ds, dong)`                              |
| Trường (dạng bản ghi)    | `getCsvField(ban_ghi, "cot")`, `setCsvField(ban_ghi, "cot", gt)`, `addCsvRecord(ds[, ban_ghi])` → trả về bản ghi mới để `setCsvField`, `removeCsvRecord(ds, vi_tri)` |
| Sắp xếp                  | `sortCsvByColumn(ds, cot, tang)`, `sortCsvRecordsByField(ds, "cot", tang)` — `tang` là `true`/`false`, so sánh số nếu đọc được, không thì so chuỗi                   |
| Lọc (khớp **đúng bằng**) | `filterCsvByColumn(ds, cot, gia_tri)`, `filterCsvRecordsByField(ds, "cot", gia_tri)`                                                                                 |
| JSON                     | `jsonToCsvRecords(chuoi_json)` (đọc mảng đối tượng **phẳng**), `csvRecordsToJson(ds)` (xem lưu ý mục 9)                                                              |

```java
use hql.csv;
use hql.io;

var bang = [["ten","diem"], ["An","8"], ["Binh","9.5"], ["Chi","7"]];
writeCsvFile("sv.csv", bang);

var d = readCsvFile("sv.csv");
print(csvRowCount(d) + " dòng, " + csvColumnCount(d) + " cột");   // 4 dòng, 2 cột
print(getCsvCell(d, 1, 0));                                       // An
addCsvRow(d, ["Dung", "6"]);

// Dạng bản ghi
var rec = readCsvRecords("sv.csv");
var moi = addCsvRecord(rec);            // bản ghi rỗng, thêm sẵn vào rec
setCsvField(moi, "ten", "Em");
setCsvField(moi, "diem", "5");
sortCsvRecordsByField(rec, "diem", true);
for x in rec { print(x.ten + " = " + x.diem); }   // Em=5, Chi=7, An=8, Binh=9.5

writeCsvRecords("ketqua.csv", ["ten", "diem"], rec);
print(filterCsvRecordsByField(rec, "ten", "An"));
```

### 7.18 `hql.xml` — Đọc / sửa / ghi XML

```java
use hql.xml;
```

Một nút XML là map có `@name`, `@attrs`, `@text`, `@children`. **Đường dẫn** của các hàm `xmlGetNode`, `xmlSetNode`... đi từ **các nút con** của nút bạn truyền vào, ngăn bằng `/`, chọn phần tử thứ n bằng `[n]` (từ `0`): `"sp/ten"`, `"sp[1]/gia"`. Nút gốc **không** tự khớp, và `xmlGetNode(x, "gia")` chỉ tìm ở **con trực tiếp**.

| Nhóm       | Hàm                                                                                                                                                                                                                              |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Đọc / ghi  | `xmlParseString(chuoi)`, `xmlReadFile(path)`, `xmlToString(nut)`, `xmlWriteFile(nut, "ten_goc", path)`, `xmlCreateRoot("ten")`                                                                                                   |
| Truy vấn   | `xmlGetNode(nut, duong_dan)` (không có → `null`), `xmlHasNode(nut, duong_dan)`, `xmlGetAttr(nut, duong_dan, "thuoc_tinh")`, `xmlChildrenCount(nut, duong_dan)`, `xmlFindNodes(nut, "ten_the")` (tìm **đệ quy** mọi nút cùng tên) |
| XPath      | `xmlXpathQuery(chuoi_xml, xpath)` → mảng chữ; `xmlXpathAttr(chuoi_xml, xpath, "thuoc_tinh")` — **nhận chuỗi XML**, không nhận nút                                                                                                |
| Sửa        | `xmlSetNode(nut, dd, chu)`, `xmlSetAttr(nut, dd, "tt", gt)`, `xmlAddChild(nut_cha, dd_cha, nut_con)`, `xmlRemoveNode(nut, dd)`, `xmlRemoveAttr(nut, dd, "tt")`                                                                   |
| Chuyển đổi | `xmlToJson(nut)`, `jsonToXml(chuoi_json, "ten_goc")`, `xmlToCsv(nut, path)`, `csvToXml(mang_ban_ghi, "ten_goc", "ten_dong")`                                                                                                     |

```java
use hql.xml;
var s = "<shop><sp id=\"1\"><ten>Laptop</ten><gia>1200</gia></sp><sp id=\"2\"><ten>Chuot</ten><gia>20</gia></sp></shop>";
var x = xmlParseString(s);

print(xmlGetAttr(x, "sp", "id"));          // 1
print(xmlXpathQuery(s, "//sp/ten"));       // [Laptop, Chuot]
print(xmlXpathAttr(s, "//sp", "id"));      // [1, 2]
print(xmlGetNode(x, "sp[1]/ten"));         // nút <ten>Chuot</ten>

xmlSetNode(x, "sp[0]/gia", "999");         // sửa giá của sản phẩm đầu
xmlSetAttr(x, "sp[0]", "mau", "do");
print(xmlToString(x));
xmlWriteFile(x, "shop", "shop.xml");
```

### 7.19 `hql.binary` — Byte nhị phân & hex

```java
use hql.binary;
```

Dữ liệu nhị phân luôn đi qua lại dưới dạng **chuỗi hex** (`"48656C6C6F"` = `Hello`). Vị trí và độ dài tính bằng **byte**.

| Nhóm         | Hàm                                                                                                                                                                                                                                                  |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| File         | `binaryRead(path)` → hex; `binaryWrite(path, hex)`; `binaryFileSize(path)`; `binaryReadByte(path, vi_tri)` → 0..255; `binaryWriteByte(path, vi_tri, gia_tri)`; `binaryReadBytes(path, vi_tri, so_byte)` → hex; `binaryWriteBytes(path, vi_tri, hex)` |
| Đóng gói     | `binaryPack(dinh_dang, mang_gia_tri)` → hex; `binaryUnpack(dinh_dang, hex)` → mảng; `binaryPackToFile(dinh_dang, mang, path)`; `binaryUnpackFromFile(dinh_dang, path)`; `binaryCalcSize(dinh_dang)`                                                  |
| Chuyển đổi   | `binaryHexToBytes(hex)` → mảng số; `binaryBytesToHex(mang_so)`; `binaryToBase64(hex)`; `binaryFromBase64(chuoi)` → hex                                                                                                                               |
| Thao tác hex | `binarySlice(hex, tu_byte, so_byte)`, `binaryConcat(h1, h2)`, `binaryRepeat(hex, n)`, `binaryReverse(hex)` (đảo thứ tự byte), `binaryXor(hex, khoa_hex)`, `binaryCompare(h1, h2)` → vị trí byte khác nhau đầu tiên, `-1` nếu giống hệt               |
| Kiểm tra     | `binaryCRC32(hex)`, `binaryMD5(hex)`, `binarySHA256(hex)`, `binaryHexDump(hex[, so_cot])` (bảng xem byte kiểu hex editor)                                                                                                                            |

**Định dạng pack:** bắt đầu bằng `>` (big-endian, mặc định) hoặc `<` (little-endian); các token cách nhau dấu cách, có thể kèm số lượng (`3h`):

| Ký tự | Kiểu                                          | Byte |
| ----- | --------------------------------------------- | ---- |
| `b`   | byte (0..255)                                 | 1    |
| `?`   | logic                                         | 1    |
| `h`   | số nguyên ngắn                                | 2    |
| `i`   | số nguyên                                     | 4    |
| `q`   | số nguyên dài                                 | 8    |
| `f`   | số thực                                       | 4    |
| `d`   | số thực kép                                   | 8    |
| `Ns`  | chuỗi dài đúng `N` byte (thiếu thì chèn `00`) | N    |

```java
use hql.binary;
var h = binaryPack(">i h b ? 4s", [1000, 7, 255, true, "AB"]);
print(h);                                   // 000003E80007FF0141420000
print(binaryUnpack(">i h b ? 4s", h));      // [1000, 7, 255, true, AB]
print(binaryCalcSize(">i h b ? 4s"));       // 12
print(binaryHexToBytes("48656C6C6F"));      // [72, 101, 108, 108, 111]
print(binarySHA256("48656C6C6F"));
print(binaryHexDump("48656C6C6F20576F726C64", 8));
binaryWrite("t.bin", "48656C6C6F");
print(binaryReadByte("t.bin", 1));          // 101
```

### 7.20 `hql.color` — Màu sắc

```java
use hql.color;
```

Màu dạng chuỗi `"#RRGGBB"`. Với HSV/HSL: `H` tính bằng **độ** (0..360), `S`, `V`, `L` trong khoảng **0..1**.

| Nhóm       | Hàm                                                                                                                                                                                                                                                                                                  |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Chuyển đổi | `colorRgbToHex(r,g,b)`, `colorHexToRgb(hex)` → `[r,g,b]`, `colorRgbToHsv(r,g,b)`, `colorHsvToRgb(h,s,v)`, `colorRgbToHsl(r,g,b)`, `colorHslToRgb(h,s,l)`, `colorRgbToCmyk(r,g,b)`, `colorCmykToRgb(c,m,y,k)`                                                                                         |
| Phối trộn  | `colorBlend(hex1, hex2, ti_le)` (0 = màu 1, 1 = màu 2), `colorMultiBlend(mang_mau, mang_trong_so)`, `colorGradient(hex1, hex2, so_buoc)` → mảng màu, `colorMultiGradient(mang_mau, so_buoc)`, `colorRandomGradient(so_buoc[, seed])`                                                                 |
| Điều chỉnh | `colorLighten(hex, luong)`, `colorDarken(hex, luong)`, `colorSaturate(hex, luong)`, `colorDesaturate(hex, luong)` (**luong 0..1**, vd `0.1` = 10%), `colorAdjustHue(hex, do)`                                                                                                                        |
| Phân tích  | `colorLuminance(hex)`, `colorContrastRatio(hex1, hex2)` (1..21, chuẩn WCAG), `colorIsLight(hex)`, `colorIsDark(hex)`, `colorDistance(hex1, hex2)`, `colorSimilarity(hex1, hex2)` (≈1 = giống), `colorToGreyscale(hex)`                                                                               |
| Sinh màu   | `colorRandomHex()`, `colorRandomRgb()`, `colorRandomPalette(n, kieu)` với `kieu` = `"complementary"`, `"analogous"`, `"triadic"`, `"monochromatic"` (kiểu khác = ngẫu nhiên)                                                                                                                         |
| Khác       | `colorNameToRgb("red")`, `colorRgbToName(r,g,b)` (18 tên CSS: red green blue white black yellow cyan magenta gray orange purple pink brown skyblue tomato teal navy gold), `colorWithOpacity(hex, 0..1)` → `#RRGGBBAA`, `colorExtractPalette(id_anh, n)` (**id ảnh** từ `loadImage` của `hql.image`) |

```java
use hql.color;
print(colorRgbToHex(255, 128, 0));              // #FF8000
print(colorHexToRgb("#FF8000"));                // [255, 128, 0]
print(colorBlend("#FF0000", "#0000FF", 0.5));   // #7F007F
print(colorGradient("#000000", "#FFFFFF", 5));  // [#000000, #3F3F3F, #7F7F7F, #BFBFBF, #FFFFFF]
print(colorLighten("#336699", 0.1));
print(colorContrastRatio("#000000", "#FFFFFF")); // 21
print(colorWithOpacity("#FF0000", 0.5));         // #FF00007F
```

### 7.21 `hql.socket` — TCP / UDP / WebSocket cấp thấp

```java
use hql.socket;
```

Mỗi socket là một **id** chuỗi (`@tcp1`, `@server2`, `@client3`, `@udp4`, `@ws5`...). Phần lớn hàm **chặn luồng** (`socketTcpAccept`, `socketTcpReceive`, `socketUdpReceive`) trừ khi đặt timeout. Muốn không chặn, dùng `hql.multiplayer` (mục 7.22).

| Nhóm               | Hàm                                                                                                                                                                                                                                                                                    |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| TCP client         | `socketTcpConnect(host, cong)` → id; `socketTcpSend(id, du_lieu)`; `socketTcpReceive(id, so_byte_toi_da)` → **chuỗi hex** (`""` khi hết dữ liệu / timeout); `socketTcpStatus(id)`; `socketTcpClose(id)`                                                                                |
| TCP server         | `socketTcpListen(cong)` → id server; `socketTcpAccept(id_server)` → id khách (chờ tới khi có khách); `socketTcpBroadcast(id_server, du_lieu)` (gửi cho mọi khách); `socketTcpDisconnectClient(id_server, id_khach)`; `socketTcpSendTo` (cùng cách dùng với `socketTcpSend`)            |
| SSL                | `socketSslTcpConnect(host, cong)`, `socketSslTcpListen(cong, keystore, mat_khau)`, `socketSslTcpAccept(id)`, `socketSslTcpSend(id, dl)`                                                                                                                                                |
| UDP                | `socketUdpBind(cong)` → id; `socketUdpSend(id, host, cong, du_lieu)`; `socketUdpReceive(id, so_byte)` → `[du_lieu, ip_gui, cong_gui]`; `socketUdpClose(id)`; `socketUdpBroadcast(cong, du_lieu)`; `socketUdpMulticastJoin(ip_nhom, cong)` → id; `socketUdpMulticastLeave(id, ip_nhom)` |
| Thời gian chờ      | `socketSetTimeout(id, ms)`, `socketSetNonBlocking(id, true/false)` (timeout 1 ms), `socketWaitData(id, ms)` → `true` nếu có dữ liệu để đọc                                                                                                                                             |
| Tuỳ chọn           | `socketSetTcpNoDelay(id, bool)`, `socketSetKeepAlive(id, bool)`, `socketSetOption(id, "SO_REUSEADDR"/"SO_RCVBUF"/"SO_SNDBUF"/"SO_KEEPALIVE"/"SO_LINGER", gia_tri)`                                                                                                                     |
| Thông tin          | `socketResolveHost(ten)` → IP; `socketGetLocalIp()`, `socketGetHostName()`, `socketGetRemoteIp(id)`, `socketGetRemotePort(id)`, `socketListInterfaces()` → mảng map `{name, ip}`, `socketRawPing(host, timeout_ms)` → ms (`-1` nếu không thông)                                        |
| WebSocket (client) | `socketWebSocketConnect(url)` → id; `socketWebSocketSend(id, chu)`; `socketWebSocketReceive(id)` → tin cũ nhất trong hàng đợi (`""` nếu chưa có)                                                                                                                                       |
| Gửi file           | `socketSendFile(id, path)`; `socketReceiveFile(id, path_luu)` (chặn tới khi nhận xong)                                                                                                                                                                                                 |
| Mã hoá             | `socketEnableEncryption(id, "AES", khoa_hex)` (khoá 16/24/32 byte dạng hex), `socketDisableEncryption(id)`                                                                                                                                                                             |
| Không dùng được    | `socketRawSend` luôn báo lỗi (Java không hỗ trợ raw socket)                                                                                                                                                                                                                            |

> **Hai bẫy:** `socketTcpReceive` trả về **hex**, còn `socketTcpSend` coi chuỗi **toàn ký tự hex và độ dài chẵn** là byte thô (vd `"AB12"` gửi 2 byte, không gửi 4 chữ). Chuỗi văn bản thường được gửi dạng UTF-8.

```java
use hql.socket;
var sv = socketTcpListen(9301);
var c  = socketTcpConnect("127.0.0.1", 9301);
var k  = socketTcpAccept(sv);

socketTcpSend(c, "Hello!");
print(socketWaitData(k, 1000));      // true
print(socketTcpReceive(k, 100));     // 48656C6C6F21  (hex của "Hello!")
socketTcpClose(c); socketTcpClose(k); socketTcpClose(sv);

var u = socketUdpBind(9302);
socketUdpSend(u, "127.0.0.1", 9302, "udp-hi");
print(socketUdpReceive(u, 100));     // [udp-hi, 127.0.0.1, 9302]
socketUdpClose(u);
```

### 7.22 `hql.multiplayer` — Mạng cho game (không chặn luồng)

```java
use hql.multiplayer;
```

Nhắn tin theo **dòng** (mỗi tin một dòng, UTF-8), có luồng nền nhận tin nên gọi `mpReceive()` / `mpReceiveFromServer()` trong vòng lặp game 60 khung hình/giây không bị đứng.

**Phía máy chủ**

| Hàm                                                  | Công dụng                                                                |
| ---------------------------------------------------- | ------------------------------------------------------------------------ |
| `mpStartServer(cong)`                                 | Mở server (nhận khách ở luồng nền)                                       |
| `mpReceive()`                                         | Lấy 1 tin từ hộp thư: `[id_khach, noi_dung]`, hoặc mảng rỗng nếu chưa có |
| `mpSend(id_khach, noi_dung)` / `mpBroadcast(noi_dung)` | Gửi cho một khách / cho tất cả                                           |
| `mpClientList()`, `mpClientCount()`               | Danh sách id / số khách đang nối                                         |
| `mpKickClient(id_khach)`                            | Đuổi một khách                                                           |
| `mpStopServer()`                                   | Đóng server                                                              |

Tin đặc biệt trong hộp thư server: `@JOIN` (khách mới vào) và `@LEAVE` (khách vừa thoát).

**Phía người chơi**

| Hàm                                | Công dụng                                                                |
| ---------------------------------- | ------------------------------------------------------------------------ |
| `mpConnect(host, cong[, cho_ms])` | Nối tới server (mặc định chờ 4000 ms) → `true/false`                     |
| `mpSendToServer(noi_dung)`          | Gửi cho server → `true/false`                                            |
| `mpReceiveFromServer()`                 | Lấy 1 tin từ server (`""` nếu chưa có); `"@DISCONNECTED"` khi mất kết nối |
| `mpIsConnected()`, `mpDisconnect()`  | Còn nối không / ngắt                                                     |
| `mpLocalIp()`                      | IP của máy này                                                           |

```java
use hql.multiplayer;
use hql.util;
mpStartServer(9303);
print(mpConnect("127.0.0.1", 9303));       // true
sleep(300);
print(mpReceive());                          // [K1, @JOIN]
mpSendToServer("CHAT xin chao");
sleep(300);
var tin = mpReceive();                       // [K1, CHAT xin chao]
mpSend(tin[0], "PHAN HOI");
sleep(300);
print(mpReceiveFromServer());                    // PHAN HOI
mpDisconnect();
mpStopServer();
```

### 7.23 `hql.schedule` — Hẹn giờ, tác vụ lặp, cron

```java
use hql.schedule;
use hql.util;      // cho sleep()
```

Tác vụ có **tên** (duy nhất; trùng tên báo `Task tồn tại!`) và gọi một hàm HQL **không tham số** theo **tên dạng chuỗi**. Chương trình phải còn sống thì tác vụ mới chạy (dùng `sleep` hoặc `webWait`). Thời điểm viết `"2026-11-25 09:00:00"` hoặc `"2026-11-25T09:00:00"`.

| Nhóm                                    | Hàm                                                                                                                                                                                                                                    |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cơ bản                                  | `taskRunOnce(ten, "ham", tre_ms)`, `taskRunAt(ten, "ham", "thoi_diem")`, `taskRepeat(ten, "ham", chu_ky_ms)` (chạy ngay rồi lặp), `taskRepeatFixedDelay(ten, "ham", nghi_ms)`, `taskCancel(ten)`, `taskCancelAll()`, `taskExists(ten)` |
| Cron 5 trường `phút giờ ngày tháng thứ` | `taskCron(ten, "ham", "*/5 * * * *")`, `taskCronAt(ten, "ham", cron, "bat_dau")`, `taskCronUntil(ten, "ham", cron, "ket_thuc")`, `cronValidate(cron)`, `cronNextRun(cron)`, `taskReschedule(ten, cron_moi)`                            |
| Điều khiển                              | `taskPause(ten)`, `taskResume(ten)`, `taskFireNow(ten)`, `taskSetMaxRuns(ten, n)` (`-1` = vô hạn)                                                                                                                                      |
| Chuỗi / phụ thuộc                       | `taskChain(ten, ["ham1","ham2"])` (chạy sau khi tác vụ chạy xong), `taskDepend(cha, "ham_con")` (chạy khi tác vụ `cha` đã biến mất), `taskOnSuccess(ten, "ham")`, `taskOnFailure(ten, "ham")`                                          |
| Theo lịch                               | `taskWeekly(ten, "ham", "MON,WED", "08:30")`, `taskMonthly(ten, "ham", ngay_trong_thang, "HH:mm")`, `taskLastDayOfMonth(ten, "ham", "HH:mm")`, `taskHoliday(ten, "ham", ["2026-12-25"], "HH:mm")`                                      |
| Lưu trạng thái                          | `taskSaveState(path)` (ghi danh sách tác vụ dạng JSON), `taskEnablePersistence(ten, path)` (ghi số lần chạy sau mỗi lần chạy), `taskGetRuns(ten)`, `taskResetStats(ten)`                                                               |
| Song song / khoá                        | `taskRunParallel(["ham1","ham2"])` (chờ tất cả xong), `taskAcquireLock(ten, file_khoa)` → `false` nếu khoá đã có, `taskReleaseLock(ten, file_khoa)`, `taskWaitFor(ten, ten_khac)` (chờ tác vụ kia kết thúc)                            |
| Giám sát                                | `taskList()` → mảng chuỗi mô tả, `taskDetails(ten)` → map `{name, type, isPaused, runCount, lastRun, func, cron}`, `taskCount()`, `taskActiveCount()`, `taskPauseAll()`, `taskResumeAll()`, `taskShutdown()`                           |
| Chưa làm thật                           | `taskLoadState`, `taskBroadcastEvent` chỉ trả `true`, chưa có tác dụng                                                                                                                                                                 |

> Cron của HQL là bản rút gọn: hỗ trợ `*`, `*/n` ở phút và giờ, và so khớp theo chữ số (chưa hỗ trợ dải `1-5`). Tác vụ cron chỉ kích hoạt ở **giây 00**. `taskRunOnce`, `taskRunAt` tự xoá khỏi danh sách sau khi chạy.

```java
use hql.schedule;
use hql.util;

var dem = 0;
func chao() { print("Xin chào từ task"); }
func lap()  { dem = dem + 1; print("lặp lần " + dem); }

taskRunOnce("mot_lan", "chao", 300);
taskRepeat("lap", "lap", 400);
taskSetMaxRuns("lap", 3);

print(cronValidate("*/5 * * * *"));    // true
print(cronNextRun("0 9 * * *"));       // lần chạy kế tiếp, vd 2026-11-23T09:00
sleep(1800);
taskCancelAll();
```

### 7.24 `hql.export` — Xuất XLSX / DOCX / PDF / CSV có màu

```java
use hql.export;
```

Không dùng thư viện ngoài; PDF tự nhúng font của máy để hiện đúng tiếng Việt. Không truyền tuỳ chọn vẫn ra bảng đẹp (tiêu đề xanh, viền xám, đóng băng dòng đầu).

| Hàm                                            | Công dụng                                                  |
| ---------------------------------------------- | ---------------------------------------------------------- |
| `exportXlsx(du_lieu, "file.xlsx"[, tuy_chon])` | Xuất Excel                                                 |
| `exportDocx(du_lieu, "file.docx"[, tuy_chon])` | Xuất Word (bảng)                                           |
| `exportPdf(du_lieu, "file.pdf"[, tuy_chon])`   | Xuất PDF (tự sang trang, lặp tiêu đề, số trang)            |
| `exportFile(du_lieu, "file.xxx"[, tuy_chon])`  | Tự chọn định dạng theo đuôi: `.xlsx` `.docx` `.pdf` `.csv` |
| `webExport(du_lieu, "ten.xlsx"[, tuy_chon])`   | (cần `hql.web`) trả file về trình duyệt để **tải xuống**   |

Tất cả trả `true` (riêng `webExport` trả số byte). Tên file thiếu đuôi thì tự thêm.

**`du_lieu`** có thể là: **chuỗi SQL** (chạy trên database đang nối); **mảng bản ghi** (kết quả `query(...)`); **mảng các mảng** (dòng đầu là tiêu đề); hoặc một bản ghi.

**`tuy_chon`** là **chuỗi JSON** (cho phép nháy đơn và khoá viết trần). Vì HQL không có map literal, hãy luôn viết tuỳ chọn thành chuỗi:

| Khoá                                                                     | Ý nghĩa                                                                                                                                                                                     |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `title`, `subtitle`                                                      | Tiêu đề / phụ đề phía trên bảng                                                                                                                                                             |
| `sheet`                                                                  | Tên sheet (XLSX)                                                                                                                                                                            |
| `header`, `body`                                                         | Kiểu dòng tiêu đề / dữ liệu                                                                                                                                                                 |
| `zebra`                                                                  | Dòng xen kẽ: `true` hoặc màu `'#F3F4F6'`                                                                                                                                                    |
| `columns`                                                                | Kiểu theo cột: `{'Điểm':{format:'0.00', bold:true, width:18}}`                                                                                                                              |
| `rules`                                                                  | Tô theo điều kiện: `[{col:'Điểm', op:'<', value:6, bg:'#FEE2E2', color:'#B91C1C'}]`; `op` = `< <= > >= == != contains startsWith endsWith empty notEmpty`; thêm `scope:'row'` để tô cả dòng |
| `cells`                                                                  | Tô riêng ô/dòng/cột: `[{row:0, bg:'#111827'}]` (`row:0` = dòng tiêu đề)                                                                                                                     |
| `only`, `labels`, `headers`                                              | Chọn & sắp xếp cột / đổi tên cột / đặt tên cột cho mảng các mảng                                                                                                                            |
| `freeze`, `filter`, `gridlines`                                          | XLSX: đóng băng dòng đầu (mặc định `true`), bộ lọc, đường lưới                                                                                                                              |
| `orientation`, `pageSize`, `margin`, `pageNumbers`, `footer`, `fontFile` | PDF/DOCX: giấy ngang (mặc định) hoặc `'portrait'`, cỡ giấy `a4 a3 a5 letter legal`, lề, số trang, chân trang, file `.ttf` riêng                                                             |
| `numberLocale`, `delimiter`, `bom`                                       | Định dạng số `'vi'`/`'us'`; CSV: ký tự ngăn cột, có BOM không                                                                                                                               |

Khoá kiểu chữ dùng cho `header`, `body`, `columns`, `rules`, `cells`: `font size bold italic underline strike color bg align valign wrap border format`. Màu viết `'#RRGGBB'`, `'rgb(r,g,b)'` hoặc tên (`white`, `red`, `xanhla`...). Chi tiết thêm: `docs/HQL_EXPORT.md`.

```java
use hql.export;
var bang = [["Tên","Điểm","Ghi chú"], ["An",8,"Tốt"], ["Bình",5.5,"Nợ học phí"], ["Chi",9.25,""]];

var tuyChon = "{ title:'BẢNG ĐIỂM', subtitle:'Lớp 12A', zebra:true, header:{bg:'#7C3AED', color:'white'}, columns:{'Điểm':{format:'0.00', bold:true}}, rules:[{col:'Điểm', op:'<', value:6, bg:'#FEE2E2', color:'#B91C1C'}], filter:true, footer:'LinhScript' }";

exportXlsx(bang, "diem.xlsx", tuyChon);
exportDocx(bang, "diem.docx", tuyChon);
exportPdf (bang, "diem.pdf",  tuyChon);
exportFile(bang, "diem.csv");              // CSV có BOM để Excel đọc đúng tiếng Việt
exportXlsx(bang, "mac_dinh");              // không tuỳ chọn → tự thêm .xlsx
```

### 7.25 `hql.web` — Web server, `.hqlsp`, servlet

```java
use hql.web;
```

HQL có **HQLSP** (như JSP) và **`<Tên>Servlet.hql`** (như Servlet). File main đặt tên project (bắt buộc), giống `http://localhost:8080/TenProject`:

```java
// web.hql
use hql.web;
var TEN_PROJECT = "/QuanLyKho";
webStart(8080, TEN_PROJECT, "web");     // "web" = thư mục chứa .hqlsp / .html / *Servlet.hql
webWait();                              // giữ chương trình chạy (Ctrl+C để dừng)
```

Khi chạy, màn hình in ra toàn bộ đường dẫn (trang, servlet, route, địa chỉ LAN). Tên servlet trong URL viết **chữ thường**: `BaoCaoServlet.hql` → `/QuanLyKho/baocao`.

**Máy chủ**

| Hàm                                   | Công dụng                                      |
| ------------------------------------- | ---------------------------------------------- |
| `webStart(cong, "/ten"[, "thu_muc"])` | Khởi động server                               |
| `webWait()`, `webStop()`              | Giữ chương trình chạy / dừng server            |
| `webRoot("thu_muc")`                  | Đặt thư mục gốc (dùng trước `webExportTomcat`) |
| `webRoute("/duong-dan", "tenHam")`    | Gán một hàm HQL cho một URL                    |
| `webServlet("/duong-dan", "X.hql")`   | Gán servlet cho URL tuỳ ý                      |
| `webContext()`                        | Tên project, vd `/QuanLyKho`                   |
| `webExportTomcat("Ten"[, "web.hql"])` | Đóng gói thành thư mục chạy trên Tomcat        |
| `webShowDetailedErrors([bool])`           | Bật/tắt hiện lỗi chi tiết trên trang           |

**WebSocket server:** `wsStart(cong)`, `wsBroadcast(chu)` → số khách đã gửi, `wsClientCount()`, `wsStop()`.

**Đọc request:** `param("ten"[, mac_dinh])`, `params()`, `webMethod()`, `webPath()`, `webQuery()`, `webBody()`, `webIP()`, `reqHeader("ten")`, `cookieGet("ten")`.

**Trả response:** `printWeb(x)`, `webStatus(ma)`, `webType(kieu)`, `resHeader(ten, gia_tri)`, `webRedirect(url)`, `cookieSet(ten, gia_tri[, giay])`, `webJson(du_lieu)`, `webJsonText(chuoi_json)`, `webError(thong_bao)`, `webInclude(file)`, `webRender(file.hqlsp)` (chạy `.hqlsp` và trả HTML dạng chuỗi), `webExport(...)`, và trả ảnh / file nhị phân: `webImage(base64[, mime])` (= `webImage`), `webBinary(base64[, mime[, ten_file]])` (= `webBinary`).

**Session:** `sessionSet(k, v)`, `sessionGet(k)`, `sessionDel(k)`, `sessionId()`, `sessionKill()`.

**Tiện ích:** `htmlEscape(s)` (chống XSS), `urlEncode(s)`, `urlDecode(s)`, `webMime("ten_file")`.

**Cú pháp HQLSP** (file `web/index.hqlsp`, kiểm tra đã chạy đúng):

| Cú pháp                              | Ý nghĩa                                         |
| ------------------------------------ | ----------------------------------------------- |
| `<% code %>`                         | Chạy code HQL (`if`, `for` bao quanh HTML được) |
| `<%= bieu_thuc %>`                   | In giá trị ra trang                             |
| `<%-- ghi chu --%>`                  | Ghi chú, không ra HTML                          |
| `<%@ page contentType="..." %>`      | Đặt kiểu nội dung                               |
| `<%@ include file="header.hqlsp" %>` | Nhúng file khác                                 |

```html
<h1>Xin chào <%= htmlEscape(param("ten", "khách")) %></h1>
<ul>
  <% for m in ["Toán", "Lý"] { %>
  <li><%= m %></li>
  <% } %>
</ul>
```

Truy cập `/Demo/?ten=Linh` → `<h1>Xin chào Linh</h1><ul><li>Toán</li><li>Lý</li></ul>`.

**Servlet** (file `web/XinChaoServlet.hql`, truy cập `/Demo/xinchao?x=5`):

```java
use hql.web;
func init()   { print("Servlet đã nạp"); }       // chạy 1 lần lúc khởi động
func doGet()  { printWeb("servlet GET " + param("x", "?")); }   // → servlet GET 5
func doPost() { printWeb("Đã nhận POST: " + param("ten")); }
```

Mỗi servlet có vùng hàm / lớp riêng nên hai servlet cùng có `doGet()` không đụng nhau. Server tự chặn `../` thoát thư mục web và không bao giờ trả mã nguồn `.hql` ra trình duyệt. Chạy trên **Tomcat 10.1**: dùng `webExportTomcat`, lúc đó `webStart`/`webWait` tự vô hiệu. Chi tiết thêm: `docs/HQL_WEB.md`.

### 7.26 `hql.mail` — Gửi email SMTP

```java
use hql.mail;
```

Cần `jakarta.mail` + `jakarta.activation` trong `lib/`. Gmail bắt buộc dùng **Mật khẩu ứng dụng** 16 ký tự, không dùng mật khẩu đăng nhập thường. Thư viện **không tự thêm gì** vào thư: tiêu đề và nội dung hoàn toàn do bạn viết.

| Hàm                                                            | Công dụng                                                                                                                                                    |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `mailConfig(email, mat_khau[, host, cong])`                    | Cấu hình (mặc định `smtp.gmail.com`; cổng `465` tự bật SSL)                                                                                                  |
| `mailConfigEnv()`                                              | Cấu hình theo `.henv` (`MAIL_EMAIL`, `MAIL_PASSWORD`, `MAIL_HOST`, `MAIL_PORT`, `MAIL_NAME`); `.henv` có đủ thì không cần gọi, thư viện tự đọc ở lần gửi đầu |
| `mailSenderName("Tên hiển thị")`                               | Tên người gửi                                                                                                                                                |
| `mailIsConfigured()`, `mailError()`                            | Đã cấu hình chưa / lỗi của lần gửi gần nhất                                                                                                                  |
| `mailWarmUp()`                                                 | Mở sẵn kết nối SMTP ở luồng nền để thư đầu gửi ngay                                                                                                          |
| `mailSend(den, tieu_de, html)`                                 | Gửi thư HTML → `true/false`                                                                                                                                  |
| `mailSendText(den, tieu_de, van_ban)`                          | Gửi chữ thuần                                                                                                                                                |
| `mailSendAsync(den, tieu_de, html)` / `mailSendAsyncText(...)` | Xếp hàng đợi, gửi ở luồng nền (trang web không đứng chờ)                                                                                                     |
| `mailAttach(path)`, `mailClearAttach()`                        | Thêm / xoá tệp đính kèm cho lần gửi kế tiếp                                                                                                                  |
| `mailOtp([n])`                                                 | Mã OTP `n` chữ số (mặc định 6, 1..12), dùng `SecureRandom`                                                                                                   |
| `mailPassword([n])`                                            | Mật khẩu ngẫu nhiên dễ đọc (tối thiểu 6, có chữ hoa, thường, số)                                                                                             |
| `mailIsValid(email)`                                           | Kiểm tra định dạng email                                                                                                                                     |

> `docs/HQL_MAIL.md` còn nhắc `mailBrand`, `mailSupport`, `mailSendAccount`, `mailSendOtp`, `mailSendNotify`. Các hàm này **không có trong mã nguồn** hiện tại, đừng dùng.

```java
use hql.mail;
mailConfig("ten-hop-thu@gmail.com", "xxxxxxxxxxxxxxxx");   // tốt hơn: đặt trong .henv
mailSenderName("GearZone Support");

var ma = mailOtp(6);
var html = "<h2>Mã xác thực</h2><p>Mã của bạn: <b>" + ma + "</b> (hết hạn sau 10 phút)</p>";
if (mailSend("khach@gmail.com", "Mã xác thực", html) == false) {
    print("Gửi lỗi: " + mailError());
}
mailAttach("baocao.pdf");
mailSendAsync("sep@gmail.com", "Báo cáo", "<p>Xem file đính kèm</p>");
```

### 7.27 `hql.game` — Game 2D

```java
use hql.game;
```

Cần màn hình (Swing). Toạ độ tính bằng pixel, gốc ở góc trên-trái. **Màu** là tên (`"red"`) hoặc `"#rrggbb"`. Hàm `update`/`draw` của vòng lặp là **tên hàm dạng chuỗi**, không tham số, biến toàn cục dùng chung được.

**Cửa sổ & vòng lặp**

| Hàm                                                          | Công dụng                                                                                |
| ------------------------------------------------------------ | ---------------------------------------------------------------------------------------- |
| `gameWindow(tieu_de, rong, cao)` / `gameWindowFull(tieu_de)` | Mở cửa sổ / toàn màn hình                                                                |
| `screenW()`, `screenH()`, `setTitle(s)`, `closeGame()`       | Kích thước màn hình, đổi tiêu đề, đóng game                                              |
| `gameLoop("update"[, "draw"])`                               | Chạy vòng lặp (chặn cho tới khi `closeGame()`); sprite, hạt và chữ bay được engine tự vẽ |
| `setFPS(n)`, `getFPS()`, `deltaTime()`, `frameCount()`       | Tốc độ khung hình, FPS thực, thời gian một khung (giây), số khung đã chạy                |

**Vẽ**

| Hàm                                                                       | Công dụng                                       |
| ------------------------------------------------------------------------- | ----------------------------------------------- |
| `clear([mau])`                                                            | Xoá màn hình (mặc định đen) — gọi đầu mỗi khung |
| `drawRect(x,y,w,h,mau)` / `fillRect(...)`                                 | Hình chữ nhật viền / đặc                        |
| `drawCircle(x,y,r,mau)` / `fillCircle(...)`                               | Hình tròn (tâm x,y)                             |
| `drawOval(x,y,w,h,mau)` / `fillOval(...)`                                 | Ellipse                                         |
| `drawRoundRect(x,y,w,h,bo_goc,mau)` / `fillRoundRect(...)`                | Chữ nhật bo góc                                 |
| `drawArc(x,y,w,h,goc_dau,goc_quet,mau)` / `fillArc(...)`                  | Cung tròn                                       |
| `drawLine(x1,y1,x2,y2,mau)`                                               | Đường thẳng                                     |
| `fillTriangle(x1,y1,x2,y2,x3,y3,mau)`                                     | Tam giác                                        |
| `drawPolygon(mang_x, mang_y, mau)` / `fillPolygon(...)`                   | Đa giác                                         |
| `gradientRect(x,y,w,h,mau_tren,mau_duoi)`                                 | Chữ nhật chuyển màu dọc                         |
| `drawText(chu,x,y,co_chu,mau)` / `drawTextCenter(chu,tam_x,y,co_chu,mau)` | Vẽ chữ / chữ căn giữa theo x                    |
| `textWidth(chu,co_chu)`, `getTextWidth(chu)`                              | Đo bề rộng chữ                                  |
| `setFont(ten[, in_dam])`, `setAlpha(0..1)`, `setStroke(do_day)`           | Phông, độ trong suốt, độ dày nét                |
| `drawImage(duong_dan,x,y,w,h)`                                            | Vẽ ảnh                                          |
| `drawImageEx(duong_dan,x,y,w,h[,lat_ngang[,goc_xoay]])`                   | Vẽ ảnh lật / xoay quanh tâm                     |
| `imageReady(duong_dan)`                                                   | Ảnh nạp được chưa                               |
| `saveScreenshot(path)`                                                    | Chụp màn hình game                              |

**Sprite** (tự vẽ mỗi khung, có camera và xoay)

`sprite(id,x,y,w,h,duong_dan_anh)`, `moveTo(id,x,y)`, `moveBy(id,dx,dy)`, `spritePos(id)` → `[x,y]`, `rotateSprite(id,goc_do)`, `scaleSprite(id,w,h)`, `collide(id1,id2)` → va chạm hình chữ nhật, `removeSprite(id)`.

**Bàn phím & chuột**

`isKeyDown("LEFT")` (đang giữ), `isKeyPressed("SPACE")` (chỉ khung vừa bấm), `pressKey(k)`, `releaseKey(k)` (giả lập), `mouseX()`, `mouseY()`, `isMousePressed("LEFT"/"RIGHT")` (khung vừa bấm), `isMouseDown("LEFT"/"RIGHT")` (đang giữ), `hideCursor([bool])`, `getTypedText()` (chữ vừa gõ, dùng cho ô chat, rồi xoá), `clearTypedText()`. Tên phím: `LEFT RIGHT UP DOWN SPACE ENTER ESC CTRL SHIFT ALT TAB`, hoặc một ký tự như `"A"`, `"5"`.

**Camera & giao diện cố định:** `setCamera(x,y)`, `camX()`, `camY()`; giữa `beginUI()` và `endUI()` nét vẽ bám màn hình, không bị camera kéo (dùng cho HUD).

**Âm thanh:** `playSound(path[, lap])`, `stopSound()`, `setVolume(0..100)`, `playMusic(path[, lap[, am_luong]])`, `stopMusic()`, `setMusicVolume(v)`, `playSfx(path[, am_luong])`, `tone(tan_so, ms[, am_luong 0..1])` (phát tiếng bíp không cần file).

**Toán tiện ích:** `randomInt(min,max)`, `randomFloat(min,max)`, `distance(x1,y1,x2,y2)`, `lerp(a,b,t)`, `angleTo(x1,y1,x2,y2)` (độ).

**Hiệu ứng:** `screenShake(do_manh, so_khung)`, `spawnParticle(x,y,vx,vy,kich_thuoc,tuoi_tho,mau[,trong_luc])`, `burst(x,y,so_hat,mau[,toc_do[,kich_thuoc[,tuoi_tho[,trong_luc]]]])` (nổ hạt), `floatText(chu,x,y,co_chu,mau[,tuoi_tho[,vy]])` (chữ bay lên), `clearEffects()`.

**Bản đồ lưới & tầm nhìn** (game góc nhìn từ trên xuống, bản đồ là mảng chuỗi, mỗi ký tự một ô; `#` là tường)

| Hàm                                                                      | Công dụng                                                |
| ------------------------------------------------------------------------ | -------------------------------------------------------- |
| `mapLoad(mang_hang[, kich_thuoc_o[, ky_tu_chan_nhin[, ky_tu_chan_dan]]])` | Nạp bản đồ (mặc định ô 32 px)                            |
| `mapCell(cot,hang)` / `mapCellAt(x,y)`                                        | Ký tự của ô theo chỉ số / theo toạ độ pixel              |
| `mapSetCell(cot,hang,ky_tu)`                                                | Đổi ô (mở cửa, phá tường)                                |
| `mapCols()`, `mapRows()`, `mapCellSize()`                           | Kích thước bản đồ                                        |
| `mapCanSee(x1,y1,x2,y2)`                                               | Hai điểm có nhìn thấy nhau không                         |
| `castRay(x,y,goc_do,tam_xa)`                                              | Bắn tia → `[x_trung, y_trung, ky_tu_tuong, khoang_cach]` |
| `drawMap(anh_san, anh_tuong[, anh_go[, anh_thung[, anh_site]]])`         | Vẽ phần bản đồ trong khung nhìn                          |
| `drawMinimap(x,y,ty_le,mau_san,mau_tuong[,mau_site])`                     | Vẽ minimap                                               |
| `addSmoke(x,y,r)`, `clearSmoke()`, `inSmoke(x,y)`                      | Vùng tròn (khói...) chặn tầm nhìn                        |
| `drawFog(x,y,ban_kinh[,so_tia[,mau_toi[,do_toi]]])`                    | Phủ tối phần không nhìn thấy                             |
| `drawVision(x,y,ban_kinh[,so_tia[,mau[,do_mo]]])`                        | Tô sáng vùng nhìn thấy                                   |

**Isometric (góc nhìn chéo 45°, ô mặc định 64×32):** `isoSetup(rong_o, cao_o)`, `isoX(wx,wy)`, `isoY(wx,wy)`, `isoScreenX(wx,wy)`, `isoScreenY(wx,wy)` (toạ độ thế giới → màn hình), `isoToWorld(sx,sy)` → `[wx,wy]` (con trỏ chuột trỏ vào ô nào), `isoDrawMap(cao_tuong, mau_san, mau_san_toi, mau_site, tuong_tren, tuong_trai, tuong_phai, go_tren, go_trai, go_phai, thung_tren, thung_trai, thung_phai)`, `isoDrawGroundImage(duong_dan)`, `isoDrawImage(duong_dan,wx,wy,rong,cao[,nhac_len])` (ảnh neo ở chân nhân vật), `isoFillEllipse(wx,wy,ban_kinh,mau)`, `isoDrawEllipseOutline(...)`, `isoDrawLine(wx1,wy1,wx2,wy2,mau[,nhac_len])`, `isoDrawFog(x,y,ban_kinh[,so_tia[,mau_toi[,do_toi]]])`.

```java
use hql.game;
var x = 300;
var y = 200;

func capNhat() {
    if (isKeyDown("LEFT"))  { x = x - 4; }
    if (isKeyDown("RIGHT")) { x = x + 4; }
    if (isKeyDown("UP"))    { y = y - 4; }
    if (isKeyDown("DOWN"))  { y = y + 4; }
    if (isKeyPressed("SPACE")) { burst(x, y, 20, "orange"); screenShake(4, 8); }
    if (isKeyPressed("ESC"))   { closeGame(); }
}

func ve() {
    clear("#101820");
    fillCircle(x, y, 20, "cyan");
    drawText("Mũi tên: di chuyển | SPACE: nổ | ESC: thoát", 20, 30, 16, "white");
}

gameWindow("Game đầu tay", 640, 480);
setFPS(60);
gameLoop("capNhat", "ve");
```

### 7.28 `hql.app` — Mở web thành cửa sổ app, chạy tiến trình con

```java
use hql.app;
```

`appWindow` nhúng Chromium (JCEF, cần thư viện `jcef-maven` trong `lib/`) thành cửa sổ riêng, có hồ sơ cookie riêng cho từng app.

| Hàm                                                   | Công dụng                                                                                                                   |
| ----------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `appHasBrowser()`                                   | Máy có trình duyệt để mở app không                                                                                          |
| `appWindow(url[, tieu_de[, rong[, cao[, anh_icon]]]])` | Mở cửa sổ app (mặc định 1400×900) → **mã** cửa sổ                                                                           |
| `appIsOpen(ma)`, `appWait(ma)`, `appClose(ma)`          | Còn mở không / chờ tới khi đóng / đóng                                                                                      |
| `appTray(ma[, anh[, nhan_mo[, nhan_thoat]]])`         | Thu nhỏ xuống khay hệ thống                                                                                                 |
| `processRun(lenh[, thu_muc])`                      | Chạy tiến trình con; `lenh` là **mảng** `["java","-jar","a.jar"]` (nên dùng mảng nếu đường dẫn có dấu cách) → mã tiến trình |
| `processAlive(ma)`, `processKill(ma)`               | Còn chạy không / tắt (xin tử tế trước, không được mới ép)                                                                   |
| `portIsOpen(cong)`, `waitPort(cong[, giay])`           | Cổng đang có ai dùng không / chờ cổng mở (mặc định 30 giây)                                                                 |
| `sysProp("ten")`, `appRootDir("moc")`               | Thuộc tính hệ thống Java / tìm thư mục cha chứa tệp `moc`                                                                   |

```java
use hql.app;
use hql.web;

webStart(8080, "/App", "web");
if (waitPort(8080, 10)) {
    var ma = appWindow("http://localhost:8080/App/", "Ứng dụng của tôi", 1200, 800);
    appWait(ma);        // chờ người dùng đóng cửa sổ
}
webStop();
```

---

## 8. Tệp cấu hình `.henv`

`.henv` giống `.env` của Node.js: cất mật khẩu, địa chỉ máy chủ, cổng... ra khỏi mã nguồn. Sao chép `.henv.example` thành `.henv` cạnh file main (`.henv` đã nằm trong `.gitignore`).

```
# dòng chú thích (cũng nhận dấu ; ở đầu dòng)
DB_TYPE=sqlserver              # sqlserver | mysql | postgres | sqlite
DB_HOST=127.0.0.1              # " #" cắt chú thích cuối dòng
DB_PORT=1433
DB_NAME="Gear Zone"            # nháy kép: hiểu \n \t \" \\
DB_USER=sa
DB_PASSWORD=dien-mat-khau
DB_URL=jdbc:sqlserver://${DB_HOST}:${DB_PORT};databaseName=${DB_NAME}   # có DB_URL thì bỏ qua DB_HOST...
DB_POOL_MAX=10                 # số kết nối tối đa trong bể
DB_POOL_MIN=2                  # số kết nối giữ sẵn
DB_TIMEOUT_MS=15000            # chờ mượn kết nối tối đa
DB_ENCRYPT=false               # SQL Server cũ / không có chứng chỉ

MAIL_EMAIL=ten@gmail.com
MAIL_PASSWORD=xxxx xxxx xxxx xxxx
MAIL_NAME="GearZone Support"
MAIL_HOST=smtp.gmail.com
MAIL_PORT=465

DB_USER2=${DB_USER_MAC_DINH:-sa}     # ${BIEN:-gia_tri_du_phong}
MAIL_BRAND='GEAR #ZONE'              # nháy đơn: giữ nguyên từng chữ
export DB_X=1                        # chữ export ở đầu được bỏ qua
```

Tệp lưu **UTF-8**; dòng sai cú pháp bị bỏ qua, không làm hỏng cả tệp. Khi chạy `main.hql`, LinhScript **tự tìm `.henv`** ở thư mục của file main rồi ngược lên tối đa 6 cấp cha. Thứ tự ưu tiên từ cao xuống thấp:

| #   | Nguồn                                 | Ghi chú                                                                |
| --- | ------------------------------------- | ---------------------------------------------------------------------- |
| 1   | Biến môi trường thật của hệ điều hành | Không bị tệp ghi đè → đặt mật khẩu thật trên máy chủ mà khỏi sửa tệp   |
| 2   | `.henv.local`                         | Riêng từng máy, không đưa lên git                                      |
| 3   | `.henv.<chế độ>`                      | Chọn bằng biến `HQL_ENV`, vd `HQL_ENV=production` → `.henv.production` |
| 4   | `.henv`                               | Giá trị chung của dự án                                                |

**Bể kết nối database.** `connect(...)` / `connectEnv()` dùng bể kết nối `HqlDbPool` (tự viết): mỗi lệnh `query`, `execute` mượn một kết nối rồi trả ngay; kết nối chết tự được thay mới; `beginTransaction()` giữ một kết nối riêng cho từng luồng tới khi `commit()` / `rollback()`. Request web kết thúc mà quên `commit`/`rollback` thì tự rollback và in cảnh báo. Nếu `.henv` có cấu hình DB mà script chưa gọi `connect`, lệnh SQL đầu tiên sẽ tự kết nối theo `.henv`. Chỉnh cỡ bể bằng `dbPoolSize(toi_da[, giu_san])` (gọi **trước** `connect`) hoặc `DB_POOL_MAX` / `DB_POOL_MIN`; xem `dbPoolStats()`.

Hàm `env("KHOA"[, mac_dinh])` của `hql.sys` cũng đọc được `.henv`.

---

## 9. Lưu ý quan trọng & lỗi thường gặp

**Những điều khác với ngôn ngữ quen thuộc**

- **Không có map literal.** `{ "a": 1 }` là lỗi cú pháp. Map chỉ đến từ kết quả hàm (`query`, `fetchOne`, `readCsvRecords`, `addCsvRecord`...). Muốn gom dữ liệu có tên trường, dùng **class**. Tuỳ chọn của `hql.export` truyền bằng **chuỗi JSON**.
- **Callback truyền bằng tên chuỗi:** `ds.map("gapDoi")`, `taskRepeat("t", "ham", 1000)`, `button("id", "Chữ", "tenHam")`, `gameLoop("update", "draw")`, `call("tenHam", ...)`.
- **Class con không thừa kế constructor.** Ví dụ ở mục 4.2 phải viết lại `func @init` trong class con (có thể gọi lại cách gán giống class cha).
- **`@ten` và `ten` là hai tên khác nhau** trong class: `this.@ten` và `this.ten` là hai thuộc tính riêng.
- **Private chỉ chặn một phần:** gọi hàm private hoặc gán thuộc tính private từ ngoài class báo lỗi, nhưng _đọc_ thuộc tính private từ ngoài vẫn được.
- **Mọi số là số thực.** `10 / 4` ra `2.5`; số nguyên in ra không có phần thập phân.
- **`sort` so sánh theo chuỗi** nên `[10, 9, 2]` ra `[10, 2, 9]`.
- **Hàm phải `use` thư viện trước khi gọi**, nếu không báo `Hàm không có sẵn hoặc thư viện chưa được 'use'.`
- **Biến toàn cục** sửa được bên trong hàm bằng phép gán (`dem = dem + 1`), nên dùng cho vòng lặp game, tác vụ hẹn giờ.

**Lỗi / hạn chế của từng thư viện**

- `csvRecordsToJson` hiện cho ra chuỗi JSON **sai** với mảng bản ghi (mỗi bản ghi thành chuỗi `"{ten=An, ...}"`). Muốn JSON đúng thì tự ghép chuỗi hoặc dùng `exportFile(..., "x.csv")`.
- `sortCsvByColumn` sắp xếp **cả dòng tiêu đề**; hãy tách tiêu đề ra trước khi sắp xếp.
- `xmlGetNode(x, "gia")` chỉ tìm con trực tiếp: dùng đường dẫn đầy đủ `"sp[0]/gia"`, hoặc `xmlFindNodes` để tìm đệ quy.
- `colorLighten("#336699", 10)` ra trắng: lượng là `0..1`, không phải phần trăm.
- `socketTcpReceive` trả hex; `socketTcpSend` coi chuỗi toàn hex (độ dài chẵn) là byte thô.
- `fromJson` chỉ đọc mảng đơn giản `[a, b, c]`.
- `input()` cần bàn phím chuẩn; chạy không có stdin sẽ lỗi.
- `exec`/`execRead` cắt lệnh theo dấu cách; dùng `processRun([...])` khi đường dẫn có dấu cách.
- Hàm phần cứng của `hql.sys` (nhiệt độ, độ mòn ổ...) chỉ đầy đủ trên Windows.
- Các hàm giả lập chưa có tác dụng: `taskLoadState`, `taskBroadcastEvent`; `socketRawSend` luôn lỗi.

**Đọc thông báo lỗi.** Lỗi không bắt sẽ in khung có loại lỗi, thời gian, **tên file + số dòng**, đoạn mã nguồn quanh dòng lỗi, gợi ý "Nên làm gì" và đường đi các hàm. Một bẫy hay dính được ghi sẵn trong gợi ý: nối mảng phải dùng `.push()`, vì `+` giữa hai mảng là phép cộng số.

| Lỗi                                                  | Cách xử lý                                                             |
| ---------------------------------------------------- | ---------------------------------------------------------------------- |
| `LỖI CÚ PHÁP ... L_BRACE`                            | Bạn đang dùng map literal `{...}`; HQL không hỗ trợ (xem trên)         |
| `Không tìm thấy biến 'tenHam'`                       | Truyền hàm làm callback phải viết tên dạng chuỗi `"tenHam"`            |
| `Lỗi OOP: Phương thức 'x()' là Private!`             | Thêm `@` trước tên hàm trong class nếu muốn gọi từ ngoài               |
| `Lỗi OOP: Không thể gán! Thuộc tính 'x' là Private.` | Khai báo `var @x` hoặc gán qua một hàm `@set...`                       |
| `Hàm không có sẵn hoặc thư viện chưa được 'use'.`    | Thêm dòng `use hql.<tên>;` ở đầu file                                  |
| `Chưa kết nối Database!`                             | Gọi `connect(...)` / `connectEnv()` hoặc điền DB\_\* trong `.henv`     |
| `Thiếu cấu hình bắt buộc trong tệp .henv: ...`       | Điền các khoá được liệt kê                                             |
| `Hết kết nối trong bể ...`                           | Có giao dịch bỏ quên chưa `commit`/`rollback`, hoặc tăng `DB_POOL_MAX` |
| `Task tồn tại!`                                      | Tên tác vụ bị trùng; `taskCancel(ten)` trước hoặc đổi tên              |
| Đổi `.henv` mà không thấy tác dụng                   | Biến môi trường hệ điều hành cùng tên đang được ưu tiên hơn tệp        |

**Mức độ kiểm chứng của tài liệu này.** Các ví dụ cho `csv`, `xml`, `binary`, `color`, `config`, `env`, `schedule`, `export`, `socket`, `multiplayer`, `web` (trang `.hqlsp` và servlet) đã được chạy thử thật và kết quả ghi trong chú thích là kết quả thu được. Riêng `hql.game`, `hql.app`, `hql.ui`, `hql.mail`, `hql.database` (cần giao diện, driver hoặc tài khoản thật) được viết theo mã nguồn, chưa chạy thử trong môi trường soạn tài liệu này.

---

_LinhScript (HQL) — Hà Quang Linh. Tài liệu chi tiết từng phần nằm trong thư mục `docs/`._
