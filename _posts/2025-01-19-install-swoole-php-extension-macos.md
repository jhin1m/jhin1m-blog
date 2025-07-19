---
title: Cài đặt Extension Swoole cho Herd trên macOS
date: 2025-07-19
categories: [PHP]
tags: [php]  
author: 
  name: jhin1m
  link: https://github.com/jhin1m
---

Dưới đây là hướng dẫn **đầy đủ và chuẩn xác** để **cài đặt extension Swoole cho PHP của Herd trên macOS**, áp dụng cho máy dùng chip Intel **hoặc Apple Silicon (M1/M2)**.

----------

# 🧠 Hướng dẫn cài đặt **Swoole cho Herd** trên macOS (ví dụ PHP 8.3)

----------

## ✅ 1. Cài PHP  từ Homebrew (bắt buộc để dùng `pecl`)

Herd không đi kèm `pecl`, nên bạn cần PHP Homebrew để sử dụng:

Tôi sử dụng PHP 8.3 nên cài phiên bản php 8.3 tương ứng với homebrew, mục đích để cài extension khớp với php của Herd

```bash
brew install php@8.3
```

Sau khi cài xong, bạn sẽ có `pecl` tại:

```bash
/opt/homebrew/opt/php@8.3/bin/pecl  # (Apple Silicon)
# hoặc
/usr/local/opt/php@8.3/bin/pecl     # (Intel)

```

----------

## ✅ 2. Cài `pcre2` – thư viện bắt buộc để build Swoole

```bash
brew install pcre2
```

----------

## ✅ 3. Xuất biến môi trường để tránh lỗi build

Vì lỗi linh tinh khi cài swoole, sau 1 hồi google thì tìm được cách này

```bash
export CPPFLAGS="-I/opt/homebrew/include"
export LDFLAGS="-L/opt/homebrew/lib"

```

> Bạn có thể thêm 2 dòng này vào `~/.zshrc` để dùng mãi mãi.

----------

## ✅ 4. Cài đặt Swoole bằng `pecl`

```bash
/opt/homebrew/opt/php@8.3/bin/pecl install swoole
```

> Nếu đang dùng Intel Mac, thay đường dẫn bằng `/usr/local/opt/php@8.3/bin/pecl`

----------

## ✅ 5. Xác định đường dẫn `.so` sau khi cài

Chạy lệnh này để tìm đường dẫn swoole

 `find /opt/homebrew/lib/php -name swoole.so`

Sẽ có kết quả sau

`/opt/homebrew/lib/php/pecl/20230831/swoole.so` 

### 📍 Ghi chú:

Kiến trúc máy

Đường dẫn `.so`

Apple Silicon (M1/M2)

`/opt/homebrew/lib/php/pecl/.../swoole.so`

Intel Mac

`/usr/local/lib/php/pecl/.../swoole.so`

----------

## ✅ 6. Kích hoạt Swoole trong PHP của Herd

Herd sử dụng file `php.ini` riêng, không dùng chung với Homebrew.

### 📄 File `php.ini` của Herd nằm ở:

Với PHP 8.3 của Herd tôi đang sử dụng

```
~/Library/Application Support/Herd/config/php/8.3/php.ini

```

### ➕ Mở và thêm dòng sau:

```ini
extension=/opt/homebrew/lib/php/pecl/XXXXXX/swoole.so
```

> Điều chỉnh `XXXXXXXX` theo đúng thư mục version của Swoole được tạo trên máy bạn.

----------

## ✅ 7. Kiểm tra hoạt động

```bash
php -m | grep swoole
php -r "echo swoole_version();"
```

Kết quả ví dụ:

```
swoole
6.0.2
```
----------