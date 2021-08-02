# OS のイメージを使ってみる

最近軽量ディストロとして alpine を使うのは非推奨[^alpine]らしいので、Debian の`-slim`系を使う。
なお 2021 年 8 月現在の安定版は buster。

```shell-session
# image を探す
$ docker search ruby

# image の pull
$ docker pull debian:buster-slim
Status: Downloaded newer image for debian:buster-slim
docker.io/library/debian:buster-slim

# ホストOSの確認
$ cat /etc/issue
Ubuntu 20.04.1 LTS \n \l

# imageのOSの確認
$ docker run debian:buster-slim cat /etc/issue
Debian GNU/Linux 10 \n \l

# コンテナでbashを動かす
# 対話的コマンドを使う際は、 `-i -t` オプションが必要。
# -i, --interactive             コンテナの STDIN にアタッチ
# -t, --tty                     疑似ターミナル (pseudo-TTY) を割り当て
$ docker run -it debian:buster-slim bash
```

[^alpine]: [performance - Why is the Alpine Docker image over 50% slower than the Ubuntu image? - Super User](https://superuser.com/questions/1219609/why-is-the-alpine-docker-image-over-50-slower-than-the-ubuntu-image)
