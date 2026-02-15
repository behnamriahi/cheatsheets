# 🌿 Git Cheatsheet

## ⚙️ تنظیمات اولیه

``` bash
git config --global user.name "Your Name"        # تنظیم نام کاربر
git config --global user.email "you@example.com" # تنظیم ایمیل
git config --global init.defaultBranch main      # تنظیم نام برنچ پیش‌فرض
git config --list                                # مشاهده تنظیمات
git config --global core.editor "vim"            # تنظیم ویرایشگر پیش‌فرض
git config --global color.ui auto                # فعال‌سازی رنگ‌ها
```

------------------------------------------------------------------------

## 🆕 ساخت و Clone مخزن

``` bash
git init                              # ساخت مخزن جدید در فولدر فعلی
git init my-project                   # ساخت فولدر جدید و مخزن در آن
git clone https://github.com/user/repo.git      # کپی مخزن از سرور
git clone https://github.com/user/repo.git mydir # کپی با نام فولدر سفارشی
```

------------------------------------------------------------------------

## 📝 تغییرات و Staging

``` bash
git status                            # وضعیت فعلی فایل‌ها
git status -s                         # خروجی خلاصه
git add file.txt                      # افزودن فایل به staging
git add .                             # افزودن همه فایل‌های تغییریافته
git add *.js                          # افزودن فایل‌های با پترن مشخص
git add -p                            # افزودن تعاملی (قسمت به قسمت)
git rm file.txt                       # حذف فایل از git و filesystem
git rm --cached file.txt              # حذف فایل فقط از git (نگه‌داشتن لوکال)
git mv old.txt new.txt                # تغییر نام فایل
```

------------------------------------------------------------------------

## 💾 Commit کردن

``` bash
git commit -m "commit message"        # ثبت تغییرات با پیام
git commit -am "message"              # add و commit همزمان (فقط tracked files)
git commit --amend                    # ویرایش آخرین کامیت
git commit --amend -m "new message"   # تغییر پیام آخرین کامیت
git commit --amend --no-edit          # افزودن تغییرات به کامیت قبلی بدون تغییر پیام
```

------------------------------------------------------------------------

## 📜 تاریخچه و لاگ

``` bash
git log                               # تاریخچه کامیت‌ها
git log --oneline                     # نمایش خلاصه (یک خط برای هر کامیت)
git log --graph                       # نمایش گرافیکی برنچ‌ها
git log --all --graph --oneline       # نمایش کامل تمام برنچ‌ها
git log -n 5                          # نمایش 5 کامیت آخر
git log --since="2 weeks ago"         # کامیت‌های 2 هفته اخیر
git log --author="Ali"                # کامیت‌های یک نفر خاص
git log --grep="fix"                  # جستجو در پیام کامیت‌ها
git log file.txt                      # تاریخچه یک فایل خاص
git log -p                            # نمایش diff هر کامیت
git show <commit-hash>                # نمایش جزئیات یک کامیت
git show HEAD                         # نمایش آخرین کامیت
```

------------------------------------------------------------------------

## 🔍 مقایسه و تفاوت‌ها

``` bash
git diff                              # تغییرات unstaged
git diff --staged                     # تغییرات staged
git diff HEAD                         # تمام تغییرات (staged + unstaged)
git diff branch1 branch2              # مقایسه دو برنچ
git diff commit1 commit2              # مقایسه دو کامیت
git diff --stat                       # خلاصه آماری تغییرات
```

------------------------------------------------------------------------

## 🌿 مدیریت برنچ‌ها

``` bash
git branch                            # لیست برنچ‌های لوکال
git branch -a                         # لیست همه برنچ‌ها (لوکال و ریموت)
git branch feature-x                  # ساخت برنچ جدید
git branch -d feature-x               # حذف برنچ (safe delete)
git branch -D feature-x               # حذف اجباری برنچ
git branch -m old-name new-name       # تغییر نام برنچ
git checkout feature-x                # جابجایی به برنچ دیگر
git checkout -b feature-x             # ساخت و رفتن به برنچ جدید
git switch feature-x                  # جابجایی برنچ (روش جدید)
git switch -c feature-x               # ساخت و جابجایی (روش جدید)
```

------------------------------------------------------------------------

## 🔀 Merge کردن

