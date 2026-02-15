# 🐚 Bash/Shell Scripting Cheatsheet

## 📜 ساختار پایه اسکریپت

```bash
#!/bin/bash
# این یک کامنت است
# توضیحات اسکریپت

# متغیرها
NAME="علی"
AGE=30

# دستورات اسکریپت
echo "سلام، $NAME!"
echo "شما $AGE ساله هستید."
```

---

## 📌 خطوط Shebang

```bash
#!/bin/bash                            # اسکریپت Bash
#!/bin/sh                              # اسکریپت POSIX shell
#!/usr/bin/env bash                    # Bash قابل حمل
#!/usr/bin/env python3                 # اسکریپت Python
```

---

## 🔤 متغیرها

```bash
# تخصیص متغیر
NAME="علی رضایی"
AGE=30
readonly PI=3.14                       # متغیر فقط خواندنی

# استفاده از متغیر
echo $NAME
echo ${NAME}                           # روش ترجیحی
echo "سلام، $NAME!"
echo 'سلام، $NAME!'                    # کوتیشن تکی - بدون تبدیل

# جایگزینی دستور
TODAY=$(date)
FILES=$(ls)
USERS=`who`                            # نحو قدیمی

# متغیرهای خاص
$0                                     # نام اسکریپت
$1, $2, $3...                          # پارامترهای موقعیتی
$#                                     # تعداد آرگومان‌ها
$@                                     # تمام آرگومان‌ها به صورت جداگانه
$*                                     # تمام آرگومان‌ها به صورت یک کلمه
$?                                     # وضعیت خروج آخرین دستور
$$                                     # شناسه پروسس شل فعلی
$!                                     # PID آخرین دستور پس‌زمینه
```

---

## 🔢 آرایه‌ها

```bash
# تعریف آرایه
FRUITS=("سیب" "موز" "پرتقال")
NUMBERS=(1 2 3 4 5)

# عملیات آرایه
echo ${FRUITS[0]}                      # دسترسی به عنصر
echo ${FRUITS[@]}                      # تمام عناصر
echo ${#FRUITS[@]}                     # طول آرایه
FRUITS+=("انبه")                       # افزودن عنصر
FRUITS[1]="انگور"                      # تغییر عنصر

# آرایه‌های انجمنی (bash 4+)
declare -A CAPITALS
CAPITALS[UK]="London"
CAPITALS[France]="Paris"
echo ${CAPITALS[UK]}
echo ${!CAPITALS[@]}                   # تمام کلیدها
echo ${CAPITALS[@]}                    # تمام مقادیر
```

---

## 🎛️ ورودی کاربر

```bash
# خواندن ورودی
read NAME
read -p "نام خود را وارد کنید: " NAME           # با prompt
read -sp "رمز عبور را وارد کنید: " PASSWORD    # ورودی خاموش
read -t 5 -p "سریع! نام را وارد کنید: " NAME   # زمان محدود
read -n 1 -p "یک کلید بزنید..." KEY             # یک کاراکتر

# خواندن چند مقدار
read -p "نام و نام خانوادگی وارد کنید: " FIRST LAST

# خواندن از فایل
while read LINE; do
    echo $LINE
done < file.txt
```

---

## ➕ عملیات محاسباتی

```bash
# استفاده از $(( ))
NUM=$((5 + 3))                         # جمع
NUM=$((10 - 2))                        # تفریق
NUM=$((4 * 3))                         # ضرب
NUM=$((20 / 5))                        # تقسیم
NUM=$((10 % 3))                        # باقیمانده
NUM=$((2 ** 8))                        # توان

# استفاده از let
let "NUM = 5 + 3"
let "NUM++"                            # افزایش
let "NUM--"                            # کاهش

# استفاده از expr
NUM=$(expr 5 + 3)
NUM=$(expr $NUM \* 2)                  # فرار *

# اعشاری (استفاده از bc)
RESULT=$(echo "scale=2; 10 / 3" | bc)
```

---

## ✅ دستورات شرطی

