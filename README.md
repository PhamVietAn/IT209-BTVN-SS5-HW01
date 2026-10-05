# Bài 1: Khôi phục commit đã mất bằng Git Reflog

## 1. Chuẩn bị repository thử nghiệm

Khởi tạo repository và cấu hình người thực hiện:

```powershell
git init -b main
git config user.name "CrouAn244"
git config user.email "crouan244@gmail.com"
```

Tạo commit nền để có thể mô phỏng thao tác lùi một commit:

```powershell
Set-Content -Path project.txt -Value "Du an Git Reflog"
git add project.txt
git commit -m "Khoi tao du an"
```

Tạo tệp chứa tính năng quan trọng và commit thay đổi:

```powershell
Set-Content -Path feature.txt -Value "Day la tinh nang quan trong"
git add feature.txt
git commit -m "Them tinh nang quan trong"
```

Kiểm tra lịch sử trước khi xảy ra sự cố:

```powershell
git log --oneline
```

Kết quả:

```text
4f8c2a7 (HEAD -> main) Them tinh nang quan trong
19bd601 Khoi tao du an
```

Commit `4f8c2a7` đang chứa tệp `feature.txt` và là commit cần được bảo vệ.

## 2. Giả lập tình huống mất commit

Thực hiện lệnh lùi nhánh hiện tại về commit trước đó:

```powershell
git reset --hard HEAD~1
```

Kết quả từ Git:

```text
HEAD is now at 19bd601 Khoi tao du an
```

Kiểm tra lại lịch sử:

```powershell
git log --oneline
```

Kết quả:

```text
19bd601 (HEAD -> main) Khoi tao du an
```

Commit `Them tinh nang quan trong` không còn xuất hiện trong `git log`. Tệp `feature.txt` cũng đã biến mất khỏi thư mục làm việc.

## 3. Tra cứu commit bằng Reflog

Sử dụng Reflog để xem các vị trí mà `HEAD` đã từng trỏ tới:

```powershell
git reflog
```

Kết quả:

```text
19bd601 (HEAD -> main) HEAD@{0}: reset: moving to HEAD~1
4f8c2a7 HEAD@{1}: commit: Them tinh nang quan trong
19bd601 (HEAD -> main) HEAD@{2}: commit (initial): Khoi tao du an
```

Dòng `HEAD@{1}` cho biết commit bị mất có mã rút gọn là `4f8c2a7`. Commit vẫn còn trong cơ sở dữ liệu cục bộ của Git dù không còn được nhánh `main` tham chiếu.

## 4. Khôi phục commit bị mất

Đưa nhánh hiện tại trở lại đúng commit tìm được trong Reflog:

```powershell
git reset --hard 4f8c2a7
```

Kết quả:

```text
HEAD is now at 4f8c2a7 Them tinh nang quan trong
```

Lệnh trên khôi phục cả lịch sử commit, vùng staging và thư mục làm việc về trạng thái của commit `4f8c2a7`. Không cần viết lại nội dung tệp bằng tay.

## 5. Kiểm tra kết quả

Kiểm tra lịch sử commit:

```powershell
git log --oneline
```

Kết quả:

```text
4f8c2a7 (HEAD -> main) Them tinh nang quan trong
19bd601 Khoi tao du an
```

Kiểm tra nội dung tệp đã được khôi phục:

```powershell
Get-Content feature.txt
```

Kết quả:

```text
Day la tinh nang quan trong
```

Kiểm tra trạng thái repository:

```powershell
git status
```

Kết quả:

```text
On branch main
nothing to commit, working tree clean
```

Commit `Them tinh nang quan trong` đã xuất hiện trở lại trong lịch sử và tệp `feature.txt` được phục hồi đúng nội dung ban đầu.
