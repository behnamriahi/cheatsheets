# Kubernetes Cheatsheet

## اطلاعات کلاستر
```bash
kubectl cluster-info                    # نمایش اطلاعات کلاستر
kubectl version                         # نمایش نسخه کلاینت و سرور
kubectl get nodes                       # لیست تمام نودها
kubectl describe node <node-name>       # اطلاعات کامل نود
```

## پادها (Pods)
```bash
kubectl get pods                        # لیست پادها در namespace جاری
kubectl get pods -A                     # لیست تمام پادها در تمام namespaceها
kubectl get pods -o wide                # لیست پادها با جزئیات بیشتر
kubectl describe pod <pod-name>         # اطلاعات کامل پاد
kubectl logs <pod-name>                 # مشاهده لاگ‌های پاد
kubectl logs <pod-name> -f              # دنبال کردن لاگ‌ها به صورت لحظه‌ای
kubectl logs <pod-name> -c <container>  # لاگ از یک کانتینر خاص
kubectl exec -it <pod-name> -- /bin/bash # اجرای خط فرمان در پاد
kubectl delete pod <pod-name>           # حذف پاد
kubectl port-forward <pod-name> 8080:80 # انتقال پورت محلی به پاد
```

## استقرارها (Deployments)
```bash
kubectl get deployments                 # لیست استقرارها
kubectl describe deployment <name>      # جزئیات استقرار
kubectl create deployment <name> --image=<image> # ایجاد استقرار
kubectl scale deployment <name> --replicas=3     # تغییر مقیاس استقرار
kubectl set image deployment/<name> <container>=<image> # بروزرسانی ایمیج
kubectl rollout status deployment/<name>         # بررسی وضعیت rollout
kubectl rollout history deployment/<name>        # مشاهده تاریخچه rollout
kubectl rollout undo deployment/<name>           # بازگشت به نسخه قبلی
kubectl delete deployment <name>                 # حذف استقرار
```

## سرویس‌ها (Services)
```bash
kubectl get services                    # لیست سرویس‌ها
kubectl get svc                         # فرم کوتاه
kubectl describe service <name>         # جزئیات سرویس
kubectl expose deployment <name> --port=80 --type=NodePort # قابل دسترس کردن استقرار
kubectl delete service <name>           # حذف سرویس
```

## Namespaceها
```bash
kubectl get namespaces                  # لیست namespaceها
kubectl get ns                          # فرم کوتاه
kubectl create namespace <name>         # ایجاد namespace
kubectl delete namespace <name>         # حذف namespace
kubectl config set-context --current --namespace=<name> # تنظیم namespace پیش‌فرض
```

## ConfigMaps و Secrets
```bash
kubectl get configmaps                  # لیست configmapها
kubectl get cm                          # فرم کوتاه
kubectl create configmap <name> --from-file=<file> # ایجاد از فایل
kubectl create configmap <name> --from-literal=key=value # ایجاد از مقدار
kubectl get secrets                     # لیست secretها
kubectl create secret generic <name> --from-literal=password=secret # ایجاد secret
kubectl describe secret <name>          # جزئیات secret
```

## Context و پیکربندی
```bash
kubectl config view                     # مشاهده kubeconfig
kubectl config get-contexts             # لیست contextها
kubectl config current-context          # نمایش context جاری
kubectl config use-context <context>    # تغییر context
kubectl config set-context <context> --namespace=<ns> # تنظیم namespace برای context
```

## اعمال و حذف منابع
```bash
kubectl apply -f <file.yaml>            # ایجاد/بروزرسانی منابع از فایل
kubectl apply -f <directory>            # اعمال تمام فایل‌های YAML در پوشه
kubectl delete -f <file.yaml>           # حذف منابع از فایل
kubectl replace -f <file.yaml>          # جایگزینی منبع از فایل
```

## برچسب‌ها و انتخابگرها (Labels & Selectors)
```bash
kubectl get pods -l app=nginx           # دریافت پادها بر اساس برچسب
kubectl label pods <pod-name> env=prod  # افزودن برچسب به پاد
kubectl label pods <pod-name> env-      # حذف برچسب از پاد
```

## مدیریت منابع
```bash
kubectl top nodes                       # نمایش مصرف منابع نودها
kubectl top pods                        # نمایش مصرف منابع پادها
kubectl get all                         # دریافت تمام منابع
kubectl get all -A                      # دریافت تمام منابع در تمام namespaceها
```

## عیب‌یابی (Debugging)
```bash
kubectl describe <resource> <name>      # اطلاعات کامل منبع
kubectl logs <pod-name> --previous      # لاگ از کانتینر قبلی
kubectl get events                      # لیست رویدادهای اخیر
kubectl get events --sort-by=.metadata.creationTimestamp # رویدادها مرتب شده
```

## خروجی YAML و ویرایش
```bash
kubectl get pod <name> -o yaml          # دریافت پاد در فرمت YAML
kubectl get pod <name> -o json          # دریافت پاد در فرمت JSON
kubectl edit pod <name>                 # ویرایش مستقیم پاد
kubectl explain pod                     # مستندات منبع پاد
kubectl explain pod.spec                # مستندات spec پاد
```

## DaemonSets، StatefulSets، Jobs
```bash
kubectl get daemonsets                  # لیست daemonsetها
kubectl get statefulsets                # لیست statefulsetها
kubectl get jobs                        # لیست jobها
kubectl get cronjobs                    # لیست cronjobها
```

## حجم‌های دائمی (Persistent Volumes)
```bash
kubectl get pv                          # لیست persistent volumeها
kubectl get pvc                         # لیست persistent volume claimها
kubectl describe pv <name>              # جزئیات PV
kubectl describe pvc <name>             # جزئیات PVC
```

## فلگ‌های رایج
```bash
-n, --namespace=<namespace>             # مشخص کردن namespace
-A, --all-namespaces                    # تمام namespaceها
-o, --output=<format>                   # فرمت خروجی (json, yaml, wide, name)
-w, --watch                             # تماشای تغییرات
--dry-run=client                        # تست دستور بدون اجرا
-f, --filename=<file>                   # فایل مورد استفاده
-l, --selector=<label>                  # انتخابگر برچسب
```
