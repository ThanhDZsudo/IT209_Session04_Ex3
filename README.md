# Báo Cáo Bài Tập 3: Cấu Hình Xác Thực SSH Và Đẩy Dự Án Lên GitHub

- **Khóa học:** IT209 - DevOps
- **Họ và tên:** Nguyễn Tiến Thành
- **Mã số sinh viên (MSSV):** B24DTCN283
- **Email:** thenngk6@gmail.com
- **GitHub Username:** ThanhDZsudo
- **Đường dẫn nộp bài dự kiến:** `homework/session_04/ex3/`
- **GitHub Repository URL:** `git@github.com:ThanhDZsudo/IT209_Devops.git`

---

## 1. Mục Tiêu Bài Tập
- Khởi tạo cặp khóa SSH bảo mật với thuật toán **Ed25519** phục vụ mục đích xác thực kết nối an toàn từ máy cục bộ tới máy chủ GitHub.
- Cấu hình liên kết an toàn giữa repository cục bộ và máy chủ GitHub qua giao thức SSH (không dùng HTTPS để tránh phải nhập token/mật khẩu thủ công).
- Đẩy (push) mã nguồn và toàn bộ lịch sử commit thành công lên GitHub bằng giao thức SSH.
- Kiểm tra tính xác thực của kết nối và đường dẫn remote URL.

---

## 2. Các Bước Thực Hiện Chi Tiết

### Bước 1: Khởi tạo cặp khóa SSH với thuật toán Ed25519
Sử dụng thuật toán **Ed25519** (chuẩn mã hóa hiện đại, an toàn và có hiệu năng cao hơn RSA):
```bash
ssh-keygen -t ed25519 -C "thenngk6@gmail.com"
```
- Khi được hỏi nơi lưu khóa: Nhấn `Enter` để lưu vào đường dẫn mặc định (`~/.ssh/id_ed25519`).
- Khi được hỏi Passphrase: Nhập mật khẩu bảo vệ khóa (hoặc nhấn `Enter` để bỏ qua).

**Kết quả sau khi sinh khóa:**
- Khóa bí mật (Private key): `C:\Users\OS\.ssh\id_ed25519` *(tuyệt đối không chia sẻ)*
- Khóa công khai (Public key): `C:\Users\OS\.ssh\id_ed25519.pub`

**Nội dung khóa công khai (`id_ed25519.pub`):**
```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBogmFywsQJz0LEZ4DEPLFveqncU7EUpMkZCTHlssDIe thenngk6@gmail.com
```

---

### Bước 2: Thêm khóa công khai vào tài khoản GitHub
1. Truy cập vào GitHub: [https://github.com/settings/keys](https://github.com/settings/keys)
2. Bấm vào nút **"New SSH key"**.
3. Điền các trường thông tin:
   - **Title:** `Laptop Windows - DevOps` (hoặc tên máy tính).
   - **Key type:** `Authentication Key`.
   - **Key:** Dán toàn bộ nội dung tệp `id_ed25519.pub` ở trên.
4. Bấm **"Add SSH key"** để hoàn tất xác thực.

---

### Bước 3: Kiểm tra kết nối SSH tới GitHub
Chạy lệnh kiểm tra tính xác thực của cặp khóa với máy chủ GitHub:
```bash
ssh -T git@github.com
```

**Kết quả thực thi thành công (Expected Output):**
```text
Hi ThanhDZsudo! You've successfully authenticated, but GitHub does not provide shell access.
```
> **Phân tích:** Thông báo xác nhận GitHub đã nhận diện đúng danh tính tài khoản `ThanhDZsudo` thông qua khóa SSH công khai đã thêm.

---

### Bước 4: Liên kết Repository cục bộ với Remote Repository trên GitHub
Thiết lập đường dẫn remote origin bằng giao thức SSH:
```bash
# Thêm remote origin qua giao thức SSH
git remote add origin git@github.com:ThanhDZsudo/IT209_Devops.git

# Kiểm tra đường dẫn remote
git remote -v
```

**Kết quả kiểm tra `git remote -v`:**
```text
origin  git@github.com:ThanhDZsudo/IT209_Devops.git (fetch)
origin  git@github.com:ThanhDZsudo/IT209_Devops.git (push)
```

---

### Bước 5: Đẩy (Push) mã nguồn và lịch sử commit lên GitHub
Chuyển nhánh chính sang `main` và đẩy toàn bộ lịch sử commit lên kho lưu trữ từ xa:
```bash
git branch -M main
git push -u origin main
```

**Kết quả thực thi (Output):**
```text
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:ThanhDZsudo/IT209_Devops.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

---

## 3. Kết Quả Kiểm Tra Và Bằng Chứng Thực Thi (Verification)

### 3.1. Kết quả kiểm tra SSH (`ssh -T git@github.com`)
```text
Hi ThanhDZsudo! You've successfully authenticated, but GitHub does not provide shell access.
```

### 3.2. Kết quả kiểm tra Remote URL (`git remote -v`)
```text
origin  git@github.com:ThanhDZsudo/IT209_Devops.git (fetch)
origin  git@github.com:ThanhDZsudo/IT209_Devops.git (push)
```

### 3.3. Hình ảnh chụp màn hình minh chứng:
![Minh chứng cấu hình SSH và Remote](ssh_evidence.png)

---

## 4. Phân Tích Kỹ Thuật

1. **Tại sao sử dụng thuật toán Ed25519 thay vì RSA?**
   - **Bảo mật vượt trội:** Ed25519 dựa trên đường cong elliptic (Curve25519), mang lại độ an toàn tương đương RSA 3072-bit nhưng với kích thước khóa chỉ 256-bit.
   - **Tốc độ và hiệu năng:** Quá trình sinh khóa, ký số và xác thực nhanh hơn đáng kể so với thuật toán RSA.
   - **Chống tấn công Side-Channel:** Thiết kế của Ed25519 miễn nhiễm với các kiểu tấn công phân tích thời gian (timing attacks).

2. **So sánh giao thức SSH và HTTPS trong Git:**
   - **Giao thức HTTPS:** Thường yêu cầu người dùng phải tạo và nhập Personal Access Token (PAT) hoặc sử dụng Git Credential Manager; token có thời hạn và dễ gây gián đoạn quy trình tự động hóa.
   - **Giao thức SSH:** Xác thực bất đối xứng dựa trên cặp khóa Public/Private Key. Sau khi cài đặt một lần, mọi thao tác `push`, `pull`, `clone` diễn ra tự động, an toàn tuyệt đối và thích hợp nhất cho các luồng CI/CD trong DevOps.

3. **Nguyên tắc an toàn bảo mật khóa:**
   - Khóa bí mật (`id_ed25519`): Tuyệt đối giữ trên máy cục bộ, không gửi cho bất kỳ ai, không đưa vào repository hay commit lên mạng.
   - Khóa công khai (`id_ed25519.pub`): Chỉ dùng để khai báo cho các dịch vụ từ xa (GitHub, GitLab, Server) để xác minh danh tính.