``` bash
git merge feature-x                   # ادغام feature-x به برنچ فعلی
git merge --no-ff feature-x           # merge بدون fast-forward
git merge --squash feature-x          # تمام کامیت‌ها را در یک کامیت ادغام کن
git merge --abort                     # لغو merge در صورت conflict
```

------------------------------------------------------------------------

## 🔄 Rebase

``` bash
git rebase main                       # اعمال کامیت‌های فعلی روی main
git rebase -i HEAD~3                  # rebase تعاملی 3 کامیت آخر
git rebase --continue                 # ادامه rebase بعد از حل conflict
git rebase --abort                    # لغو rebase
git rebase --skip                     # رد شدن از کامیت فعلی
```

------------------------------------------------------------------------

## 🔗 کار با Remote

``` bash
git remote                            # لیست remote‌ها
git remote -v                         # نمایش URL‌ها
git remote add origin <url>           # افزودن remote جدید
git remote remove origin              # حذف remote
git remote rename origin upstream     # تغییر نام remote
git remote show origin                # اطلاعات کامل remote
git fetch                             # دریافت تغییرات بدون merge
git fetch --all                       # دریافت از همه remote‌ها
git pull                              # دریافت و merge تغییرات
git pull --rebase                     # دریافت با rebase به جای merge
git push                              # ارسال تغییرات به remote
git push origin main                  # ارسال برنچ مشخص
git push -u origin main               # ارسال و تنظیم upstream
git push --all                        # ارسال همه برنچ‌ها
git push --tags                       # ارسال تگ‌ها
git push --force                      # ارسال اجباری (خطرناک)
git push --force-with-lease           # ارسال اجباری امن‌تر
```

------------------------------------------------------------------------

## 🏷 تگ‌ها

``` bash
git tag                               # لیست تگ‌ها
git tag v1.0.0                        # ساخت تگ lightweight
git tag -a v1.0.0 -m "Version 1.0"    # ساخت تگ annotated
git tag -a v1.0.0 <commit-hash>       # تگ‌گذاری کامیت خاص
git show v1.0.0                       # نمایش اطلاعات تگ
git tag -d v1.0.0                     # حذف تگ لوکال
git push origin v1.0.0                # ارسال یک تگ
git push origin --delete v1.0.0       # حذف تگ از remote
```

------------------------------------------------------------------------

## ↩️ بازگردانی تغییرات

``` bash
git restore file.txt                  # بازگردانی فایل به آخرین کامیت
git restore --staged file.txt         # خارج کردن از staging
git checkout -- file.txt              # بازگردانی فایل (روش قدیمی)
git reset HEAD file.txt               # unstage کردن فایل
git reset --soft HEAD~1               # حذف آخرین کامیت (حفظ تغییرات در staging)
git reset --mixed HEAD~1              # حذف آخرین کامیت (حفظ تغییرات unstaged)
git reset --hard HEAD~1               # حذف کامل آخرین کامیت
git reset --hard <commit-hash>        # بازگشت به کامیت خاص
git revert <commit-hash>              # ساخت کامیت جدید برای لغو تغییرات
```

------------------------------------------------------------------------

## 🗂 Stash (ذخیره موقت)

``` bash
git stash                             # ذخیره تغییرات جاری
git stash save "work in progress"     # ذخیره با پیام
git stash -u                          # ذخیره شامل untracked files
git stash list                        # لیست stash‌ها
git stash show                        # نمایش آخرین stash
git stash show -p                     # نمایش diff آخرین stash
git stash apply                       # اعمال آخرین stash (حفظ stash)
git stash pop                         # اعمال و حذف آخرین stash
git stash apply stash@{2}             # اعمال stash مشخص
git stash drop                        # حذف آخرین stash
git stash drop stash@{2}              # حذف stash مشخص
git stash clear                       # حذف همه stash‌ها
git stash branch feature-x            # ساخت برنچ از stash
```

------------------------------------------------------------------------

## 🔍 جستجو و بررسی

``` bash
git grep "search term"                # جستجو در فایل‌های پروژه
git grep -n "search term"             # جستجو با شماره خط
git blame file.txt                    # نمایش نویسنده هر خط
git blame -L 10,20 file.txt           # blame برای خطوط مشخص
git bisect start                      # شروع جستجوی باینری برای باگ
git bisect bad                        # مشخص کردن کامیت بد
git bisect good <commit>              # مشخص کردن کامیت خوب
git bisect reset                      # پایان bisect
```

