# HƯỚNG DẪN THỰC HÀNH DEVOPS TỪNG BƯỚC (CONSOLE COMMAND PLAYBOOK)
> **Tài liệu tập trung 100% vào thao tác thực hành, lệnh gõ console và mẫu hiển thị kết quả terminal.**  
> **Áp dụng:** Windows PowerShell (Máy cá nhân) kết hợp SSH vào VPS Ubuntu Server.

---

## BẢNG THÔNG SỐ CẦN ĐIỀN TRƯỚC KHI THỰC HÀNH

Hãy thay thế các giá trị mẫu bên dưới bằng thông tin thực tế của bạn khi chạy các câu lệnh:

| Ký hiệu mẫu | Ý nghĩa | Ví dụ mẫu |
|---|---|---|
| `<VPS_IP>` | Địa chỉ IP công khai của máy chủ VPS | `103.176.178.50` |
| `<MA_SINH_VIEN>` | Mã sinh viên viết thường | `b21dccn001` |
| `<GITHUB_USERNAME>` | Tên tài khoản GitHub của bạn | `nguyenvana-devops` |
| `<APP_PORT>` | Cổng public trên VPS (từ 8080 trở lên) | `8088` |
| `devops_student` | Tên user Linux cá nhân tạo trên VPS | `devops_student` |

---

## NGUYÊN TẮC NHẬN BIẾT MÔI TRƯỜNG TERMINAL

* **`[PowerShell - máy cá nhân]`**: Dấu nhắc lệnh có chữ `PS` và đường dẫn ổ đĩa Windows:
  ```powershell
  PS D:\IT209\HN_K24_CNTT2_DEVOPS_Session14-main\devops-final-app-de001>
  ```
* **`[VPS Ubuntu - qua SSH (root)]`**: Dấu nhắc kết thúc bằng dấu thăng `#`:
  ```bash
  root@ubuntu-server:~#
  ```
* **`[VPS Ubuntu - qua SSH (user cá nhân)]`**: Dấu nhắc kết thúc bằng dấu đô la `$`:
  ```bash
  devops_student@ubuntu-server:~$
  ```

---

# BÀI 1: QUẢN TRỊ VPS LINUX

---

### Bước 1.1: Đăng nhập SSH vào VPS bằng tài khoản root/quản trị ban đầu

Mở **Windows PowerShell** trên máy tính của bạn và gõ:

```powershell
# [PowerShell - máy cá nhân]
ssh root@<VPS_IP>
```

#### Màn hình hiển thị thực tế:
```text
The authenticity of host '103.176.178.50 (103.176.178.50)' can't be established.
ED25519 key fingerprint is SHA256:7uK3j8LpQ2Xz1Wv9YmN4rB6tG0sC8eA1dF5gH2jK4lM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```
👉 **Thao tác:** Gõ chữ `yes` rồi nhấn **Enter**.

Tiếp theo hệ thống yêu cầu nhập mật khẩu root của VPS:
```text
root@103.176.178.50's password: 
```
👉 **Thao tác:** Nhập mật khẩu VPS do giảng viên/nhà cung cấp cấp (khi gõ mật khẩu trên Linux, màn hình sẽ **không hiển thị** bất kỳ ký tự nào, cứ gõ đúng rồi nhấn **Enter**).

#### Kết quả đăng nhập thành công:
```text
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-105-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

Last login: Thu Oct 16 01:20:15 2026 from 14.232.112.5
root@ubuntu-server:~# 
```
*(Dấu nhắc lệnh đã chuyển sang `root@ubuntu-server:~#` tức bạn đang ở trong VPS).*

---

### Bước 1.2: Kiểm tra tài khoản hiện tại

```bash
# [VPS Ubuntu - qua SSH]
whoami
id
```

#### Màn hình hiển thị thực tế:
```text
root@ubuntu-server:~# whoami
root
root@ubuntu-server:~# id
uid=0(root) gid=0(root) groups=0(root)
```

---

### Bước 1.3: Tạo tài khoản Linux cá nhân mới (`devops_student`)

```bash
# [VPS Ubuntu - qua SSH]
adduser devops_student
```

