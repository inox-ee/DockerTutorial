# Dockerfile 主要コマンドを使ったサンプル

- [introduction-docker/handson/dockerfile at master · y-ohgi/introduction-docker](https://github.com/y-ohgi/introduction-docker/tree/master/handson/dockerfile)

## `WORKDIR` や `COPY` の前後でディレクトリを確認

基本 `RUN` コマンドの結果は標準出力に出てこないが、`--progress="plain"` を指定することで確認できる。キャッシュがある場合は `--no-cache` オプションをつければよい。

```sh
# WORKDIR 後
RUN pwd
#6 0.162 /scripts
RUN ls -la
#7 0.341 total 8
#7 0.341 drwxr-xr-x 2 root root 4096 Aug  2 03:42 .
#7 0.341 drwxr-xr-x 1 root root 4096 Aug  2 03:44 ..

# COPY 後
RUN ls -la
#10 0.295 total 24
#10 0.295 drwxr-xr-x 1 root root 4096 Aug  2 03:45 .
#10 0.295 drwxr-xr-x 1 root root 4096 Aug  2 03:45 ..
#10 0.295 -rw-r--r-- 1 root root  477 Aug  2 03:43 Dockerfile
#10 0.295 -rw-r--r-- 1 root root  218 Aug  2 03:31 README.md
#10 0.295 -rw-r--r-- 1 root root   47 Aug  2 03:32 app.js
#10 0.295 -rw-r--r-- 1 root root   81 Aug  2 03:32 package.json

# chown & USER app 後
RUN ls -la
#9 0.353 total 32
#9 0.353 drwxr-xr-x 1 app  app  4096 Aug  2 03:49 .
#9 0.353 drwxr-xr-x 1 root root 4096 Aug  2 03:49 ..
#9 0.353 -rw-r--r-- 1 app  app   469 Aug  2 03:49 Dockerfile
#9 0.353 -rw-r--r-- 1 app  app  1085 Aug  2 03:49 README.md
#9 0.353 -rw-r--r-- 1 app  app    47 Aug  2 03:32 app.js
#9 0.353 -rw-r--r-- 1 app  app   151 Aug  2 03:49 package-lock.json
#9 0.353 -rw-r--r-- 1 app  app    81 Aug  2 03:32 package.json
RUN whoami
#10 0.329 app
```
