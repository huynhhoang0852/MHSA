# MHSA
## Mục lục
* [linear_top](#linear_top)
* [attention_score_calc](#attention_score_calc)

---

## linear_top

**Mô tả module:**
Module `linear_top` đảm nhiệm phase 1 trong phần cứng, thực hiện các phép tính nhân ma trận (matrix multiplication) cốt lõi để tạo ra các giá trị Query, Key và Value cho khối MHSA.

**Sơ đồ khối (Block Diagram):**
[![Sơ đồ khối linear_top](https://via.placeholder.com/600x300?text=Sơ+đồ+khối+linear_top)](https://via.placeholder.com/600x300)

**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng (Direction) | Độ rộng (Width) | Mô tả (Description) |
| :--- | :---: | :---: | :--- |
| `clk` | input | 1 | Xung clock hệ thống |
| `rst_n` | input | 1 | Tín hiệu reset (tích cực mức thấp) |
| `valid_in` | input | 1 | Tín hiệu báo dữ liệu đầu vào hợp lệ |
| `data_in` | input | 32 | Dữ liệu đầu vào từ RAM/ROM |
| `data_out` | output| 32 | Dữ liệu đầu ra sau tính toán |
| `valid_out`| output| 1 | Tín hiệu báo dữ liệu đầu ra hợp lệ |

**Thông số tham số hóa (Parameters):**
| Tên Parameter | Giá trị mặc định | Mô tả |
| :--- | :---: | :--- |
| `DATA_WIDTH` | 32 | Độ rộng bit của dữ liệu vào/ra |
| `ADDR_WIDTH` | 8 | Độ rộng bus địa chỉ |
