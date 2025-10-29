# Azure Search OpenAI Demo - 技術文件 / Technical Documentation

## 目錄 / Table of Contents

1. [專案概述 / Project Overview](#專案概述--project-overview)
2. [系統架構 / System Architecture](#系統架構--system-architecture)
3. [技術堆疊 / Technology Stack](#技術堆疊--technology-stack)
4. [目錄結構 / Directory Structure](#目錄結構--directory-structure)
5. [核心組件 / Core Components](#核心組件--core-components)
6. [開發環境設置 / Development Environment Setup](#開發環境設置--development-environment-setup)
7. [部署指南 / Deployment Guide](#部署指南--deployment-guide)
8. [數據處理流程 / Data Processing Pipeline](#數據處理流程--data-processing-pipeline)
9. [API 端點 / API Endpoints](#api-端點--api-endpoints)
10. [測試策略 / Testing Strategy](#測試策略--testing-strategy)
11. [維護與擴展 / Maintenance and Extension](#維護與擴展--maintenance-and-extension)

---

## 專案概述 / Project Overview

### 中文說明

這是一個使用檢索增強生成（RAG）模式的企業級 ChatGPT 風格應用程式。它結合了 Azure OpenAI Service 和 Azure AI Search，讓使用者能夠與自己的企業文檔進行對話式查詢。

**主要特性：**
- 多輪對話（Chat）和單輪問答（Q&A）介面
- 為每個回答提供引用來源和思考過程
- 支援多種文檔格式的資料索引和檢索
- 可選的多模態模型支援（圖像理解）
- 可選的語音輸入/輸出功能
- 使用者登入和數據訪問控制
- 使用 Application Insights 進行性能追蹤和監控

### English Description

This is an enterprise-grade ChatGPT-like application using the Retrieval Augmented Generation (RAG) pattern. It combines Azure OpenAI Service and Azure AI Search to enable conversational queries over enterprise documents.

**Key Features:**
- Multi-turn chat and single-turn Q&A interfaces
- Citations and thought process for each answer
- Data indexing and retrieval supporting multiple document formats
- Optional multimodal model support (image understanding)
- Optional speech input/output functionality
- User authentication and data access control
- Performance tracing and monitoring with Application Insights

---

## 系統架構 / System Architecture

### 架構圖 / Architecture Diagram

```
┌─────────────────┐
│   使用者/User   │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│         前端 / Frontend                 │
│  (React + TypeScript + Vite)            │
│  - Chat Interface                       │
│  - Settings Panel                       │
│  - Multi-language Support               │
└────────┬────────────────────────────────┘
         │ HTTP/REST API
         ▼
┌─────────────────────────────────────────┐
│         後端 / Backend                  │
│  (Python + Quart Framework)             │
│  - API Routes                           │
│  - RAG Approaches                       │
│  - Authentication                       │
└────────┬────────────────────────────────┘
         │
    ┌────┴────┐
    ▼         ▼
┌─────────┐ ┌──────────────┐
│ Azure   │ │ Azure        │
│ OpenAI  │ │ AI Search    │
│ Service │ │              │
└─────────┘ └──────────────┘
                   │
                   ▼
            ┌──────────────┐
            │ Azure Blob   │
            │ Storage      │
            └──────────────┘
```

### 資料流 / Data Flow

1. **資料準備階段 / Data Preparation:**
   - 文檔上傳至 Azure Blob Storage
   - 使用 Document Intelligence 解析文檔
   - 生成文本和圖像嵌入向量
   - 索引至 Azure AI Search

2. **查詢處理階段 / Query Processing:**
   - 使用者輸入查詢
   - 系統使用 Azure AI Search 檢索相關文檔片段
   - 將檢索結果和查詢一起傳送給 Azure OpenAI
   - 生成帶有引用來源的回答

---

## 技術堆疊 / Technology Stack

### 前端 / Frontend
- **框架 / Framework:** React 18.3+
- **語言 / Language:** TypeScript 5.6+
- **建置工具 / Build Tool:** Vite 6.0+
- **UI 庫 / UI Library:** Fluent UI (@fluentui/react, @fluentui/react-components)
- **路由 / Routing:** React Router 6.28+
- **國際化 / i18n:** i18next 24.2+
- **樣式 / Styling:** CSS Modules
- **狀態管理 / State Management:** React Hooks

### 後端 / Backend
- **語言 / Language:** Python 3.10+
- **Web 框架 / Web Framework:** Quart (異步 ASGI 框架)
- **Azure SDK:**
  - azure-search-documents - Azure AI Search 整合
  - azure-identity - 認證管理
  - azure-storage-blob - Blob 儲存
  - openai - OpenAI/Azure OpenAI 客戶端
  - azure-monitor-opentelemetry - 監控和遙測

### 基礎設施 / Infrastructure
- **IaC 工具 / IaC Tool:** Azure Bicep
- **部署工具 / Deployment Tool:** Azure Developer CLI (azd)
- **預設主機 / Default Host:** Azure Container Apps
- **備選主機 / Alternative Host:** Azure App Service
- **監控 / Monitoring:** Azure Monitor + Application Insights

### 開發工具 / Development Tools
- **測試框架 / Testing Framework:** pytest
- **E2E 測試 / E2E Testing:** Playwright
- **程式碼品質 / Code Quality:** Ruff, Black, MyPy
- **前端格式化 / Frontend Formatting:** Prettier
- **版本控制 / Version Control:** Git
- **CI/CD:** GitHub Actions, Azure DevOps

---

## 目錄結構 / Directory Structure

```
azure-search-openai-demo/
│
├── .azdo/                          # Azure DevOps 管線配置
│   └── pipelines/
│       └── azure-dev.yml
│
├── .devcontainer/                  # VS Code 開發容器配置
│   └── devcontainer.json
│
├── .github/                        # GitHub Actions 工作流程
│   └── workflows/
│       └── azure-dev.yml
│
├── .vscode/                        # VS Code 設置
│
├── app/                            # 主要應用程式碼
│   ├── backend/                    # Python 後端
│   │   ├── approaches/            # RAG 方法實作
│   │   │   ├── approach.py        # 基礎類別
│   │   │   ├── retrievethenread.py # Ask 方法
│   │   │   ├── chatreadretrieveread.py # Chat 方法
│   │   │   └── prompts/           # Prompt 模板
│   │   ├── chat_history/          # 聊天歷史管理
│   │   ├── core/                  # 核心工具類別
│   │   ├── prepdocslib/          # 文檔處理庫
│   │   ├── app.py                 # 主應用程式
│   │   ├── config.py              # 配置管理
│   │   ├── prepdocs.py           # 數據準備腳本
│   │   └── requirements.txt       # Python 依賴
│   │
│   ├── frontend/                   # React 前端
│   │   ├── public/                # 靜態資源
│   │   ├── src/
│   │   │   ├── api/              # API 客戶端
│   │   │   ├── components/       # React 組件
│   │   │   ├── locales/          # 國際化翻譯
│   │   │   ├── pages/            # 頁面組件
│   │   │   └── index.tsx         # 入口點
│   │   ├── package.json           # Node 依賴
│   │   └── vite.config.ts        # Vite 配置
│   │
│   ├── start.sh                   # Linux/Mac 啟動腳本
│   └── start.ps1                  # Windows 啟動腳本
│
├── data/                           # 示例文檔資料
│   ├── *.pdf                      # PDF 文件
│   ├── *.md                       # Markdown 文件
│   └── Json_Examples/             # JSON 示例
│
├── docs/                           # 文檔
│   ├── README.md
│   ├── architecture.md
│   ├── deploy_features.md
│   ├── data_ingestion.md
│   └── ... (更多文檔)
│
├── evals/                          # 評估腳本
│
├── infra/                          # Azure 基礎設施配置
│   ├── main.bicep                 # 主要 Bicep 檔案
│   ├── main.parameters.json       # 參數檔案
│   └── core/                      # 可重用的 Bicep 模組
│       ├── ai/                    # AI 服務
│       ├── host/                  # 主機配置
│       ├── search/                # 搜尋服務
│       ├── security/              # 安全性
│       └── storage/               # 儲存
│
├── scripts/                        # 工具腳本
│   ├── auth_init.sh/.ps1          # 認證初始化
│   ├── auth_update.sh/.ps1        # 認證更新
│   ├── prepdocs.sh/.ps1           # 資料準備
│   └── roles.sh/.ps1              # 角色設置
│
├── tests/                          # 測試套件
│   ├── conftest.py                # pytest 配置
│   ├── e2e.py                     # E2E 測試
│   ├── test_app.py                # 應用整合測試
│   └── test_*.py                  # 單元測試
│
├── AGENTS.md                       # AI 代理指南
├── CONTRIBUTING.md                 # 貢獻指南
├── README.md                       # 專案說明
├── azure.yaml                      # Azure Developer CLI 配置
├── pyproject.toml                  # Python 專案配置
└── requirements-dev.txt            # 開發依賴

```

---

## 核心組件 / Core Components

### 1. 後端 RAG 方法 / Backend RAG Approaches

#### RetrieveThenReadApproach (Ask 方法)
**檔案位置:** app/backend/approaches/retrievethenread.py

**功能描述:**
- 單輪問答處理
- 直接從使用者查詢檢索相關文檔
- 使用檢索到的內容生成答案

**流程:**
```
使用者查詢 → 生成查詢向量 → Azure AI Search 檢索 
→ 重新排序結果 → 構建 Prompt → OpenAI 生成答案
```

#### ChatReadRetrieveReadApproach (Chat 方法)
**檔案位置:** app/backend/approaches/chatreadretrieveread.py

**功能描述:**
- 多輪對話處理
- 包含查詢重寫步驟以處理對話上下文
- 維護聊天歷史

**流程:**
```
使用者查詢 + 歷史 → 查詢重寫 → 生成查詢向量 
→ Azure AI Search 檢索 → 重新排序結果 → 構建 Prompt 
→ OpenAI 生成答案 → 更新歷史
```

### 2. 前端組件 / Frontend Components

#### 主要頁面 / Main Pages

**Chat 頁面** (app/frontend/src/pages/chat/Chat.tsx)
- 多輪對話介面
- 支援流式回應
- 顯示引用來源
- 可配置設定面板

**Ask 頁面** (app/frontend/src/pages/ask/Ask.tsx)
- 單次問答介面
- 即時回應顯示
- 引用來源展示

#### 關鍵組件 / Key Components

**Settings 組件** (app/frontend/src/components/Settings.tsx)
- 檢索設定（搜尋模式、嚴格性）
- Prompt 配置
- GPT 模型選擇
- 回應長度控制

**Answer 組件** (app/frontend/src/components/Answer/)
- 回答渲染
- 引用來源顯示
- 思考過程展示
- Markdown 解析

### 3. 基礎設施組件 / Infrastructure Components

#### Azure 資源 / Azure Resources

**main.bicep 主要配置:**
```
- Azure OpenAI Service (GPT 模型)
- Azure AI Search (Basic/Standard 層級)
- Azure Container Apps / App Service
- Azure Container Registry
- Azure Blob Storage
- Azure AI Document Intelligence
- Azure Monitor + Application Insights
- (選用) Azure Cosmos DB (聊天歷史)
- (選用) Azure AI Vision (多模態)
```

---

## 開發環境設置 / Development Environment Setup

### 先決條件 / Prerequisites

```bash
# 必要工具 / Required Tools
- Azure Developer CLI (azd) >= 1.17.0
- Python 3.10/3.11/3.12/3.13
- Node.js 20+
- Git
- PowerShell 7+ (Windows only)
```

### 本地開發設置 / Local Development Setup

```bash
# 1. 克隆儲存庫 / Clone Repository
git clone https://github.com/Wiiki0807/azure-search-openai-demo.git
cd azure-search-openai-demo

# 2. 安裝 Python 依賴 / Install Python Dependencies
python -m pip install -r requirements-dev.txt

# 3. 安裝前端依賴 / Install Frontend Dependencies
cd app/frontend
npm install
cd ../..

# 4. 安裝 pre-commit hooks
pre-commit install

# 5. Azure 登入 / Azure Login
azd auth login

# 6. 建立新環境 / Create New Environment
azd env new

# 7. 部署到 Azure / Deploy to Azure
azd up

# 8. 運行本地開發伺服器 / Run Local Dev Server
./app/start.sh  # Linux/Mac
# 或 / or
./app/start.ps1  # Windows
```

### GitHub Codespaces

最快速的方式是使用 GitHub Codespaces：
1. 點擊 README 中的 "Open in GitHub Codespaces" 按鈕
2. 等待容器啟動（可能需要幾分鐘）
3. 開啟終端機執行 azd up

### VS Code Dev Containers

1. 安裝 Docker Desktop
2. 安裝 VS Code Remote - Containers 擴充套件
3. 開啟專案並在容器中重新開啟
4. 執行 azd up

---

## 部署指南 / Deployment Guide

### Azure Container Apps 部署 (預設)

```bash
# 完整部署 / Full Deployment
azd up

# 僅部署程式碼更改 / Code-only Deployment
azd deploy

# 查看部署狀態 / Check Deployment Status
azd env get-values
```

### 部署配置選項 / Deployment Configuration Options

#### 環境變數 / Environment Variables

**常用配置:**
```bash
# 使用現有資源 / Use Existing Resources
azd env set AZURE_RESOURCE_GROUP existing-rg-name
azd env set AZURE_OPENAI_RESOURCE existing-openai-name

# 啟用功能 / Enable Features
azd env set AZURE_USE_AUTHENTICATION true
azd env set AZURE_ENABLE_MULTIMODAL true
azd env set AZURE_ENABLE_SPEECH true

# 成本優化 / Cost Optimization
azd env set AZURE_SEARCH_SERVICE_SKU free
azd env set AZURE_OPENAI_CHATGPT_MODEL gpt-4o-mini
```

### 部署到 Azure App Service

參考 docs/azure_app_service.md 文檔：
1. 修改 azure.yaml 中的 host: appservice
2. 執行 azd up

---

## 數據處理流程 / Data Processing Pipeline

### 資料準備腳本 / Data Preparation Scripts

**prepdocs.py / prepdocs.sh / prepdocs.ps1**

#### 支援的文檔格式 / Supported Document Formats
- PDF
- HTML
- Markdown
- JSON
- Text
- Word (DOCX)
- PowerPoint (PPTX)

#### 處理步驟 / Processing Steps

1. **文檔解析 / Document Parsing**
   ```
   文檔 → Document Intelligence API → 結構化文本 + 表格 + 圖像
   ```

2. **文本分割 / Text Chunking**
   ```
   chunk_size = 1024 tokens
   overlap = 256 tokens
   使用 SentenceTextSplitter
   ```

3. **向量化 / Vectorization**
   ```
   文本片段 → Azure OpenAI Embeddings (text-embedding-ada-002)
   圖像 → Azure AI Vision Embeddings (multimodal)
   ```

4. **索引 / Indexing**
   ```
   向量 + 元數據 → Azure AI Search Index
   ```

### 自訂資料處理 / Custom Data Processing

```bash
# 上傳新文檔 / Upload New Documents
# 1. 將文件放入 data/ 目錄
# 2. 執行處理腳本
./scripts/prepdocs.sh

# 或使用應用內上傳 / Or Use In-App Upload
# 啟用使用者上傳功能
azd env set AZURE_USE_USER_UPLOAD true
azd up
```

---

## API 端點 / API Endpoints

### 核心 API / Core APIs

#### 1. Chat API
```http
POST /chat
Content-Type: application/json

{
  "messages": [
    {"role": "user", "content": "What are the benefits?"}
  ],
  "context": {
    "overrides": {
      "retrieval_mode": "hybrid",
      "semantic_ranker": true,
      "top": 3
    }
  }
}
```

**回應格式:**
```json
{
  "message": {
    "role": "assistant",
    "content": "Based on the documents..."
  },
  "context": {
    "data_points": ["doc1.pdf", "doc2.pdf"],
    "thoughts": "Searched for: benefits..."
  }
}
```

#### 2. Ask API
```http
POST /ask
Content-Type: application/json

{
  "question": "What are the benefits?",
  "context": {
    "overrides": {
      "retrieval_mode": "hybrid",
      "top": 3
    }
  }
}
```

#### 3. Chat Stream API
```http
POST /chat/stream
Content-Type: application/json

# 回應為 Server-Sent Events (SSE)
```

#### 4. Config API
```http
GET /config

# 回應應用配置
{
  "showGPT4VOptions": true,
  "showSemanticRankerOption": true,
  "showVectorOption": true
}
```

### 管理 API / Management APIs

```http
POST /upload               # 上傳文檔
GET /content/{filename}    # 獲取文檔內容
POST /deleteUploaded       # 刪除上傳的文檔
GET /speech               # 語音合成
```

---

## 測試策略 / Testing Strategy

### 測試類型 / Test Types

#### 1. 單元測試 / Unit Tests
**位置:** tests/test_*.py

```bash
# 運行所有單元測試 / Run All Unit Tests
python -m pytest

# 運行特定測試檔案 / Run Specific Test File
python -m pytest tests/test_chatapproach.py

# 生成覆蓋率報告 / Generate Coverage Report
pytest --cov --cov-report=html
```

**測試範疇:**
- RAG 方法邏輯
- 文檔解析器
- 文本分割器
- 認證助手
- Blob 管理器

#### 2. 整合測試 / Integration Tests
**位置:** tests/test_app.py

```bash
# 運行應用整合測試 / Run App Integration Tests
python -m pytest tests/test_app.py
```

**測試範疇:**
- API 端點
- 請求/回應格式
- 錯誤處理
- 使用 mock 服務（Azure OpenAI, Azure Search）

#### 3. E2E 測試 / End-to-End Tests
**位置:** tests/e2e.py

```bash
# 安裝 Playwright / Install Playwright
playwright install --with-deps

# 運行 E2E 測試 / Run E2E Tests
python -m pytest tests/e2e.py --tracing=retain-on-failure

# 查看測試追蹤 / View Test Trace
playwright show-trace test-results/<trace-zip>
```

**測試範疇:**
- 使用者介面互動
- 聊天流程
- 設定變更
- 引用來源顯示

### 測試數據 / Test Data

```
tests/
├── test-data/              # 測試文檔
├── snapshots/              # 快照測試基準
└── mocks.py                # Mock 物件和回應
```

### Mock 策略 / Mocking Strategy

使用 conftest.py 中定義的 fixtures：
- mock_openai_client - Mock OpenAI API
- mock_search_client - Mock Azure Search
- mock_blob_client - Mock Blob Storage

```python
# 測試範例 / Test Example
def test_chat_approach(mock_openai_client, mock_search_client):
    approach = ChatReadRetrieveReadApproach(
        search_client=mock_search_client,
        openai_client=mock_openai_client,
    )
    result = await approach.run(...)
    assert result["message"]["content"] is not None
```

---

## 維護與擴展 / Maintenance and Extension

### 添加新功能 / Adding New Features

#### 1. 添加新的 azd 環境變數 / Add New azd Environment Variable

更新以下檔案 / Update the following files:
1. infra/main.parameters.json - 添加新參數
2. infra/main.bicep - 添加到 appEnvVariables
3. .azdo/pipelines/azure-dev.yml - 添加到 env 區塊
4. .github/workflows/azure-dev.yml - 添加到 env 區塊

#### 2. 添加新的開發者設定 / Add New Developer Setting

**前端 / Frontend:**
1. app/frontend/src/api/models.ts - 添加到 ChatAppRequestOverrides
2. app/frontend/src/components/Settings.tsx - 添加 UI 元素
3. app/frontend/src/locales/*/translation.json - 添加翻譯
4. app/frontend/src/pages/chat/Chat.tsx - 添加狀態和傳遞
5. app/frontend/src/pages/ask/Ask.tsx - 添加狀態和傳遞

**後端 / Backend:**
1. app/backend/approaches/chatreadretrieveread.py - 從 overrides 讀取
2. app/backend/approaches/retrievethenread.py - 從 overrides 讀取
3. app/backend/app.py - 必要時更新 /config 路由

#### 3. 添加新的資料來源 / Add New Data Source

```bash
# 1. 將文件添加到 data/ 資料夾
cp your-document.pdf data/

# 2. 運行資料準備腳本
./scripts/prepdocs.sh  # Linux/Mac
./scripts/prepdocs.ps1  # Windows

# 3. 重新部署（如果需要）
azd deploy
```

#### 4. 添加新的 UI 語言 / Add New UI Language

```bash
# 1. 創建新的翻譯檔案
cp app/frontend/src/locales/en/translation.json \
   app/frontend/src/locales/<lang-code>/translation.json

# 2. 翻譯所有字串

# 3. 在 i18n 配置中註冊語言
# 編輯 app/frontend/src/i18n/config.ts
```

### 升級依賴 / Upgrading Dependencies

#### 後端依賴 / Backend Dependencies

```bash
# 升級特定套件 / Upgrade Specific Package
cd app/backend
uv pip compile requirements.in -o requirements.txt \
  --python-version 3.10 --upgrade-package <package-name>

# 升級所有套件 / Upgrade All Packages
uv pip compile requirements.in -o requirements.txt \
  --python-version 3.10 --upgrade
```

#### 前端依賴 / Frontend Dependencies

```bash
cd app/frontend

# 檢查過時套件 / Check Outdated Packages
npm outdated

# 升級特定套件 / Upgrade Specific Package
npm update <package-name>

# 升級所有套件 / Upgrade All Packages
npm update
```

### 代碼品質檢查 / Code Quality Checks

#### Python

```bash
# Ruff (Linting)
cd app/backend
python -m ruff check .

# Black (Formatting)
python -m black .

# MyPy (Type Checking)
cd app/backend
mypy . --config-file=../../pyproject.toml

cd scripts
mypy . --config-file=../pyproject.toml
```

#### TypeScript/JavaScript

```bash
cd app/frontend

# TypeScript 編譯檢查 / TypeScript Compilation Check
npm run build

# Prettier (Formatting)
npx prettier --write src/
```

### 監控與日誌 / Monitoring and Logging

#### Application Insights

**查看遙測資料 / View Telemetry:**
1. Azure Portal → 您的 Application Insights 資源
2. 查看 Metrics, Logs, Performance

**常用查詢 / Common Queries:**
```kusto
// API 請求追蹤 / API Request Traces
requests
| where timestamp > ago(1h)
| summarize count() by name, resultCode

// 例外追蹤 / Exception Traces
exceptions
| where timestamp > ago(1h)
| project timestamp, problemId, outerMessage

// 自定義事件 / Custom Events
customEvents
| where timestamp > ago(1h)
| summarize count() by name
```

#### 本地日誌 / Local Logging

```python
# 後端日誌配置 / Backend Logging Config
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# 使用 / Usage
logger.info("Processing query: %s", query)
logger.error("Error occurred: %s", error)
```

### 效能優化 / Performance Optimization

#### 1. 搜尋優化 / Search Optimization

```python
# 調整檢索參數 / Adjust Retrieval Parameters
{
    "top": 3,  # 減少檢索的文檔數量
    "semantic_ranker": True,  # 使用語義排序
    "query_language": "zh-TW"  # 指定查詢語言
}
```

#### 2. Token 優化 / Token Optimization

```python
# 使用較小的模型 / Use Smaller Models
"gpt-4o-mini"  # 而非 gpt-4

# 控制回應長度 / Control Response Length
{
    "max_tokens": 1024,  # 限制最大 token 數
}
```

#### 3. 快取策略 / Caching Strategy

```python
# 使用 Azure Cache for Redis（選用）
# 快取常見查詢結果
```

### 安全性最佳實踐 / Security Best Practices

#### 1. 認證與授權 / Authentication & Authorization

```bash
# 啟用 Microsoft Entra 認證
azd env set AZURE_USE_AUTHENTICATION true
azd up
```

#### 2. 網路隔離 / Network Isolation

參考 docs/deploy_private.md:
- 使用 Private Endpoints
- 啟用 Virtual Network 整合
- 配置 Network Security Groups

#### 3. 資料保護 / Data Protection

- 使用加密的 Blob Storage
- 啟用 Azure Key Vault 進行秘密管理
- 實作存取控制列表（ACL）

### 故障排除 / Troubleshooting

#### 常見問題 / Common Issues

**1. 部署失敗 / Deployment Failure**
```bash
# 檢查詳細錯誤 / Check Detailed Error
azd deploy --debug

# 查看 Azure 資源狀態
azd env get-values
```

**2. 應用無法啟動 / App Won't Start**
```bash
# 檢查容器日誌 / Check Container Logs
az containerapp logs show --name <app-name> --resource-group <rg-name>

# 本地測試 / Test Locally
./app/start.sh
```

**3. 搜尋不返回結果 / Search Returns No Results**
- 檢查 Azure AI Search 中的索引
- 驗證文檔已正確上傳和索引
- 確認搜尋查詢語法正確

**4. OpenAI API 錯誤 / OpenAI API Errors**
- 檢查 API 配額和速率限制
- 驗證模型部署名稱
- 確認 Azure OpenAI 資源配置正確

參考完整故障排除指南: docs/deploy_troubleshooting.md

---

## 成本估算 / Cost Estimation

### 每月估算成本 / Monthly Estimated Costs

**基本配置 / Basic Configuration:**
- Azure Container Apps: ~$30-50/月
- Azure OpenAI (GPT-4o-mini): ~$50-200/月（取決於使用量）
- Azure AI Search (Basic): ~$75/月
- Azure Blob Storage: ~$5-20/月
- Azure Monitor: ~$10-30/月

**總計 / Total:** 約 $170-375/月（基於中等使用量）

**成本優化建議 / Cost Optimization Tips:**
```bash
# 使用較小的 SKU
azd env set AZURE_SEARCH_SERVICE_SKU free
azd env set AZURE_OPENAI_CHATGPT_MODEL gpt-4o-mini

# 參考低成本部署指南
# See docs/deploy_lowcost.md
```

---

## 相關資源 / Related Resources

### 官方文檔 / Official Documentation
- [Azure OpenAI Service](https://learn.microsoft.com/azure/cognitive-services/openai/)
- [Azure AI Search](https://learn.microsoft.com/azure/search/)
- [Azure Developer CLI](https://learn.microsoft.com/azure/developer/azure-developer-cli/)

### 專案文檔 / Project Documentation
- docs/architecture.md - 詳細架構說明
- docs/data_ingestion.md - 資料接入詳情
- docs/customization.md - 自訂指南
- docs/evaluation.md - 評估方法
- docs/productionizing.md - 生產化建議

### 社群支援 / Community Support
- [GitHub Issues](https://github.com/Azure-Samples/azure-search-openai-demo/issues)
- [Azure AI Discord](https://aka.ms/foundry/discord)
- [Azure AI Forum](https://aka.ms/foundry/forum)

---

## 版本資訊 / Version Information

**當前版本 / Current Version:** 0.0.3
**最後更新 / Last Updated:** 2025-10-29
**Python 版本 / Python Version:** 3.10+
**Node 版本 / Node Version:** 20+
**azd 版本 / azd Version:** >= 1.17.0

---

## 授權 / License

MIT License - 詳見 LICENSE 檔案

---

## 貢獻 / Contributing

歡迎貢獻！請參閱 CONTRIBUTING.md 了解如何參與此專案。

---

**注意 / Note:** 此文件基於專案的當前狀態編寫。隨著專案的發展，請定期更新此文件以反映最新的架構和實作細節。
