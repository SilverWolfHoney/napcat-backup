# NapCat + QQ Linux 备份（2026-11-24）

该备份从Debian 12导出。

## 文件清单

| 文件 | 说明 | 大小 |
|---|---|---|
| `tar/napcat-qq-linux-nodata-20261124.tar.gz` | 裸机包（程序，无数据） | 291 MB |
| `Dokcer/napcat-qq-docker-nodata-20261124.tar.gz` | Docker 镜像（无数据） | 527 MB |
| `*.sha256` | 校验 | — |

## 方案 A：Docker 恢复

```bash
sha256sum -c napcat-qq-docker-nodata-20261124.tar.gz.sha256

docker load -i napcat-qq-docker-nodata-20261124.tar.gz

docker run -d --name napcat \
  --restart unless-stopped \
  --shm-size=1g \
  -p 6099:6099 -p 3001:3001 \
  napcat-qq:20261124-nodata
```

- WebUI token 自动生成：容器内 `.../napcat/config/webui.json`
- 持久化数据：加 `-v napcat-data:/root/.config/QQ`

## 方案 B：裸机恢复

前提：x86_64 Linux + **glibc ≥ 2.28**（Debian 11+/Ubuntu 20.04+），root，能联网装包。

自动：
```bash
sha256sum -c napcat-qq-linux-nodata-20261124.tar.gz.sha256
bash restore.sh
```

手动：

```bash
apt-get install -y xvfb screen fonts-noto-cjk libgtk-3-0 libnss3 libgbm1 libasound2 libcups2 libatspi2.0-0
tar -xzf napcat-qq-linux-nodata-20261124.tar.gz -C /
screen -dmS napcat xvfb-run -a /root/Napcat/opt/QQ/qq --no-sandbox
```

- 解包后只有 `/root/Napcat`；`/root/.config/QQ` 由 QQ 首次启动自动创建
