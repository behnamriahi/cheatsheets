# 🔍 Elasticsearch Cheatsheet

## 🔌 اتصال و پایه‌ها

```bash
# بررسی سلامت کلاستر
curl -X GET "localhost:9200/_cluster/health?pretty"

# دریافت اطلاعات کلاستر
curl -X GET "localhost:9200"

# لیست تمام نودها
curl -X GET "localhost:9200/_cat/nodes?v"

# لیست تمام ایندکس‌ها
curl -X GET "localhost:9200/_cat/indices?v"

# بررسی آمار کلاستر
curl -X GET "localhost:9200/_cluster/stats?pretty"
```

---

## 📇 مدیریت ایندکس

```bash
# ساخت ایندکس
curl -X PUT "localhost:9200/my_index?pretty"

# ساخت ایندکس با تنظیمات
curl -X PUT "localhost:9200/my_index?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 2
  }
}'

# حذف ایندکس
curl -X DELETE "localhost:9200/my_index?pretty"

# دریافت تنظیمات ایندکس
curl -X GET "localhost:9200/my_index/_settings?pretty"

# به‌روزرسانی تنظیمات ایندکس
curl -X PUT "localhost:9200/my_index/_settings?pretty" -H 'Content-Type: application/json' -d'
{
  "index": {
    "number_of_replicas": 1
  }
}'

# بستن ایندکس
curl -X POST "localhost:9200/my_index/_close?pretty"

# باز کردن ایندکس
curl -X POST "localhost:9200/my_index/_open?pretty"

# بررسی وجود ایندکس
curl -I "localhost:9200/my_index"
```

---

## 🗺️ Mapping

```bash
# تعریف mapping
curl -X PUT "localhost:9200/my_index?pretty" -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "author": { "type": "keyword" },
      "publish_date": { "type": "date" },
      "pages": { "type": "integer" },
      "price": { "type": "float" }
    }
  }
}'

# دریافت mapping
curl -X GET "localhost:9200/my_index/_mapping?pretty"

# افزودن فیلد به mapping موجود
curl -X PUT "localhost:9200/my_index/_mapping?pretty" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "isbn": { "type": "keyword" }
  }
}'
```

---

## 📝 عملیات سند

```bash
# ایندکس سند (تولید خودکار ID)
curl -X POST "localhost:9200/my_index/_doc?pretty" -H 'Content-Type: application/json' -d'
{
  "title": "راهنمای Elasticsearch",
  "author": "جان دو",
  "pages": 350
}'

# ایندکس سند (با ID مشخص)
curl -X PUT "localhost:9200/my_index/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "title": "راهنمای Elasticsearch",
  "author": "جان دو",
  "pages": 350
}'

# دریافت سند
curl -X GET "localhost:9200/my_index/_doc/1?pretty"

# به‌روزرسانی سند
curl -X POST "localhost:9200/my_index/_update/1?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": {
    "pages": 375
  }
}'

# حذف سند
curl -X DELETE "localhost:9200/my_index/_doc/1?pretty"

# عملیات دسته‌ای
curl -X POST "localhost:9200/_bulk?pretty" -H 'Content-Type: application/json' -d'
{ "index": { "_index": "my_index", "_id": "1" }}
{ "title": "کتاب 1", "author": "نویسنده 1" }
{ "index": { "_index": "my_index", "_id": "2" }}
{ "title": "کتاب 2", "author": "نویسنده 2" }
{ "delete": { "_index": "my_index", "_id": "3" }}
'
```

---

## 🔍 عملیات جستجو

```bash
# جستجوی تمام اسناد
curl -X GET "localhost:9200/my_index/_search?pretty"

# کوئری match
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "title": "elasticsearch"
    }
  }
}'

# انتخاب همه
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  }
}'

# کوئری term (مطابقت دقیق)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "term": {
      "author.keyword": "جان دو"
    }
  }
}'

# کوئری بازه
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "range": {
      "pages": {
        "gte": 200,
        "lte": 500
      }
    }
  }
}'

# کوئری Bool (must, should, must_not)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": [
        { "match": { "title": "elasticsearch" }}
      ],
      "filter": [
        { "range": { "pages": { "gte": 100 }}}
      ],
      "must_not": [
        { "term": { "author.keyword": "نامشخص" }}
      ]
    }
  }
}'

# کوئری Wildcard
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "wildcard": {
      "title": "*راهنما*"
    }
  }
}'

# کوئری Fuzzy (تحمل خطای تایپی)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "fuzzy": {
      "title": {
        "value": "elasticseerch",
        "fuzziness": "AUTO"
      }
    }
  }
}'
```

---

## 🎯 فیلتر و مرتب‌سازی

```bash
# مرتب‌سازی نتایج
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "pages": { "order": "desc" }}
  ]
}'

# صفحه‌بندی
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 20
}'

# فیلتر منبع (انتخاب فیلدها)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "_source": ["title", "author"]
}'
```

---

## 📊 تجمیع‌ها (Aggregations)

