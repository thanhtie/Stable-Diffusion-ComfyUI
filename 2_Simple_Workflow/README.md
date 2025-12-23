2_Simple_Workflow

<img width="1432" height="1057" alt="image" src="https://github.com/user-attachments/assets/ba971b11-e860-4d0b-b450-a48250add5cd" />


## Must-have nodes (Stable Diffusion / ComfyUI)

### CLIP
- Mã hóa prompt văn bản thành vector embedding.
- Mỗi token trong prompt được mã hóa thành vector số nhiều chiều (embedding) chứa nghĩa, ngữ cảnh, mức độ liên quan giữa các từ.
- Cung cấp conditioning cho U‑Net trong quá trình khử nhiễu thông qua cơ chế cross‑attention.
- Ảnh hưởng trực tiếp đến khả năng hiểu prompt của từng checkpoint (SD 1.5, 2.x, SDXL…).

### VAE
- Nén ảnh từ pixel space (ví dụ 512×512×3) xuống latent space nhỏ hơn (ví dụ 64×64×4) để mô hình diffusion làm việc, tiết kiệm VRAM/RAM.
- Giải mã latent đã khử nhiễu thành ảnh RGB cuối cùng.
- VAE chất lượng tốt cho chi tiết mắt, da, texture; VAE sai/checkpoint lệch có thể gây mờ, ám màu, méo mặt, artifact.

### KSampler
- Nhận latent ban đầu (noise hoặc từ img2img) + model (U‑Net + VAE) + conditioning từ CLIP (positive/negative) + tham số sampling (seed, steps, cfg, sampler, scheduler, denoise).
- Thực hiện quá trình khuếch tán/khử nhiễu nhiều bước để xuất latent sạch, chuẩn bị đưa qua VAE decode.
- Tham số quan trọng:
  - `seed`: quyết định mẫu noise khởi đầu.
  - `steps`: số bước khử nhiễu.
  - `cfg`: độ “nghe lời prompt”.
  - `sampler_name`: thuật toán sampling (Euler, DPM++, Heun…).
  - `scheduler`: cách phân bố/noise schedule theo thời gian.
  - `denoise`: 1.0 = text2img, <1.0 = giữ cấu trúc ảnh gốc hơn (img2img/inpaint).

### Tóm tắt pipeline
- **CLIP**: hiểu prompt → tạo conditioning.
- **U‑Net + KSampler**: khuếch tán/khử nhiễu trong latent space theo conditioning.
- **VAE**: giải mã latent sạch thành ảnh pixel cuối cùng.

