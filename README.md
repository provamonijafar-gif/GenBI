# BI Platform

基于 React 与 Spring Boot 的智能数据分析应用：上传表格数据，由 AI 生成可视化图表与分析结论。

## 技术栈

| 层 | 技术 |
|---|---|
| 前端 | React 18、Ant Design Pro、UmiJS Max、TypeScript、ECharts |
| 后端 | Spring Boot 2.7、MyBatis-Plus、MySQL |
| 校验 | 前端对 AI 返回的图表 JSON 做 Zod 结构校验与安全过滤 |

## 仓库结构

```
├── frontend/     # Web 前端
├── backend/      # REST API
└── backend/sql/  # 建表等 SQL
```

## 本地运行

### 环境

- Node.js 16+
- Java 8、Maven
- MySQL 8.0，并创建数据库

### 端口（开发）

| 端口 | 说明 |
|------|------|
| 12345 | 后端 API |
| 8000 | 前端开发服务器 |
| 3306 | MySQL |

### 数据库

```sql
-- 在 MySQL 中执行
source backend/sql/create_table.sql;
```

### 后端

```bash
cd backend
mvn spring-boot:run
```

启动后可访问接口文档：http://localhost:12345/api/doc.html  

### 前端

```bash
cd frontend
npm install
npm run dev
```

默认访问：http://localhost:8000  

### 生成前端 API 代码

后端保持运行时：

```bash
cd frontend
npm run openapi
```

### 账号

通过接口注册后登录，具体以当前后端路由为准。
