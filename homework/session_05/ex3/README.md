## 1. Mục tiêu

* Hiểu sự khác nhau giữa Merge và Rebase.
* Thực hành xử lý nhiều xung đột trong quá trình Rebase.
* Đưa lịch sử của nhánh `feature-api` lên trên phiên bản mới nhất của `main`.
* Đảm bảo lịch sử Git tuyến tính và không tạo Merge Commit.
* Bảo toàn các thay đổi hợp lệ từ cả `main` và `feature-api`.

## 2. Cấu trúc ban đầu

Repository:

`PTIT_CNTT2-K24_IT209_Bai3_Session05`

Các nhánh được sử dụng:

* `main`
* `feature-api`

File chính được sử dụng để tạo xung đột:

```text
config.json
```

Nội dung ban đầu:

```json
{
  "port": 8080,
  "debug": false
}
```

Commit ban đầu:

```text
5c18296 init config
```

## 3. Các commit trên nhánh feature-api

Sau khi tạo nhánh `feature-api`, thực hiện hai commit:

### Commit 1 — thay đổi port

```text
f306f0e feat: change port
```

Thay đổi:

```json
{
  "port": 9000,
  "debug": false
}
```

### Commit 2 — bật debug

```text
62a81b2 feat: enable debug
```

Thay đổi:

```json
{
  "port": 9000,
  "debug": true
}
```

## 4. Các commit mới trên nhánh main

Sau khi quay lại `main`, tạo hai commit.

### Commit 1 — cập nhật port

```text
ff7420e update port on main
```

Thay đổi:

```json
{
  "port": 8081,
  "debug": false
}
```

### Commit 2 — thêm cấu hình production

```text
9568afe add production config
```

Thay đổi:

```json
{
  "port": 8081,
  "debug": "production",
  "env": "production"
}
```

## 5. Cấu trúc branch trước khi Rebase

Trước khi thực hiện Rebase, lịch sử có dạng:

```text
* 9568afe (HEAD -> main) add production config
* ff7420e update port on main
| * 62a81b2 (feature-api) feat: enable debug
| * f306f0e feat: change port
|/
* 5c18296 (origin/main) init config
```

Hai nhánh cùng phát triển từ commit `5c18296`.

## 6. Thực hiện Rebase

Chuyển sang nhánh `feature-api`:

```bash
git checkout feature-api
```

Sau đó thực hiện:

```bash
git rebase main
```

Git bắt đầu đưa từng commit của `feature-api` lên trên các commit mới nhất của `main`.

## 7. Conflict 1

### Nguyên nhân

Conflict đầu tiên xảy ra khi Git cố áp dụng:

```text
f306f0e feat: change port
```

Hai nhánh cùng thay đổi `config.json`.

`main` có:

```json
{
  "port": 8081,
  "debug": "production",
  "env": "production"
}
```

Trong khi `feature-api` có:

```json
{
  "port": 9000,
  "debug": false
}
```

### Cách giải quyết

Giữ thay đổi `port: 9000` từ `feature-api`, đồng thời giữ các cấu hình production từ `main`.

Kết quả:

```json
{
  "port": 9000,
  "debug": "production",
  "env": "production"
}
```

Sau đó đánh dấu conflict đã được giải quyết:

```bash
git add config.json
```

và tiếp tục Rebase:

```bash
git rebase --continue
```

Commit được tạo lại sau Rebase:

```text
19e0f00 feat: change port
```

---

## 8. Conflict 2

### Nguyên nhân

Conflict thứ hai xảy ra khi Git cố áp dụng:

```text
62a81b2 feat: enable debug
```

Tại thời điểm này, `main` có:

```json
{
  "port": 9000,
  "debug": "production",
  "env": "production"
}
```

Trong khi commit `feature-api` muốn thay đổi:

```json
{
  "port": 9000,
  "debug": true
}
```

Hai thay đổi cùng tác động đến trường `debug`.

### Cách giải quyết

Giữ:

* `port: 9000` từ feature.
* `debug: true` từ feature.
* `env: "production"` từ main.

Kết quả:

```json
{
  "port": 9000,
  "debug": true,
  "env": "production"
}
```

Sau đó:

```bash
git add config.json
git rebase --continue
```

Commit được tạo lại sau Rebase:

```text
5b63a6f feat: enable debug
```

---

## 9. Kết quả sau khi Rebase

Sau khi hoàn thành Rebase, kiểm tra:

```bash
git status
```

Kết quả:

```text
On branch feature-api
nothing to commit, working tree clean
```

Điều này cho thấy quá trình Rebase đã hoàn thành và working tree sạch.

---

## 10. Lịch sử Git sau Rebase

Kiểm tra bằng:

```bash
git log --graph --oneline --decorate --all
```

Kết quả:

```text
* 5b63a6f (HEAD -> feature-api) feat: enable debug
* 19e0f00 feat: change port
* 9568afe (main) add production config
* ff7420e update port on main
* 5c18296 (origin/main) init config
```

Lịch sử đã trở thành một đường thẳng.

Không xuất hiện Merge Commit.

Các commit của `feature-api` được đặt trực tiếp phía trên các commit mới nhất của `main`.

---

## 11. Nội dung config.json cuối cùng

Sau khi xử lý toàn bộ conflict:

```json
{
  "port": 9000,
  "debug": true,
  "env": "production"
}
```

Các thay đổi quan trọng từ cả hai nhánh đều được giữ lại.

---

## 12. Kết luận

Qua bài tập, đã thực hiện thành công quá trình Rebase nhánh `feature-api` lên `main` và xử lý hai xung đột phát sinh trong cùng một file `config.json`.

Kết quả đạt được:

* Xử lý thành công Conflict 1.
* Xử lý thành công Conflict 2.
* Bảo toàn các thay đổi cần thiết từ cả `main` và `feature-api`.
* Rebase thành công.
* Lịch sử Git tuyến tính.
* Không tạo Merge Commit.
* Working tree sạch sau khi hoàn thành.

Lịch sử cuối cùng:

```text
* 5b63a6f feat: enable debug
* 19e0f00 feat: change port
* 9568afe add production config
* ff7420e update port on main
* 5c18296 init config
```