```bash
# دستور If
if [ condition ]; then
    # دستورات
fi

if [ condition ]; then
    # دستورات
else
    # دستورات
fi

if [ condition1 ]; then
    # دستورات
elif [ condition2 ]; then
    # دستورات
else
    # دستورات
fi

# یک خطی
[ condition ] && echo "درست" || echo "غلط"

# شرایط تست
[ -f file.txt ]                        # فایل وجود دارد
[ -d directory ]                       # دایرکتوری وجود دارد
[ -r file.txt ]                        # فایل قابل خواندن است
[ -w file.txt ]                        # فایل قابل نوشتن است
[ -x file.txt ]                        # فایل قابل اجرا است
[ -s file.txt ]                        # فایل خالی نیست
[ file1 -nt file2 ]                    # file1 جدیدتر از file2
[ file1 -ot file2 ]                    # file1 قدیمی‌تر از file2

# مقایسه رشته‌ای
[ "$str1" = "$str2" ]                  # برابر
[ "$str1" != "$str2" ]                 # نابرابر
[ -z "$str" ]                          # رشته خالی
[ -n "$str" ]                          # رشته غیرخالی

# مقایسه عددی
[ $num1 -eq $num2 ]                    # برابر
[ $num1 -ne $num2 ]                    # نابرابر
[ $num1 -lt $num2 ]                    # کمتر از
[ $num1 -le $num2 ]                    # کمتر یا مساوی
[ $num1 -gt $num2 ]                    # بزرگتر از
[ $num1 -ge $num2 ]                    # بزرگتر یا مساوی

# عملگرهای منطقی
[ condition1 ] && [ condition2 ]       # AND
[ condition1 ] || [ condition2 ]       # OR
[ ! condition ]                        # NOT
[ condition1 -a condition2 ]           # AND (داخل test)
[ condition1 -o condition2 ]           # OR (داخل test)

# نحو مدرن [[ ]] (bash)
[[ $str =~ regex ]]                    # مطابقت regex
[[ $str == pattern* ]]                 # مطابقت الگو
```

---

## 🔁 حلقه‌ها

```bash
# حلقه For
for i in 1 2 3 4 5; do
    echo $i
done

for file in *.txt; do
    echo $file
done

for ((i=1; i<=10; i++)); do
    echo $i
done

# حلقه While
COUNT=0
while [ $COUNT -lt 10 ]; do
    echo $COUNT
    ((COUNT++))
done

# حلقه Until
COUNT=0
until [ $COUNT -ge 10 ]; do
    echo $COUNT
    ((COUNT++))
done

# خواندن فایل خط به خط
while IFS= read -r line; do
    echo "$line"
done < file.txt

# حلقه بی‌نهایت
while true; do
    echo "در حال اجرا..."
    sleep 1
done

# Break و continue
for i in {1..10}; do
    [ $i -eq 5 ] && continue           # رد شدن از 5
    [ $i -eq 8 ] && break              # توقف در 8
    echo $i
done
```

---

## 🎯 دستورات Case

```bash
case $VARIABLE in
    pattern1)
        # دستورات
        ;;
    pattern2|pattern3)
        # دستورات
        ;;
    *)
        # پیش‌فرض
        ;;
esac

# مثال
case $1 in
    start)
        echo "در حال شروع..."
        ;;
    stop)
        echo "در حال توقف..."
        ;;
    restart)
        echo "راه‌اندازی مجدد..."
        ;;
    *)
        echo "استفاده: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

---

## 📞 توابع

```bash
# تعریف تابع
function_name() {
    # دستورات
}

function function_name {
    # دستورات
}

# تابع با پارامتر
greet() {
    echo "سلام، $1!"
    echo "شما $2 ساله هستید."
}
greet "علی" 30

# مقدار برگشتی
add() {
    local result=$(($1 + $2))
    echo $result
}
SUM=$(add 5 3)

# وضعیت برگشتی
check_file() {
    [ -f "$1" ] && return 0 || return 1
}
check_file "test.txt" && echo "وجود دارد" || echo "یافت نشد"

