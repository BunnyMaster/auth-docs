## Docker 镜像轮询拉取

>[!IMPORTANT]
>此方式存在一定延迟 `--interval 300`（延迟为 5 分钟）
>因为邮件发送需要转义 `%`=`%40`

### 基础使用

```bash
# 清除之前旧的
docker rm -f watchtower

# 5分钟一次
docker run -d --name watchtower --restart unless-stopped \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /home/<user>/.docker/config.json:/config.json \
  nickfedor/watchtower \
  --interval 300 --cleanup \
  auth-web service-auth service-system auth-gateway
```

### 更新时发送邮件告知

```bash
docker rm -f watchtower

docker run -d --name watchtower --restart unless-stopped \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /home/<user>/.docker/config.json:/config.json \
  -e WATCHTOWER_NOTIFICATION_URL="smtp://user%40example.com:<授权码>@smtp.example.com:465/?from=user%40example.com&to=notify%40example.com&encryption=ImplicitTLS" \
  -e WATCHTOWER_NOTIFICATION_TITLE_TAG="容器镜像更新" \
  nickfedor/watchtower \
  --interval 300 --cleanup \
  auth-web service-auth service-system auth-gateway
```

### 几分钟发一份邮件

>[!TIP]
>若不需要删除 `WATCHTOWER_NOTIFICATION_REPORT=true` 参数

```bash
docker rm -f watchtower

docker run -d --name watchtower --restart unless-stopped \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /home/<user>/.docker/config.json:/config.json \
  -e WATCHTOWER_NOTIFICATION_URL="smtp://user%40example.com:<授权码>@smtp.example.com:465/?from=user%40example.com&to=notify%40example.com&encryption=ImplicitTLS" \
  -e WATCHTOWER_NOTIFICATION_REPORT=true \
  -e WATCHTOWER_NOTIFICATION_TITLE_TAG="容器镜像更新" \
  nickfedor/watchtower \
  --interval 300 --cleanup \
  auth-web service-auth service-system auth-gateway
```
