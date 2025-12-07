# Fake_HYT_MDM

本项目基于https://github.com/ytlzq0228/Fake_HYT_MDM优化了说明文档及部署相关配置。

一个基于逆向工程的 Hytera SmartMDM 服务器模拟器，用于学习和研究海能达 PDC 系列对讲机的 MDM（移动设备管理）协议。

## 项目简介

本项目通过分析海能达 PDC 系列对讲机（如 PDC680、PDC760）与 SmartMDM 服务器之间的网络通信数据，实现了一个功能完整的 MDM 服务器模拟器。该项目可用于：

- 学习现代对讲机设备管理协议
- 理解 MDM 系统的工作机制
- 进行设备通信行为研究
- 开发自定义设备管理解决方案

## 主要功能

### 核心功能
- **设备注册与认证**：支持对讲机设备的注册和身份验证
- **设备管理**：实时监控设备状态、位置和配置信息
- **任务管理**：支持向设备下发各类管理任务
- **位置服务**：集成 APRS 协议，支持设备位置上报和追踪
- **Web 控制台**：提供可视化的设备管理界面

### 技术特性
- **双协议支持**：同时提供 HTTP 和 TCP 服务
- **实时通信**：基于 FastAPI 的异步处理架构
- **数据持久化**：支持设备数据和配置的持久化存储
- **地图集成**：支持百度地图和 OpenStreetMap
- **容器化部署**：完整的 Docker 支持，开箱即用

## 快速开始

### 环境要求

- Docker 20.10+
- Docker Compose 2.0+
- 开放端口：2232 (HTTP)、2233 (TCP)

### 使用 Docker Compose 部署（推荐）

```bash
# 1. 克隆项目
git clone https://github.com/espie2023/Fake_HYT_MDM.git
cd Fake_HYT_MDM

# 2. 启动服务
docker compose up -d

# 3. 查看日志
docker logs -f mdm

# 4. 访问 Web 界面
# 浏览器打开 http://your-server-ip:2232
```

### 本地开发部署

```bash
# 1. 克隆项目
git clone https://github.com/espie2023/Fake_HYT_MDM.git
cd Fake_HYT_MDM

# 2. 安装依赖
pip install -r requirements.txt

# 3. 启动服务
python MDM.py
```

## 项目结构

```
Fake_HYT_MDM/
├── data/                    # 数据存储目录
│   ├── sys_conf.json       # 系统配置文件
│   └── tasks.json          # 任务配置文件
├── static/                  # 静态资源文件
│   ├── style.css           # 样式文件
│   └── response.json       # 响应模板
├── templates/               # HTML 模板
│   └── dashboard.html      # 管理控制台
├── utils/                   # 工具模块
│   ├── aprs_report.py      # APRS 位置上报
│   ├── data_memory_cache.py # 数据缓存管理
│   ├── responses.py        # 响应处理
│   ├── ses_service.py      # TCP 服务
│   └── task_center.py      # 任务中心
├── MDM.py                   # 主程序入口
├── requirements.txt         # Python 依赖
├── Dockerfile              # Docker 镜像构建
├── docker-compose.yml      # Docker Compose 配置
├── DEPLOYMENT.md           # 详细部署文档
└── README.md               # 项目说明
```

## 配置说明

### 系统配置 (data/sys_conf.json)

```json
{
  "http_service_port": "2232",
  "tcp_service_port": "2233",
  "server_ip": "your-domain.com",
  "sys_admin": {
    "admin": {
      "password": "sha256-hashed-password",
      "devices": ["any"],
      "map_type": "baidu"
    }
  }
}
```

**配置项说明：**
- `http_service_port`: HTTP 服务端口（默认 2232）
- `tcp_service_port`: TCP 服务端口（默认 2233）
- `server_ip`: 服务器域名或 IP 地址
- `sys_admin`: 管理员账户配置
  - `password`: SHA-256 加密的密码
  - `devices`: 可管理的设备列表，`["any"]` 表示所有设备
  - `map_type`: 地图类型，可选 `baidu` 或 `openstreet`

### 生成密码哈希

```python
import hashlib
password = "your_password"
hashed = hashlib.sha256(password.encode()).hexdigest()
print(hashed)
```

### 地图配置

项目支持两种地图服务：

1. **百度地图**：需要在 `templates/dashboard.html` 中配置有效的 API Key
2. **OpenStreetMap**：无需 API Key，开箱即用

## 使用指南

### 1. 访问 Web 控制台

浏览器访问 `http://your-server-ip:2232`，使用配置的管理员账号登录。

