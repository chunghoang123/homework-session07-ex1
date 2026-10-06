# Bài 1: Quản lý người dùng giới hạn và SFTP trên Windows

## 1. Thông tin thực hiện
- Server: Ubuntu 24.04.5 LTS (100.115.192.85, Tailscale)
- Tài khoản SFTP: `sftp-user` / mật khẩu lab `SftpStrong2026XyZ9`
- Người thực hiện: Hoang Van Chung - CNTT1

## 2. Lệnh đã chạy trên server (bằng user chunghoang có sudo)
```bash
sudo useradd -m -s /bin/bash sftp-user
echo sftp-user:SftpStrong2026XyZ9 | sudo chpasswd
sudo mkdir -p /var/log/app-backup/
sudo touch /var/log/app-backup/backup-check.log
echo 'Backup status: SUCCESS at 2026-10-06 07:03 UTC' | sudo tee /var/log/app-backup/backup-check.log
sudo chown -R root:sftp-user /var/log/app-backup
sudo chmod 750 /var/log/app-backup
sudo chmod 640 /var/log/app-backup/backup-check.log
```

## 3. Kết quả kiểm tra trên server
```
$ id sftp-user
uid=1001(sftp-user) gid=1001(sftp-user) groups=1001(sftp-user)
$ groups sftp-user
sftp-user : sftp-user
# -> sftp-user KHONG thuoc nhom sudo (dat yeu cau)

$ sudo ls -l /var/log/app-backup/backup-check.log
-rw-r----- 1 root sftp-user 47 ... /var/log/app-backup/backup-check.log
$ sudo ls -ld /var/log/app-backup
drwxr-x--- 2 root sftp-user ... /var/log/app-backup

$ sudo cat /var/log/app-backup/backup-check.log
Backup status: SUCCESS at 2026-10-06 07:03 UTC
```

## 4. Truyền file về Windows bằng SFTP Client (Bitvise / WinSCP)
1. Mở Bitvise SSH Client (hoặc WinSCP).
2. Host: `100.115.192.85`, Port `22`, Username `sftp-user`, Method `password`, nhập mật khẩu lab ở trên.
3. Nhấn Log in. Kết nối thành công mở New SFTP Window.
4. Khung Remote files vào `/var/log/app-backup/`, kéo `backup-check.log` sang thư mục local.
5. Ảnh chụp màn hình kết nối thành công + kéo file: xem file `sftp-screenshot.png` (đính kèm khi nộp).
6. Kiểm chứng bằng dòng lệnh tương đương (đã chạy và đối chiếu hash):
```powershell
scp sftp-user@100.115.192.85:/var/log/app-backup/backup-check.log ./backup-check.log
```

## 5. Đối chiếu nội dung
- Server SHA256: `d82b8c4355460027c8052c8cf19b45c4e956f9f2f9428c2c7524f1712994a05f`
- Windows SHA256: `D82B8C4355460027C8052C8CF19B45C4E956F9F2F9428C2C7524F1712994A05F`
- Hai hash trùng nhau -> nội dung khớp 100%. File tải về: `backup-check.log` trong thư mục này.
