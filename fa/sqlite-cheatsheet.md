# 🗄️ SQLite Cheatsheet

## 🔌 شروع کار

```bash
# باز کردن شل SQLite
sqlite3                                # شروع بدون دیتابیس
sqlite3 database.db                    # باز کردن/ساخت دیتابیس
sqlite3 database.db < script.sql       # اجرای فایل SQL

# خروج از SQLite
.quit
.exit

# نسخه SQLite
sqlite3 --version
SELECT sqlite_version();
```

---

## 📊 عملیات دیتابیس

```sql
-- ساخت دیتابیس (به صورت خودکار هنگام باز کردن ساخته می‌شود)
sqlite3 mydb.db

-- متصل کردن دیتابیس اضافی
ATTACH DATABASE 'other.db' AS other_db;

-- جدا کردن دیتابیس
DETACH DATABASE other_db;

-- لیست دیتابیس‌های متصل
.databases

-- پشتیبان‌گیری از دیتابیس
.backup backup.db
.backup main backup.db

-- بازیابی دیتابیس
.restore backup.db
```

---

## 🏗️ عملیات جدول

```sql
-- ساخت جدول
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    email TEXT NOT NULL,
    age INTEGER,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- ساخت جدول اگر وجود نداشته باشد
CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY,
    name TEXT,
    price REAL
);

-- ساخت جدول از جدول دیگر
CREATE TABLE users_backup AS SELECT * FROM users;

-- نمایش جداول
.tables
.tables 'user*'                        -- مطابقت الگو

-- نمایش ساختار جدول
.schema users
.schema                                -- تمام جداول

-- توضیح جدول (استفاده از pragma)
PRAGMA table_info(users);

-- تغییر جدول
ALTER TABLE users ADD COLUMN phone TEXT;
ALTER TABLE users RENAME TO customers;
ALTER TABLE users RENAME COLUMN age TO user_age;
ALTER TABLE users DROP COLUMN phone;   -- SQLite 3.35.0+

-- حذف جدول
DROP TABLE users;
DROP TABLE IF EXISTS users;

-- خالی کردن جدول (SQLite دستور TRUNCATE ندارد)
DELETE FROM users;
```

---

## 📝 عملیات CRUD

```sql
-- INSERT
INSERT INTO users (username, email, age) VALUES ('john', 'john@example.com', 30);

INSERT INTO users (username, email) VALUES 
    ('jane', 'jane@example.com'),
    ('bob', 'bob@example.com');

-- INSERT or REPLACE
INSERT OR REPLACE INTO users (id, username, email) VALUES (1, 'john', 'new@example.com');
REPLACE INTO users (id, username, email) VALUES (1, 'john', 'new@example.com');

-- INSERT or IGNORE
INSERT OR IGNORE INTO users (username, email) VALUES ('john', 'john@example.com');

-- SELECT
SELECT * FROM users;
SELECT username, email FROM users;
SELECT * FROM users WHERE age > 25;
SELECT * FROM users ORDER BY username ASC;
SELECT * FROM users LIMIT 10;
SELECT * FROM users LIMIT 10 OFFSET 20;
SELECT DISTINCT username FROM users;

-- UPDATE
UPDATE users SET email = 'newemail@example.com' WHERE id = 1;
UPDATE users SET username = 'newname', age = 35 WHERE id = 2;

-- DELETE
DELETE FROM users WHERE id = 1;
DELETE FROM users WHERE age < 18;
DELETE FROM users;                     -- حذف همه

-- UPSERT (SQLite 3.24.0+)
INSERT INTO users (id, username, email) VALUES (1, 'john', 'john@example.com')
ON CONFLICT(id) DO UPDATE SET email = excluded.email;
```

---

## 🔍 عملیات کوئری

```sql
-- شرایط WHERE
SELECT * FROM users WHERE age > 25;
SELECT * FROM users WHERE username = 'john';
SELECT * FROM users WHERE age BETWEEN 20 AND 30;
SELECT * FROM users WHERE username IN ('john', 'jane');
SELECT * FROM users WHERE email LIKE '%@gmail.com';
SELECT * FROM users WHERE age IS NULL;
SELECT * FROM users WHERE age IS NOT NULL;

-- عملگرهای منطقی
SELECT * FROM users WHERE age > 25 AND username LIKE 'j%';
SELECT * FROM users WHERE age < 20 OR age > 60;
SELECT * FROM users WHERE NOT age = 30;

-- ORDER BY
SELECT * FROM users ORDER BY age ASC;
SELECT * FROM users ORDER BY age DESC;
SELECT * FROM users ORDER BY username, age DESC;

-- LIMIT و OFFSET
SELECT * FROM users LIMIT 10;
SELECT * FROM users LIMIT 10 OFFSET 20;

-- DISTINCT
SELECT DISTINCT username FROM users;

-- توابع تجمیعی
SELECT COUNT(*) FROM users;
SELECT COUNT(DISTINCT username) FROM users;
SELECT AVG(age) FROM users;
SELECT MAX(age), MIN(age) FROM users;
SELECT SUM(age) FROM users;

-- GROUP BY
SELECT username, COUNT(*) FROM orders GROUP BY username;
SELECT age, COUNT(*) as count FROM users GROUP BY age;
SELECT age, AVG(score) FROM users GROUP BY age HAVING AVG(score) > 75;

-- Subqueries
SELECT * FROM users WHERE id IN (SELECT user_id FROM orders WHERE total > 100);
SELECT * FROM users WHERE age > (SELECT AVG(age) FROM users);
```