#### Màn hình hiển thị thực tế:
```text
root@ubuntu-server:~# adduser devops_student
Adding user `devops_student' ...
Adding new group `devops_student' (1001) ...
Adding new user `devops_student' (1001) with group `devops_student' ...
Creating home directory `/home/devops_student' ...
Copying files from `/etc/skel' ...
New password: 
```
👉 **Thao tác:** Nhập mật khẩu mới cho tài khoản `devops_student` (ví dụ: `Devops@12345`), nhấn Enter.  
Hệ thống sẽ hỏi lại:
```text
Retype new password: 
```
👉 **Thao tác:** Nhập lại mật khẩu vừa gõ, nhấn Enter.

Sau đó hệ thống hỏi thông tin người dùng:
```text
passwd: password updated successfully
Changing the user information for devops_student
Enter the new value, or press ENTER for the default
	Full Name []: Nguyen Van A
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
root@ubuntu-server:~# 
```
👉 **Thao tác:** Nhập họ tên của bạn, các mục khác nhấn **Enter** bỏ qua, cuối cùng gõ `y` rồi nhấn **Enter**.

---

### Bước 1.4: Cấp quyền sudo và chuyển sang tài khoản mới

```bash
# [VPS Ubuntu - qua SSH]
# 1. Thêm devops_student vào nhóm sudo (để có thể chạy lệnh quản trị khi cần)
usermod -aG sudo devops_student

# 2. Chuyển sang tài khoản devops_student
su - devops_student
```

#### Màn hình hiển thị thực tế:
```text
root@ubuntu-server:~# usermod -aG sudo devops_student
root@ubuntu-server:~# su - devops_student
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

devops_student@ubuntu-server:~$ 
```
*(Dấu nhắc lệnh đổi thành `devops_student@ubuntu-server:~$` - bạn đã là user thường).*

---

### Bước 1.5: Thêm tài khoản vào nhóm Docker và kiểm tra quyền

```bash
# [VPS Ubuntu - qua SSH]
# 1. Thêm tài khoản vào nhóm docker
sudo usermod -aG docker devops_student
```
*(Hệ thống sẽ yêu cầu nhập mật khẩu của `devops_student`, hãy nhập mật khẩu).*

```bash
# [VPS Ubuntu - qua SSH]
# 2. Nạp nhóm mới ngay lập tức mà không cần logout
newgrp docker

# 3. Kiểm tra danh sách nhóm
id

# 4. Kiểm tra quyền chạy docker
docker ps
```

#### Màn hình hiển thị thực tế:
```text
devops_student@ubuntu-server:~$ sudo usermod -aG docker devops_student
[sudo] password for devops_student: 
devops_student@ubuntu-server:~$ newgrp docker
devops_student@ubuntu-server:~$ id
uid=1001(devops_student) gid=999(docker) groups=999(docker),27(sudo),1001(devops_student)
devops_student@ubuntu-server:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
devops_student@ubuntu-server:~$ 
```
*(Lệnh `docker ps` chạy thành công, không báo lỗi Permission denied là đạt yêu cầu).*

---

### Bước 1.6: Tạo thư mục `devops-exam` và tệp `info.txt`

```bash
# [VPS Ubuntu - qua SSH]
# 1. Đảm bảo ở thư mục cá nhân
cd ~

# 2. Tạo thư mục devops-exam
mkdir -p devops-exam
cd devops-exam

# 3. Mở nano để tạo và soạn thảo info.txt
nano info.txt
```

#### Thao tác trong trình soạn thảo Nano:
Gõ nội dung sau vào màn hình nano:
```text
Họ và tên: Nguyen Van A
Mã sinh viên: B21DCCN001
GitHub Repository: https://github.com/<GITHUB_USERNAME>/devops-exam-<MA_SINH_VIEN>
```
👉 **Cách lưu và thoát Nano:**
1. Nhấn tổ hợp phím: `Ctrl + O`
2. Nhấn phím: `Enter` (để xác nhận lưu tên file `info.txt`)
3. Nhấn tổ hợp phím: `Ctrl + X` (để thoát ra ngoài terminal)

---

### Bước 1.7: Kiểm tra nội dung, phân quyền `chmod` và `chown`

```bash
# [VPS Ubuntu - qua SSH]
# 1. Xem nội dung file
cat info.txt

# 2. Phân quyền file info.txt (Chủ sở hữu đọc/ghi, người khác chỉ đọc - 644)
chmod 644 info.txt

# 3. Phân quyền thư mục devops-exam (755)
chmod 755 ~/devops-exam

