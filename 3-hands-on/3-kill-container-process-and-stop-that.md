## اگر خواستی یه کانتینر رو stop کنید ولی نشد چیکار کنیم
---
1. الان مثلا ما اومدیم این کانتینر رو stop  کنیم ببنید چی گفت و نمیخوایم ببنیم دلیلش چیه چون ما روت هستیم و نباید ارور permission بده و ما وقت برسی نداریم 
```bash
root@alfamachine:~# docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
a8ecf3e15c48   nginx:latest   "/docker-entrypoint.…"   5 minutes ago   Up 5 minutes   80/tcp    keen_kapitsa
root@alfamachine:~# docker stop keen_kapitsa 
Error response from daemon: cannot stop container: keen_kapitsa: permission denied

```

2. حال میای چیکار میکنیم از docker inspect -f کمک میگیریم برایه پیدا کردن pid
3. بعد اون pid  رو kill  میکنیم و اون پردازه متوقف میشه 

```bash
root@alfamachine:~# docker ps -a 
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
a8ecf3e15c48   nginx:latest   "/docker-entrypoint.…"   9 minutes ago   Up 9 minutes   80/tcp    nginx

========


root@alfamachine:~# docker inspect -f '{{.State.Pid}}' nginx
32157


========
root@alfamachine:~# kill -9 32157

======
root@alfamachine:~# docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS                       PORTS     NAMES
a8ecf3e15c48   nginx:latest   "/docker-entrypoint.…"   9 minutes ago   Exited (137) 3 seconds ago             nginx
root@alfamachine:~# 

```
