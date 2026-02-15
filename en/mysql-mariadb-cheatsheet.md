# 🗄️ MySQL/MariaDB Cheatsheet

## 🔌 Connection

```bash
mysql -u username -p                   # Connect to MySQL/MariaDB
mysql -u username -p -h hostname       # Connect to remote server
mysql -u username -p database_name     # Connect to specific database
mysql -u root -p < script.sql          # Execute SQL file
mysqldump -u username -p database_name > backup.sql  # Backup database
```

---

## 📊 Database Operations

```sql
SHOW DATABASES;                        # List all databases
CREATE DATABASE database_name;         # Create new database
USE database_name;                     # Select database
DROP DATABASE database_name;           # Delete database
SHOW TABLES;                           # List all tables in current database
DESCRIBE table_name;                   # Show table structure
SHOW CREATE TABLE table_name;          # Show CREATE statement
```

---

## 🏗️ Table Operations

```sql
-- Create table
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Modify table
ALTER TABLE users ADD COLUMN age INT;          # Add column
ALTER TABLE users DROP COLUMN age;             # Remove column
ALTER TABLE users MODIFY COLUMN username VARCHAR(100);  # Modify column
ALTER TABLE users RENAME TO customers;         # Rename table
DROP TABLE table_name;                         # Delete table
TRUNCATE TABLE table_name;                     # Delete all records
```

---

## 📝 CRUD Operations

```sql
-- INSERT
INSERT INTO users (username, email) VALUES ('john', 'john@example.com');
INSERT INTO users (username, email) VALUES 
    ('jane', 'jane@example.com'),
    ('bob', 'bob@example.com');

-- SELECT
SELECT * FROM users;                           # Select all
SELECT username, email FROM users;             # Select specific columns
SELECT * FROM users WHERE id = 1;              # With condition
SELECT * FROM users ORDER BY username ASC;     # Sort results
SELECT * FROM users LIMIT 10;                  # Limit results
SELECT * FROM users LIMIT 10 OFFSET 20;        # Pagination

-- UPDATE
UPDATE users SET email = 'newemail@example.com' WHERE id = 1;
UPDATE users SET username = 'newname', email = 'new@example.com' WHERE id = 2;

-- DELETE
DELETE FROM users WHERE id = 1;
DELETE FROM users WHERE created_at < '2020-01-01';
```

---

## 🔍 Advanced Queries

```sql
-- JOIN operations
SELECT users.username, orders.order_date 
FROM users 
INNER JOIN orders ON users.id = orders.user_id;

SELECT users.username, orders.order_date 
FROM users 
LEFT JOIN orders ON users.id = orders.user_id;

-- GROUP BY and Aggregations
SELECT COUNT(*) FROM users;                    # Count records
SELECT COUNT(*), status FROM orders GROUP BY status;
SELECT AVG(price) FROM products;               # Average
SELECT MAX(price), MIN(price) FROM products;   # Max and Min
SELECT SUM(amount) FROM orders;                # Sum

-- HAVING clause
SELECT status, COUNT(*) as count 
FROM orders 
GROUP BY status 
HAVING count > 10;

-- Subqueries
SELECT * FROM users 
WHERE id IN (SELECT user_id FROM orders WHERE total > 100);
```

---

## 🔐 User Management

```sql
-- Create user
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';  # Allow from any host

-- Grant privileges
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'localhost';
GRANT SELECT, INSERT ON database_name.table_name TO 'username'@'localhost';
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost';  # All databases

-- Show privileges
SHOW GRANTS FOR 'username'@'localhost';

-- Revoke privileges
REVOKE ALL PRIVILEGES ON database_name.* FROM 'username'@'localhost';

-- Drop user
DROP USER 'username'@'localhost';

-- Change password
ALTER USER 'username'@'localhost' IDENTIFIED BY 'new_password';

-- Apply changes
FLUSH PRIVILEGES;
```

---

## 🔧 Index Management