### 2. 设备配置

在对讲机设备上配置 MDM 服务器地址：
- 服务器地址：`http://your-server-ip:2232`
- TCP 服务地址：`your-server-ip:2233`

### 3. 查看设备状态

登录 Web 控制台后，可以查看：
- 在线设备列表
- 设备实时位置（地图显示）
- 设备详细信息
- 设备任务状态

### 4. 管理任务

系统支持多种设备管理任务：
- 状态上报任务
- 位置上报任务
- 配置同步任务
- 自定义任务

## 服务管理

### Docker Compose 管理命令

```bash
# 启动服务
docker compose up -d

# 停止服务
docker compose down

# 重启服务
docker compose restart

# 查看日志
docker logs -f mdm

# 查看容器状态
docker ps | grep mdm

# 进入容器
docker exec -it mdm /bin/bash
```

### 更新服务

```bash
# 拉取最新代码
git pull origin main

# 重新构建并启动
docker compose up -d --build
```

## 技术栈

- **Web 框架**: FastAPI (异步高性能)
- **ASGI 服务器**: Uvicorn
- **模板引擎**: Jinja2
- **协议支持**: APRS (业余无线电定位)
- **数据处理**: JSON (配置和数据存储)
- **容器化**: Docker & Docker Compose
- **前端**: HTML/CSS + 百度地图 API / OpenStreetMap

## 开发说明

### 本地开发环境

```bash
# 安装开发依赖
pip install -r requirements.txt

# 启动开发服务器（支持热重载）
uvicorn MDM:app --reload --host 0.0.0.0 --port 2232
```

### 代码结构说明

- `MDM.py`: FastAPI 应用主入口，定义 HTTP API 路由
- `utils/ses_service.py`: TCP 服务实现，处理设备长连接
- `utils/task_center.py`: 任务调度中心，管理设备任务
- `utils/aprs_report.py`: APRS 协议处理
- `utils/data_memory_cache.py`: 内存缓存管理

## 故障排查

### 服务无法启动

1. 检查端口占用：
   ```bash
   netstat -tlnp | grep 2232
   lsof -i :2232
   ```

2. 查看容器日志：
   ```bash
   docker logs mdm
   ```

### 设备无法连接

1. 检查防火墙规则
2. 确认服务器 IP 配置正确
3. 验证端口是否开放（2232、2233）

### 地图不显示

1. 检查百度地图 API Key 是否有效
2. 确认 API Key 绑定的域名正确
3. 可切换到 OpenStreetMap（无需 API Key）

详细的故障排查和运维指南请参考 [DEPLOYMENT.md](DEPLOYMENT.md)

## 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本项目
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 提交 Pull Request

## 许可证

本项目仅供学习和研究使用。

## 免责声明

本项目或文档中所涉及的 Hytera SmartMDM 系统协议分析内容，均基于海能达 PDC 系列对讲机（如 PDC680、PDC760）与 SmartMDM 服务器之间通过网络通信所产生的未加密数据抓包所得。所有分析工作**完全基于合法拥有的设备和网络环境中进行的数据观察**，未涉及对海能达任何软硬件产品的逆向工程、反编译、破解或其他形式的逆向分析。

### 本项目的目的仅限于

- 网络通信协议学习
- 无线通信设备行为理解
- 提高个人或业余无线电爱好者对现代通信设备工作机制的认知
- 网络与设备之间交互行为的透明化研究

### 本项目不涉及

- 破解海能达产品授权机制
- 复制、模仿或绕过其商业逻辑
- 未经授权地接入其生产环境
- 任何形式的商业或营利用途

### 重要提示

- 本文档或项目中所述的所有品牌、产品名称及相关协议均归其原厂商（海能达通信股份有限公司）所有
- 所有技术内容和结论仅供学习交流使用
- 任何组织或个人不得利用本文档或项目中提供的信息从事违反《中华人民共和国著作权法》《中华人民共和国计算机软件保护条例》或其他相关法律法规的行为
- 本项目作者不承担因使用本文档内容所引起的任何法律责任或纠纷

若您是相关厂商或权利方，并对本项目内容有任何疑问或异议，欢迎及时联系项目作者，我们将积极配合处理。

## 致谢

感谢所有为开源社区做出贡献的开发者们。

---

**项目仓库**: [https://github.com/espie2023/Fake_HYT_MDM](https://github.com/ytlzq0228/Fake_HYT_MDM)

**问题反馈**: [https://github.com/espie2023/Fake_HYT_MDM/issues
](https://github.com/ytlzq0228/Fake_HYT_MDM)
