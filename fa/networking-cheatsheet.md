# 🌐 Networking Cheatsheet

## 🔍 اطلاعات شبکه

```bash
# نمایش آدرس IP
ip addr show                           # نمایش تمام اینترفیس‌های شبکه
ip a                                   # فرم کوتاه
ifconfig                               # دستور قدیمی

# نمایش اینترفیس خاص
ip addr show eth0
ifconfig eth0

# نمایش جدول مسیریابی
ip route show
route -n                               # دستور قدیمی
netstat -rn                            # جایگزین

# نمایش گیت‌وی پیش‌فرض
ip route | grep default
route -n | grep '^0.0.0.0'

# نمایش جدول ARP
ip neigh                               # نمایش کش ARP
arp -a                                 # دستور قدیمی
```

---

## 🔌 پیکربندی شبکه

```bash
# تخصیص آدرس IP
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0

# حذف آدرس IP
sudo ip addr del 192.168.1.100/24 dev eth0

# فعال/غیرفعال کردن اینترفیس
sudo ip link set eth0 up
sudo ip link set eth0 down
sudo ifconfig eth0 up
sudo ifconfig eth0 down

# تنظیم گیت‌وی پیش‌فرض
sudo ip route add default via 192.168.1.1
sudo route add default gw 192.168.1.1

# تنظیم مسیر استاتیک
sudo ip route add 10.0.0.0/24 via 192.168.1.254
sudo route add -net 10.0.0.0/24 gw 192.168.1.254

# حذف مسیر
sudo ip route del 10.0.0.0/24
sudo route del -net 10.0.0.0/24

# تغییر آدرس MAC
sudo ip link set dev eth0 address 00:11:22:33:44:55
```

---

## 📡 ابزارهای DNS

```bash
# جستجوی DNS
nslookup google.com                    # پرس‌وجوی DNS پایه
nslookup google.com 8.8.8.8            # پرس‌وجوی سرور DNS خاص

# دستور Dig (اطلاعات دقیق DNS)
dig google.com                         # پرس‌وجوی رکورد A
dig google.com MX                      # پرس‌وجوی رکوردهای MX
dig google.com NS                      # پرس‌وجوی nameserver
dig google.com +short                  # خروجی کوتاه
dig @8.8.8.8 google.com                # استفاده از سرور DNS خاص
dig -x 8.8.8.8                         # جستجوی معکوس DNS

# دستور Host
host google.com                        # جستجوی ساده DNS
host -t MX google.com                  # پرس‌وجوی رکوردهای MX
host -a google.com                     # تمام رکوردها

# تبدیل hostname به IP
getent hosts google.com
```

---

## 🔐 بررسی پورت و سرویس

```bash
# نمایش پورت‌های listening
netstat -tuln                          # پورت‌های TCP/UDP در حال گوش دادن
ss -tuln                               # جایگزین مدرن netstat
lsof -i                                # لیست اتصالات شبکه باز

# نمایش تمام اتصالات
netstat -tun                           # تمام اتصالات TCP/UDP
ss -tun                                # جایگزین مدرن

# نمایش اتصالات برای پورت خاص
netstat -tuln | grep :80
ss -tuln | grep :80
lsof -i :80

# نمایش پروسس استفاده‌کننده از پورت
sudo lsof -i :80
sudo netstat -tulnp | grep :80
sudo ss -tulnp | grep :80

# بررسی باز بودن پورت
nc -zv hostname 80                     # بررسی پورت با Netcat
telnet hostname 80                     # بررسی با Telnet
timeout 1 bash -c "echo > /dev/tcp/hostname/80" && echo "باز" || echo "بسته"
```

---

## 🌐 تست اتصال

```bash
# Ping
ping google.com                        # پینگ مداوم
ping -c 4 google.com                   # ارسال 4 بسته
ping -i 0.5 google.com                 # تنظیم فاصله به 0.5 ثانیه
ping -W 2 google.com                   # تنظیم timeout به 2 ثانیه

# Traceroute
traceroute google.com                  # ردیابی مسیر به هاست
traceroute -n google.com               # بدون تبدیل DNS
tracepath google.com                   # جایگزین traceroute
mtr google.com                         # traceroute مداوم

# تست اتصال TCP
nc -vz hostname 80                     # بررسی TCP با Netcat
telnet hostname 80                     # بررسی TCP با Telnet

# تست اتصال UDP
nc -vzu hostname 53                    # بررسی UDP با Netcat

# اتصال با Curl
curl -I https://google.com             # فقط دریافت هدرها
curl -v https://google.com             # خروجی پرجزئیات
curl -o /dev/null -s -w "%{http_code}\n" https://google.com  # کد وضعیت HTTP
```