```sql
-- Create index
CREATE INDEX idx_username ON users(username);
CREATE UNIQUE INDEX idx_email ON users(email);
CREATE INDEX idx_name_email ON users(username, email);  # Composite index

-- Show indexes
SHOW INDEX FROM table_name;

-- Drop index
DROP INDEX idx_username ON users;
```

---

## 🔗 Foreign Keys

```sql
-- Add foreign key
ALTER TABLE orders 
ADD CONSTRAINT fk_user 
FOREIGN KEY (user_id) REFERENCES users(id);

-- Add foreign key with actions
ALTER TABLE orders 
ADD CONSTRAINT fk_user 
FOREIGN KEY (user_id) REFERENCES users(id)
ON DELETE CASCADE 
ON UPDATE CASCADE;

-- Drop foreign key
ALTER TABLE orders DROP FOREIGN KEY fk_user;
```

---

## 📈 Performance and Optimization

```sql
-- Analyze query performance
EXPLAIN SELECT * FROM users WHERE username = 'john';
EXPLAIN ANALYZE SELECT * FROM users WHERE email LIKE '%@example.com';

-- Optimize table
OPTIMIZE TABLE table_name;

-- Analyze table
ANALYZE TABLE table_name;

-- Check table
CHECK TABLE table_name;

-- Repair table
REPAIR TABLE table_name;

-- Show table status
SHOW TABLE STATUS LIKE 'table_name';
```

---

## 📊 Server Management

```sql
-- Show server status
SHOW STATUS;
SHOW PROCESSLIST;                              # Show running queries
SHOW VARIABLES;                                # Show server variables
SHOW VARIABLES LIKE 'max_connections';         # Show specific variable

-- Kill query
KILL QUERY process_id;
KILL CONNECTION process_id;

-- Show storage engines
SHOW ENGINES;

-- Show character sets
SHOW CHARACTER SET;
SHOW COLLATION;
```

---

## 💾 Backup and Restore

```bash
# Backup
mysqldump -u username -p database_name > backup.sql
mysqldump -u username -p --all-databases > all_databases.sql
mysqldump -u username -p database_name table_name > table_backup.sql
mysqldump -u username -p --no-data database_name > schema_only.sql

# Backup with compression
mysqldump -u username -p database_name | gzip > backup.sql.gz

# Restore
mysql -u username -p database_name < backup.sql
gunzip < backup.sql.gz | mysql -u username -p database_name

# Copy database
mysqldump -u username -p source_db | mysql -u username -p target_db
```

---

## 🔄 Transactions

```sql
-- Start transaction
START TRANSACTION;
-- or
BEGIN;

-- Commit changes
COMMIT;

-- Rollback changes
ROLLBACK;

-- Savepoint
SAVEPOINT savepoint_name;
ROLLBACK TO SAVEPOINT savepoint_name;
RELEASE SAVEPOINT savepoint_name;

-- Transaction example
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

---

## 📅 Date and Time Functions

```sql
SELECT NOW();                                  # Current date and time
SELECT CURDATE();                              # Current date
SELECT CURTIME();                              # Current time
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d');         # Format date
SELECT DATEDIFF('2024-12-31', '2024-01-01');   # Date difference
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);        # Add days
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);      # Subtract month
SELECT YEAR(created_at), MONTH(created_at) FROM orders;
```

---

## 🔤 String Functions

```sql
SELECT CONCAT('Hello', ' ', 'World');          # Concatenate strings
SELECT UPPER('hello'), LOWER('HELLO');         # Case conversion
SELECT LENGTH('hello');                        # String length
SELECT SUBSTRING('Hello World', 1, 5);         # Extract substring
SELECT TRIM('  hello  ');                      # Remove whitespace
SELECT REPLACE('Hello World', 'World', 'MySQL');  # Replace text
```

---

## 💡 Tips and Best Practices

- Always backup before major operations
- Use transactions for critical operations
- Create indexes on frequently queried columns
- Use prepared statements to prevent SQL injection
- Regularly optimize and analyze tables
- Monitor slow queries with slow query log
- Use appropriate data types to save space
- Avoid SELECT * in production queries
