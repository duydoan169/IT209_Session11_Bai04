# Báo Cáo Bài 4: Khai Báo GitHub Secrets & Cấu Hình Đăng Nhập Container Registry (GHCR)

## 1. Mục tiêu bài thực hành
- Tạo Personal Access Token (classic) trên GitHub với quyền phù hợp (`write:packages`).
- Cấu hình Repository Secret an toàn với tên `REGISTRY_TOKEN`.
- Viết workflow `.github/workflows/ci.yml` sử dụng Action `docker/login-action@v3` để tự động đăng nhập vào GitHub Container Registry (`ghcr.io`).
- Kiểm tra kết quả thực thi trên GitHub Actions tab và xác nhận trạng thái `Login Succeeded`.

---

## 2. Hướng dẫn từng bước thực hiện

### Bước 1: Tạo Personal Access Token (PAT) trên GitHub
1. Truy cập GitHub, bấm vào ảnh đại diện (avatar) góc trên bên phải -> chọn **Settings**.
2. Cuộn xuống thanh menu bên trái, chọn **Developer Settings** (ở dưới cùng).
3. Chọn **Personal access tokens** -> **Tokens (classic)**.
4. Bấm **Generate new token** -> chọn **Generate new token (classic)**.
5. Điền thông tin:
   - **Note**: `GHCR_TOKEN` hoặc `REGISTRY_TOKEN`
   - **Expiration**: Chọn thời hạn (ví dụ: `30 days` hoặc tùy chọn)
   - **Select scopes**: Tích chọn quyền:
     - `write:packages` (tải/đẩy packages lên GitHub Packages & GHCR)
     - *(Tùy chọn: `read:packages`, `delete:packages`, `repo` nếu cần thao tác repository)*
6. Bấm **Generate token**.
7. **QUAN TRỌNG:** Sao chép mã token hiển thị (bắt đầu bằng `ghp_...`). Token này chỉ hiển thị một lần duy nhất!

---

### Bước 2: Thêm Secret vào Repository (`REGISTRY_TOKEN`)
1. Truy cập vào repository bài làm của bạn trên GitHub.
2. Chọn tab **Settings** của repository.
3. Ở menu bên trái, tìm mục **Secrets and variables** -> bấm chọn **Actions**.
4. Trong mục **Repository secrets**, bấm nút **New repository secret**.
5. Nhập các thông tin:
   - **Name**: `REGISTRY_TOKEN`
   - **Secret**: Dán mã Personal Access Token bạn vừa tạo ở Bước 1.
6. Bấm **Add secret**.
7. **Chụp ảnh màn hình:** Chụp lại giao diện trang *Actions secrets and variables* hiển thị secret `REGISTRY_TOKEN` vừa tạo để làm minh chứng nộp bài.

---

### Bước 3: Cấu hình workflow `.github/workflows/ci.yml`
File [ci.yml](file:///c:/Users/ADMIN/OneDrive/Desktop/IT209/Session11/Lesson4/.github/workflows/ci.yml) đã được tạo với nội dung:

```yaml
name: Build and Login GHCR

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
  workflow_dispatch:

jobs:
  login-registry:
    name: Login to Container Registry
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Đăng nhập vào GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.REGISTRY_TOKEN }}
```

---

### Bước 4: Đẩy code và kiểm tra kết quả (Verification)
1. Thêm file và commit đẩy lên GitHub:
   ```bash
   git add .
   git commit -m "ci: add docker login to ghcr workflow"
   git push origin main
   ```
2. Vào tab **Actions** trên GitHub repository.
3. Chọn workflow run vừa được kích hoạt -> bấm vào job **Login to Container Registry**.
4. Mở rộng bước **Đăng nhập vào GitHub Container Registry**.
5. Xác nhận dòng log hiển thị:
   ```text
   Logging into ghcr.io...
   Login Succeeded
   ```

---

## 3. Danh mục nộp bài
1. Tệp kịch bản [.github/workflows/ci.yml](file:///c:/Users/ADMIN/OneDrive/Desktop/IT209/Session11/Lesson4/.github/workflows/ci.yml).
2. Ảnh chụp màn hình trang **Settings -> Secrets and variables -> Actions** hiển thị secret `REGISTRY_TOKEN`.
3. (Tùy chọn) Ảnh chụp màn hình tab **Actions** với bước đăng nhập thành công `Login Succeeded`.