---

## 📊 آمار شبکه

```bash
# نمایش آمار شبکه
netstat -s                             # تمام آمار
ss -s                                  # خلاصه آمار

# نمایش آمار اینترفیس
ip -s link                             # آمار اینترفیس
netstat -i                             # جدول اینترفیس

# نظارت بر پهنای باند
iftop                                  # مانیتور تعاملی پهنای باند
iftop -i eth0                          # اینترفیس خاص
nethogs                                # پهنای باند به ازای هر پروسس
vnstat                                 # مانیتور ترافیک شبکه
nload                                  # ترافیک لحظه‌ای

# نمایش اتصالات TCP
ss -t                                  # اتصالات TCP
netstat -t                             # دستور قدیمی

# نمایش اتصالات UDP
ss -u                                  # اتصالات UDP
netstat -u                             # دستور قدیمی
```

---

## 🔥 فایروال (iptables)

```bash
# لیست قوانین
sudo iptables -L                       # لیست تمام قوانین
sudo iptables -L -n                    # بدون تبدیل DNS
sudo iptables -L -v                    # خروجی پرجزئیات
sudo iptables -L INPUT                 # لیست زنجیره INPUT

# اجازه پورت ورودی
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# مسدود کردن آدرس IP
sudo iptables -A INPUT -s 192.168.1.100 -j DROP
sudo iptables -A INPUT -s 10.0.0.0/8 -j DROP

# اجازه از IP خاص
sudo iptables -A INPUT -s 192.168.1.50 -j ACCEPT

# حذف قانون
sudo iptables -D INPUT 3               # حذف قانون شماره 3
sudo iptables -D INPUT -s 192.168.1.100 -j DROP  # حذف قانون خاص

# پاک‌سازی تمام قوانین
sudo iptables -F                       # پاک‌سازی تمام زنجیره‌ها
sudo iptables -F INPUT                 # پاک‌سازی زنجیره INPUT

# ذخیره قوانین
sudo iptables-save > /etc/iptables/rules.v4
sudo service iptables save             # در RHEL/CentOS

# بازیابی قوانین
sudo iptables-restore < /etc/iptables/rules.v4
```

---

## 🔥 فایروال (firewalld)

```bash
# بررسی وضعیت
sudo firewall-cmd --state
sudo systemctl status firewalld

# لیست تمام قوانین
sudo firewall-cmd --list-all
sudo firewall-cmd --list-all --zone=public

# افزودن پورت
sudo firewall-cmd --add-port=80/tcp
sudo firewall-cmd --add-port=80/tcp --permanent

# حذف پورت
sudo firewall-cmd --remove-port=80/tcp
sudo firewall-cmd --remove-port=80/tcp --permanent

# افزودن سرویس
sudo firewall-cmd --add-service=http
sudo firewall-cmd --add-service=https --permanent

# مسدود کردن IP
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.100" reject'

# بارگذاری مجدد فایروال
sudo firewall-cmd --reload

# لیست سرویس‌ها
sudo firewall-cmd --get-services
```

---

## 🛡️ فایروال (UFW - Ubuntu)

```bash
# فعال/غیرفعال کردن UFW
sudo ufw enable
sudo ufw disable

# بررسی وضعیت
sudo ufw status
sudo ufw status verbose
sudo ufw status numbered

# اجازه پورت
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 22                      # SSH

# رد پورت
sudo ufw deny 80/tcp

# اجازه از IP خاص
sudo ufw allow from 192.168.1.100
sudo ufw allow from 192.168.1.0/24 to any port 22

# حذف قانون
sudo ufw delete allow 80/tcp
sudo ufw delete 2                      # حذف قانون شماره 2

# ریست فایروال
sudo ufw reset

# تنظیم سیاست‌های پیش‌فرض
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

---

## 📦 ضبط بسته‌های شبکه

```bash
# tcpdump
sudo tcpdump -i eth0                   # ضبط روی اینترفیس
sudo tcpdump -i any                    # ضبط روی تمام اینترفیس‌ها
sudo tcpdump -i eth0 port 80           # پورت خاص
sudo tcpdump -i eth0 host 192.168.1.1  # هاست خاص
sudo tcpdump -i eth0 -w capture.pcap   # ذخیره در فایل
sudo tcpdump -r capture.pcap           # خواندن از فایل
sudo tcpdump -i eth0 -n                # بدون تبدیل DNS
sudo tcpdump -i eth0 -c 100            # ضبط 100 بسته

