# Systemd Cheatsheet

## مدیریت سرویس‌ها
```bash
systemctl start <service>               # شروع سرویس
systemctl stop <service>                # توقف سرویس
systemctl restart <service>             # راه‌اندازی مجدد سرویس
systemctl reload <service>              # بارگذاری مجدد پیکربندی سرویس
systemctl status <service>              # نمایش وضعیت سرویس
systemctl enable <service>              # فعال‌سازی سرویس در بوت
systemctl disable <service>             # غیرفعال‌سازی سرویس در بوت
systemctl is-enabled <service>          # بررسی فعال بودن سرویس
systemctl is-active <service>           # بررسی فعال بودن سرویس
systemctl is-failed <service>           # بررسی خطای سرویس
```

## لیست سرویس‌ها
```bash
systemctl list-units                    # لیست تمام واحدهای فعال
systemctl list-units --type=service     # لیست تمام سرویس‌ها
systemctl list-units --all              # لیست تمام واحدها (فعال + غیرفعال)
systemctl list-unit-files               # لیست تمام فایل‌های واحد
systemctl list-unit-files --type=service # لیست تمام فایل‌های سرویس
systemctl list-dependencies <service>   # نمایش وابستگی‌های سرویس
systemctl list-sockets                  # لیست تمام socketها
systemctl list-timers                   # لیست تمام timerها
```

## وضعیت سیستم
```bash
systemctl reboot                        # راه‌اندازی مجدد سیستم
systemctl poweroff                      # خاموش کردن سیستم
systemctl suspend                       # حالت تعلیق
systemctl hibernate                     # حالت Hibernate
systemctl hybrid-sleep                  # Hybrid sleep
systemctl get-default                   # نمایش target پیش‌فرض
systemctl set-default multi-user.target # تنظیم target پیش‌فرض
systemctl isolate multi-user.target     # تغییر به target
```

## فایل‌های سرویس
مکان فایل سرویس:
- `/etc/systemd/system/` - سرویس‌های سیستم
- `/usr/lib/systemd/system/` - سرویس‌های توزیع
- `~/.config/systemd/user/` - سرویس‌های کاربر

ساختار پایه فایل سرویس:
```ini
[Unit]
Description=My Service
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/myapp
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## انواع سرویس
```ini
Type=simple         # پیش‌فرض، پروسس اصلی است
Type=forking        # پروسس fork می‌کند، والد خارج می‌شود
Type=oneshot        # پروسس کوتاه‌مدت
Type=notify         # سرویس اعلان می‌فرستد وقتی آماده است
Type=dbus           # سرویس وقتی آماده است که نام D-Bus را دریافت کند
Type=idle           # تاخیر تا پایان سایر jobها
```

## گزینه‌های Restart
```ini
Restart=no              # هرگز راه‌اندازی مجدد نشود (پیش‌فرض)
Restart=on-success      # فقط در خروج تمیز
Restart=on-failure      # در صورت خطا
Restart=on-abnormal     # در پایان غیرعادی
Restart=on-watchdog     # در timeout watchdog
Restart=on-abort        # در سیگنال ناتمیز
Restart=always          # همیشه راه‌اندازی مجدد
```

## Journal (لاگ‌ها)
```bash
journalctl                              # نمایش تمام لاگ‌ها
journalctl -u <service>                 # نمایش لاگ‌های سرویس خاص
journalctl -f                           # دنبال کردن لاگ‌ها (tail -f)
journalctl -u <service> -f              # دنبال کردن لاگ‌های سرویس
journalctl -b                           # لاگ‌ها از boot فعلی
journalctl -b -1                        # لاگ‌ها از boot قبلی
journalctl --since "2024-01-01"         # لاگ‌ها از تاریخ
journalctl --since "1 hour ago"         # لاگ‌ها از ساعت گذشته
journalctl --since today                # لاگ‌های امروز
journalctl --until "2024-01-31"         # لاگ‌ها تا تاریخ
journalctl -p err                       # فقط خطاها
journalctl -p warning                   # هشدارها و بالاتر
journalctl -n 50                        # نمایش 50 خط آخر
journalctl --disk-usage                 # نمایش مصرف دیسک journal
journalctl --vacuum-size=100M           # محدود کردن اندازه journal
journalctl --vacuum-time=2weeks         # نگهداری فقط 2 هفته
journalctl -o json-pretty               # خروجی JSON
```

## سطوح اولویت
```
0: emerg        # اضطراری
1: alert        # هشدار
2: crit         # بحرانی
3: err          # خطا
4: warning      # هشدار
5: notice       # اطلاع
6: info         # اطلاعات
7: debug        # اشکال‌زدایی
```

## مدیریت فایل‌های واحد
```bash
systemctl daemon-reload                 # بارگذاری مجدد پیکربندی systemd
systemctl edit <service>                # ویرایش override سرویس
systemctl edit --full <service>         # ویرایش کامل فایل سرویس
systemctl cat <service>                 # نمایش محتوای فایل سرویس
systemctl show <service>                # نمایش تمام ویژگی‌ها
systemctl mask <service>                # غیرفعال کردن کامل سرویس (جلوگیری از شروع)
systemctl unmask <service>              # فعال کردن مجدد سرویس
```

## Targetها (Runlevelها)
```bash
systemctl get-default                   # نمایش target پیش‌فرض
systemctl set-default multi-user.target # تنظیم target پیش‌فرض
systemctl list-units --type=target      # لیست targetهای موجود
systemctl isolate rescue.target         # تغییر به حالت rescue
systemctl isolate multi-user.target     # تغییر به multi-user
systemctl isolate graphical.target      # تغییر به حالت گرافیکی
```

Targetهای رایج:
- `poweroff.target` - خاموشی
- `rescue.target` - حالت تک کاربره
- `multi-user.target` - چند کاربره (بدون GUI)
- `graphical.target` - چند کاربره با GUI
- `reboot.target` - راه‌اندازی مجدد

## Timerها
```bash
systemctl list-timers                   # لیست تمام timerها
systemctl list-timers --all             # لیست تمام timerها (فعال + غیرفعال)
systemctl start <timer>                 # شروع timer
systemctl enable <timer>                # فعال‌سازی timer در بوت
```

مثال فایل timer (`backup.timer`):
```ini
[Unit]
Description=Backup Timer

