# 🗄️ SQLite Cheatsheet

## 🔌 Getting Started

```bash
# Open SQLite shell
sqlite3                                # Start without database
sqlite3 database.db                    # Open/create database
sqlite3 database.db < script.sql       # Execute SQL file

# Exit SQLite
.quit
.exit

# SQLite version
sqlite3 --version
SELECT sqlite_version();
```

---

## 📊 Database Operations

```sql
-- Create database (automatically created when you open)
sqlite3 mydb.db

-- Attach additional database
ATTACH DATABASE 'other.db' AS other_db;

-- Detach database
DETACH DATABASE other_db;

-- List attached databases
.databases

-- Backup database
.backup backup.db
.backup main backup.db

-- Restore database
.restore backup.db
```

---

## 🏗️ Table Operations

```sql
-- Create table
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    email TEXT NOT NULL,
    age INTEGER,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Create table if not exists
CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY,
    name TEXT,
    price REAL
);

-- Create table from another
CREATE TABLE users_backup AS SELECT * FROM users;

-- Show tables
.tables
.tables 'user*'                        -- Pattern match

-- Show table schema
.schema users
.schema                                -- All tables

-- Describe table (using pragma)
PRAGMA table_info(users);

-- Alter table
ALTER TABLE users ADD COLUMN phone TEXT;
ALTER TABLE users RENAME TO customers;
ALTER TABLE users RENAME COLUMN age TO user_age;
ALTER TABLE users DROP COLUMN phone;   -- SQLite 3.35.0+

-- Drop table
DROP TABLE users;
DROP TABLE IF EXISTS users;

-- Truncate table (SQLite doesn't have TRUNCATE)
DELETE FROM users;
```

---

## 📝 CRUD Operations

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
DELETE FROM users;                     -- Delete all

-- UPSERT (SQLite 3.24.0+)
INSERT INTO users (id, username, email) VALUES (1, 'john', 'john@example.com')
ON CONFLICT(id) DO UPDATE SET email = excluded.email;
```

---

## 🔍 Query Operations

```sql
-- WHERE conditions
SELECT * FROM users WHERE age > 25;
SELECT * FROM users WHERE username = 'john';
SELECT * FROM users WHERE age BETWEEN 20 AND 30;
SELECT * FROM users WHERE username IN ('john', 'jane');
SELECT * FROM users WHERE email LIKE '%@gmail.com';
SELECT * FROM users WHERE age IS NULL;
SELECT * FROM users WHERE age IS NOT NULL;

-- Logical operators
SELECT * FROM users WHERE age > 25 AND username LIKE 'j%';
SELECT * FROM users WHERE age < 20 OR age > 60;
SELECT * FROM users WHERE NOT age = 30;

-- ORDER BY
SELECT * FROM users ORDER BY age ASC;
SELECT * FROM users ORDER BY age DESC;
SELECT * FROM users ORDER BY username, age DESC;

-- LIMIT and OFFSET
SELECT * FROM users LIMIT 10;
SELECT * FROM users LIMIT 10 OFFSET 20;

-- DISTINCT
SELECT DISTINCT username FROM users;

-- Aggregate functions
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

## 🔗 Joins

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

-- Multiple joins
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

## 🔑 Indexes

```sql
-- Create index
CREATE INDEX idx_username ON users(username);
CREATE INDEX idx_email ON users(email);
CREATE INDEX idx_age_username ON users(age, username);

-- Create unique index
CREATE UNIQUE INDEX idx_unique_email ON users(email);

-- Create index if not exists
CREATE INDEX IF NOT EXISTS idx_username ON users(username);

-- List indexes
.indexes
.indexes users                         -- For specific table
PRAGMA index_list(users);

-- Show index info
PRAGMA index_info(idx_username);

-- Drop index
DROP INDEX idx_username;
DROP INDEX IF EXISTS idx_username;

-- Analyze index
ANALYZE;
ANALYZE users;
```

---

## 🔐 Constraints

```sql
-- Primary key
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT
);

-- Unique constraint
CREATE TABLE users (
    email TEXT UNIQUE
);

-- NOT NULL constraint
CREATE TABLE users (
    username TEXT NOT NULL
);

-- Check constraint
CREATE TABLE users (
    age INTEGER CHECK(age >= 0 AND age <= 150)
);

-- Default value
CREATE TABLE users (
    status TEXT DEFAULT 'active',
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Foreign key
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Enable foreign keys (disabled by default)
PRAGMA foreign_keys = ON;

-- Check foreign keys
PRAGMA foreign_key_check;
PRAGMA foreign_key_check(orders);
```

