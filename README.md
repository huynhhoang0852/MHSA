# Multi-head Self Attention

## Mục lục

| Tên file | 
| :--- | 
| [`mhsa_top.sv`](#mhsa_top) |
| [`linear_qkv_multihead.sv`](#linear_qkv_multihead) | 
| [`attention_head.sv`](#attention_head) |
| [`mac.sv`](#mac) |
| [`softmax_top.sv`](#softmax_top) |
| [`sum_exp.sv`](#sum_exp) | 
| [`linear_output.sv`](#linear_output) | 

---

<a name="mhsa_top"></a>
## 1. mhsa_top

**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng | Độ rộng | Mô tả |
| :--- | :---: | :---: | :--- |
| `clk` | input | 1 | Xung clock hệ thống |
| `rst_n` | input | 1 | Reset tích cực mức thấp |
| `start` | input | 1 | Tín hiệu bắt đầu quá trình tính toán MHSA |
| `done` | output| 1 | Cờ báo hiệu tính toán xong toàn khối |
| `addr_X` | output| 12 | Bus địa chỉ truy cập ma trận đầu vào X (RAM 64x64) |
| `data_X` | input | 16 | Dữ liệu ma trận X đọc từ RAM |
| `we_Z_final` | output| 1 | Cờ cho phép ghi vào RAM Z_Final đầu ra |
| `addr_Z_final_A/B`| output| 12 | Bus địa chỉ Port A và B của RAM Z_Final |
| `data_Z_final_A/B`| output| 16 | Dữ liệu đầu ra Port A và B ghi vào RAM Z_Final |

---

<a name="linear_qkv_multihead"></a>
## 2. linear_qkv_multihead

**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng | Độ rộng | Mô tả |
| :--- | :---: | :---: | :--- |
| `clk`, `rst_n` | input | 1 | Clock và Reset |
| `start`, `done`| in/out| 1 | Tín hiệu bắt đầu và hoàn thành |
| `addr_X`, `addr_W`| output| 12 | Địa chỉ đọc RAM X và 3 ROM trọng số đồng bộ |
| `data_X` | input | 16 | Dữ liệu từ ma trận X |
| `data_Wq/Wk/Wv`| input | 16 | Dữ liệu trọng số từ các ROM Wq, Wk, Wv |
| `we_Q/K/V [4]` | output| 1 | (Mảng) Cờ cho phép ghi Q, K, V cho 4 Heads |
| `addr_out [4]` | output| 10 | (Mảng) Địa chỉ ghi vào RAM của 4 Heads |
| `data_Q/K/V_out[4]`| output| 16 | (Mảng) Dữ liệu Q, K, V phân bổ cho 4 Heads |

---

<a name="attention_head"></a>
## 3. attention_head


**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng | Độ rộng | Mô tả |
| :--- | :---: | :---: | :--- |
| `clk`, `rst_n` | input | 1 | Clock và Reset |
| `start`, `done`| in/out| 1 | Tín hiệu điều khiển FSM Head |
| `linear_we` | input | 1 | Cờ cho phép ghi dữ liệu trực tiếp từ khối Linear QKV |
| `linear_addr` | input | 10 | Địa chỉ ghi dữ liệu từ khối Linear QKV |
| `linear_data_Q/K/V`| input | 16 | Dữ liệu Q, K, V do Linear QKV truyền tới |
| `addr_Z_read` | input | 10 | Đường ray cho phép khối Linear Output bên ngoài đọc kết quả Z |
| `data_Z_out` | output| 16 | Dữ liệu ma trận Z xuất ra cho khối Linear Output |

---

<a name="mac"></a>
## 4. mac

**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng | Độ rộng | Mô tả |
| :--- | :---: | :---: | :--- |
| `clk`, `rst_n` | input | 1 | Clock và Reset |
| `en` | input | 1 | Cho phép tích lũy vào accumulator |
| `clr_acc` | input | 1 | Xóa thanh ghi tích lũy (độc lập với tín hiệu en) |
| `a_in`, `b_in` | input | 16 | Hai toán hạng đầu vào (định dạng Q8.8 signed) |
| `mac_out` | output| 16 | Kết quả sau khi tích lũy và làm tròn (Q8.8 signed) |

---

<a name="softmax_top"></a>
## 5. softmax_top


**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng | Độ rộng | Mô tả |
| :--- | :---: | :---: | :--- |
| `clk`, `rst_n`, `start`, `done` | in/out| 1 | Các tín hiệu hệ thống |
| `addr_Score` | output| 12 | Địa chỉ đọc điểm Score từ RAM |
| `data_Score` | input | 16 | Điểm Score tương ứng đọc từ RAM |
| `max_val_in` | input | 16 | Giá trị Max của hàng hiện tại (truyền vào từ pha online) |
| `addr_Exp` | output| 13 | Địa chỉ xuất ra để tra cứu ROM $e^x$ |
| `data_Exp` | input | 16 | Dữ liệu trả về từ ROM $e^x$ |
| `we_Softmax` | output| 1 | Cờ cho phép ghi vào RAM Softmax |
| `addr_Softmax`| output| 12 | Địa chỉ ghi kết quả Softmax |
| `data_Softmax`| output| 16 | Giá trị Softmax cuối cùng sau khi chia |

---

<a name="sum_exp"></a>
## 6. sum_exp

**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng | Độ rộng | Mô tả |
| :--- | :---: | :---: | :--- |
| `clk`, `rst_n`, `start`, `done` | in/out| 1 | Các tín hiệu hệ thống |
| `row_idx` | input | 6 | Chỉ mục hàng đang được xử lý tổng |
| `max_val` | input | 16 | Giá trị Max của hàng (dùng để tính $x_{norm}$) |
| `sum_val` | output| 32 | Kết quả cộng dồn mẫu số hàm Softmax |
| `addr_Score`, `data_Score` | out/in| 12/16| Giao tiếp lấy điểm Score từ RAM |
| `addr_Exp`, `data_Exp` | out/in| 13/16| Giao tiếp tra cứu bảng LUT Exponential |

---

<a name="linear_output"></a>
## 7. linear_output

**Danh sách tín hiệu I/O:**

| Tên tín hiệu | Hướng | Độ rộng | Mô tả |
| :--- | :---: | :---: | :--- |
| `clk`, `rst_n`, `start`, `done` | in/out| 1 | Tín hiệu điều khiển hệ thống |
| `addr_Z [4]` | output| 10 | (Mảng) Địa chỉ xuất cho 4 RAM Z |
| `data_Z [4]` | input | 16 | (Mảng) Dữ liệu đọc về từ 4 RAM Z tương ứng |
| `addr_Wo_A/B` | output| 12 | Địa chỉ cổng A và B của ROM Dual-port $W_O$ |
| `data_Wo_A/B` | input | 16 | Trọng số đọc từ cổng A và B của ROM $W_O$ |
| `we_Out` | output| 1 | Cờ cho phép ghi vào RAM kết quả cuối cùng |
| `addr_Out_A/B`| output| 12 | Địa chỉ ghi cổng A và B cho RAM Output |
| `data_Out_A/B`| output| 16 | Dữ liệu ma trận Output cuối cùng |