---

## 🔗 پیوندها

```sql
-- INNER JOIN
SELECT users.username, orders.order_date 
FROM users 
INNER JOIN orders ON users.id = orders.user_id;

-- LEFT JOIN
SELECT users.username, orders.order_date 
FROM users 
LEFT JOIN orders ON users.id = orders.user_id;

-- CROSS JOIN
SELECT * FROM table1 CROSS JOIN table2;

-- چند پیوند
SELECT u.username, o.order_date, p.product_name
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN products p ON o.product_id = p.id;

-- Self join
SELECT a.username, b.username as referrer
FROM users a
LEFT JOIN users b ON a.referrer_id = b.id;
```

---

## 🔑 ایندکس‌ها

```sql
-- ساخت ایندکس
CREATE INDEX idx_username ON users(username);
CREATE INDEX idx_email ON users(email);
CREATE INDEX idx_age_username ON users(age, username);

-- ساخت ایندکس یکتا
CREATE UNIQUE INDEX idx_unique_email ON users(email);

-- ساخت ایندکس اگر وجود نداشته باشد
CREATE INDEX IF NOT EXISTS idx_username ON users(username);

-- لیست ایندکس‌ها
.indexes
.indexes users                         -- برای جدول خاص
PRAGMA index_list(users);

-- نمایش اطلاعات ایندکس
PRAGMA index_info(idx_username);

-- حذف ایندکس
DROP INDEX idx_username;
DROP INDEX IF EXISTS idx_username;

-- تحلیل ایندکس
ANALYZE;
ANALYZE users;
```

---

## 🔐 محدودیت‌ها

```sql
-- کلید اصلی
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT
);

-- محدودیت یکتا
CREATE TABLE users (
    email TEXT UNIQUE
);

-- محدودیت NOT NULL
CREATE TABLE users (
    username TEXT NOT NULL
);

-- محدودیت بررسی
CREATE TABLE users (
    age INTEGER CHECK(age >= 0 AND age <= 150)
);

-- مقدار پیش‌فرض
CREATE TABLE users (
    status TEXT DEFAULT 'active',
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- کلید خارجی
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- فعال‌سازی کلیدهای خارجی (پیش‌فرض غیرفعال است)
PRAGMA foreign_keys = ON;

-- بررسی کلیدهای خارجی
PRAGMA foreign_key_check;
PRAGMA foreign_key_check(orders);
```

---

## 🎨 نماها

```sql
-- ساخت نما
CREATE VIEW active_users AS
SELECT * FROM users WHERE status = 'active';

-- ساخت یا جایگزینی نما (حذف و ساخت مجدد)
DROP VIEW IF EXISTS active_users;
CREATE VIEW active_users AS
SELECT * FROM users WHERE status = 'active';

-- کوئری نما
SELECT * FROM active_users;

-- لیست نماها
.tables                                -- نمایش جداول و نماها
SELECT name FROM sqlite_master WHERE type = 'view';

-- نمایش تعریف نما
.schema active_users

-- حذف نما
DROP VIEW active_users;
DROP VIEW IF EXISTS active_users;
```

---

## 🔄 تراکنش‌ها

```sql
-- شروع تراکنش
BEGIN TRANSACTION;
-- یا
BEGIN;

-- تایید تراکنش
COMMIT;

-- برگشت تراکنش
ROLLBACK;

-- نقطه ذخیره
SAVEPOINT sp1;
RELEASE SAVEPOINT sp1;
ROLLBACK TO SAVEPOINT sp1;

-- مثال تراکنش
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- تاخیری، فوری، انحصاری
BEGIN DEFERRED TRANSACTION;
BEGIN IMMEDIATE TRANSACTION;
BEGIN EXCLUSIVE TRANSACTION;
```

---

## 📅 تاریخ و زمان