# 4. Kiểm tra quyền hiển thị
ls -l info.txt
ls -ld ~/devops-exam
```

#### Màn hình hiển thị thực tế:
```text
devops_student@ubuntu-server:~/devops-exam$ cat info.txt
Họ và tên: Nguyen Van A
Mã sinh viên: B21DCCN001
GitHub Repository: https://github.com/nguyenvana-devops/devops-exam-b21dccn001

devops_student@ubuntu-server:~/devops-exam$ chmod 644 info.txt
devops_student@ubuntu-server:~/devops-exam$ chmod 755 ~/devops-exam

devops_student@ubuntu-server:~/devops-exam$ ls -l info.txt
-rw-r--r-- 1 devops_student devops_student 112 Oct 16 02:40 info.txt

devops_student@ubuntu-server:~/devops-exam$ ls -ld ~/devops-exam
drwxr-xr-x 2 devops_student devops_student 4096 Oct 16 02:39 /home/devops_student/devops-exam
```
📷 **CHỤP ẢNH MINH CHỨNG BÀI 1:** Chụp toàn bộ màn hình terminal có các lệnh `whoami`, `id`, `cat info.txt`, `ls -l`.

---

### Bước 1.8: Thoát phiên làm việc VPS về lại Windows PowerShell

```bash
# [VPS Ubuntu - qua SSH]
# Thoát tài khoản devops_student (về root)
exit

# Thoát root (đóng hẳn kết nối SSH)
exit
```

#### Màn hình hiển thị thực tế:
```text
devops_student@ubuntu-server:~/devops-exam$ exit
exit
root@ubuntu-server:~# exit
logout
Connection to 103.176.178.50 closed.
PS D:\IT209\HN_K24_CNTT2_DEVOPS_Session14-main> 
```
*(Bạn đã quay trở lại giao diện **PowerShell của máy tính Windows**).*

---

# BÀI 2: CẤU HÌNH BIẾN MÔI TRƯỜNG & KIỂM TRA DOCKER

---

### Bước 2.1: Chỉnh sửa tệp `student.env` trên máy Windows

Mở PowerShell trên máy tính, chuyển vào thư mục dự án:

```powershell
# [PowerShell - máy cá nhân]
cd D:\IT209\HN_K24_CNTT2_DEVOPS_Session14-main\devops-final-app-de001
notepad student.env
```
*(Cửa sổ Notepad sẽ hiện ra để bạn chỉnh sửa file `student.env`).*

#### Điền nội dung chính xác vào `student.env`:
```env
# ---------- 1. Thông tin sinh viên ----------
STUDENT_NAME="Nguyen Van A"
STUDENT_ID="B21DCCN001"
STUDENT_CLASS="D21CNPM01"
STUDENT_EMAIL="vana@ptit.edu.vn"
GITHUB_USERNAME="nguyenvana-devops"

# ---------- 2. Thông số triển khai trên server chung ----------
EXAM_CODE="001"
APP_PORT="8088"
COMPOSE_PROJECT_NAME="nguyenvana-de001"

# ---------- 3. Cơ sở dữ liệu MySQL ----------
MYSQL_DATABASE="ptit_lab_db"
MYSQL_USER="app_user"
MYSQL_PASSWORD="Password123@"
MYSQL_ROOT_PASSWORD="RootPassword123@"
```
👉 Nhấn `Ctrl + S` để lưu, sau đó đóng Notepad.

---

### Bước 2.2: Kiểm tra cấu hình Dockerfile và docker-compose.yml

Dự án đã có sẵn file `Dockerfile` và `docker-compose.yml`. Bạn dùng PowerShell kiểm tra nội dung:

```powershell
# [PowerShell - máy cá nhân]
Get-Content Dockerfile -TotalCount 10
Get-Content docker-compose.yml -TotalCount 15
```

---

# BÀI 3: QUẢN LÝ MÃ NGUỒN VỚI GIT VÀ GITHUB

---

### Bước 3.1: Kiểm tra trạng thái Git cục bộ

```powershell
# [PowerShell - máy cá nhân]
cd D:\IT209\HN_K24_CNTT2_DEVOPS_Session14-main\devops-final-app-de001
git status
```

#### Màn hình hiển thị thực tế (nếu chưa có git):
```text
fatal: not a git repository (or any of the parent directories): .git
```

---

### Bước 3.2: Khởi tạo Git cục bộ và tạo commit đầu tiên trên nhánh `main`

```powershell
# [PowerShell - máy cá nhân]
# 1. Khởi tạo Git
git init

