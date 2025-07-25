
<p align="center">
  <a href="https://veops.cn">
    <img src="https://github.com/user-attachments/assets/c5cfb272-899b-418d-9e69-8e1dd07db0f6" alt="维易CMDB"/>
  </a>
</p>

<h4 align="center">简单、轻量、通用的运维配置管理数据库</h4>

<p align="center">
  <a href="https://github.com/veops/cmdb/blob/master/LICENSE"><img src="https://img.shields.io/badge/License-AGPLv3-brightgreen" alt="License: GPLv3"></a>
  <a href="https://github.com/veops/cmdb/releases"><img alt="the latest release version" src="https://img.shields.io/github/v/release/veops/cmdb?color=75C1C4&include_prereleases&label=Release&logo=github&logoColor=white"></a>
  <a href="https:https://github.com/sendya/ant-design-pro-vue"><img src="https://img.shields.io/badge/UI-Ant%20Design%20Pro%20Vue-green" alt="UI"></a>
  <a href="https://github.com/pallets/flask"><img src="https://img.shields.io/badge/API-Flask-bright" alt="API"></a>
  <a href="https://github.com/veops/cmdb/stargazers"><img src="https://img.shields.io/github/stars/veops/cmdb" alt="Stars Badge"/></a>
  <a href="https://github.com/veops/cmdb"><img src="https://img.shields.io/github/forks/veops/cmdb" alt="Forks Badge"/></a>
</p>
<p align="center">
  中文(简体) · <a href="docs/README_en.md">English</a>
</p>

## 系统介绍

Pixocial CMDB是一个简洁、轻量且高度可定制的运维配置管理数据库（CMDB）。它支持灵活的模型配置和资源自动发现，旨在为企业提供便捷的资产管理解决方案，帮助运维团队高效地管理 IT 基础设施和服务。

- 产品文档：[https://veops.cn/docs/](https://veops.cn/docs/)
- 在线体验：[https://vault-cmdb.int.pixocial.com](https://vault-cmdb.int.pixocial.com)

### 主要功能

- **自定义模型和模型关系**：支持模型属性的自定义，包括下拉列表、字体颜色、计算属性等高级功能，满足不同业务需求。
- **自动发现资源**：支持计算机、网络设备、存储设备、数据库、中间件、公有云资源等自动发现。
- **多维度视图展示**：包括资源视图、层级视图、关系视图等，帮助运维人员全面管理资源。
- **细粒度权限控制**：通过精确的访问控制和完备的操作日志保障系统的安全性。
- **全面的资源搜索功能**：支持灵活的资源和关系搜索，快速定位和操作资源。
- **集成 IP 地址管理（IPAM）和数据中心基础设施管理（DCIM）**：简化网络资源和数据中心设备的管理。


### 系统优势

- 灵活性
  + 无需指定固定运维场景，支持自由配置并内置多种模板
  + 支持自动发现和入库 IT 资产，快速搭建资产管理系统
- 安全性
  + 细粒度的权限控制机制，确保资源管理的安全性
  + 完整的操作日志记录，便于审计和问题追踪
- 多应用
  + 提供多种视图展示方式，满足不同场景的需求
  + 强大的 API 接口，支持深度集成
  + 支持定义属性触发器和计算属性，增强数据处理能力

### 技术栈

+ 后端：Python [3.8-3.11]
+ 数据存储：MySQL、Redis
+ 前端：Vue.js
+ UI组件库：Ant Design Vue

### 系统概览

<table style="border-collapse: collapse;">
  <tr>
    <td style="padding: 5px;background-color:#fff;">
      <img width="400" src="https://github.com/user-attachments/assets/6d2df835-ae93-4d91-9bd9-213c270eca7a"/>
    </td>
    <td style="padding: 5px;background-color:#fff;">
      <img width="400" src="https://github.com/user-attachments/assets/cb8b598a-a1f9-4c74-adf1-6e59aea2c9b3"/>
    </td>
  </tr>

  <tr>
    <td style="padding: 5px;background-color:#fff;">
      <img width="400" src="https://github.com/user-attachments/assets/b440224f-53c3-4b7f-a9be-285d7a4b848f"/>
    </td>
    <td style="padding: 5px;background-color:#fff;">
      <img width="400" src="https://github.com/user-attachments/assets/f457d5a0-b60b-4949-b94e-020f4c61444b"/>
    </td>
  </tr>
</table>

## 快速开始

### 1. 生产部署

修改 .env 文件中的配置项，确保数据库、Redis 等服务已正确配置。
```yaml
 MYSQL_ROOT_PASSWORD=''
 MYSQL_HOST=''
 MYSQL_PORT=3306
 MYSQL_USER='root'
 MYSQL_DATABASE='cmdb'
 MYSQL_PASSWORD=''

 CACHE_REDIS_HOST=''
 CACHE_REDIS_PORT=7379
 CACHE_REDIS_PASSWORD=''
```

在mysql中执行以下命令：
```
set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
```

启动服务：
``
docker-compose -f docker-compose-prod.yaml up -d
``

### 2. 访问
- 打开浏览器并访问: [http://127.0.0.1:8000](http://127.0.0.1:8000)
- 用户名: demo 或者 admin
- 密码: 123456

## 代码贡献
我们欢迎所有开发者贡献代码，改善和扩展这个项目。请先阅读我们的[贡献指南](docs/CONTRIBUTING.md)。此外，您还可以通过社交媒体、活动和分享来支持 Veops 的开源。

<a href="https://github.com/veops/cmdb/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=veops/cmdb" />
</a>