```sql
-- تاریخ/زمان فعلی
SELECT date('now');                    -- تاریخ فعلی
SELECT time('now');                    -- زمان فعلی
SELECT datetime('now');                -- تاریخ‌زمان فعلی
SELECT strftime('%Y-%m-%d %H:%M:%S', 'now');

-- فرمت تاریخ
SELECT strftime('%Y', 'now');          -- سال
SELECT strftime('%m', 'now');          -- ماه
SELECT strftime('%d', 'now');          -- روز
SELECT strftime('%H:%M:%S', 'now');    -- زمان

-- محاسبات تاریخ
SELECT date('now', '+7 days');
SELECT date('now', '-1 month');
SELECT date('now', '+1 year');
SELECT datetime('now', '+2 hours');

-- توابع تاریخ
SELECT julianday('now');
SELECT unixepoch('now');               -- Unix timestamp
SELECT datetime(1234567890, 'unixepoch');
```

---

## 🔤 توابع رشته‌ای

```sql
-- عملیات رشته‌ای
SELECT length('hello');                -- 5
SELECT upper('hello');                 -- HELLO
SELECT lower('HELLO');                 -- hello
SELECT substr('hello world', 1, 5);    -- hello
SELECT trim('  hello  ');              -- hello
SELECT ltrim('  hello');               -- 'hello'
SELECT rtrim('hello  ');               -- 'hello'
SELECT replace('hello world', 'world', 'sqlite');
SELECT instr('hello world', 'world');  -- 7 (موقعیت)

-- ترکیب رشته‌ها
SELECT 'Hello' || ' ' || 'World';      -- Hello World
SELECT printf('%s %s', 'Hello', 'World');
```

---

## 🔢 توابع ریاضی

```sql
-- عملیات ریاضی
SELECT abs(-5);                        -- 5
SELECT round(3.14159, 2);              -- 3.14
SELECT random();                       -- عدد تصادفی
SELECT abs(random() % 100);            -- تصادفی 0-99
SELECT max(10, 20, 30);                -- 30
SELECT min(10, 20, 30);                -- 10
```

---

## 📊 دستورات Pragma

```sql
-- اطلاعات دیتابیس
PRAGMA database_list;
PRAGMA table_info(users);
PRAGMA index_list(users);

-- تنظیمات کارایی
PRAGMA cache_size = 10000;
PRAGMA page_size = 4096;
PRAGMA synchronous = NORMAL;           -- OFF, NORMAL, FULL
PRAGMA journal_mode = WAL;             -- DELETE, WAL, MEMORY

-- کلیدهای خارجی
PRAGMA foreign_keys = ON;
PRAGMA foreign_key_check;

-- بهینه‌سازی دیتابیس
PRAGMA optimize;
PRAGMA incremental_vacuum;
PRAGMA vacuum;

-- نمایش تنظیمات
PRAGMA compile_options;
```

---

## 🛠️ دستورات شل SQLite

```bash
# دستورات نقطه (در شل SQLite)
.help                                  # نمایش تمام دستورات
.tables                                # لیست جداول
.tables 'user*'                        # مطابقت الگو
.schema users                          # نمایش ساختار جدول
.schema                                # نمایش تمام ساختارها
.indexes users                         # نمایش ایندکس‌ها
.databases                             # لیست دیتابیس‌ها

# حالت‌های خروجی
.mode csv                              # خروجی CSV
.mode column                           # ستون‌های تراز شده
.mode html                             # جدول HTML
.mode insert                           # دستورات INSERT
.mode json                             # خروجی JSON
.mode line                             # یک مقدار در هر خط
.mode list                             # لیست (پیش‌فرض)
.mode quote                            # نقل قول SQL
.mode table                            # جدول ASCII
.mode tabs                             # جدا شده با تب

# هدرها
.headers on                            # نمایش نام ستون‌ها
.headers off                           # مخفی کردن نام ستون‌ها

# خروجی به فایل
.output result.txt
SELECT * FROM users;
.output stdout                         # برگشت به صفحه

# Import/Export
.import file.csv users                 # وارد کردن CSV
.dump                                  # دامپ دیتابیس به صورت SQL
.dump users                            # دامپ جدول خاص
.save backup.db                        # ذخیره در فایل

# خواندن فایل SQL
.read script.sql

# زمان‌سنجی
.timer on                              # نمایش زمان اجرا
.timer off

# نمایش SQL
.echo on                               # نمایش دستورات SQL
.echo off

# تنظیمات عرض
.width 15 20 10                        # تنظیم عرض ستون‌ها

# جداکننده
.separator ','                         # تنظیم جداکننده فیلد
```

---

## 💾 پشتیبان‌گیری و بازیابی