# 2. Cấu hình thông tin người commit
git config user.name "Nguyen Van A"
git config user.email "vana@ptit.edu.vn"

# 3. Đổi tên nhánh mặc định thành main
git branch -M main

# 4. Thêm toàn bộ file vào git staging
git add .

# 5. Commit phiên bản đầu tiên
git commit -m "feat: initial base project"
```

#### Màn hình hiển thị thực tế:
```text
Initialized empty Git repository in D:/IT209/HN_K24_CNTT2_DEVOPS_Session14-main/devops-final-app-de001/.git/
[main (root-commit) a1b2c3d] feat: initial base project
 22 files changed, 580 insertions(+)
 create mode 100644 .dockerignore
 create mode 100644 .github/workflows/ci-cd.yml
 create mode 100644 .gitignore
 create mode 100644 Dockerfile
 create mode 100644 README.md
 create mode 100644 build.gradle
 create mode 100644 docker-compose.yml
 create mode 100644 student.env
 ...
```

---

### Bước 3.3: Tạo Repository trên GitHub và đẩy mã nguồn lên

1. Mở trình duyệt web, vào: **https://github.com/new**
2. **Repository name:** Nhập `devops-exam-<ma-sinh-vien>` (ví dụ: `devops-exam-b21dccn001`).
3. Tùy chọn: Chọn **Public**.
4. **Lưu ý quan trọng:** Không tích chọn bất kỳ ô nào (Add README, .gitignore...).
5. Bấm nút **Create repository**.

Sau khi tạo xong, quay lại PowerShell gõ lệnh liên kết và push:

```powershell
# [PowerShell - máy cá nhân]
# Liên kết tới repository GitHub cá nhân vừa tạo
git remote add origin https://github.com/<GITHUB_USERNAME>/devops-exam-<MA_SINH_VIEN>.git

# Push mã nguồn lên nhánh main
git push -u origin main
```

#### Màn hình hiển thị thực tế:
```text
Enumerating objects: 26, done.
Counting objects: 100% (26/26), done.
Delta compression using up to 12 threads
Compressing objects: 100% (22/22), done.
Writing objects: 100% (26/26), 18.50 KiB | 3.70 MiB/s, done.
Total 26 (delta 2), reused 0 (delta 0), pack-reused 0
To https://github.com/nguyenvana-devops/devops-exam-b21dccn001.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

---

### Bước 3.4: Tạo nhánh chức năng `feature/employee-count`

```powershell
# [PowerShell - máy cá nhân]
git checkout -b feature/employee-count
git branch
```

#### Màn hình hiển thị thực tế:
```text
Switched to a new branch 'feature/employee-count'
* feature/employee-count
  main
```

---

### Bước 3.5: Mô phỏng và xử lý Merge Conflict (Theo yêu cầu 3.3 của đề thi)

#### Bước 3.5.1: Sửa file `README.md` trên nhánh `main`
```powershell
# [PowerShell - máy cá nhân]
# Chuyển về main
git checkout main

# Thêm một dòng ghi chú vào cuối file README.md
Add-Content -Path README.md -Value "`n<!-- Phiên bản cập nhật trên Main Branch -->"
git add README.md
git commit -m "docs: update readme note on main"
```

#### Bước 3.5.2: Sửa cùng dòng file `README.md` trên nhánh `feature/employee-count`
```powershell
# [PowerShell - máy cá nhân]
# Chuyển sang nhánh feature
git checkout feature/employee-count

# Thêm một dòng ghi chú khác vào cuối file README.md
Add-Content -Path README.md -Value "`n<!-- Phiên bản cập nhật trên Feature Branch -->"
git add README.md
git commit -m "docs: update readme note on feature"
```

#### Bước 3.5.3: Kích hoạt xung đột bằng lệnh Merge
```powershell
# [PowerShell - máy cá nhân]
# Đứng tại nhánh main để merge nhánh feature vào
git checkout main
git merge feature/employee-count
```

#### Màn hình hiển thị Conflict thực tế:
```text
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