```bash
# تجمیع terms (گروه‌بندی)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "authors": {
      "terms": {
        "field": "author.keyword",
        "size": 10
      }
    }
  }
}'

# تجمیع آماری
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "pages_stats": {
      "stats": {
        "field": "pages"
      }
    }
  }
}'

# هیستوگرام تاریخ
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "by_month": {
      "date_histogram": {
        "field": "publish_date",
        "calendar_interval": "month"
      }
    }
  }
}'

# تجمیع‌های تو در تو
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "authors": {
      "terms": {
        "field": "author.keyword"
      },
      "aggs": {
        "avg_pages": {
          "avg": {
            "field": "pages"
          }
        }
      }
    }
  }
}'
```

---

## 🔧 مدیریت کلاستر

```bash
# دریافت تنظیمات کلاستر
curl -X GET "localhost:9200/_cluster/settings?pretty"

# به‌روزرسانی تنظیمات کلاستر
curl -X PUT "localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": "all"
  }
}'

# دریافت اطلاعات نود
curl -X GET "localhost:9200/_nodes?pretty"

# دریافت آمار نود
curl -X GET "localhost:9200/_nodes/stats?pretty"

# مسیریابی مجدد شاردها
curl -X POST "localhost:9200/_cluster/reroute?pretty"

# دریافت تخصیص شارد
curl -X GET "localhost:9200/_cat/shards?v"

# توضیح تخصیص
curl -X GET "localhost:9200/_cluster/allocation/explain?pretty"
```

---

## 📊 نظارت

```bash
# آمار ایندکس
curl -X GET "localhost:9200/my_index/_stats?pretty"

# اطلاعات segments
curl -X GET "localhost:9200/my_index/_segments?pretty"

# اطلاعات بازیابی
curl -X GET "localhost:9200/_cat/recovery?v"

# آمار thread pool
curl -X GET "localhost:9200/_cat/thread_pool?v"

# وظایف در انتظار
curl -X GET "localhost:9200/_cat/pending_tasks?v"

# Thread های داغ
curl -X GET "localhost:9200/_nodes/hot_threads"
```

---

## 🔄 Snapshot و بازیابی

```bash
# ثبت مخزن snapshot
curl -X PUT "localhost:9200/_snapshot/my_backup?pretty" -H 'Content-Type: application/json' -d'
{
  "type": "fs",
  "settings": {
    "location": "/mount/backups/my_backup"
  }
}'

# ساخت snapshot
curl -X PUT "localhost:9200/_snapshot/my_backup/snapshot_1?wait_for_completion=true&pretty"

# لیست snapshot ها
curl -X GET "localhost:9200/_snapshot/my_backup/_all?pretty"

# بازیابی snapshot
curl -X POST "localhost:9200/_snapshot/my_backup/snapshot_1/_restore?pretty"

# حذف snapshot
curl -X DELETE "localhost:9200/_snapshot/my_backup/snapshot_1?pretty"
```

---

## 🎨 تحلیلگرها و Tokenizer ها

```bash
# تست analyzer
curl -X POST "localhost:9200/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "analyzer": "standard",
  "text": "The Quick Brown Fox"
}'

# analyzer سفارشی
curl -X PUT "localhost:9200/my_index?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "stop"]
        }
      }
    }
  }
}'
```

---

## 🔐 امنیت (X-Pack)

```bash
# ساخت کاربر
curl -X POST "localhost:9200/_security/user/johndoe?pretty" -u elastic:password -H 'Content-Type: application/json' -d'
{
  "password": "userpassword",
  "roles": ["kibana_admin", "monitoring_user"],
  "full_name": "جان دو"
}'

# ساخت نقش
curl -X POST "localhost:9200/_security/role/my_role?pretty" -u elastic:password -H 'Content-Type: application/json' -d'
{
  "cluster": ["all"],
  "indices": [
    {
      "names": ["my_index"],
      "privileges": ["read", "write"]
    }
  ]
}'

# دریافت کاربران
curl -X GET "localhost:9200/_security/user?pretty" -u elastic:password
```

---

## 🛠️ الگوهای ایندکس

```bash
# ساخت الگوی ایندکس
curl -X PUT "localhost:9200/_index_template/my_template?pretty" -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["logs-*"],
  "template": {
    "settings": {
      "number_of_shards": 1
    },
    "mappings": {
      "properties": {
        "timestamp": { "type": "date" },
        "message": { "type": "text" }
      }
    }
  }
}'

# لیست الگوها
curl -X GET "localhost:9200/_index_template?pretty"

# حذف الگو
curl -X DELETE "localhost:9200/_index_template/my_template?pretty"
```

---

## 💡 بهترین روش‌ها

- از تعداد شارد مناسب استفاده کنید (5-10 گیگابایت به ازای هر شارد بهینه است)
- لاگ کوئری‌های کند را برای تنظیم کارایی فعال کنید
- برای ایندکس چند سند از bulk API استفاده کنید
- تا جایی که ممکن است از filter context به جای query context استفاده کنید
- نوع mapping مناسب را برای بهینه‌سازی ذخیره‌سازی و جستجو تنظیم کنید
- سلامت کلاستر و آمار نود را به طور منظم نظارت کنید
- از alias ایندکس برای reindexing بدون توقف استفاده کنید
- استراتژی پشتیبان‌گیری و snapshot مناسب پیاده‌سازی کنید
- از بازه refresh مناسب استفاده کنید (پیش‌فرض 1 ثانیه است)
- mapping فیلدها را بهینه کنید (فیلدهایی که نیاز ندارید را غیرفعال کنید)