# متغیرهای محلی
my_function() {
    local LOCAL_VAR="محلی"
    GLOBAL_VAR="سراسری"
}
```

---

## 🔤 دستکاری رشته

```bash
# طول رشته
STRING="سلام دنیا"
echo ${#STRING}                        # 10

# زیررشته
echo ${STRING:0:5}                     # سلام
echo ${STRING:6}                       # دنیا

# جایگزینی
echo ${STRING/دنیا/Bash}               # جایگزینی اول
echo ${STRING//ا/آ}                    # جایگزینی همه

# حذف پیشوند/پسوند
FILE="example.tar.gz"
echo ${FILE%.gz}                       # example.tar
echo ${FILE%.*}                        # example.tar
echo ${FILE%%.*}                       # example
echo ${FILE#*.}                        # tar.gz
echo ${FILE##*.}                       # gz

# بزرگ/کوچک کردن حروف
STRING="Hello World"
echo ${STRING^^}                       # HELLO WORLD (bash 4+)
echo ${STRING,,}                       # hello world (bash 4+)
echo $STRING | tr '[:lower:]' '[:upper:]'  # HELLO WORLD
echo $STRING | tr '[:upper:]' '[:lower:]'  # hello world

# مقادیر پیش‌فرض
echo ${VAR:-default}                   # استفاده از پیش‌فرض اگر VAR خالی
echo ${VAR:=default}                   # تنظیم و استفاده از پیش‌فرض
echo ${VAR:+alternate}                 # استفاده از جایگزین اگر VAR تنظیم شده
```

---

## 📁 عملیات فایل

```bash
# خواندن فایل
cat file.txt
cat < file.txt
while read line; do echo $line; done < file.txt

# نوشتن در فایل
echo "متن" > file.txt                  # بازنویسی
echo "متن" >> file.txt                 # افزودن

# ساخت فایل
touch file.txt
> file.txt                             # فایل خالی

# حذف فایل
rm file.txt
rm -f file.txt                         # حذف اجباری

# کپی/جابجایی
cp source.txt dest.txt
mv source.txt dest.txt

# اطلاعات فایل
ls -l file.txt
stat file.txt
file file.txt                          # نوع فایل

# بررسی وجود فایل
if [ -f file.txt ]; then
    echo "فایل وجود دارد"
fi
```

---

## 🗂️ عملیات دایرکتوری

```bash
# دایرکتوری فعلی
pwd

# تغییر دایرکتوری
cd /path/to/dir
cd ~                                   # دایرکتوری خانگی
cd -                                   # دایرکتوری قبلی

# ساخت دایرکتوری
mkdir mydir
mkdir -p parent/child/grandchild       # ساخت والدین

# حذف دایرکتوری
rmdir mydir                            # دایرکتوری خالی
rm -r mydir                            # حذف بازگشتی
rm -rf mydir                           # حذف اجباری بازگشتی

# لیست دایرکتوری
ls
ls -la                                 # فرمت طولانی، فایل‌های مخفی
ls -lh                                 # اندازه‌های قابل خواندن
ls -lt                                 # مرتب‌سازی بر اساس زمان
```

---

## 🔄 تغییر مسیر ورودی/خروجی

```bash
# تغییر مسیر خروجی
command > file                         # Stdout به فایل (بازنویسی)
command >> file                        # Stdout به فایل (افزودن)
command 2> file                        # Stderr به فایل
command &> file                        # هر دو stdout و stderr
command > file 2>&1                    # هر دو (سازگار)

# تغییر مسیر ورودی
command < file                         # ورودی از فایل

# Here document
cat << EOF
خط 1
خط 2
EOF

# Here string
grep "الگو" <<< "جستجو در این رشته"

# Pipe
command1 | command2                    # خروجی cmd1 به ورودی cmd2
command1 |& command2                   # هر دو stdout و stderr

# Tee (نوشتن در فایل و stdout)
command | tee file.txt
command | tee -a file.txt              # افزودن
```

---

## ⚙️ مدیریت پروسه

```bash
# پروسه پس‌زمینه
command &
nohup command &                        # نادیده گرفتن سیگنال hangup

# کنترل job
jobs                                   # لیست job ها
fg %1                                  # آوردن job 1 به پیش‌زمینه
bg %1                                  # ادامه job 1 در پس‌زمینه
kill %1                                # کشتن job 1

# اطلاعات پروسه
ps aux                                 # تمام پروسه‌ها
ps -ef                                 # تمام پروسه‌ها (فرمت متفاوت)
pgrep process_name                     # یافتن پروسه با نام
pidof process_name                     # دریافت PID

# کشتن پروسه
kill PID                               # خاتمه
kill -9 PID                            # کشتن اجباری
killall process_name                   # کشتن با نام
pkill process_name                     # کشتن با الگوی نام

# انتظار برای پروسه
wait PID
wait                                   # انتظار برای تمام job های پس‌زمینه
```

---

## 🛠️ دستورات مفید

```bash
# جایگزینی دستور
FILES=$(ls)
DATE=`date`

# Pipeline
cat file.txt | grep "الگو" | sort | uniq

# عملگرهای منطقی
command1 && command2                   # اجرای cmd2 اگر cmd1 موفق شود
command1 || command2                   # اجرای cmd2 اگر cmd1 شکست بخورد
command1 ; command2                    # اجرای هر دو صرف‌نظر از نتیجه

# گروه‌بندی
(command1; command2)                   # اجرا در subshell
{ command1; command2; }                # اجرا در شل فعلی

# دستور Test
test -f file.txt
[ -f file.txt ]                        # مشابه test

# Printf (خروجی فرمت‌شده)
printf "نام: %s, سن: %d\n" "علی" 30

# Sleep
sleep 5                                # خواب 5 ثانیه
sleep 1m                               # خواب 1 دقیقه
sleep 1h                               # خواب 1 ساعت
```

---

## 🐛 اشکال‌زدایی

```bash
# حالت debug
bash -x script.sh                      # چاپ دستورات
set -x                                 # فعال‌سازی حالت debug
set +x                                 # غیرفعال‌سازی حالت debug

# حالت verbose
bash -v script.sh                      # چاپ خطوط به محض خواندن
set -v                                 # فعال‌سازی verbose
set +v                                 # غیرفعال‌سازی verbose

# خروج در خطا
set -e                                 # خروج در خطا
set -u                                 # خروج در متغیر تعریف نشده
set -o pipefail                        # خروج در شکست pipe

# ترکیبی
set -euo pipefail                      # حالت سختگیرانه

# تله خطاها
trap 'echo "خطا در خط $LINENO"' ERR
```

---

## 💡 بهترین روش‌ها

```bash
# همیشه متغیرها را quote کنید
echo "$VAR"                            # خوب
echo $VAR                              # بد (word splitting)

# از [[ ]] به جای [ ] استفاده کنید
[[ $var = value ]]                     # خوب (bash)
[ $var = value ]                       # قابل قبول (POSIX)

# بررسی وجود دستور
command -v git >/dev/null 2>&1 || { echo "git لازم است"; exit 1; }

# استفاده از توابع
do_something() {
    local var="value"
    # دستورات
}

# مدیریت خطا
if ! command; then
    echo "دستور شکست خورد"
    exit 1
fi

# از نام‌های معنادار برای متغیرها استفاده کنید
USER_NAME="علی"                        # خوب
un="علی"                               # بد

# اضافه کردن کامنت
# این تابع کار X را انجام می‌دهد
function_name() {
    # پیاده‌سازی
}

# تنظیم گزینه‌های اسکریپت
set -euo pipefail                      # شکست در خطاها
```

---

## 📋 الگوی اسکریپت کامل

```bash
#!/bin/bash
#
# نام اسکریپت: script_name.sh
# توضیحات: توضیحات اسکریپت در اینجا
# نویسنده: نام شما
# تاریخ: 2024-01-01
#

set -euo pipefail                      # خروج در خطا

# ثابت‌ها
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"

# متغیرها
DEBUG=false
VERBOSE=false

# توابع
usage() {
    cat << EOF
استفاده: $SCRIPT_NAME [OPTIONS]

گزینه‌ها:
    -h, --help      نمایش این پیام راهنما
    -v, --verbose   خروجی پرجزئیات
    -d, --debug     حالت اشکال‌زدایی
EOF
    exit 0
}

log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*"
}

error() {
    echo "[خطا] $*" >&2
    exit 1
}

# تجزیه آرگومان‌ها
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            ;;
        -v|--verbose)
            VERBOSE=true
            shift
            ;;
        -d|--debug)
            DEBUG=true
            set -x
            shift
            ;;
        *)
            error "گزینه ناشناخته: $1"
            ;;
    esac
done

# اسکریپت اصلی
main() {
    log "شروع اسکریپت"
    
    # کد شما در اینجا
    
    log "اسکریپت تکمیل شد"
}

# اجرای تابع main
main "$@"
```