[Timer]
OnCalendar=daily
OnCalendar=*-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

## سرویس‌های کاربر
```bash
systemctl --user start <service>        # شروع سرویس کاربر
systemctl --user enable <service>       # فعال‌سازی سرویس کاربر
systemctl --user status <service>       # وضعیت سرویس کاربر
journalctl --user -u <service>          # لاگ‌های سرویس کاربر
loginctl enable-linger <user>           # فعال‌سازی سرویس‌های کاربر بدون ورود
```

## تحلیل زمان بوت
```bash
systemd-analyze                         # نمایش زمان بوت
systemd-analyze blame                   # نمایش زمان هر سرویس
systemd-analyze critical-chain          # نمایش زنجیره بحرانی
systemd-analyze plot > boot.svg         # ایجاد نمودار بوت
systemd-analyze verify <service>        # تأیید صحت فایل واحد
```

## کنترل منابع (cgroups)
```ini
[Service]
CPUQuota=50%                            # محدود کردن CPU به 50%
MemoryLimit=1G                          # محدود کردن حافظه به 1GB
TasksMax=100                            # محدود کردن تعداد taskها
IOWeight=500                            # اولویت IO
```

```bash
systemctl show <service> -p CPUQuota    # نمایش سهمیه CPU
systemctl set-property <service> CPUQuota=50% # تنظیم سهمیه CPU
```

## متغیرهای محیطی
```ini
[Service]
Environment="VAR1=value1"
Environment="VAR2=value2"
EnvironmentFile=/etc/myapp/env          # بارگذاری از فایل
```

## Socket Activation
فایل Socket (`myapp.socket`):
```ini
[Unit]
Description=My App Socket

[Socket]
ListenStream=8080

[Install]
WantedBy=sockets.target
```

## وابستگی‌های سرویس
```ini
[Unit]
Requires=network.target                 # وابستگی سخت
Wants=network.target                    # وابستگی نرم
After=network.target                    # شروع بعد از
Before=multi-user.target                # شروع قبل از
Conflicts=other.service                 # نمی‌تواند همزمان اجرا شود
```

## الگوهای رایج سرویس

### Daemon ساده
```ini
[Unit]
Description=My Daemon
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/mydaemon
Restart=always

[Install]
WantedBy=multi-user.target
```

### Daemon با Forking
```ini
[Service]
Type=forking
PIDFile=/var/run/mydaemon.pid
ExecStart=/usr/bin/mydaemon --daemon
```

### اسکریپت One-shot
```ini
[Service]
Type=oneshot
ExecStart=/usr/local/bin/myscript.sh
RemainAfterExit=yes
```
