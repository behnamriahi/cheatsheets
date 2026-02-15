# 🔴 Redis Cheatsheet

## 🔌 اتصال

```bash
redis-cli                              # اتصال به Redis محلی
redis-cli -h hostname -p port          # اتصال به Redis راه دور
redis-cli -a password                  # اتصال با رمز عبور
redis-cli --raw                        # نمایش خروجی به صورت خام
redis-cli -n database_number           # اتصال به دیتابیس خاص
redis-cli --eval script.lua            # اجرای اسکریپت Lua
```

---

## 🔑 عملیات کلید

```bash
SET key value                          # تنظیم مقدار کلید
GET key                                # دریافت مقدار کلید
DEL key                                # حذف کلید
EXISTS key                             # بررسی وجود کلید
KEYS pattern                           # یافتن کلیدها با الگو
KEYS *                                 # لیست تمام کلیدها (در تولید استفاده نکنید)
SCAN cursor                            # پیمایش تدریجی کلیدها
TYPE key                               # دریافت نوع کلید
RENAME oldkey newkey                   # تغییر نام کلید
RENAMENX oldkey newkey                 # تغییر نام اگر کلید جدید وجود نداشته باشد
```

---

## ⏱️ انقضا

```bash
EXPIRE key seconds                     # تنظیم زمان انقضا به ثانیه
EXPIREAT key timestamp                 # تنظیم انقضا به Unix timestamp
TTL key                                # دریافت زمان باقیمانده به ثانیه
PTTL key                               # دریافت TTL به میلی‌ثانیه
PERSIST key                            # حذف انقضا از کلید
SETEX key seconds value                # تنظیم کلید با انقضا
PSETEX key milliseconds value          # تنظیم کلید با انقضا (ms)
```

---

## 📝 عملیات رشته‌ای

```bash
SET key value                          # تنظیم مقدار رشته‌ای
GET key                                # دریافت مقدار رشته‌ای
MSET key1 val1 key2 val2              # تنظیم چند کلید
MGET key1 key2                         # دریافت چند کلید
APPEND key value                       # افزودن به رشته
STRLEN key                             # دریافت طول رشته
GETRANGE key start end                 # دریافت زیررشته
SETRANGE key offset value              # بازنویسی بخشی از رشته
INCR key                               # افزایش به اندازه 1
DECR key                               # کاهش به اندازه 1
INCRBY key amount                      # افزایش به اندازه مشخص
DECRBY key amount                      # کاهش به اندازه مشخص
INCRBYFLOAT key amount                 # افزایش به اندازه اعشاری
SETNX key value                        # تنظیم اگر وجود نداشته باشد
GETSET key value                       # تنظیم و برگرداندن مقدار قبلی
```

---

## 📋 عملیات لیست

```bash
LPUSH key value                        # افزودن به چپ (ابتدا)
RPUSH key value                        # افزودن به راست (انتها)
LPOP key                               # برداشتن از چپ
RPOP key                               # برداشتن از راست
LRANGE key start stop                  # دریافت بازه عناصر
LRANGE key 0 -1                        # دریافت تمام عناصر
LLEN key                               # دریافت طول لیست
LINDEX key index                       # دریافت عنصر در اندیس
LSET key index value                   # تنظیم عنصر در اندیس
LINSERT key BEFORE|AFTER pivot value   # درج قبل/بعد از عنصر
LREM key count value                   # حذف عناصر
LTRIM key start stop                   # کوتاه کردن لیست به بازه
BLPOP key timeout                      # برداشتن مسدود از چپ
BRPOP key timeout                      # برداشتن مسدود از راست
RPOPLPUSH source dest                  # برداشتن از مبدا، افزودن به مقصد
```

---

## 🗂️ عملیات مجموعه

