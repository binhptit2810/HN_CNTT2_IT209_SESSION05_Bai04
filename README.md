# Bài 4: Mô phỏng quy trình Hotfix & Gitflow thực tế

## 1. Mục tiêu

- Áp dụng mô hình phân nhánh Gitflow trong quản lý vòng đời phần mềm.
- Thực hành tạo và xử lý nhánh Hotfix từ phiên bản production.
- Gộp bản sửa lỗi vào nhánh `main` và tạo tag phiên bản mới.
- Đồng bộ bản sửa lỗi từ `hotfix` về nhánh `develop` để tránh lỗi quay trở lại trong các phiên bản sau.

---

## 2. Mô hình Gitflow sử dụng

Quy trình được thực hiện theo sơ đồ:

```text
                         hotfix/v1.0.1
                        /            \
                       /              \
main ----------------●----------------●----------------
      v1.0.0                           v1.0.1
                                         \
                                          \
develop ----------------●------------------●

Trong đó:

main: chứa phiên bản production ổn định.
develop: chứa mã nguồn đang phát triển cho phiên bản tiếp theo.
hotfix/v1.0.1: nhánh sửa lỗi khẩn cấp, được tách trực tiếp từ main.
v1.0.1: tag đánh dấu phiên bản đã sửa lỗi và chuẩn bị release.
3. Tạo nhánh Hotfix

Đầu tiên kiểm tra các branch hiện có:

git branch

Chuyển sang nhánh main:

git checkout main

Tạo nhánh hotfix trực tiếp từ main:

git checkout -b hotfix/v1.0.1

Kiểm tra branch:

git branch

Kết quả mong đợi:

* hotfix/v1.0.1
  develop
  main
4. Thực hiện sửa lỗi

Trên nhánh hotfix/v1.0.1, thực hiện sửa lỗi nghiêm trọng liên quan đến việc lộ dữ liệu người dùng.

Ví dụ cập nhật file:

security.txt

Sau khi sửa lỗi, kiểm tra thay đổi:

git status

Thêm file và tạo commit:

git add .
git commit -m "fix: patch user data exposure"

Commit này chính là bản sửa lỗi khẩn cấp của phiên bản v1.0.1.

5. Gộp Hotfix vào main

Chuyển về nhánh main:

git checkout main

Gộp nhánh hotfix:

git merge hotfix/v1.0.1

Sau khi merge thành công, main đã chứa bản sửa lỗi khẩn cấp.

6. Tạo tag phiên bản v1.0.1

Tạo tag cho phiên bản mới:

git tag -a v1.0.1 -m "Release Hotfix 1.0.1"

Kiểm tra danh sách tag:

git tag

Kết quả mong đợi:

v1.0.1

Tag v1.0.1 đánh dấu phiên bản production đã được sửa lỗi.

7. Đồng bộ Hotfix về develop

Sau khi cập nhật main, cần đưa bản sửa lỗi về nhánh develop.

Chuyển sang develop:

git checkout develop

Merge nhánh hotfix:

git merge hotfix/v1.0.1

Sau bước này, nhánh develop cũng chứa bản sửa lỗi bảo mật.

Việc merge ngược này rất quan trọng vì các tính năng đang phát triển trong develop phải tiếp tục kế thừa bản sửa lỗi từ production.

8. Xóa nhánh Hotfix

Sau khi đã merge vào cả main và develop, có thể xóa nhánh hotfix cục bộ:

git branch -d hotfix/v1.0.1

Kiểm tra lại:

git branch

Kết quả mong đợi:

* develop
  main
9. Kiểm tra các branch và tag

Kiểm tra toàn bộ branch:

git branch -a

Kiểm tra tag:

git tag

Kết quả cần có:

v1.0.1
10. Kiểm tra lịch sử Git

Sử dụng lệnh:

git log --graph --oneline --all

Sơ đồ lịch sử có thể có dạng:

*   <commit> Merge branch 'hotfix/v1.0.1' into develop
|\
| * <commit> fix: patch user data exposure
|/
*   <commit> Merge branch 'hotfix/v1.0.1' into main
|\
| * <commit> fix: patch user data exposure
|/
* <commit> phiên bản v1.0.0
|
* <commit> các commit phát triển của develop

Trong trường hợp Git sử dụng fast-forward, sơ đồ có thể khác nhưng bản sửa lỗi vẫn phải xuất hiện trên cả main và develop.

11. Kết quả đạt được

Sau khi hoàn thành bài thực hành:

Nhánh hotfix/v1.0.1 được tạo trực tiếp từ main.
Bản sửa lỗi được commit trên nhánh Hotfix.
Nhánh Hotfix được merge vào main.
Tag v1.0.1 được tạo cho phiên bản production mới.
Nhánh Hotfix được merge ngược vào develop.
Nhánh develop đã được đồng bộ với bản sửa lỗi của production.
Nhánh Hotfix được xóa sau khi hoàn tất quy trình.
12. Sơ đồ Gitflow cuối cùng
                         hotfix/v1.0.1
                        /            \
                       /              \
                      /                \
main ---- v1.0.0 ----●----------------●
                                      │
                                   tag v1.0.1
                                      │
                                      ▼
develop -----------------------------●

Quy trình trên đảm bảo bản sửa lỗi khẩn cấp được triển khai ngay trên main nhưng đồng thời cũng được đồng bộ về develop, tránh tình trạng lỗi cũ xuất hiện lại trong phiên bản tương lai.

13. Các lệnh chính đã sử dụng
git checkout main
git checkout -b hotfix/v1.0.1

git add .
git commit -m "fix: patch user data exposure"

git checkout main
git merge hotfix/v1.0.1
git tag -a v1.0.1 -m "Release Hotfix 1.0.1"

git checkout develop
git merge hotfix/v1.0.1

git branch -d hotfix/v1.0.1

git branch -a
git tag
git log --graph --oneline --all
```
