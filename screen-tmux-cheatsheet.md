# Screen & Tmux Cheatsheet

## Screen

### دستورات پایه
```bash
screen                          # شروع جلسه جدید
screen -S name                  # شروع جلسه با نام
screen -ls                      # لیست جلسات
screen -r                       # اتصال مجدد به جلسه
screen -r name                  # اتصال مجدد به جلسه با نام
screen -d                       # جدا کردن جلسه
screen -d -r                    # جدا کردن و اتصال مجدد
screen -X quit                  # خروج از جلسه
```

### کلیدهای Screen (پیشوند Ctrl+a)
```
Ctrl+a ?        # نمایش راهنما
Ctrl+a d        # جدا شدن از جلسه
Ctrl+a D D      # جدا شدن و خروج
Ctrl+a c        # ایجاد پنجره جدید
Ctrl+a n        # پنجره بعدی
Ctrl+a p        # پنجره قبلی
Ctrl+a 0-9      # تغییر به پنجره 0-9
Ctrl+a "        # لیست تمام پنجره‌ها
Ctrl+a '        # درخواست شماره/نام پنجره
Ctrl+a A        # تغییر نام پنجره جاری
Ctrl+a k        # بستن پنجره جاری
Ctrl+a \        # بستن تمام پنجره‌ها و خاتمه
Ctrl+a [        # ورود به حالت کپی
Ctrl+a ]        # چسباندن بافر
Ctrl+a S        # تقسیم افقی
Ctrl+a |        # تقسیم عمودی
Ctrl+a Tab      # تغییر به ناحیه بعدی
Ctrl+a X        # بستن ناحیه جاری
Ctrl+a Q        # بستن تمام نواحی به جز جاری
```

### حالت کپی Screen
```
Ctrl+a [        # ورود به حالت کپی
Space           # شروع انتخاب
Enter           # پایان انتخاب و کپی
Ctrl+a ]        # چسباندن
```

---

## Tmux

### مدیریت جلسه
```bash
tmux                            # شروع جلسه جدید
tmux new -s name                # شروع جلسه با نام
tmux ls                         # لیست جلسات
tmux list-sessions              # لیست جلسات
tmux attach                     # اتصال به آخرین جلسه
tmux attach -t name             # اتصال به جلسه با نام
tmux a -t name                  # فرم کوتاه
tmux kill-session -t name       # بستن جلسه با نام
tmux kill-server                # بستن تمام جلسات
```

### کلیدهای Tmux (پیشوند Ctrl+b)

#### دستورات جلسه
```
Ctrl+b s        # لیست جلسات
Ctrl+b $        # تغییر نام جلسه
Ctrl+b d        # جدا شدن از جلسه
Ctrl+b (        # جلسه قبلی
Ctrl+b )        # جلسه بعدی
```

#### دستورات پنجره
```
Ctrl+b c        # ایجاد پنجره جدید
Ctrl+b ,        # تغییر نام پنجره جاری
Ctrl+b &        # بستن پنجره جاری
Ctrl+b w        # لیست پنجره‌ها
Ctrl+b n        # پنجره بعدی
Ctrl+b p        # پنجره قبلی
Ctrl+b 0-9      # تغییر به پنجره 0-9
Ctrl+b l        # آخرین پنجره
Ctrl+b f        # یافتن پنجره
```

#### دستورات پنل
```
Ctrl+b %        # تقسیم عمودی
Ctrl+b "        # تقسیم افقی
Ctrl+b o        # رفتن به پنل بعدی
Ctrl+b ;        # رفتن به آخرین پنل فعال
Ctrl+b x        # بستن پنل جاری
Ctrl+b q        # نمایش شماره پنل‌ها
Ctrl+b q 0-9    # تغییر به پنل با شماره
Ctrl+b {        # انتقال پنل به چپ
Ctrl+b }        # انتقال پنل به راست
Ctrl+b z        # تغییر حالت زوم پنل
Ctrl+b !        # تبدیل پنل به پنجره
Ctrl+b Ctrl+o   # چرخش پنل‌ها
```

#### ناوبری پنل
```
Ctrl+b ↑        # حرکت به پنل بالا
Ctrl+b ↓        # حرکت به پنل پایین
Ctrl+b ←        # حرکت به پنل چپ
Ctrl+b →        # حرکت به پنل راست
```

#### تغییر اندازه پنل
```
Ctrl+b :resize-pane -D 5    # کاهش اندازه پایین
Ctrl+b :resize-pane -U 5    # افزایش اندازه بالا
Ctrl+b :resize-pane -L 5    # کاهش اندازه چپ
Ctrl+b :resize-pane -R 5    # افزایش اندازه راست
Ctrl+b Ctrl+↑   # تغییر اندازه بالا
Ctrl+b Ctrl+↓   # تغییر اندازه پایین
Ctrl+b Ctrl+←   # تغییر اندازه چپ
Ctrl+b Ctrl+→   # تغییر اندازه راست
```

#### حالت کپی (سبک Vi)
```
Ctrl+b [        # ورود به حالت کپی
q               # خروج از حالت کپی
Space           # شروع انتخاب
Enter           # کپی انتخاب
Ctrl+b ]        # چسباندن بافر
g               # رفتن به بالا
G               # رفتن به پایین
h,j,k,l         # حرکت نشانگر
w               # کلمه بعدی
b               # کلمه قبلی
/               # جستجو به جلو
?               # جستجو به عقب
n               # نتیجه جستجوی بعدی
N               # نتیجه جستجوی قبلی
```

