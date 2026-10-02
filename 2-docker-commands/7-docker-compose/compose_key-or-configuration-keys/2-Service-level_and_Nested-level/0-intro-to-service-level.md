# چیزایی که قراره درون این فولدر بخونیم
- ما درون این فولدر به configuration keys  یا compose key هایه درون  service level بپردازیم


ا-**Service-level keys** کلیدهایی هستند که زیر نام هر سرویس در بخش `services` نوشته می‌شوند و مشخص می‌کنند آن سرویس **چطور ساخته شود، چطور اجرا شود، به چه چیزی وصل شود و چه منابعی داشته باشد**.

مثلاً:

```
services:
  backend:
    image: my-backend
    ports:
      - "5000:5000"
    volumes:
      - app-data:/app/data
    networks:
      - app-net
```

اینجا `image`، `ports`، `volumes` و `networks` همگی **Service-level key** هستند.

برای یادگیری، این ترتیب اهمیت خوبه:

|Key|کاربرد|اهمیت|
|---|---|---|
|`image`|تعیین Image سرویس|★★★★★|
|`build`|ساخت Image از Dockerfile|★★★★★|
|`ports`|Publish کردن پورت|★★★★★|
|`volumes`|Mount کردن Volume/Bind|★★★★★|
|`environment`|تعریف Environment Variable|★★★★★|
|`env_file`|خواندن متغیرها از فایل|★★★★★|
|`networks`|اتصال سرویس به Network|★★★★★|
|`depends_on`|وابستگی به سرویس دیگر|★★★★★|
|`restart`|سیاست Restart|★★★★★|
|`healthcheck`|بررسی سلامت سرویس|★★★★★|
|`command`|تغییر دستور اجرای Container|★★★★★|
|`configs`|دادن فایل Config به سرویس|★★★★☆|
|`secrets`|دادن Secret به سرویس|★★★★☆|
|`entrypoint`|تغییر `ENTRYPOINT`|★★★★☆|
|`container_name`|تعیین نام Container|★★★★☆|
|`read_only`|Read-only کردن filesystem|★★★★☆|
|`user`|اجرای سرویس با User مشخص|★★★★☆|
|`profiles`|فعال/غیرفعال کردن گروهی سرویس‌ها|★★★★☆|
|`logging`|تنظیم Logging|★★★★☆|
|`working_dir`|تعیین Working Directory|★★★☆☆|
|`expose`|مشخص کردن پورت داخلی|★★★☆☆|
|`tmpfs`|Mount موقت در RAM|★★★☆☆|
|`extra_hosts`|اضافه کردن Host mapping|★★★☆☆|
|`platform`|تعیین معماری CPU/OS|★★★☆☆|
|`init`|اجرای init process|★★★☆☆|
|`stop_grace_period`|زمان Graceful shutdown|★★★☆☆|
|`cap_add` / `cap_drop`|مدیریت Linux capabilities|★★★☆☆|
|`security_opt`|تنظیمات امنیتی|★★★☆☆|
|`ulimits`|محدودیت‌های Linux|★★★☆☆|
|`devices`|دادن Device به Container|★★☆☆☆|
|`privileged`|دسترسی خیلی زیاد به Host|★★☆☆☆|
|`hostname`|تعیین hostname|★★☆☆☆|
|`dns`|تعیین DNS|★★☆☆☆|
|`links`|روش قدیمی ارتباط سرویس‌ها|★☆☆☆☆|


`image`, `build`, `ports`, `volumes`, `environment`, `env_file`, `networks`, `depends_on`, `restart`, `healthcheck`, `command`

اگر این ۱۱ تا رو عمیق بلد باشی، بخش بزرگی از Compose واقعی رو پوشش می‌دی.