---

## 🎨 Views

```sql
-- Create view
CREATE VIEW active_users AS
SELECT * FROM users WHERE status = 'active';

-- Create or replace view (drop and recreate)
DROP VIEW IF EXISTS active_users;
CREATE VIEW active_users AS
SELECT * FROM users WHERE status = 'active';

-- Query view
SELECT * FROM active_users;

-- List views
.tables                                -- Shows both tables and views
SELECT name FROM sqlite_master WHERE type = 'view';

-- Show view definition
.schema active_users

-- Drop view
DROP VIEW active_users;
DROP VIEW IF EXISTS active_users;
```

---

## 🔄 Transactions

```sql
-- Begin transaction
BEGIN TRANSACTION;
-- or
BEGIN;

-- Commit transaction
COMMIT;

-- Rollback transaction
ROLLBACK;

-- Savepoint
SAVEPOINT sp1;
RELEASE SAVEPOINT sp1;
ROLLBACK TO SAVEPOINT sp1;

-- Transaction example
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- Deferred, immediate, exclusive
BEGIN DEFERRED TRANSACTION;
BEGIN IMMEDIATE TRANSACTION;
BEGIN EXCLUSIVE TRANSACTION;
```

---

## 📅 Date and Time

```sql
-- Current date/time
SELECT date('now');                    -- Current date
SELECT time('now');                    -- Current time
SELECT datetime('now');                -- Current datetime
SELECT strftime('%Y-%m-%d %H:%M:%S', 'now');

-- Format date
SELECT strftime('%Y', 'now');          -- Year
SELECT strftime('%m', 'now');          -- Month
SELECT strftime('%d', 'now');          -- Day
SELECT strftime('%H:%M:%S', 'now');    -- Time

-- Date arithmetic
SELECT date('now', '+7 days');
SELECT date('now', '-1 month');
SELECT date('now', '+1 year');
SELECT datetime('now', '+2 hours');

-- Date functions
SELECT julianday('now');
SELECT unixepoch('now');               -- Unix timestamp
SELECT datetime(1234567890, 'unixepoch');
```

---

## 🔤 String Functions

```sql
-- String operations
SELECT length('hello');                -- 5
SELECT upper('hello');                 -- HELLO
SELECT lower('HELLO');                 -- hello
SELECT substr('hello world', 1, 5);    -- hello
SELECT trim('  hello  ');              -- hello
SELECT ltrim('  hello');               -- 'hello'
SELECT rtrim('hello  ');               -- 'hello'
SELECT replace('hello world', 'world', 'sqlite');
SELECT instr('hello world', 'world');  -- 7 (position)

-- Concatenation
SELECT 'Hello' || ' ' || 'World';      -- Hello World
SELECT printf('%s %s', 'Hello', 'World');
```

---

## 🔢 Math Functions

```sql
-- Math operations
SELECT abs(-5);                        -- 5
SELECT round(3.14159, 2);              -- 3.14
SELECT random();                       -- Random number
SELECT abs(random() % 100);            -- Random 0-99
SELECT max(10, 20, 30);                -- 30
SELECT min(10, 20, 30);                -- 10
```

---

## 📊 Pragma Commands

```sql
-- Database info
PRAGMA database_list;
PRAGMA table_info(users);
PRAGMA index_list(users);

-- Performance settings
PRAGMA cache_size = 10000;
PRAGMA page_size = 4096;
PRAGMA synchronous = NORMAL;           -- OFF, NORMAL, FULL
PRAGMA journal_mode = WAL;             -- DELETE, WAL, MEMORY

-- Foreign keys
PRAGMA foreign_keys = ON;
PRAGMA foreign_key_check;

-- Optimize database
PRAGMA optimize;
PRAGMA incremental_vacuum;
PRAGMA vacuum;

-- Show settings
PRAGMA compile_options;
```

---

## 🛠️ SQLite Shell Commands