#### Bước 3.5.4: Giải quyết Conflict
Mở file `README.md` bằng Notepad:
```powershell
# [PowerShell - máy cá nhân]
notepad README.md
```
Ở cuối file bạn sẽ thấy các dấu conflict:
```text
<<<<<<< HEAD
<!-- Phiên bản cập nhật trên Main Branch -->
=======
<!-- Phiên bản cập nhật trên Feature Branch -->
>>>>>>> feature/employee-count
```
👉 **Cách xử lý:** Xóa toàn bộ các dòng `<<<<<<< HEAD`, `=======`, `>>>>>>> feature/employee-count`, chỉ để lại một dòng thống nhất:
```text
<!-- Phiên bản cập nhật đã hòa nhập Main & Feature -->
```
👉 Nhấn `Ctrl + S` lưu lại, đóng Notepad.

#### Bước 3.5.5: Hoàn tất Merge và xem biểu đồ Git Graph
```powershell
# [PowerShell - máy cá nhân]
git add README.md
git commit -m "fix: resolve merge conflict between main and feature/employee-count"

# Hiển thị biểu đồ lịch sử commit
git log --oneline --graph --all
```

#### Màn hình hiển thị Git Graph thực tế:
```text
*   e7f8g9h (HEAD -> main) fix: resolve merge conflict between main and feature/employee-count
|\  
| * b3c4d5e (feature/employee-count) docs: update readme note on feature
* | a2b3c4d docs: update readme note on main
|/  
* a1b2c3d (origin/main) feat: initial base project
```
📷 **CHỤP ẢNH MINH CHỨNG BÀI 3:** Chụp màn hình terminal hiển thị biểu đồ Git Graph rẽ nhánh và gộp nhánh ở trên.

```powershell
# [PowerShell - máy cá nhân]
# Đẩy kết quả đã xử lý conflict lên GitHub main
git push origin main
```

---

# BÀI 4.1: PHÁT TRIỂN API ĐẾM TỔNG SỐ NHÂN VIÊN

---

### Bước 4.1.1: Bổ sung bảng `employees` vào `database/init.sql`

Mở file `database/init.sql` bằng Notepad:
```powershell
# [PowerShell - máy cá nhân]
notepad database/init.sql
```

Cuộn xuống cuối file và dán thêm đoạn mã SQL sau:
```sql
-- =============================================================================
-- Bổ sung bảng employees phục vụ API /api/employees/count (Bài 4.1)
-- =============================================================================
CREATE TABLE IF NOT EXISTS employees (
    id              BIGINT AUTO_INCREMENT PRIMARY KEY,
    full_name       VARCHAR(100) NOT NULL,
    email           VARCHAR(100) NOT NULL,
    department      VARCHAR(50) NOT NULL,
    position        VARCHAR(50) NOT NULL,
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

INSERT INTO employees (full_name, email, department, position) VALUES
    ('Nguyen Van A', 'vana@ptit.edu.vn', 'DevOps', 'Ky su DevOps'),
    ('Tran Thi B', 'thib@ptit.edu.vn', 'Cloud', 'Kien truc su Cloud'),
    ('Le Van C', 'vanc@ptit.edu.vn', 'Security', 'Chuyen gia Bao mat'),
    ('Pham Thi D', 'thid@ptit.edu.vn', 'AI', 'Nghien cuu vien AI'),
    ('Hoang Van E', 'vane@ptit.edu.vn', 'IT', 'Quan tri He thong');
```
👉 Nhấn `Ctrl + S` lưu lại, đóng Notepad.

---

### Bước 4.1.2: Tạo Entity `Employee.java`

Mở Notepad để tạo file mới:
```powershell
# [PowerShell - máy cá nhân]
notepad src/main/java/com/ptit/devops/lab/entity/Employee.java
```
*(Nếu Notepad hỏi "Do you want to create a new file?", bấm **Yes**).*

Dán toàn bộ mã nguồn sau vào:
```java
package com.ptit.devops.lab.entity;

import jakarta.persistence.*;

@Entity
@Table(name = "employees")
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "full_name", nullable = false)
    private String fullName;

    @Column(name = "email", nullable = false)
    private String email;

    @Column(name = "department", nullable = false)
    private String department;

    @Column(name = "position", nullable = false)
    private String position;

    public Employee() {
    }

    public Long getId() {
        return id;
    }

    public String getFullName() {
        return fullName;
    }

    public String getEmail() {
        return email;
    }

    public String getDepartment() {
        return department;
    }

    public String getPosition() {
        return position;
    }
}
```
👉 Nhấn `Ctrl + S` lưu lại, đóng Notepad.

