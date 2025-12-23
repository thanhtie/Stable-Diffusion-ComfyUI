

<img width="2013" height="1047" alt="image" src="https://github.com/user-attachments/assets/c80d6df5-b274-4c90-b9fd-0981ce69b9d5" />



### ControlNet – Quy trình node cơ bản trong ComfyUI

1. **Nạp model ControlNet**
   - Dùng node **Load ControlNet Model** (hoặc tương đương) để nạp checkpoint ControlNet phù hợp với model base (SD/SDXL/Flux…).
   - Kết nối output ControlNet này vào node **Apply ControlNet** trong pipeline.

2. **Chuẩn bị ảnh điều khiển (control image)**
   - Dùng node **Load Image** để nạp ảnh tham chiếu (pose, depth, edge…).
   - Đưa ảnh này vào node **AIO AUX Preprocessor** (hoặc các preprocessor riêng lẻ) để sinh ra map control chuyên biệt.
   - Kết nối output đã preprocess của AIO AUX vào input ảnh của **Apply ControlNet**.

3. **Route prompt vào ControlNet**
   - Route **positive prompt** (từ CLIP/Text Encoder) vào input conditioning của **Apply ControlNet**.
   - Route **negative prompt** tương tự, nếu node hỗ trợ cả positive/negative conditioning.
   - Đảm bảo output conditioning sau ControlNet quay lại U‑Net/KSampler trong nhánh chính của workflow.

---

### Một số preprocessor thường dùng

- **Canny (Edge / Line art)**
  - Trích xuất biên dạng sắc nét từ ảnh gốc để giữ **layout, hình khối, đường viền**.
  - Hữu ích cho style chuyển đổi (style transfer) nhưng vẫn khóa bố cục.  
  - Ví dụ node setup: Canny → Apply ControlNet → KSampler.

  ![Canny Example](https://github.com/user-attachments/assets/d5a8e197-0834-4a72-9689-9b9048eb0d4c)

- **Depth**
  - Sinh bản đồ **độ sâu** (depth map) để model hiểu cấu trúc không gian 3D.
  - Dùng cho các bài toán giữ **perspective, khoảng cách, bố cục không gian** khi đổi style hoặc nội dung.  

  ![Depth Example](https://github.com/user-attachments/assets/6dd93550-80c6-48d3-83d8-6c957011564e)

- **Pose (OpenPose / Pose control)**
  - Trích xuất **khung xương / tư thế cơ thể** để khóa pose nhân vật.
  - Phù hợp cho: nhân vật nhất quán pose, tạo lại ảnh người với trang phục/style khác nhưng giữ dáng.  

  ![Pose Example](https://github.com/user-attachments/assets/1d207ea9-0370-4272-92fd-7615208581a7)
