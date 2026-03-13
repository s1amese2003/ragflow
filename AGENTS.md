# RAGFlow 项目说明（面向 GitHub Copilot）

本文档为 RAGFlow 项目提供上下文、构建指引与编码规范。
结构参考 GitHub Copilot 的[响应定制指南](https://docs.github.com/en/copilot/concepts/prompting/response-customization)。

## 1. 项目概览
RAGFlow 是一个基于深度文档理解的开源 RAG（Retrieval-Augmented Generation）引擎。
项目为全栈架构，后端采用 Python，前端采用 React/TypeScript。

- **后端**：Python 3.10+（Flask/Quart）
- **前端**：TypeScript、React、UmiJS
- **架构**：基于 Docker 的微服务。
  - `api/`：后端 API 服务。
  - `rag/`：RAG 核心逻辑（索引、检索）。
  - `deepdoc/`：文档解析与 OCR。
  - `web/`：前端应用。

## 2. 目录结构
- `api/`：后端 API 服务（Flask/Quart）。
  - `apps/`：API 蓝图（知识库、对话等）。
  - `db/`：数据库模型与服务。
- `rag/`：RAG 核心逻辑。
  - `llm/`：LLM、Embedding、Rerank 模型抽象。
- `deepdoc/`：文档解析与 OCR 模块。
- `agent/`：Agent 推理组件。
- `web/`：前端应用（React + UmiJS）。
- `docker/`：Docker 部署配置。
- `sdk/`：Python SDK。
- `test/`：后端测试。

## 3. 构建说明

### 后端（Python）
项目使用 **uv** 进行依赖管理。

1. **初始化环境**：
   ```bash
   uv sync --python 3.12 --all-extras
   uv run download_deps.py
   ```

2. **启动服务**：
   - **前置条件**：先启动依赖服务（MySQL、ES/Infinity、Redis、MinIO）。
     ```bash
     docker compose -f docker/docker-compose-base.yml up -d
     ```
   - **启动命令**：
     ```bash
     source .venv/bin/activate
     export PYTHONPATH=$(pwd)
     bash docker/launch_backend_service.sh
     ```

### 前端（TypeScript/React）
前端位于 `web/`。

1. **安装依赖**：
   ```bash
   cd web
   npm install
   ```

2. **启动开发服务**：
   ```bash
   npm run dev
   ```
   默认运行在 8000 端口。

### Docker 部署
使用 Docker 启动完整栈：
```bash
cd docker
docker compose -f docker-compose.yml up -d
```

## 4. 测试说明

### 后端测试
- **运行全部测试**：
  ```bash
  uv run pytest
  ```
- **运行指定测试**：
  ```bash
  uv run pytest test/test_api.py
  ```

### 前端测试
- **运行测试**：
  ```bash
  cd web
  npm run test
  ```

## 5. 编码规范与约定
- **Python 格式化**：使用 `ruff` 做检查与格式化。
  ```bash
  ruff check
  ruff format
  ```
- **前端 Lint**：
  ```bash
  cd web
  npm run lint
  ```
- **Pre-commit**：确保已安装并执行预提交钩子。
  ```bash
  pre-commit install
  pre-commit run --all-files
  ```