---

### Bước 4.1.3: Tạo Repository `EmployeeRepository.java`

```powershell
# [PowerShell - máy cá nhân]
notepad src/main/java/com/ptit/devops/lab/repository/EmployeeRepository.java
```
*(Bấm **Yes** để tạo file mới).*

Dán toàn bộ mã nguồn sau vào:
```java
package com.ptit.devops.lab.repository;

import com.ptit.devops.lab.entity.Employee;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    // Phương thức count() được JpaRepository kế thừa tự động thực thi: SELECT COUNT(*) FROM employees;
}
```
👉 Nhấn `Ctrl + S` lưu lại, đóng Notepad.

---

### Bước 4.1.4: Tạo Controller `EmployeeController.java`

```powershell
# [PowerShell - máy cá nhân]
notepad src/main/java/com/ptit/devops/lab/controller/EmployeeController.java
```
*(Bấm **Yes** để tạo file mới).*

Dán toàn bộ mã nguồn sau vào:
```java
package com.ptit.devops.lab.controller;

import com.ptit.devops.lab.repository.EmployeeRepository;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class EmployeeController {

    private final EmployeeRepository employeeRepository;

    public EmployeeController(EmployeeRepository employeeRepository) {
        this.employeeRepository = employeeRepository;
    }

    /**
     * GET /api/employees/count
     * Trả về tổng số lượng nhân viên hiện có trong MySQL (kết quả mong đợi: 5)
     */
    @GetMapping("/employees/count")
    public long countEmployees() {
        return employeeRepository.count();
    }
}
```
👉 Nhấn `Ctrl + S` lưu lại, đóng Notepad.

---

### Bước 4.1.5: Biên dịch kiểm tra lỗi bằng Gradle Wrapper trên Windows

```powershell
# [PowerShell - máy cá nhân]
.\gradlew.bat bootJar -x test
```

#### Màn hình hiển thị thực tế:
```text
Starting a Gradle Daemon, 1 incompatible Daemon could not be reused...
> Task :compileJava
> Task :processResources
> Task :classes
> Task :bootJar

BUILD SUCCESSFUL in 8s
3 actionable tasks: 3 executed
```
*(Hiện `BUILD SUCCESSFUL` nghĩa là code không có lỗi cú pháp và file JAR đã được build thành công).*

---

### Bước 4.1.6: Commit code chức năng mới

```powershell
# [PowerShell - máy cá nhân]
git add .
git commit -m "feat: implement API GET /api/employees/count with 5 sample records"
```

---

# BÀI 4.2: TẠO SSH KEY VÀ CẤU HÌNH GITHUB SECRETS

---

### Bước 4.2.1: Tạo cặp khóa SSH Deploy trên PowerShell

```powershell
# [PowerShell - máy cá nhân]
ssh-keygen -t ed25519 -C "github-actions-deploy" -f "$HOME\.ssh\id_ed25519_deploy" -N '""'
```

#### Màn hình hiển thị thực tế:
```text
Generating public/private ed25519 key pair.
Your identification has been saved in C:\Users\HPC/.ssh/id_ed25519_deploy.
Your public key has been saved in C:\Users\HPC/.ssh/id_ed25519_deploy.pub.
The key fingerprint is:
SHA256:mN9kL8pQ2xR5wV7tZ1aB4cD6eF8gH0jK2lM4oP6rS8u github-actions-deploy
```

---

### Bước 4.2.2: Đưa Public Key lên VPS cho tài khoản `devops_student`

Xem nội dung Public Key trên PowerShell:
```powershell
# [PowerShell - máy cá nhân]
Get-Content "$HOME\.ssh\id_ed25519_deploy.pub"
```
#### Màn hình in ra chuỗi có dạng:
```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIExamplePublicKeyStringHere... github-actions-deploy
```
👉 Hãy bôi đen và sao chép toàn bộ dòng trên.

Bây giờ SSH vào VPS bằng tài khoản `devops_student`:
```powershell
# [PowerShell - máy cá nhân]
ssh devops_student@<VPS_IP>
```

