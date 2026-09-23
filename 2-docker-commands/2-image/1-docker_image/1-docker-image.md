

## Docker Image Commands

```shell
docker image build
# Build an image from a Dockerfile

docker image history
# Show the history and layers of an image

docker image import
# Create an image from a tar archive

docker image inspect
# Show detailed information about an image

docker image load
# Load an image from a tar archive or standard input

docker image ls
# List images stored on the local system

docker image prune
# Remove unused images

docker image pull
# Download an image from a registry

docker image push
# Upload an image to a registry

docker image rm
# Remove one or more images

docker image save
# Save one or more images to a tar archive

docker image tag
# Create a new tag for an image
```

نکته: بعضی از این commandها شکل کوتاه هم دارن. مثلاً:

```bash
docker image ls
# Same as: docker images

docker image rm nginx
# Same as: docker rmi nginx

docker image pull nginx
# Same as: docker pull nginx
```

## مقدمه Docker Image

ا-`Docker Image` یک قالب آماده و `read-only` هست که Docker از روی اون Container می‌سازه.

به شکل ساده:

```text
Dockerfile
    |
    v
Docker Image
    |
    v
Container
```

مثلاً Image مربوط به `nginx` شامل فایل‌ها، dependencyها و تنظیماتی هست که برای اجرای Nginx نیاز داریم.

وقتی می‌زنی:

```bash
docker pull nginx
```

ا-Docker Image مربوط به `nginx` دانلود میشه.

بعد با:

```bash
docker image ls
```

می‌تونی ببینیش.

و وقتی می‌زنی:

```bash
docker run nginx
```

ا-Docker از روی همون Image یک Container می‌سازه.

پس رابطه اصلی:

```text
Image = Template
Container = Running instance of an Image
```

یک Image می‌تونه چند Container بسازه:

```text
nginx Image
    |
    |-- Container 1
    |-- Container 2
    |-- Container 3
```

برای شروع یادگیری، از بین همه commandهای بالا فعلاً این 5 تا از همه مهم‌ترن:

```bash
docker image ls
# List local images

docker image pull IMAGE_NAME
# Download an image

docker image inspect IMAGE_NAME
# Show image details

docker image rm IMAGE_NAME
# Remove an image

docker image prune
# Remove unused images
```

ا-`build`، `tag`، `push` و `save/load` رو بهتره کمی بعدتر بخونی؛ وقتی رسیدی به `Dockerfile` و Registry، خیلی قابل‌فهم‌تر میشن.