```bash
SADD key member                        # افزودن عضو به مجموعه
SREM key member                        # حذف عضو از مجموعه
SMEMBERS key                           # دریافت تمام اعضا
SISMEMBER key member                   # بررسی وجود عضو
SCARD key                              # دریافت اندازه مجموعه
SPOP key                               # حذف و برگرداندن عضو تصادفی
SRANDMEMBER key [count]                # دریافت عضو تصادفی
SMOVE source dest member               # جابجایی عضو بین مجموعه‌ها

# عملیات مجموعه
SUNION key1 key2                       # اجتماع مجموعه‌ها
SINTER key1 key2                       # اشتراک مجموعه‌ها
SDIFF key1 key2                        # تفاضل مجموعه‌ها
SUNIONSTORE dest key1 key2             # ذخیره نتیجه اجتماع
SINTERSTORE dest key1 key2             # ذخیره نتیجه اشتراک
SDIFFSTORE dest key1 key2              # ذخیره نتیجه تفاضل
```

---

## 📊 عملیات مجموعه مرتب

```bash
ZADD key score member                  # افزودن عضو با امتیاز
ZREM key member                        # حذف عضو
ZSCORE key member                      # دریافت امتیاز عضو
ZINCRBY key increment member           # افزایش امتیاز
ZCARD key                              # دریافت اندازه مجموعه
ZCOUNT key min max                     # شمارش اعضا در بازه امتیاز
ZRANK key member                       # دریافت رتبه (صعودی)
ZREVRANK key member                    # دریافت رتبه (نزولی)
ZRANGE key start stop [WITHSCORES]     # دریافت بازه (صعودی)
ZREVRANGE key start stop [WITHSCORES]  # دریافت بازه (نزولی)
ZRANGEBYSCORE key min max              # دریافت بازه بر اساس امتیاز
ZREVRANGEBYSCORE key max min           # دریافت بازه بر اساس امتیاز (نزولی)
ZREMRANGEBYRANK key start stop         # حذف بازه بر اساس رتبه
ZREMRANGEBYSCORE key min max           # حذف بازه بر اساس امتیاز
```

---

## 🗺️ عملیات Hash

```bash
HSET key field value                   # تنظیم فیلد hash
HGET key field                         # دریافت فیلد hash
HMSET key field1 val1 field2 val2      # تنظیم چند فیلد
HMGET key field1 field2                # دریافت چند فیلد
HGETALL key                            # دریافت تمام فیلدها و مقادیر
HDEL key field                         # حذف فیلد
HEXISTS key field                      # بررسی وجود فیلد
HKEYS key                              # دریافت تمام نام فیلدها
HVALS key                              # دریافت تمام مقادیر
HLEN key                               # دریافت تعداد فیلدها
HINCRBY key field increment            # افزایش مقدار فیلد
HINCRBYFLOAT key field increment       # افزایش به اندازه اعشاری
HSETNX key field value                 # تنظیم اگر فیلد وجود نداشته باشد
```

---

## 🔄 عملیات تراکنش

```bash
MULTI                                  # شروع تراکنش
EXEC                                   # اجرای تراکنش
DISCARD                                # لغو تراکنش
WATCH key                              # نظارت بر کلید برای تغییرات
UNWATCH                                # لغو نظارت

# مثال
MULTI
SET key1 value1
SET key2 value2
EXEC
```

---

## 📡 عملیات Pub/Sub

```bash
PUBLISH channel message                # انتشار پیام در کانال
SUBSCRIBE channel                      # اشتراک در کانال
UNSUBSCRIBE channel                    # لغو اشتراک از کانال
PSUBSCRIBE pattern                     # اشتراک در الگو
PUNSUBSCRIBE pattern                   # لغو اشتراک از الگو
PUBSUB CHANNELS [pattern]              # لیست کانال‌های فعال
PUBSUB NUMSUB [channel]                # تعداد مشترکین
PUBSUB NUMPAT                          # تعداد اشتراک‌های الگویی
```

---

## 💾 پایداری

```bash
SAVE                                   # ذخیره همزمان (مسدود می‌کند)
BGSAVE                                 # ذخیره در پس‌زمینه
LASTSAVE                               # تاریخ آخرین ذخیره
BGREWRITEAOF                           # بازنویسی فایل AOF
```

---

## 🗄️ عملیات دیتابیس

```bash
SELECT database_number                 # انتخاب دیتابیس (0-15)
FLUSHDB                                # پاک‌سازی دیتابیس فعلی
FLUSHALL                               # پاک‌سازی تمام دیتابیس‌ها
DBSIZE                                 # دریافت تعداد کلیدها
SWAPDB db1 db2                         # جابجایی دیتابیس‌ها
```