Trên VPS, tạo file `authorized_keys` và dán key vào:
```bash
# [VPS Ubuntu - qua SSH]
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
```
👉 Dán chuỗi public key vừa copy vào.  
👉 Nhấn `Ctrl + O`, `Enter` để lưu, `Ctrl + X` để thoát.  
Phân quyền bảo mật:
```bash
# [VPS Ubuntu - qua SSH]
chmod 600 ~/.ssh/authorized_keys

# Thoát VPS về lại PowerShell
exit
```

---

### Bước 4.2.3: Lấy Private Key và cấu hình GitHub Secrets

Chạy lệnh tự động copy toàn bộ nội dung Private Key vào Clipboard:
```powershell
# [PowerShell - máy cá nhân]
Get-Content "$HOME\.ssh\id_ed25519_deploy" | Set-Clipboard
```

Mở trình duyệt web:
1. Vào repository GitHub của bạn: `https://github.com/<GITHUB_USERNAME>/devops-exam-<MA_SINH_VIEN>`
2. Bấm vào tab **Settings** (bánh răng).
3. Cột bên trái: Bấm **Secrets and variables** $\rightarrow$ chọn **Actions**.
4. Bấm nút màu xanh **New repository secret** để lần lượt tạo 3 Secret:

* **Secret 1:**
  * Name: `SERVER_HOST`
  * Secret: Nhập địa chỉ `<VPS_IP>` (ví dụ: `103.176.178.50`).
  * Bấm *Add secret*.
* **Secret 2:**
  * Name: `SERVER_USER`
  * Secret: Nhập `devops_student`
  * Bấm *Add secret*.
* **Secret 3:**
  * Name: `SERVER_SSH_KEY`
  * Secret: Nhấn `Ctrl + V` để dán nội dung Private Key đã copy ở trên (phải có đủ dòng `-----BEGIN OPENSSH PRIVATE KEY-----` và `-----END OPENSSH PRIVATE KEY-----`).
  * Bấm *Add secret*.

---

# BÀI 4.3: TRIỂN KHAI TỰ ĐỘNG VÀ KIỂM CHỨNG KẾT QUẢ

---

### Bước 4.3.1: Đẩy code lên nhánh `main` để kích hoạt CI/CD Pipeline

```powershell
# [PowerShell - máy cá nhân]
cd D:\IT209\HN_K24_CNTT2_DEVOPS_Session14-main\devops-final-app-de001
git push origin main
```

#### Màn hình hiển thị thực tế:
```text
Enumerating objects: 18, done.
Counting objects: 100% (18/18), done.
Delta compression using up to 12 threads
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 2.85 KiB | 2.85 MiB/s, done.
Total 14 (delta 4), reused 0 (delta 0), pack-reused 0
To https://github.com/nguyenvana-devops/devops-exam-b21dccn001.git
   e7f8g9h..f1g2h3j  main -> main
```

---

### Bước 4.3.2: Theo dõi quá trình chạy trên GitHub Actions

1. Mở trình duyệt web, vào tab **Actions** trong repository GitHub.
2. Bấm vào luồng chạy có tên commit `feat: implement API GET /api/employees/count...`.
3. Quan sát cả 2 Jobs chạy tuần tự:
   * **Job `Build & Test`**: Kiểm tra student.env $\rightarrow$ Build Docker $\rightarrow$ Smoke Test $\rightarrow$ Push image lên GHCR $\rightarrow$ **Success $\checkmark$**
   * **Job `Deploy lên server`**: SSH vào VPS $\rightarrow$ Pull image mới $\rightarrow$ Chạy `docker compose up -d` $\rightarrow$ **Success $\checkmark$**

📷 **CHỤP ẢNH MINH CHỨNG 01 (`01-actions.png`):** Chụp màn hình cả 2 jobs đều hiện dấu tích xanh lá cây hoàn tất.

---

### Bước 4.3.3: Kiểm tra trực tiếp trên VPS

Mở PowerShell SSH vào VPS để kiểm tra:

```powershell
# [PowerShell - máy cá nhân]
ssh devops_student@<VPS_IP>
```

Sau khi vào VPS:
```bash
# [VPS Ubuntu - qua SSH]
# 1. Chuyển vào thư mục do GitHub Actions vừa deploy
cd ~/devops-exam-<MA_SINH_VIEN>

# 2. Xem trạng thái các container
docker compose --env-file student.env ps

# 3. Xem log khởi động của ứng dụng
docker compose --env-file student.env logs --tail=40 app
```

