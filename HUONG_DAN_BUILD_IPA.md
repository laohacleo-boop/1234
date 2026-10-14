# 📱 Hướng Dẫn Build iOS IPA từ Xcode bằng GitHub Actions

Tài liệu này hướng dẫn chi tiết từ việc chuẩn bị đến lúc tải file `.ipa` thành công từ GitHub Actions.

---

## 📁 1. Cấu trúc các file đã được tạo sẵn trong thư mục `buiild_ipa/`

Trong thư mục `C:\Users\AD\Desktop\New folder (4)\buiild_ipa\` đã được thiết lập sẵn đầy đủ:
- `.github/workflows/build-ios.yml`: Workflow tự động hóa build IPA trên macOS runner của GitHub. Hỗ trợ cả 2 chế độ:
  - **Unsigned IPA**: Không cần tài khoản Apple Developer (dành cho TrollStore, AltStore, Sideloadly, Scarlet, ESign).
  - **Signed IPA**: Ký với chứng chỉ Apple Developer P12 và Provisioning Profile nếu có.
- `.gitattributes`: Cấu hình Git LFS để tự động xử lý các file lớn vượt quá 100MB (`libiPhone-lib.a`, `resources.assets.resS`, `xcode.zip`).
- `ExportOptions.plist`: File cấu hình xuất IPA khi dùng chế độ ký chứng chỉ.
- `xcode.zip` (hoặc thư mục `extracted/xcode`): Chứa toàn bộ source code Xcode Unity 2022.3.62f1 đã xuất sẵn.

---

## 🚀 2. Các bước triển khai lên GitHub

### Bước 1: Khởi tạo Git và cài đặt Git LFS
Mở cửa sổ PowerShell tại thư mục `buiild_ipa`:

```powershell
cd "C:\Users\AD\Desktop\New folder (4)\buiild_ipa"

# 1. Bật Git LFS
git lfs install

# 2. Khởi tạo Git Repo
git init
git branch -M main

# 3. Add cấu hình và file mã nguồn
git add .gitattributes
git add .github/
git add ExportOptions.plist
git add xcode.zip

git commit -m "feat: setup github actions for building ios ipa"
```

> **Lưu ý:** Nếu máy tính của bạn chưa có `git-lfs`, bạn có thể tải nhanh tại [git-lfs.com](https://git-lfs.com) hoặc chạy lệnh `winget install GitHub.GitLFS`.

### Bước 2: Tạo Repository trên GitHub và Push code
1. Truy cập [https://github.com/new](https://github.com/new) tạo một repository mới (đặt tên ví dụ: `nro-flat-ios`).
2. Chạy lệnh đẩy code lên:
   ```powershell
   git remote add origin https://github.com/<tai-khoan-cua-ban>/nro-flat-ios.git
   git push -u origin main
   ```

### Bước 3: Kích hoạt Build trên GitHub Actions
1. Mở trang repo của bạn trên trình duyệt, vào tab **Actions**.
2. Ở danh sách bên trái, chọn **Build iOS IPA (Unity Xcode)**.
3. Nhấp vào nút **Run workflow**:
   - **Chế độ build IPA**: Chọn `unsigned` (hoặc `signed` nếu bạn đã cấu hình secrets).
   - **Phiên bản Xcode**: Để mặc định `15.4`.
4. Bấm nút **Run workflow** màu xanh để bắt đầu build.

### Bước 4: Tải file `.ipa` hoàn thiện
1. Chờ GitHub Actions biên dịch (khoảng 10-15 phút vì dự án có hơn 1,500 file C++ IL2CPP).
2. Khi hoàn tất (icon tích xanh), bấm vào lần chạy đó, kéo xuống dưới cùng tìm mục **Artifacts**.
3. Bấm tải **NROFLAT-iOS-IPA**. Bạn sẽ nhận được file `NROFLAT_unsigned.ipa` sẵn sàng cài lên iPhone!

---

## ⚙️ Cấu hình thêm (Chỉ cần nếu muốn dùng chế độ SIGNED)
Nếu bạn có tài khoản Apple Developer ($99/năm) và muốn ký để cài thẳng hoặc phân phối Ad-Hoc:
Vào **Settings > Secrets and variables > Actions** của Repo trên GitHub và thêm các Secrets:
- `BUILD_CERTIFICATE_BASE64`: File `.p12` đã chuyển sang dạng chuỗi base64 (`certutil -encode cert.p12 cert.b64`).
- `P12_PASSWORD`: Mật khẩu của file `.p12`.
- `BUILD_PROVISION_PROFILE_BASE64`: File `.mobileprovision` dạng base64.
- `TEAM_ID`: Team ID của tài khoản Apple (10 ký tự, vd: `ABC1234XYZ`).
- `PROVISION_NAME`: Tên của Provisioning Profile trong Apple Developer Portal.
