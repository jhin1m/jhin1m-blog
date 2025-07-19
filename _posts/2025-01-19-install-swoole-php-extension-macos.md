---
title: Cài đặt Extension Swoole cho Herd trên macOS
date: 2025-07-19
categories: [PHP, Laravel, MacOS]
tags: [php, swoole, herd, extension, install, mac]
---

Tình trạng là tôi đang dùng **Herd trên macOS** và muốn cài **Swoole** để tăng tốc cho Laravel Octane, nhưng đọc tài liệu trên Herd thì báo không support thư viện này, sau 1 hồi vật lộn và research bằng mấy con AI thì cuối cùng cũng xong.

 Bài viết này sẽ giúp bạn thiết lập mọi thứ một cách chuẩn chỉnh — áp dụng cho cả máy **Intel** lẫn **Apple Silicon (M1/M2)**.

---

## 1. Cài PHP từ Homebrew (để dùng `pecl`)

Herd không tích hợp sẵn `pecl`, vì vậy bạn cần cài PHP thông qua Homebrew để sử dụng lệnh này.

Ví dụ: mình đang dùng PHP 8.3, nên sẽ chạy:

```bash
brew install php@8.3
```

Sau khi cài xong, bạn sẽ có thể chạy `pecl` tại:

* Apple Silicon:
  `/opt/homebrew/opt/php@8.3/bin/pecl`

* Intel:
  `/usr/local/opt/php@8.3/bin/pecl`

---

## 2. Cài thêm thư viện `pcre2`

Đây là thư viện cần thiết để biên dịch Swoole:

```bash
brew install pcre2
```

---

## 3. Xuất biến môi trường (để tránh lỗi build)

Một số lỗi khi cài Swoole sẽ biến mất nếu bạn thêm các biến môi trường sau:

Cái này mình hỏi ChatGPT.

```bash
export CPPFLAGS="-I/opt/homebrew/include"
export LDFLAGS="-L/opt/homebrew/lib"
```

Nếu muốn dùng luôn về sau, bạn có thể thêm vào `~/.zshrc` hoặc `~/.bash_profile`.

---

## 4. Cài đặt Swoole bằng `pecl`

Chạy lệnh cài:

```bash
/opt/homebrew/opt/php@8.3/bin/pecl install swoole
```

Cái này dùng PHP của homebrew để cài nhé.

> Nếu bạn dùng Intel Mac, thay đường dẫn thành `/usr/local/opt/php@8.3/bin/pecl`

---

## 5. Tìm đường dẫn extension `.so` của Swoole sau khi cài

Sau khi cài xong, chạy lệnh sau để tìm file extension `.so`:

```bash
find /opt/homebrew/lib/php -name swoole.so
```

Ví dụ kết quả:

```
/opt/homebrew/lib/php/pecl/20230831/swoole.so
```

### Ghi chú nhanh:

| Kiến trúc     | Đường dẫn extension `.so`                  |
| ------------- | ------------------------------------------ |
| Apple Silicon | `/opt/homebrew/lib/php/pecl/.../swoole.so` |
| Intel         | `/usr/local/lib/php/pecl/.../swoole.so`    |

---

## 6. Thêm Swoole vào `php.ini` của Herd

Herd sử dụng `php.ini` riêng, không dùng chung với PHP Homebrew. Với PHP 8.3, đường dẫn file là:

```
~/Library/Application Support/Herd/config/php/8.3/php.ini
```

Mở file này và thêm dòng:

```ini
extension=/opt/homebrew/lib/php/pecl/XXXXXXXX/swoole.so
```

> Hãy thay `XXXXXXXX` bằng đúng thư mục mà bạn tìm được ở bước 5.

---

## 7. Kiểm tra Swoole đã hoạt động chưa

Bạn có thể kiểm tra bằng lệnh sau:

```bash
php -m | grep swoole
php -r "echo swoole_version();"
```

Kết quả mong đợi:

```
swoole
6.0.2
```

---

Vậy là xong! Giờ bạn đã có Swoole hoạt động ngon lành cùng Herd trên macOS.