---

## 📊 مدیریت سرور

```bash
INFO                                   # اطلاعات سرور
INFO section                           # اطلاعات بخش خاص
MONITOR                                # نظارت بر دستورات به صورت لحظه‌ای
CLIENT LIST                            # لیست کلاینت‌های متصل
CLIENT KILL ip:port                    # قطع اتصال کلاینت
CONFIG GET parameter                   # دریافت تنظیمات
CONFIG SET parameter value             # تنظیم پیکربندی
CONFIG REWRITE                         # بازنویسی فایل تنظیمات
SLOWLOG GET [count]                    # دریافت کوئری‌های کند
SLOWLOG LEN                            # دریافت طول لاگ کند
SLOWLOG RESET                          # پاک‌سازی لاگ کند
TIME                                   # دریافت زمان سرور
PING                                   # پینگ سرور
ECHO message                           # نمایش پیام
QUIT                                   # بستن اتصال
SHUTDOWN [SAVE|NOSAVE]                 # خاموش کردن سرور
```

---

## 🔐 امنیت

```bash
AUTH password                          # احراز هویت با رمز عبور
ACL LIST                               # لیست قوانین ACL
ACL USERS                              # لیست کاربران
ACL SETUSER username ...               # ساخت/تغییر کاربر
ACL DELUSER username                   # حذف کاربر
ACL WHOAMI                             # دریافت کاربر فعلی
```

---

## 🔄 تکرار

```bash
REPLICAOF host port                    # تنظیم master
REPLICAOF NO ONE                       # ارتقا به master
ROLE                                   # دریافت نقش تکرار
INFO replication                       # اطلاعات تکرار
```

---

## 💡 ویژگی‌های پیشرفته

```bash
# عملیات بیت
SETBIT key offset value                # تنظیم بیت
GETBIT key offset                      # دریافت بیت
BITCOUNT key                           # شمارش بیت‌های تنظیم شده
BITOP operation destkey key [key ...]  # عملیات بیتی

# HyperLogLog
PFADD key element                      # افزودن به HyperLogLog
PFCOUNT key                            # دریافت تخمین کاردینالیتی
PFMERGE destkey sourcekey [sourcekey ...]  # ادغام HyperLogLog

# مکانی
GEOADD key longitude latitude member   # افزودن نقطه جغرافیایی
GEODIST key member1 member2 [unit]     # دریافت فاصله
GEORADIUS key long lat radius unit     # جستجو بر اساس شعاع
GEOPOS key member                      # دریافت مختصات

# Streams
XADD stream * field value              # افزودن به stream
XREAD COUNT count STREAMS stream id    # خواندن از stream
XLEN stream                            # دریافت طول stream
```

---

## 🛠️ نکات CLI

```bash
redis-cli --scan --pattern 'user:*'    # اسکن با الگو
redis-cli --bigkeys                    # یافتن کلیدهای بزرگ
redis-cli --latency                    # بررسی تاخیر
redis-cli --stat                       # نمایش آمار
redis-cli --rdb dump.rdb               # تهیه فایل RDB
redis-cli --pipe                       # حالت pipe برای درج انبوه
redis-cli SHUTDOWN SAVE                # خاموش کردن محترمانه
redis-cli --no-raw                     # غیرفعال کردن خروجی خام
```

---

## 💡 بهترین روش‌ها

- در تولید از `SCAN` به جای `KEYS` استفاده کنید
- زمان انقضای مناسب تنظیم کنید تا از مشکلات حافظه جلوگیری شود
- برای عملیات دسته‌ای از pipelining استفاده کنید
- مصرف حافظه را به طور منظم نظارت کنید
- از ساختار داده مناسب برای مورد استفاده خود استفاده کنید
- برای دوام داده، پایداری (RDB/AOF) را فعال کنید
- برای دسترسی بالا از Redis Sentinel یا Cluster استفاده کنید
- از مقادیر بزرگ اجتناب کنید (مقادیر زیر 1MB نگه دارید)
- در برنامه‌ها از connection pooling استفاده کنید
- پشتیبان‌گیری منظم از فایل‌های RDB/AOF