```bash
# Dot commands (in SQLite shell)
.help                                  # Show all commands
.tables                                # List tables
.tables 'user*'                        # Pattern match
.schema users                          # Show table schema
.schema                                # Show all schemas
.indexes users                         # Show indexes
.databases                             # List databases

# Output modes
.mode csv                              # CSV output
.mode column                           # Column-aligned
.mode html                             # HTML table
.mode insert                           # INSERT statements
.mode json                             # JSON output
.mode line                             # One value per line
.mode list                             # List (default)
.mode quote                            # SQL literal quoting
.mode table                            # ASCII-art table
.mode tabs                             # Tab-separated

# Headers
.headers on                            # Show column names
.headers off                           # Hide column names

# Output to file
.output result.txt
SELECT * FROM users;
.output stdout                         # Back to screen

# Import/Export
.import file.csv users                 # Import CSV
.dump                                  # Dump database as SQL
.dump users                            # Dump specific table
.save backup.db                        # Save to file

# Read SQL file
.read script.sql

# Timing
.timer on                              # Show execution time
.timer off

# Show SQL
.echo on                               # Echo SQL commands
.echo off

# Width settings
.width 15 20 10                        # Set column widths

# Separator
.separator ','                         # Set field separator
```

---

## 💾 Backup and Restore

```bash
# Backup methods

# Method 1: Using .backup command
sqlite3 database.db ".backup backup.db"

# Method 2: Using .dump command
sqlite3 database.db .dump > backup.sql

# Method 3: File copy (database must be closed)
cp database.db backup.db

# Method 4: Using sqlite3 API
sqlite3 database.db "VACUUM INTO 'backup.db'"

# Restore from SQL dump
sqlite3 newdb.db < backup.sql
sqlite3 newdb.db ".read backup.sql"

# Restore from backup
sqlite3 newdb.db ".restore backup.db"
```

---

## 🔍 Query Optimization

```sql
-- Explain query plan
EXPLAIN QUERY PLAN SELECT * FROM users WHERE username = 'john';

-- Analyze query
EXPLAIN SELECT * FROM users WHERE username = 'john';

-- Analyze tables
ANALYZE;
ANALYZE users;

-- Optimize database
PRAGMA optimize;
VACUUM;

-- Reindex
REINDEX;
REINDEX users;
REINDEX idx_username;
```

---

## 🔐 Encryption (SQLCipher)

```sql
-- Encrypt database (requires SQLCipher)
PRAGMA key = 'your-secret-password';

-- Change password
PRAGMA rekey = 'new-password';

-- Export encrypted database
ATTACH DATABASE 'encrypted.db' AS encrypted KEY 'password';
SELECT sqlcipher_export('encrypted');
DETACH DATABASE encrypted;
```

---

## 📋 Common Patterns

```sql
-- Get last insert ID
SELECT last_insert_rowid();

-- Check if table exists
SELECT name FROM sqlite_master WHERE type='table' AND name='users';

-- Get table row count
SELECT COUNT(*) FROM users;

-- Copy table structure
CREATE TABLE users_backup AS SELECT * FROM users WHERE 1=0;

-- Find duplicate rows
SELECT username, COUNT(*) as count 
FROM users 
GROUP BY username 
HAVING count > 1;

-- Random rows
SELECT * FROM users ORDER BY RANDOM() LIMIT 10;

-- Case expression
SELECT username,
    CASE 
        WHEN age < 18 THEN 'Minor'
        WHEN age BETWEEN 18 AND 65 THEN 'Adult'
        ELSE 'Senior'
    END as age_group
FROM users;

-- Coalesce (first non-null value)
SELECT COALESCE(phone, email, 'No contact') FROM users;

-- Null handling
SELECT IFNULL(phone, 'N/A') FROM users;
SELECT NULLIF(age, 0) FROM users;       -- Return NULL if age is 0
```

---

## 💡 Best Practices

- Use transactions for multiple operations
- Create indexes on frequently queried columns
- Use EXPLAIN QUERY PLAN to optimize queries
- Enable WAL mode for better concurrency: `PRAGMA journal_mode=WAL`
- Use prepared statements to prevent SQL injection
- Regularly VACUUM the database to reclaim space
- Keep SQLite updated to the latest version
- Use appropriate data types (INTEGER, TEXT, REAL, BLOB)
- Limit result sets with LIMIT clause
- Enable foreign keys: `PRAGMA foreign_keys = ON`
- Backup regularly using .backup or VACUUM INTO
- Use WITHOUT ROWID tables for optimization when appropriate

---

## 🔗 Data Types

SQLite uses dynamic typing with storage classes:

- **NULL**: Null value
- **INTEGER**: Signed integer (1, 2, 3, 4, 6, or 8 bytes)
- **REAL**: Floating-point number (8-byte IEEE)
- **TEXT**: Text string (UTF-8, UTF-16BE, UTF-16LE)
- **BLOB**: Binary data (stored exactly as input)

Type affinity rules: INTEGER, TEXT, BLOB, REAL, NUMERIC
