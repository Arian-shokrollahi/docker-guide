دقیقاً همین دید کلی درسته.

فرآیند ذهنی Mount کردن در Docker اینه:
1. اول باید بدونیم کدوم data است که برایه ما مهمه و میخوایم به صورت داعمی داشته باشیمش و با پاک شدن کانتینر پاک نشه
2. مسیرش کجاست 
3. مونت کردن اون مسیر به ولوم
4. تست اینکه مثلا اگر دیتا بیس بود رویه کانتینر بعدی که به همون ولوم مونت شده بود هنوز دیتا بیس هست یا نه
5.  تو اینجا تستشو گفتم 
[test mounting on volume](../../../3-hands-on/4-0to100-dockervolume.md)
مثلاً برای MySQL:

```
Data مهم:
Database files

مسیر داخل Container:
/var/lib/mysql

Volume:
mysql-data
```

بعد:

```
docker run -d \
  --name mysql-db \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql
```

یعنی:

```
mysql-data
     ↓
/var/lib/mysql
```

پس خلاصه خیلی خوب برای جزوه‌ات:

> اول مشخص می‌کنیم چه دیتایی باید Persistent بماند، بعد مسیر آن دیتا داخل Container را پیدا می‌کنیم و همان مسیر را به یک Volume Mount می‌کنیم.
