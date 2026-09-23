## قانون پاک کردن Imageها
- خیلی خلاصه قانون پاک کردن image ها اینه که اکر کانتینری از اون image ساخته شده باشه باید اول اون کانتینر رو پاک کنید و بعد برید سراغه پاک کردن اون image

```shell
docker ps -a
# Show all containers, including running and stopped containers

docker rm CONTAINER_ID
# Remove a container

docker image rm IMAGE_NAME
# Remove an image by image name

docker image rm IMAGE_ID
# Remove an image by image ID

docker image rm -f IMAGE_NAME
# Force remove an image
```

قانون اصلی اینه:

اگر هیچ Containerی از یک Image استفاده نکنه، می‌تونی مستقیم Image رو پاک کنی:

```
docker image rm IMAGE_NAME
```

مثلاً:

```
docker image rm hello-world:latest
```

اما اگر حتی یک Container از روی اون Image ساخته شده باشه، Docker معمولاً اجازه حذف Image رو نمی‌ده؛ حتی اگر اون Container الان `Stopped` باشه.

```
Image
  |
  +-- Container exists
        |
        +--> Image cannot be removed normally
```

اول Containerهای موجود رو ببین:

```
docker ps -a
```

بعد Container مرتبط رو حذف کن:

```
docker rm CONTAINER_ID
```

مثلاً:

```
docker rm 30d75aba34bf
```

بعد Image رو پاک کن:

```
docker image rm hello-world:latest
```

یا با `IMAGE ID`:

```
docker image rm 5e2309035332
```

اگر بخوای اجباری حذفش کنی:

```
docker image rm -f hello-world:latest
```

قانون طلایی:

```
Container depends on Image

First:
Remove Container

Then:
Remove Image
```

و این نکته مهم:

```
Stopped != Removed
```

یعنی `Stopped Container` فقط متوقف شده، ولی هنوز وجود داره.
