# Fake_HYT_MDM 部署文档

## 项目概述

**项目名称**: Fake_HYT_MDM
**项目地址**: https://github.com/ytlzq0228/Fake_HYT_MDM
**部署时间**: 2025-12-02
**部署路径**: `/root/Fake_HYT_MDM`
**部署方式**: Docker Compose

## 项目说明

本项目为 Hytera SmartMDM 系统协议分析工具，基于海能达 PDC 系列对讲机与 SmartMDM 服务器之间的网络通信数据分析。
仅供学习交流使用，不涉及任何商业用途。

## 系统要求

- 操作系统: Linux
- Docker: 已安装
- Docker Compose: 已安装
- 端口需求: 2232

## 项目结构

```
/root/Fake_HYT_MDM/
├── data/                   # 数据目录
├── static/                 # 静态资源目录
├── templates/              # 模板文件目录
├── utils/                  # 工具模块目录
├── MDM.py                  # 主程序文件
├── requirements.txt        # Python 依赖
├── Dockerfile              # Docker 构建文件
├── docker-compose.yml      # Docker Compose 配置
└── README.md               # 项目说明
```

## 依赖组件

### Python 依赖 (requirements.txt)
- aprs - APRS 协议支持
- fastapi - Web 框架
- uvicorn - ASGI 服务器
- jinja2 - 模板引擎
- aiofiles - 异步文件操作
- python-multipart - 多部分表单数据处理
- itsdangerous - 安全数据签名

### Docker 配置
- 基础镜像: python:3.6-slim
- 工作目录: /app
- 暴露端口: 2232

## 部署步骤

### 1. 克隆项目

```bash
cd /root
git clone https://github.com/ytlzq0228/Fake_HYT_MDM.git
cd Fake_HYT_MDM
```

### 2. 构建并启动服务

```bash
# 使用 Docker Compose 构建并启动
docker compose up -d --build
```

### 3. 验证部署

```bash
# 查看容器状态
docker ps | grep mdm

# 查看容器日志
docker logs mdm

# 测试服务
curl http://localhost:2232
```

## 服务信息

### 容器信息
- **容器名称**: mdm
- **镜像名称**: fake_hyt_mdm-mdm:latest
- **镜像大小**: 269MB
- **启动命令**: python MDM.py

### 网络配置
- **监听地址**: 0.0.0.0:2232
- **对外端口**: 2232
- **协议**: HTTP
- **访问地址**: http://localhost:2232

### 运行状态
- **服务器**: Uvicorn (ASGI)
- **自动重载**: 启用
- **主界面**: MDM QR Code 生成页面

## 服务管理命令

### 启动服务

```bash
cd /root/Fake_HYT_MDM
docker compose up -d
```

### 停止服务

```bash
cd /root/Fake_HYT_MDM
docker compose down
```

### 重启服务

```bash
cd /root/Fake_HYT_MDM
docker compose restart
```

### 查看日志

```bash
# 实时查看日志
docker logs -f mdm

# 查看最近 100 行日志
docker logs --tail 100 mdm
```

### 查看容器状态

```bash
# 查看运行中的容器
docker ps

# 查看所有容器（包括已停止）
docker ps -a
```

### 进入容器

```bash
docker exec -it mdm /bin/bash
```

### 重新构建镜像

```bash
cd /root/Fake_HYT_MDM
docker compose up -d --build
```

## 数据管理

### 数据目录
项目数据存储在 `/root/Fake_HYT_MDM/data/` 目录下

### 备份数据

```bash
# 备份数据目录
tar -czf fake_hyt_mdm_data_$(date +%Y%m%d_%H%M%S).tar.gz /root/Fake_HYT_MDM/data/

# 备份整个项目
tar -czf fake_hyt_mdm_full_$(date +%Y%m%d_%H%M%S).tar.gz /root/Fake_HYT_MDM/
```

### 恢复数据

```bash
# 恢复数据目录
tar -xzf fake_hyt_mdm_data_YYYYMMDD_HHMMSS.tar.gz -C /
```

## 故障排查

### 服务无法启动

1. 检查端口是否被占用：
   ```bash
   netstat -tlnp | grep 2232
   # 或
   lsof -i :2232
   ```

2. 检查 Docker 服务状态：
   ```bash
   systemctl status docker
   ```

3. 查看详细日志：
   ```bash
   docker logs mdm
   ```

### 服务访问不了

1. 检查防火墙规则：
   ```bash
   # 如果使用 iptables
   iptables -L -n | grep 2232

   # 如果使用 firewalld
   firewall-cmd --list-ports
   ```

2. 检查容器网络：
   ```bash
   docker network ls
   docker network inspect fake_hyt_mdm_default
   ```

### 容器异常退出

1. 查看容器退出代码：
   ```bash
   docker ps -a | grep mdm
   ```

2. 查看详细错误日志：
   ```bash
   docker logs mdm
   ```

3. 重启容器：
   ```bash
   docker restart mdm
   ```

## 更新升级

### 更新代码

```bash
cd /root/Fake_HYT_MDM
git pull origin main
docker compose up -d --build
```

### 更新依赖

如果 requirements.txt 有变更：

```bash
cd /root/Fake_HYT_MDM
docker compose build --no-cache
docker compose up -d
```

## 安全建议

1. **端口限制**: 建议使用防火墙限制 2232 端口的访问来源
2. **反向代理**: 生产环境建议配置 Nginx 反向代理
3. **HTTPS**: 建议配置 SSL/TLS 证书启用 HTTPS
4. **日志管理**: 定期清理和归档日志文件

## 性能优化

### 资源限制

可以在 docker-compose.yml 中添加资源限制：

```yaml
services:
  mdm:
    # ... 其他配置 ...
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
        reservations:
          cpus: '0.5'
          memory: 256M
```

### 日志限制

```yaml
services:
  mdm:
    # ... 其他配置 ...
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

## 监控建议

### 健康检查

可以添加健康检查配置：

```yaml
services:
  mdm:
    # ... 其他配置 ...
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:2232"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### 监控命令

```bash
# 查看容器资源使用情况
docker stats mdm

# 查看容器详细信息
docker inspect mdm
```

## 卸载清理

### 停止并删除容器

```bash
cd /root/Fake_HYT_MDM
docker compose down
```

### 删除镜像

```bash
docker rmi fake_hyt_mdm-mdm:latest
```

### 删除项目文件

```bash
# 备份后再删除
rm -rf /root/Fake_HYT_MDM
```

## 常见问题

### Q1: 如何修改监听端口？

修改 `docker-compose.yml` 中的端口映射：

```yaml
ports:
  - "新端口:2232"
```

然后重启服务：

```bash
docker compose up -d
```

### Q2: 如何查看实时访问日志？

```bash
docker logs -f mdm
```

### Q3: 容器重启后数据会丢失吗？

容器本身的数据会丢失，但 data 目录通过卷映射保存在宿主机上，不会丢失。
如需持久化更多数据，可在 docker-compose.yml 中添加卷映射。

## 技术支持

- 项目主页: https://github.com/ytlzq0228/Fake_HYT_MDM
- Issue 提交: https://github.com/ytlzq0228/Fake_HYT_MDM/issues

## 免责声明

本项目仅供学习交流使用，所有技术内容和结论仅供学习参考。
请严格遵守相关法律法规，不得用于任何非法用途。

---

**文档版本**: 1.0
**最后更新**: 2025-12-02
**维护者**: System Administrator
