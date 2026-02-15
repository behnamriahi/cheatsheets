# 🗄️ MySQL/MariaDB Cheatsheet

## 🔌 اتصال

```bash
mysql -u username -p                   # اتصال به MySQL/MariaDB
mysql -u username -p -h hostname       # اتصال به سرور راه دور
mysql -u username -p database_name     # اتصال به دیتابیس خاص
mysql -u root -p < script.sql          # اجرای فایل SQL
mysqldump -u username -p database_name > backup.sql  # پشتیبان‌گیری از دیتابیس
```

---

## 📊 عملیات دیتابیس

```sql
SHOW DATABASES;                        # نمایش تمام دیتابیس‌ها
CREATE DATABASE database_name;         # ساخت دیتابیس جدید
USE database_name;                     # انتخاب دیتابیس
DROP DATABASE database_name;           # حذف دیتابیس
SHOW TABLES;                           # نمایش تمام جداول در دیتابیس فعلی
DESCRIBE table_name;                   # نمایش ساختار جدول
SHOW CREATE TABLE table_name;          # نمایش دستور CREATE
```

---

## 🏗️ عملیات جدول

```sql
-- ساخت جدول
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- تغییر جدول
ALTER TABLE users ADD COLUMN age INT;          # افزودن ستون
ALTER TABLE users DROP COLUMN age;             # حذف ستون
ALTER TABLE users MODIFY COLUMN username VARCHAR(100);  # تغییر ستون
ALTER TABLE users RENAME TO customers;         # تغییر نام جدول
DROP TABLE table_name;                         # حذف جدول
TRUNCATE TABLE table_name;                     # حذف تمام رکوردها
```

---

## 📝 عملیات CRUD

```sql
-- INSERT
INSERT INTO users (username, email) VALUES ('john', 'john@example.com');
INSERT INTO users (username, email) VALUES 
    ('jane', 'jane@example.com'),
    ('bob', 'bob@example.com');

-- SELECT
SELECT * FROM users;                           # انتخاب همه
SELECT username, email FROM users;             # انتخاب ستون‌های خاص
SELECT * FROM users WHERE id = 1;              # با شرط
SELECT * FROM users ORDER BY username ASC;     # مرتب‌سازی نتایج
SELECT * FROM users LIMIT 10;                  # محدود کردن نتایج
SELECT * FROM users LIMIT 10 OFFSET 20;        # صفحه‌بندی

-- UPDATE
UPDATE users SET email = 'newemail@example.com' WHERE id = 1;
UPDATE users SET username = 'newname', email = 'new@example.com' WHERE id = 2;

-- DELETE
DELETE FROM users WHERE id = 1;
DELETE FROM users WHERE created_at < '2020-01-01';
```

---

## 🔍 کوئری‌های پیشرفته

```sql
-- عملیات JOIN
SELECT users.username, orders.order_date 
FROM users 
INNER JOIN orders ON users.id = orders.user_id;

SELECT users.username, orders.order_date 
FROM users 
LEFT JOIN orders ON users.id = orders.user_id;

-- GROUP BY و توابع تجمیعی
SELECT COUNT(*) FROM users;                    # شمارش رکوردها
SELECT COUNT(*), status FROM orders GROUP BY status;
SELECT AVG(price) FROM products;               # میانگین
SELECT MAX(price), MIN(price) FROM products;   # بیشترین و کمترین
SELECT SUM(amount) FROM orders;                # مجموع

-- بند HAVING
SELECT status, COUNT(*) as count 
FROM orders 
GROUP BY status 
HAVING count > 10;

-- Subqueries
SELECT * FROM users 
WHERE id IN (SELECT user_id FROM orders WHERE total > 100);
```

---

## 🔐 مدیریت کاربران

```sql
-- ساخت کاربر
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';  # اجازه از هر هاست

-- اعطای دسترسی
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'localhost';
GRANT SELECT, INSERT ON database_name.table_name TO 'username'@'localhost';
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost';  # تمام دیتابیس‌ها

-- نمایش دسترسی‌ها
SHOW GRANTS FOR 'username'@'localhost';

-- لغو دسترسی
REVOKE ALL PRIVILEGES ON database_name.* FROM 'username'@'localhost';

-- حذف کاربر
DROP USER 'username'@'localhost';

-- تغییر رمز عبور
ALTER USER 'username'@'localhost' IDENTIFIED BY 'new_password';

-- اعمال تغییرات
FLUSH PRIVILEGES;
```

---

## 🔧 مدیریت ایندکس

```sql
-- ساخت ایندکس
CREATE INDEX idx_username ON users(username);
CREATE UNIQUE INDEX idx_email ON users(email);
CREATE INDEX idx_name_email ON users(username, email);  # ایندکس ترکیبی

-- نمایش ایندکس‌ها
SHOW INDEX FROM table_name;

-- حذف ایندکس
DROP INDEX idx_username ON users;
```