### حالت دستوری Tmux
```
Ctrl+b :        # ورود به حالت دستوری
:list-keys      # لیست تمام کلیدها
:list-commands  # لیست تمام دستورات
:source-file ~/.tmux.conf # بارگذاری مجدد پیکربندی
```

### دستورات مفید Tmux
```bash
# مدیریت جلسه
tmux new -s mysession           # جلسه جدید با نام
tmux attach -t mysession        # اتصال به جلسه
tmux switch -t mysession        # تغییر به جلسه
tmux rename-session -t old new  # تغییر نام جلسه
tmux kill-session -t mysession  # بستن جلسه
tmux kill-session -a            # بستن همه به جز جاری
tmux kill-session -a -t mysession # بستن همه به جز با نام

# مدیریت پنجره
tmux new-window -n mywindow     # ایجاد پنجره با نام
tmux select-window -t :0        # انتخاب پنجره با ایندکس
tmux rename-window newname      # تغییر نام پنجره
tmux kill-window -t :0          # بستن پنجره

# مدیریت پنل
tmux split-window -h            # تقسیم افقی
tmux split-window -v            # تقسیم عمودی
tmux select-pane -t :.+         # انتخاب پنل بعدی
tmux swap-pane -U               # تعویض با پنل قبلی
tmux swap-pane -D               # تعویض با پنل بعدی
```

### پیکربندی Tmux (~/.tmux.conf)
```bash
# تغییر پیشوند به Ctrl+a
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# فعال‌سازی پشتیبانی ماوس
set -g mouse on

# افزایش محدودیت تاریخچه
set -g history-limit 10000

# شروع پنجره‌ها و پنل‌ها از 1، نه 0
set -g base-index 1
setw -g pane-base-index 1

# حالت Vi
setw -g mode-keys vi

# تقسیم پنل‌ها با | و -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# بارگذاری مجدد پیکربندی
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# تغییر پنل با Alt-arrow بدون پیشوند
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# تغییر نام خودکار پنجره‌ها نباشد
set -g allow-rename off

# نوار وضعیت
set -g status-position bottom
set -g status-bg colour234
set -g status-fg colour137
```

## جدول مقایسه

| ویژگی | Screen | Tmux |
|---------|--------|------|
| پیشوند | Ctrl+a | Ctrl+b |
| پنجره جدید | Ctrl+a c | Ctrl+b c |
| تقسیم افقی | Ctrl+a S | Ctrl+b " |
| تقسیم عمودی | Ctrl+a \| | Ctrl+b % |
| پنجره بعدی | Ctrl+a n | Ctrl+b n |
| پنجره قبلی | Ctrl+a p | Ctrl+b p |
| جدا شدن | Ctrl+a d | Ctrl+b d |
| لیست پنجره‌ها | Ctrl+a " | Ctrl+b w |
| حالت کپی | Ctrl+a [ | Ctrl+b [ |
| چسباندن | Ctrl+a ] | Ctrl+b ] |

## گردش کارهای رایج

### گردش کار Screen
```bash
# شروع جلسه با نام
screen -S myproject

# کار در چند پنجره
Ctrl+a c    # ایجاد پنجره‌ها
Ctrl+a n    # ناوبری

# جدا شدن
Ctrl+a d

# بعداً، اتصال مجدد
screen -r myproject
```

### گردش کار Tmux
```bash
# شروع جلسه با نام
tmux new -s myproject

# تقسیم به پنل‌ها
Ctrl+b %    # تقسیم عمودی
Ctrl+b "    # تقسیم افقی

# ناوبری پنل‌ها
Ctrl+b arrow keys

# ایجاد پنجره‌ها
Ctrl+b c

# جدا شدن
Ctrl+b d

# بعداً، اتصال مجدد
tmux attach -t myproject
```

## نکات و ترفندها

### Screen
```bash
# اجرای دستور در پنجره جدید
screen -t "logs" tail -f /var/log/syslog

# Hardcopy (ذخیره صفحه در فایل)
Ctrl+a h

# قفل صفحه
Ctrl+a x

# نمایش اطلاعات سیستم
Ctrl+a i
```

### Tmux
```bash
# اجرای دستور در جلسه جدید
tmux new -s logs "tail -f /var/log/syslog"

# ایجاد چیدمان
tmux new -s dev "vim" \; split-window -h "npm start" \; split-window -v

# گرفتن خروجی پنل در فایل
tmux capture-pane -p > output.txt

# همگام‌سازی پنل‌ها (ارسال ورودی به همه)
Ctrl+b :setw synchronize-panes on

# ساعت
Ctrl+b t

# نمایش اطلاعات tmux
Ctrl+b i
```

### جلسات تودرتو
```bash
# Screen در Screen: استفاده از Ctrl+a a برای ارسال پیشوند
Ctrl+a a n   # پنجره بعدی در screen تودرتو

# Tmux در Tmux: استفاده دوبار از پیشوند
Ctrl+b Ctrl+b n   # پنجره بعدی در tmux تودرتو
```