#### Màn hình hiển thị thực tế của lệnh `docker compose ps`:
```text
NAME                     IMAGE                                                  COMMAND                  SERVICE   CREATED         STATUS                   PORTS
cuongnt-de001-app-1      ghcr.io/nguyenvana-devops/devops-exam-b21dccn001/app   "java -jar /app/app.…"   app       2 minutes ago   Up 2 minutes             8080/tcp
cuongnt-de001-mysql-1    mysql:8.0                                              "docker-entrypoint.s…"   mysql     2 minutes ago   Up 2 minutes (healthy)   3306/tcp
cuongnt-de001-nginx-1    nginx:1.27-alpine                                      "/docker-entrypoint.…"   nginx     2 minutes ago   Up 2 minutes             0.0.0.0:8088->80/tcp
```
*(Cả 3 service `app`, `mysql`, `nginx` đều `Up`).*

#### Màn hình hiển thị log của Spring Boot:
```text
2026-10-16T02:50:12.123Z  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port 8080 (http) with context path '/'
2026-10-16T02:50:12.150Z  INFO 1 --- [           main] c.p.d.l.DevopsApplication                : Started DevopsApplication in 4.512 seconds (process running for 5.12)
```

📷 **CHỤP ẢNH MINH CHỨNG 03 (`03-compose-ps.png`):** Chụp màn hình terminal hiển thị kết quả lệnh `docker compose ps`.

Thoát VPS:
```bash
# [VPS Ubuntu - qua SSH]
exit
```

---

### Bước 4.3.4: Kiểm chứng API từ bên ngoài bằng PowerShell

Mở cửa sổ **Windows PowerShell** trên máy tính cá nhân của bạn và gọi API kiểm tra:

```powershell
# [PowerShell - máy cá nhân]
# 1. Gọi API danh sách phòng Lab sẵn có:
curl.exe -i http://<VPS_IP>:<APP_PORT>/api/labs

# 2. Gọi API đếm nhân viên mới viết (Bài 4.1):
curl.exe -i http://<VPS_IP>:<APP_PORT>/api/employees/count
```

#### Màn hình hiển thị thực tế khi gọi API đếm nhân viên:
```text
PS D:\IT209\HN_K24_CNTT2_DEVOPS_Session14-main> curl.exe -i http://103.176.178.50:8088/api/employees/count
HTTP/1.1 200 OK
Server: nginx/1.27.0
Date: Thu, 16 Oct 2026 02:52:00 GMT
Content-Type: application/json
Content-Length: 1
Connection: keep-alive

5
```
*(Kết quả trả về mã `HTTP/1.1 200 OK` và nội dung là số `5` khớp hoàn toàn với 5 bản ghi trong MySQL).*

📷 **CHỤP ẢNH MINH CHỨNG 04 (`04-website.png`):** Chụp màn hình PowerShell hiển thị lệnh `curl.exe` và kết quả số `5` trả về từ địa chỉ IP VPS thực tế.

---

## TỔNG KẾT CÁC LỆNH CẦN NHỚ NHẤT KHI THI

| Tình huống thao tác | Nơi chạy lệnh | Lệnh thực thi |
|---|---|---|
| Đăng nhập vào VPS | PowerShell | `ssh <user>@<VPS_IP>` |
| Thoát khỏi VPS về lại máy Windows | VPS Terminal | `exit` |
| Chuyển sang user khác trên VPS | VPS Terminal | `su - <username>` |
| Xem quyền và nhóm tài khoản | VPS Terminal | `id` |
| Khởi động hệ thống container | VPS Terminal | `docker compose --env-file student.env up -d` |
| Kiểm tra trạng thái container | VPS Terminal | `docker compose --env-file student.env ps` |
| Xem log container ứng dụng | VPS Terminal | `docker compose --env-file student.env logs -f app` |
| Dừng và xóa container | VPS Terminal | `docker compose --env-file student.env down` |
| Đẩy code lên GitHub | PowerShell | `git push origin main` |
| Xem nhánh và xung đột Git | PowerShell | `git log --oneline --graph --all` |
| Kiểm tra API từ máy cá nhân | PowerShell | `curl.exe -i http://<VPS_IP>:<APP_PORT>/api/employees/count` |
