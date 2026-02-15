# SSH Cheatsheet

## اتصال پایه
```bash
ssh user@hostname                       # اتصال به هاست راه دور
ssh user@hostname -p 2222               # اتصال روی پورت سفارشی
ssh -i /path/to/key user@hostname       # اتصال با کلید خاص
ssh user@hostname 'command'             # اجرای دستور روی هاست راه دور
```

## کلیدهای SSH
```bash
ssh-keygen                              # ساخت جفت کلید SSH جدید
ssh-keygen -t rsa -b 4096               # ساخت کلید RSA با 4096 بیت
ssh-keygen -t ed25519                   # ساخت کلید Ed25519 (توصیه شده)
ssh-keygen -t rsa -b 4096 -C "email@example.com" # با کامنت
ssh-copy-id user@hostname               # کپی کلید عمومی به هاست راه دور
ssh-copy-id -i ~/.ssh/id_rsa.pub user@hostname # کپی کلید خاص
ssh-add ~/.ssh/id_rsa                   # افزودن کلید به SSH agent
ssh-add -l                              # لیست کلیدها در SSH agent
ssh-add -D                              # حذف تمام کلیدها از agent
```

## فایل پیکربندی SSH (~/.ssh/config)
```
Host myserver
    HostName example.com
    User myuser
    Port 2222
    IdentityFile ~/.ssh/id_rsa
    
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

سپس اتصال با: `ssh myserver`

## انتقال فایل (SCP)
```bash
scp file.txt user@host:/path/          # کپی فایل به سرور
scp user@host:/path/file.txt .         # کپی فایل از سرور
scp -r directory user@host:/path/      # کپی پوشه به صورت بازگشتی
scp -P 2222 file.txt user@host:/path/  # استفاده از پورت سفارشی
scp -i ~/.ssh/key file.txt user@host:/ # استفاده از کلید خاص
```

## انتقال فایل (SFTP)
```bash
sftp user@hostname                      # شروع جلسه SFTP
```

دستورات SFTP:
```
put file.txt                            # آپلود فایل
get file.txt                            # دانلود فایل
put -r directory                        # آپلود پوشه
get -r directory                        # دانلود پوشه
ls                                      # لیست فایل‌های راه دور
lls                                     # لیست فایل‌های محلی
cd /path                                # تغییر پوشه راه دور
lcd /path                               # تغییر پوشه محلی
pwd                                     # نمایش پوشه راه دور
lpwd                                    # نمایش پوشه محلی
mkdir dirname                           # ایجاد پوشه راه دور
exit                                    # خروج از SFTP
```

## انتقال پورت (Port Forwarding)
```bash
# انتقال پورت محلی (دسترسی به سرویس راه دور از طریق لوکال)
ssh -L 8080:localhost:80 user@host      # انتقال 8080 محلی به 80 راه دور

# انتقال پورت راه دور (قابل دسترس کردن سرویس محلی از راه دور)
ssh -R 8080:localhost:80 user@host      # انتقال 8080 راه دور به 80 محلی

# انتقال پویا (پروکسی SOCKS)
ssh -D 1080 user@host                   # ایجاد پروکسی SOCKS روی پورت 1080
```

## تونل SSH
```bash
ssh -L 3306:db.internal:3306 user@gateway # دسترسی به دیتابیس داخلی از طریق gateway
ssh -N -L 8080:localhost:80 user@host     # N-: بدون اجرای دستور
ssh -f -N -L 8080:localhost:80 user@host  # f-: اجرا در پس‌زمینه
```

## انتقال X11
```bash
ssh -X user@hostname                    # فعال‌سازی انتقال X11
ssh -Y user@hostname                    # فعال‌سازی انتقال X11 قابل اعتماد
```

## SSH Agent
```bash
eval "$(ssh-agent -s)"                  # شروع SSH agent
ssh-add                                 # افزودن کلیدهای پیش‌فرض
ssh-add ~/.ssh/id_rsa                   # افزودن کلید خاص
ssh-add -l                              # لیست کلیدهای بارگذاری شده
ssh-add -D                              # حذف تمام کلیدها از agent
killall ssh-agent                       # بستن SSH agent
```

## امنیت و عیب‌یابی
```bash
ssh -v user@hostname                    # حالت پرجزئیات (عیب‌یابی)
ssh -vv user@hostname                   # پرجزئیات‌تر
ssh -vvv user@hostname                  # بیشترین جزئیات
ssh -o "StrictHostKeyChecking=no" user@host # غیرفعال کردن بررسی کلید هاست (توصیه نمی‌شود)
ssh-keyscan hostname                    # دریافت کلید هاست
```

## مدیریت جلسه SSH
```bash
# در جلسه SSH:
~.                                      # قطع اتصال
~^Z                                     # تعلیق اتصال
~?                                      # نمایش دستورات escape
```

## جلسات چندگانه
```bash
ssh -M -S /tmp/ssh_socket user@host     # اتصال اصلی (Master)
ssh -S /tmp/ssh_socket user@host        # استفاده از اتصال موجود
ssh -O check -S /tmp/ssh_socket user@host # بررسی اتصال اصلی
ssh -O exit -S /tmp/ssh_socket user@host  # بستن اتصال اصلی
```

## گزینه‌های پیکربندی SSH
```
Host example
    HostName example.com
    User myuser
    Port 22
    IdentityFile ~/.ssh/id_rsa
    ForwardAgent yes
    Compression yes
    ServerAliveInterval 60
    ServerAliveCountMax 3
    TCPKeepAlive yes
    ControlMaster auto
    ControlPath ~/.ssh/sockets/%r@%h:%p
    ControlPersist 10m
```

## دسترسی‌های فایل
```bash
chmod 700 ~/.ssh                        # پوشه SSH
chmod 600 ~/.ssh/id_rsa                 # کلید خصوصی
chmod 644 ~/.ssh/id_rsa.pub             # کلید عمومی
chmod 644 ~/.ssh/authorized_keys        # کلیدهای مجاز
chmod 644 ~/.ssh/known_hosts            # هاست‌های شناخته شده
chmod 600 ~/.ssh/config                 # فایل پیکربندی
```

## ProxyJump (Jump Host)
```bash
ssh -J jumphost user@finalhost          # اتصال از طریق jump host
ssh -J user1@host1,user2@host2 user@final # چندین jump
```

یا در فایل config:
```
Host final
    HostName finalhost.com
    User myuser
    ProxyJump jumphost
```

## مشکلات رایج
```bash
ssh-keygen -R hostname                  # حذف هاست از known_hosts
ssh-keygen -f ~/.ssh/known_hosts -R hostname # همانند بالا
eval "$(ssh-agent -s)" && ssh-add       # رفع مشکلات agent
```

## Rsync روی SSH
```bash
rsync -avz -e ssh source/ user@host:/dest/ # همگام‌سازی با SSH
rsync -avz --progress -e "ssh -p 2222" source/ user@host:/dest/ # پورت سفارشی
```