---

## 🔗 کلیدهای خارجی

```sql
-- افزودن کلید خارجی
ALTER TABLE orders 
ADD CONSTRAINT fk_user 
FOREIGN KEY (user_id) REFERENCES users(id);

-- افزودن کلید خارجی با اکشن‌ها
ALTER TABLE orders 
ADD CONSTRAINT fk_user 
FOREIGN KEY (user_id) REFERENCES users(id)
ON DELETE CASCADE 
ON UPDATE CASCADE;

-- حذف کلید خارجی
ALTER TABLE orders DROP FOREIGN KEY fk_user;
```

---

## 📈 کارایی و بهینه‌سازی

```sql
-- تحلیل کارایی کوئری
EXPLAIN SELECT * FROM users WHERE username = 'john';
EXPLAIN ANALYZE SELECT * FROM users WHERE email LIKE '%@example.com';

-- بهینه‌سازی جدول
OPTIMIZE TABLE table_name;

-- تحلیل جدول
ANALYZE TABLE table_name;

-- بررسی جدول
CHECK TABLE table_name;

-- تعمیر جدول
REPAIR TABLE table_name;

-- نمایش وضعیت جدول
SHOW TABLE STATUS LIKE 'table_name';
```

---

## 📊 مدیریت سرور

```sql
-- نمایش وضعیت سرور
SHOW STATUS;
SHOW PROCESSLIST;                              # نمایش کوئری‌های در حال اجرا
SHOW VARIABLES;                                # نمایش متغیرهای سرور
SHOW VARIABLES LIKE 'max_connections';         # نمایش متغیر خاص

-- کشتن کوئری
KILL QUERY process_id;
KILL CONNECTION process_id;

-- نمایش موتورهای ذخیره‌سازی
SHOW ENGINES;

-- نمایش مجموعه کاراکترها
SHOW CHARACTER SET;
SHOW COLLATION;
```

---

## 💾 پشتیبان‌گیری و بازیابی

```bash
# پشتیبان‌گیری
mysqldump -u username -p database_name > backup.sql
mysqldump -u username -p --all-databases > all_databases.sql
mysqldump -u username -p database_name table_name > table_backup.sql
mysqldump -u username -p --no-data database_name > schema_only.sql

# پشتیبان‌گیری با فشرده‌سازی
mysqldump -u username -p database_name | gzip > backup.sql.gz

# بازیابی
mysql -u username -p database_name < backup.sql
gunzip < backup.sql.gz | mysql -u username -p database_name

# کپی دیتابیس
mysqldump -u username -p source_db | mysql -u username -p target_db
```

---

## 🔄 تراکنش‌ها

```sql
-- شروع تراکنش
START TRANSACTION;
-- یا
BEGIN;

-- تایید تغییرات
COMMIT;

-- برگشت تغییرات
ROLLBACK;

-- نقطه ذخیره
SAVEPOINT savepoint_name;
ROLLBACK TO SAVEPOINT savepoint_name;
RELEASE SAVEPOINT savepoint_name;

-- مثال تراکنش
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

---

## 📅 توابع تاریخ و زمان

```sql
SELECT NOW();                                  # تاریخ و زمان فعلی
SELECT CURDATE();                              # تاریخ فعلی
SELECT CURTIME();                              # زمان فعلی
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d');         # فرمت تاریخ
SELECT DATEDIFF('2024-12-31', '2024-01-01');   # اختلاف تاریخ
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);        # افزودن روز
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);      # کم کردن ماه
SELECT YEAR(created_at), MONTH(created_at) FROM orders;
```

---

## 🔤 توابع رشته‌ای

```sql
SELECT CONCAT('Hello', ' ', 'World');          # ترکیب رشته‌ها
SELECT UPPER('hello'), LOWER('HELLO');         # تبدیل حالت
SELECT LENGTH('hello');                        # طول رشته
SELECT SUBSTRING('Hello World', 1, 5);         # استخراج زیررشته
SELECT TRIM('  hello  ');                      # حذف فضای خالی
SELECT REPLACE('Hello World', 'World', 'MySQL');  # جایگزینی متن
```

---

## 💡 نکات و بهترین روش‌ها

- همیشه قبل از عملیات مهم پشتیبان‌گیری کنید
- از تراکنش برای عملیات حساس استفاده کنید
- روی ستون‌های پرکاربرد ایندکس بسازید
- از prepared statements برای جلوگیری از SQL injection استفاده کنید
- به طور منظم جداول را بهینه‌سازی کنید
- کوئری‌های کند را با slow query log نظارت کنید
- از نوع داده مناسب برای صرفه‌جویی در فضا استفاده کنید
- از SELECT * در کوئری‌های تولید اجتناب کنید
