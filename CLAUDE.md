# CLAUDE.md

本文件为 Claude Code (claude.ai/code) 在此代码仓库中工作时提供指导。

## 项目概述

RAGFlow 是一个基于深度文档理解的开源 RAG（检索增强生成）引擎。这是一个全栈应用，包含：
- Python 后端（基于 Flask 的 API 服务器）
- React/TypeScript 前端（使用 UmiJS 构建）
- 微服务架构，支持 Docker 部署
- 多种数据存储（MySQL、Elasticsearch/Infinity、Redis、MinIO）

## 架构说明

### 后端 (`/api/`)
- **主服务器**：`api/ragflow_server.py` - Flask 应用入口
- **应用模块**：`api/apps/` 中的模块化 Flask 蓝图，负责不同功能：
  - `kb_app.py` - 知识库管理
  - `dialog_app.py` - 对话/会话处理
  - `document_app.py` - 文档处理
  - `canvas_app.py` - Agent 工作流画布
  - `file_app.py` - 文件上传/管理
- **服务层**：`api/db/services/` 中的业务逻辑
- **数据模型**：`api/db/db_models.py` 中的数据库模型

### 核心处理 (`/rag/`)
- **文档处理**：`deepdoc/` - PDF 解析、OCR、布局分析
- **LLM 集成**：`rag/llm/` - 聊天、嵌入、重排序的模型抽象
- **RAG 流水线**：`rag/flow/` - 分块、解析、分词
- **图 RAG**：`rag/graphrag/` - 知识图谱构建和查询

### Agent 系统 (`/agent/`)
- **组件**：模块化工作流组件（LLM、检索、分类等）
- **模板**：`agent/templates/` 中的预构建 agent 工作流
- **工具**：外部 API 集成（Tavily、Wikipedia、SQL 执行等）

### 前端 (`/web/`)
- React/TypeScript，使用 UmiJS 框架
- Ant Design + shadcn/ui 组件
- 使用 Zustand 进行状态管理
- Tailwind CSS 样式

## 常用开发命令

### 后端开发
```bash
# 安装 Python 依赖
uv sync --python 3.12 --all-extras
uv run download_deps.py
pre-commit install

# 启动依赖服务
docker compose -f docker/docker-compose-base.yml up -d

# 运行后端（需要先启动依赖服务）
source .venv/bin/activate
export PYTHONPATH=$(pwd)
bash docker/launch_backend_service.sh

# 运行测试
uv run pytest

# 代码检查
ruff check
ruff format
```

### 前端开发
```bash
cd web
npm install
npm run dev        # 开发服务器
npm run build      # 生产构建
npm run lint       # ESLint 检查
npm run test       # Jest 测试
```

### Docker 开发
```bash
# 使用 Docker 运行完整堆栈
cd docker
docker compose -f docker-compose.yml up -d

# 查看服务器状态
docker logs -f ragflow-server

# 重新构建镜像
docker build --platform linux/amd64 -f Dockerfile -t infiniflow/ragflow:nightly .
```

## 关键配置文件

- `docker/.env` - Docker 部署的环境变量
- `docker/service_conf.yaml.template` - 后端服务配置
- `pyproject.toml` - Python 依赖和项目配置
- `web/package.json` - 前端依赖和脚本

## 测试

- **Python**：pytest，使用标记（p1/p2/p3 优先级）
- **前端**：Jest 配合 React Testing Library
- **API 测试**：`test/` 和 `sdk/python/test/` 中的 HTTP API 和 SDK 测试

## 数据库引擎

RAGFlow 支持在 Elasticsearch（默认）和 Infinity 之间切换：
- 在 `docker/.env` 中设置 `DOC_ENGINE=infinity` 以使用 Infinity
- 需要重启容器：`docker compose down -v && docker compose up -d`

## 开发环境要求

- Python 3.10-3.12
- Node.js >=18.20.4
- Docker & Docker Compose
- uv 包管理器
- 16GB+ 内存，50GB+ 磁盘空间