```bash
# روش‌های پشتیبان‌گیری

# روش 1: استفاده از دستور .backup
sqlite3 database.db ".backup backup.db"

# روش 2: استفاده از دستور .dump
sqlite3 database.db .dump > backup.sql

# روش 3: کپی فایل (دیتابیس باید بسته باشد)
cp database.db backup.db

# روش 4: استفاده از API sqlite3
sqlite3 database.db "VACUUM INTO 'backup.db'"

# بازیابی از دامپ SQL
sqlite3 newdb.db < backup.sql
sqlite3 newdb.db ".read backup.sql"

# بازیابی از backup
sqlite3 newdb.db ".restore backup.db"
```

---

## 🔍 بهینه‌سازی کوئری

```sql
-- توضیح برنامه کوئری
EXPLAIN QUERY PLAN SELECT * FROM users WHERE username = 'john';

-- تحلیل کوئری
EXPLAIN SELECT * FROM users WHERE username = 'john';

-- تحلیل جداول
ANALYZE;
ANALYZE users;

-- بهینه‌سازی دیتابیس
PRAGMA optimize;
VACUUM;

-- ایندکس مجدد
REINDEX;
REINDEX users;
REINDEX idx_username;
```

---

## 🔐 رمزنگاری (SQLCipher)

```sql
-- رمزنگاری دیتابیس (نیاز به SQLCipher)
PRAGMA key = 'your-secret-password';

-- تغییر رمز عبور
PRAGMA rekey = 'new-password';

-- صادرات دیتابیس رمزنگاری شده
ATTACH DATABASE 'encrypted.db' AS encrypted KEY 'password';
SELECT sqlcipher_export('encrypted');
DETACH DATABASE encrypted;
```

---

## 📋 الگوهای رایج

```sql
-- دریافت آخرین ID درج شده
SELECT last_insert_rowid();

-- بررسی وجود جدول
SELECT name FROM sqlite_master WHERE type='table' AND name='users';

-- شمارش ردیف‌های جدول
SELECT COUNT(*) FROM users;

-- کپی ساختار جدول
CREATE TABLE users_backup AS SELECT * FROM users WHERE 1=0;

-- یافتن ردیف‌های تکراری
SELECT username, COUNT(*) as count 
FROM users 
GROUP BY username 
HAVING count > 1;

-- ردیف‌های تصادفی
SELECT * FROM users ORDER BY RANDOM() LIMIT 10;

-- عبارت Case
SELECT username,
    CASE 
        WHEN age < 18 THEN 'نوجوان'
        WHEN age BETWEEN 18 AND 65 THEN 'بزرگسال'
        ELSE 'سالمند'
    END as age_group
FROM users;

-- Coalesce (اولین مقدار غیر null)
SELECT COALESCE(phone, email, 'بدون تماس') FROM users;

-- مدیریت Null
SELECT IFNULL(phone, 'N/A') FROM users;
SELECT NULLIF(age, 0) FROM users;       -- برگرداندن NULL اگر age صفر باشد
```

---

## 💡 بهترین روش‌ها

- از تراکنش برای عملیات چندگانه استفاده کنید
- روی ستون‌های پرکاربرد ایندکس بسازید
- از EXPLAIN QUERY PLAN برای بهینه‌سازی کوئری استفاده کنید
- حالت WAL را برای همزمانی بهتر فعال کنید: `PRAGMA journal_mode=WAL`
- از prepared statements برای جلوگیری از SQL injection استفاده کنید
- به طور منظم VACUUM برای بازیابی فضا اجرا کنید
- SQLite را به آخرین نسخه به‌روز نگه دارید
- از نوع داده مناسب استفاده کنید (INTEGER, TEXT, REAL, BLOB)
- نتایج را با LIMIT محدود کنید
- کلیدهای خارجی را فعال کنید: `PRAGMA foreign_keys = ON`
- پشتیبان‌گیری منظم با .backup یا VACUUM INTO
- از جداول WITHOUT ROWID برای بهینه‌سازی استفاده کنید

---

## 🔗 نوع‌های داده

SQLite از تایپ پویا با کلاس‌های ذخیره‌سازی استفاده می‌کند:

- **NULL**: مقدار Null
- **INTEGER**: عدد صحیح علامت‌دار (1، 2، 3، 4، 6 یا 8 بایت)
- **REAL**: عدد اعشاری (8-byte IEEE)
- **TEXT**: رشته متنی (UTF-8, UTF-16BE, UTF-16LE)
- **BLOB**: داده باینری (دقیقاً همانطور که وارد شده ذخیره می‌شود)

قوانین تمایل نوع: INTEGER, TEXT, BLOB, REAL, NUMERIC