# فیلترهای پیشرفته
sudo tcpdump -i eth0 'tcp port 80 and (src host 192.168.1.1)'
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'
```

---

## 🔧 ابزارهای شبکه

```bash
# Netcat (nc)
nc -l 8080                             # گوش دادن روی پورت 8080
nc hostname 80                         # اتصال به هاست پورت 80
nc -u hostname 53                      # اتصال UDP
echo "test" | nc hostname 80           # ارسال داده

# Socat
socat TCP-LISTEN:8080,fork TCP:backend:80  # پروکسی TCP
socat - TCP:hostname:80                # اتصال به پورت TCP

# wget
wget https://example.com/file.zip      # دانلود فایل
wget -c https://example.com/file.zip   # ادامه دانلود
wget -r https://example.com            # دانلود بازگشتی
wget -O output.txt https://example.com # ذخیره با نام سفارشی

# curl
curl https://example.com               # درخواست GET
curl -o file.html https://example.com  # ذخیره در فایل
curl -L https://example.com            # دنبال کردن تغییر مسیرها
curl -X POST https://api.example.com   # درخواست POST
curl -d "key=value" https://api.example.com  # POST با داده
curl -H "Content-Type: application/json" https://api.example.com  # هدر سفارشی
```

---

## 📡 دستورات Wi-Fi

```bash
# iwconfig (پیکربندی بی‌سیم)
iwconfig                               # نمایش اطلاعات بی‌سیم
iwconfig wlan0                         # اینترفیس خاص
sudo iwconfig wlan0 essid "NetworkName"  # اتصال به شبکه

# iw (ابزارهای بی‌سیم مدرن)
iw dev                                 # لیست دستگاه‌های بی‌سیم
iw dev wlan0 scan                      # اسکن شبکه‌ها
iw dev wlan0 link                      # نمایش اطلاعات اتصال
iw dev wlan0 connect "NetworkName"     # اتصال به شبکه

# nmcli (NetworkManager)
nmcli device                           # لیست دستگاه‌ها
nmcli device wifi list                 # لیست شبکه‌های Wi-Fi
nmcli device wifi connect "SSID" password "password"  # اتصال
nmcli connection show                  # نمایش اتصالات
nmcli connection up "connection-name"  # فعال‌سازی اتصال
nmcli connection down "connection-name"  # غیرفعال‌سازی اتصال
```

---

## 🔍 عیب‌یابی شبکه

```bash
# بررسی فعال بودن اینترفیس
ip link show eth0 | grep "state UP"

# بررسی کابل شبکه
ethtool eth0 | grep "Link detected"

# بررسی DNS resolution
systemd-resolve --status               # وضعیت systemd-resolved
cat /etc/resolv.conf                   # سرورهای DNS

# بررسی سرویس شبکه
sudo systemctl status NetworkManager
sudo systemctl status networking

# راه‌اندازی مجدد سرویس شبکه
sudo systemctl restart NetworkManager
sudo systemctl restart networking
sudo /etc/init.d/networking restart

# پاک‌سازی کش DNS
sudo systemd-resolve --flush-caches
sudo systemctl restart nscd            # کش NSCD
sudo systemctl restart dnsmasq         # کش Dnsmasq

# تست سرعت شبکه
speedtest-cli                          # تست سرعت CLI
iperf3 -s                              # شروع سرور
iperf3 -c server_ip                    # اتصال به عنوان کلاینت
```

---

## 💡 بهترین روش‌ها

- همیشه از نام‌های اینترفیس خاص در اسکریپت‌های تولید استفاده کنید
- از فلگ `-n` برای اجتناب از جستجوی DNS برای نتایج سریع‌تر استفاده کنید
- `ip` و `ss` را به `ifconfig` و `netstat` منسوخ ترجیح دهید
- قوانین فایروال را ذخیره کنید تا بعد از راه‌اندازی مجدد باقی بمانند
- از connection tracking برای فایروال‌های stateful استفاده کنید
- ترافیک شبکه را به طور منظم برای ناهنجاری‌ها نظارت کنید
- تمام قوانین فایروال و تغییرات شبکه را مستندسازی کنید
- از VLAN و تقسیم‌بندی شبکه برای امنیت استفاده کنید
- محدودیت نرخ را برای جلوگیری از حملات DDoS پیاده‌سازی کنید
- ممیزی امنیتی منظم پورت‌ها و سرویس‌های باز
