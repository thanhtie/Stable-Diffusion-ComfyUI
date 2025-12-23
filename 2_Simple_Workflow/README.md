2_Simple_Workflow

<img width="1509" height="607" alt="image" src="https://github.com/user-attachments/assets/7ed0db62-cd68-4ed2-91f9-888f98c46571" />

Must have node:
CLIP:
Vai trò chính của CLIP trong Stable Diffusion
• Mã hóa prompt văn bản thành vector embedding
• Mỗi chữ/tokens trong prompt được mã hóa thành vector số nhiều chiều (embedding).
• Các embedding này giữ thông tin về nghĩa, ngữ cảnh, mức độ liên quan giữa các từ.
• Cung cấp “điều kiện” cho U‑Net trong quá trình khử nhiễu
• Trong lúc U‑Net khử nhiễu latent để tạo ảnh, CLIP transformer dùng cơ chế cross‑attention để “truyền” thông tin từ prompt vào từng bước khử nhiễu.
• Nhờ đó, cấu trúc và chi tiết ảnh được “kéo” về những gì prompt mô tả (vd: “cute gnome”, “Chinese style”, “4k, detailed”…).
• Quyết định khả năng hiểu prompt của từng checkpoint
• Mỗi phiên bản SD (1.5, 2.x, SDXL…) thường gắn với một biến thể CLIP khác nhau.
CLIP càng tốt, model càng hiểu đúng ngôn ngữ tự nhiên, ít bị “đi lạc đề” khi prompt phức tạp.


VAE:
Trong Stable Diffusion, VAE (Variational Autoencoder) có 2 vai trò chính:
• Nén ảnh vào latent space để mô hình khuếch tán làm việc trên không gian nhỏ hơn.
• Giải mã latent đã khử nhiễu thành ảnh pixel cuối cùng với nhiều chi tiết.
1. Nén ảnh vào latent space
• VAE encoder nhận ảnh (khi huấn luyện, hoặc khi dùng các mode như img2img) và nén từ không gian pixel lớn (ví dụ 512×512×3) xuống tensor latent nhỏ hơn nhiều (ví dụ 64×64×4).
• Việc này giúp quá trình diffusion diễn ra trong latent space, tiết kiệm RAM, VRAM và thời gian so với khuếch tán trực tiếp trên ảnh gốc; đó là lý do Stable Diffusion chạy được trên GPU 8 GB.
2. Giải mã latent thành ảnh thật
• Sau khi U‑Net khử nhiễu xong và tạo ra latent “sạch”, VAE decoder chuyển tensor latent này trở lại thành ảnh RGB mà bạn nhìn thấy.
• Chất lượng VAE (đặc biệt là phần decoder) ảnh hưởng trực tiếp đến độ nét, mắt, mặt, texture… nên người ta hay dùng VAE custom để cải thiện chi tiết như mắt, da, chữ.
3. Ý nghĩa thực tế khi dùng SD
• Chọn VAE khác nhau có thể làm ảnh sắc hơn, mềm hơn hoặc đổi cảm giác màu sắc, tương tự như đổi “ống kính/engine giải mã”.
Nếu VAE không khớp với checkpoint (hoặc bị lỗi), ảnh có thể bị mờ, ám màu, méo mặt, hoặc xuất hiện artifact lạ.<img width="1264" height="265" alt="image" src="https://github.com/user-attachments/assets/e17fad0c-482b-4ed3-8ee6-cdd25e05a96a" />

KSampler:
• Nhận latent ban đầu (thường là noise hoặc latent từ img2img) và các điều kiện:
• Model (checkpoint U‑Net + VAE),
• Positive / Negative conditioning (prompt, negative prompt từ CLIP),
• Các tham số sampling: seed, steps, CFG, sampler, scheduler, denoise.​
• Chạy quá trình diffusion:
• Thêm/tạo noise từ seed,
• Gọi hàm sample của Stable Diffusion (Euler, DPM++, v.v.) qua nhiều steps để dần khử nhiễu latent,
• Xuất ra latent đã được khử nhiễu, sẵn để đưa qua VAE decode thành ảnh.​ 
Các tham số quan trọng trong KSampler:
• seed: quyết định mẫu noise khởi đầu; cùng seed + cùng config → ảnh giống nhau.​
• steps: số bước khử nhiễu; nhiều bước hơn thường chi tiết/ổn định hơn, nhưng chậm hơn.​​
• cfg (Classifier Free Guidance): cân bằng giữa “nghe lời prompt” và độ tự do sáng tạo; quá cao dễ cháy chi tiết, méo ảnh.​​
• sampler_name: thuật toán sampling (Euler, DPM++, Heun…); ảnh hưởng style, tốc độ, độ mượt.​
• scheduler: quy luật phân bố/noise schedule theo thời gian, ảnh hưởng đường hội tụ.​
• denoise: 1.0 = khử noise full (text2img), <1.0 = giữ cấu trúc ảnh gốc hơn (img2img, inpaint).​
Tóm tắt dễ hiểu
• CLIP: hiểu prompt → tạo conditioning.
• U‑Net + KSampler: chạy quá trình khuếch tán/khử nhiễu theo conditioning đó trong latent space.​
• VAE: giải mã latent đã khử nhiễu thành ảnh pixel cuối cùng.​
Nói ngắn gọn: KSampler chính là “lò nướng” nơi mọi ma thuật khuếch tán diễn ra – nó quyết định đường đi của latent từ noise đến ảnh.<img width="920" height="440" alt="image" src="https://github.com/user-attachments/assets/5bb9c495-769d-4c00-ae33-ca9adc067a16" />