------------------------------------------------------------------------

## 🧹 پاکسازی

``` bash
git clean -n                          # نمایش فایل‌های untracked (پیش‌نمایش)
git clean -f                          # حذف فایل‌های untracked
git clean -fd                         # حذف فایل‌ها و فولدرهای untracked
git clean -fX                         # حذف فایل‌های ignored
git clean -fx                         # حذف همه (untracked + ignored)
git gc                                # بهینه‌سازی مخزن (garbage collection)
git prune                             # حذف اشیاء غیرقابل دسترس
```

------------------------------------------------------------------------

## 🔧 Cherry-pick

``` bash
git cherry-pick <commit-hash>         # اعمال یک کامیت خاص به برنچ فعلی
git cherry-pick <hash1> <hash2>       # اعمال چند کامیت
git cherry-pick --continue            # ادامه بعد از حل conflict
git cherry-pick --abort               # لغو عملیات
```

------------------------------------------------------------------------

## 🎯 Submodule

``` bash
git submodule add <url> path/to/submodule  # افزودن submodule
git submodule init                    # مقداردهی اولیه submodule‌ها
git submodule update                  # دریافت کد submodule‌ها
git submodule update --init --recursive    # init و update همزمان
git clone --recursive <url>           # clone با submodule‌ها
```

------------------------------------------------------------------------

## 📋 .gitignore

نمونه فایل `.gitignore`:

```
# فایل‌های سیستم
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp

# Dependencies
node_modules/
vendor/

# Build
dist/
build/
*.log

# Environment
.env
.env.local

# Temporary
*.tmp
temp/
```

------------------------------------------------------------------------

## 🔐 Git Hooks (پیشرفته)

``` bash
# Hook‌ها در .git/hooks/ قرار دارند
# نمونه pre-commit hook:

#!/bin/bash
# .git/hooks/pre-commit

npm test
if [ $? -ne 0 ]; then
    echo "Tests failed, commit aborted"
    exit 1
fi
```

------------------------------------------------------------------------

## 🌐 Git Workflows

### Feature Branch Workflow

``` bash
git checkout -b feature/new-feature   # ساخت برنچ جدید
# ... توسعه و کامیت‌ها
git push -u origin feature/new-feature
# ساخت Pull Request در GitHub/GitLab
# بعد از review و merge:
git checkout main
git pull
git branch -d feature/new-feature
```

### Gitflow Workflow

``` bash
# برنچ‌های اصلی: main, develop
git checkout -b develop               # ساخت برنچ develop

# شروع feature جدید
git checkout -b feature/x develop
# ... توسعه
git checkout develop
git merge --no-ff feature/x

# شروع release
git checkout -b release/1.0 develop
# ... آماده‌سازی
git checkout main
git merge --no-ff release/1.0
git tag -a v1.0

# Hotfix
git checkout -b hotfix/1.0.1 main
# ... رفع باگ
git checkout main
git merge --no-ff hotfix/1.0.1
git checkout develop
git merge --no-ff hotfix/1.0.1
```

------------------------------------------------------------------------

## 🚀 دستورات پیشرفته

### Reflog (تاریخچه کامل)

``` bash
git reflog                            # تاریخچه تمام تغییرات HEAD
git reflog show branch-name           # reflog یک برنچ خاص
git reset --hard HEAD@{2}             # بازگشت به وضعیت قبلی
```

### Worktree (چند نسخه همزمان)

``` bash
git worktree add ../project-feature feature-branch  # ساخت worktree
git worktree list                     # لیست worktree‌ها
git worktree remove ../project-feature # حذف worktree
```

### Patch (ایجاد و اعمال)

``` bash
git format-patch -1 HEAD              # ساخت patch از آخرین کامیت
git format-patch -3                   # ساخت patch از 3 کامیت آخر
git apply patch-file.patch            # اعمال patch
git am patch-file.patch               # اعمال patch با حفظ اطلاعات کامیت
```

### Interactive Add

