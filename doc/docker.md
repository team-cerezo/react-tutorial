# [WIP]Docker

コンテナの起動。

```console
docker run -d --name=my-nginx -p 8000:80 nginx
```

コンテナの停止。

```console
docker stop my-nginx
```

コンテナの再開。

```console
docker start my-nginx
```

コンテナの破棄。

```console
docker rm my-nginx
```

起動しているコンテナの一覧。

```console
docker ps
```

```none
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                  NAMES
bfe103a8cf35        team-cerezo/hello-ui    "nginx -g 'daemon of…"   9 minutes ago       Up 8 minutes        0.0.0.0:3000->80/tcp   hello-ui
469b37ff1954        team-cerezo/hello-api   "java -jar hello-api…"   9 minutes ago       Up 9 minutes                               hello-api
```

（停止しているものも含めた）すべてのコンテナの一覧。

```console
docker ps -a
```

```none
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS                     PORTS                  NAMES
5319e0f65e9d        nginx                   "nginx -g 'daemon of…"   3 minutes ago       Exited (0) 2 minutes ago                          my-nginx
bfe103a8cf35        team-cerezo/hello-ui    "nginx -g 'daemon of…"   8 minutes ago       Up 8 minutes               0.0.0.0:3000->80/tcp   hello-ui
469b37ff1954        team-cerezo/hello-api   "java -jar hello-api…"   9 minutes ago       Up 9 minutes                                      hello-api
```

動作しているDockerコンテナの中に入る。（正確には、動作しているDockerコンテナに対してプロセスを実行する、かな）

```console
docker exec -it hello-ui /bin/bash
```

Rascaloid（ http://localhost:3000 ）とUIの参照実装を動かす（ http://localhost:3003 ）。

```console
# api
docker run -d --name=rascaloid-api -p 3000:3000 kawasima/rascaloid
# ui(RI)
docker run -d --name=rascaloid-ri -e RASCALOID_HOST=rascaloid-api -e RASCALOID_PORT=3000 -e HTTP_PROXY= -p 3003:3003 --link rascaloid-api kawasima/rascaloid-ri
```

※Docker Toolboxを使っている場合（Win7）は更に `docker-machine ssh default -L 3000:localhost:3000` でポートフォワードの設定をする

Swagger UIは http://localhost:3000/webjars/swagger-ui/index.html?url=http://localhost:3000/assets/rascaloid_api.yaml から開く。
