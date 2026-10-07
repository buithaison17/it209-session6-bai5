# Bài 5 - Thiết kế và Cấu hình Systemd Service cho ứng dụng Java

## 1. Mục tiêu

Thiết kế và cấu hình một Systemd Service để hệ điều hành quản lý vòng đời của ứng dụng.

Các yêu cầu thực hiện:

- Tạo tài khoản hệ thống `java-runner`.
- Tài khoản không được đăng nhập shell trực tiếp.
- Tạo Systemd Service tại:
  `/etc/systemd/system/java-app.service`
- Service chạy dưới quyền `java-runner`.
- Tự động restart khi tiến trình bị lỗi.
- Thời gian chờ trước khi restart là 10 giây.
- Service tự động khởi động cùng hệ thống.
- Kiểm tra trạng thái và log của service.

---

## 2. Tạo tài khoản hệ thống

Tạo user `java-runner` bằng lệnh:

```bash
sudo useradd -r -s /usr/sbin/nologin java-runner

root@vps-2:~# getent passwd java-runner
java-runner:x:999:987::/home/java-runner:/usr/sbin/nologin
root@vps-2:~# cat /etc/systemd/system/java-app.service
[Unit]
Description=Java Application Service
After=network.target

[Service]
Type=simple
User=java-runner
ExecStart=/usr/bin/sleep 1000
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
root@vps-2:~# cat /etc/systemd/system/java-app.service
[Unit]
Description=Java Application Service
After=network.target

[Service]
Type=simple
User=java-runner
ExecStart=/usr/bin/sleep 1000
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
root@vps-2:~# systemctl show java-app.service -p User -p Restart -p RestartUSec
Restart=on-failure
RestartUSec=10s
User=java-runner
root@vps-2:~# ps -eo user,pid,cmd | grep '[s]leep 1000'
java-ru+    1529 /usr/bin/sleep 1000
root@vps-2:~# sudo journalctl -u java-app.service -n 50 --no-pager
Oct 06 13:07:56 vps-2 systemd[1]: Started java-app.service - Java Application Service.
root@vps-2:~#