``` bash
git add -i                            # حالت تعاملی
git add -p                            # انتخاب قسمت‌های خاص (hunk)
# در حالت interactive:
# y = yes, n = no, s = split, e = edit
```

------------------------------------------------------------------------

## 🐛 Debugging و Troubleshooting

``` bash
git status                            # بررسی وضعیت
git diff                              # مشاهده تغییرات
git log --all --graph --oneline       # نمای کلی تاریخچه
git fsck                              # بررسی یکپارچگی مخزن
git reflog                            # یافتن کامیت‌های گم‌شده
git show :0:file.txt                  # نمایش نسخه staged
git ls-files                          # لیست فایل‌های tracked
git ls-files -u                       # فایل‌های conflicted
```

------------------------------------------------------------------------

## 📦 GitHub/GitLab Specific

``` bash
# فورک و همکاری
git remote add upstream <original-repo-url>  # افزودن مخزن اصلی
git fetch upstream                    # دریافت تغییرات مخزن اصلی
git merge upstream/main               # ادغام با مخزن اصلی

# Pull Request از CLI (با GitHub CLI)
gh pr create                          # ساخت PR
gh pr list                            # لیست PRها
gh pr checkout 123                    # checkout کردن PR
```

------------------------------------------------------------------------

## ⚡ Aliases (میانبرها)

``` bash
# تنظیم alias‌ها
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg "log --graph --oneline --all"

# استفاده:
git st                                # معادل git status
git lg                                # لاگ گرافیکی
```

------------------------------------------------------------------------

## 🏭 Production Best Practices

- ✅ کامیت‌های کوچک و منطقی بزن
- ✅ پیام‌های commit واضح و توصیفی بنویس
- ✅ قبل از push، pull کن تا conflict کمتر شود
- ✅ از `.gitignore` برای فایل‌های حساس استفاده کن
- ✅ قبل از merge، تست کن
- ✅ از برنچ‌های feature استفاده کن
- ⚠️ از `--force` احتیاط کن (بهتر است `--force-with-lease`)
- ⚠️ کامیت‌های merge را نگه دار (از squash زیاد استفاده نکن)
- 🔐 هیچ‌وقت password و key را commit نکن
- 📝 از Conventional Commits استفاده کن:
  - `feat:` برای ویژگی جدید
  - `fix:` برای رفع باگ
  - `docs:` برای مستندات
  - `refactor:` برای بازنویسی کد

------------------------------------------------------------------------

## 🔑 نکات امنیتی

``` bash
# حذف فایل حساس از تاریخچه
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD
# یا با ابزار جدیدتر:
git filter-repo --path passwords.txt --invert-paths

# امضای کامیت‌ها با GPG
git config --global user.signingkey <key-id>
git config --global commit.gpgsign true
git commit -S -m "Signed commit"

# بررسی امضای کامیت
git log --show-signature
```

------------------------------------------------------------------------

## 📊 آمارگیری

``` bash
git shortlog -sn                      # تعداد کامیت هر نفر
git shortlog -sn --all --no-merges    # بدون merge‌ها
git log --author="Ali" --oneline --shortstat  # آمار یک نفر
git diff --stat                       # آمار تغییرات
git log --since="1 month ago" --oneline | wc -l  # تعداد کامیت ماه اخیر
```

------------------------------------------------------------------------

## 💡 نکات حرفه‌ای

### جستجوی commit که باگ را ایجاد کرده:

``` bash
git bisect start
git bisect bad                        # کامیت فعلی باگ دارد
git bisect good v1.0                  # نسخه 1.0 سالم بود
# git به صورت اتوماتیک کامیت‌ها را تست می‌کند
# در هر مرحله باگ را تست کن و بگو:
git bisect good   # یا
git bisect bad
# تا پیدا شدن کامیت مشکل‌دار
git bisect reset
```

### Rebase تعاملی برای تمیزکاری:

``` bash
git rebase -i HEAD~5
# در ویرایشگر:
# pick = نگه‌داشتن
# squash = ادغام با کامیت قبلی
# reword = تغییر پیام
# edit = ویرایش کامیت
# drop = حذف کامیت
```

### نجات کامیت حذف‌شده:

``` bash
git reflog
# پیدا کردن hash کامیت گم‌شده
git checkout <commit-hash>
git checkout -b recovered-branch
```
