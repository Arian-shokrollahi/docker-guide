
### تا الان چی یاد گرفتیم در بخش docker volume

1. ساختن داکر ولوم
2. لیست داکر ولوم
3. اجرا داکر کانتینر + تعیین اسم(name--) + مونت (mount-) + و دادن پسورد به روت
4. و چک کردن اینکه ایا مونت شده یا نه با  inspect -f 

 برای بخش پایه‌ی Volume تا اینجا چیزهای مهم را یاد گرفتی:

```shell
# ساخت Volume
docker volume create mysql-data

# دیدن Volumeها
docker volume ls

# اجرای Container و Mount کردن Volume
docker run -d \
  --name mysql-db \
  --mount source=mysql-data,target=/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=PASS \
  mysql

# بررسی Mount
docker inspect -f '{{.Mounts}}' mysql-db
```

فقط یک اصلاح کوچک در Syntax:
و دومدل هم بود داکر ولوم مونتینگ با فلگ مونت (mount--) و فلگ v-

```
--mount source=mysql-data,target=/var/lib/mysql
```

بین `source` و `target` نباید Space بعد از کاما باشد.

الان برای اینکه بخش Volume را کامل‌تر ببندی، پیشنهاد می‌کنم فقط این چند مورد را هم یاد بگیری:

- ا-`docker volume inspect`
- ا-`docker volume rm`
- اا-`docker volume prune`
- فرق **Named Volume** با **Bind Mount**
- فرق `-v` با `--mount`
- تست واقعی Persistence با حذف و ساخت دوباره Container

اگر این‌ها را هم بلد باشی، برای Docker Fundamentals در بخش Volume کاملاً خوب جلو رفتی.
