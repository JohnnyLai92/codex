<<<<<<< HEAD
<h1 align="center">OpenAI Codex CLI（繁體中文）</h1>
=======
<h1 align="center">OpenAI Codex CLI-0902</h1>
>>>>>>> 4a3c0836b22b133199d446ed18ff8497853f9fbe

<p align="center"><code>npm i -g @openai/codex</code><br />或 <code>brew install codex</code></p>

<p align="center">
  <strong>Codex CLI</strong> 是在你本機電腦上執行的 OpenAI 程式設計輔助代理（coding agent）。<br/>
  若你要的是 OpenAI 的雲端代理（<strong>Codex Web</strong>），請前往 <a href="https://chatgpt.com/codex">chatgpt.com/codex</a>。
</p>

<p align="center">
  <img src="./.github/codex-cli-splash.png" alt="Codex CLI splash" width="80%" />
  </p>

---

## 快速開始（新手友善）

### 安裝 Codex CLI

你可以用喜歡的套件管理工具安裝：

```shell
npm install -g @openai/codex
```

或用 Homebrew：

```shell
brew install codex
```

也可以到 <a href="https://github.com/openai/codex/releases/latest">最新 GitHub 發佈頁</a> 下載對應平台的壓縮檔（解壓後把執行檔改名為 `codex` 並加入 PATH）：

- macOS
  - Apple Silicon/arm64：`codex-aarch64-apple-darwin.tar.gz`
  - x86_64（舊款 Mac）：`codex-x86_64-apple-darwin.tar.gz`
- Linux
  - x86_64：`codex-x86_64-unknown-linux-musl.tar.gz`
  - arm64：`codex-aarch64-unknown-linux-musl.tar.gz`

### 登入與啟動

執行：

```shell
codex
```

在登入畫面選擇「Sign in with ChatGPT」。建議使用你的 ChatGPT 帳號（Plus、Pro、Team、Edu 或 Enterprise 訂閱）以獲得最佳體驗。

如果你偏好使用 API Key，也可行，但需要額外設定，請見《<a href="./docs/authentication.md#usage-based-billing-alternative-use-an-openai-api-key">Authentication</a>》。若先前以用量計費 API Key 方式使用，請參考《<a href="./docs/authentication.md#migrating-from-usage-based-billing-api-key">遷移步驟</a>》。登入若遇到問題，請到 <a href="https://github.com/openai/codex/issues/1243">此 Issue</a> 回報。

<p align="center">
  <img src="./.github/codex-cli-login.png" alt="Codex CLI login" width="80%" />
</p>

---

## 與本地 LLM 串接（Ollama、vLLM 等）

Codex 支援以「OpenAI 相容 API」連接到本地或第三方模型服務。你可以用兩種方式設定：

1) 以環境變數覆寫內建 OpenAI Provider 的 Base URL（最簡單）

```shell
# 指向本地服務的 OpenAI 相容端點
export OPENAI_BASE_URL=http://localhost:11434/v1   # 例如：Ollama
# 如服務需要金鑰，則設定：
export OPENAI_API_KEY=your-token

codex
```

2) 在設定檔 `~/.codex/config.toml` 自訂 provider（彈性最高）

Ollama 範例（預設無需金鑰）：

```toml
# 選擇自訂的 provider 與要用的模型名稱（請以你在本地已拉取的模型為主）
model_provider = "ollama"
model = "mistral"  # 或 "llama3.1"、"qwen2.5-coder" 等

[model_providers.ollama]
name = "Ollama"
base_url = "http://localhost:11434/v1"
```

vLLM 範例（OpenAI 相容伺服器常見在 8000 連接埠）：

```toml
model_provider = "vllm"
model = "your-model-name"  # 與 vLLM 啟動時掛載的模型名稱一致

[model_providers.vllm]
name = "vLLM"
base_url = "http://localhost:8000/v1"
# 若需要金鑰，取消下一行註解並改為你的環境變數名稱
# env_key = "VLLM_API_KEY"
```

切換 provider 與模型的小抄：

```toml
model_provider = "ollama"  # 也可以是 "vllm"、"openrouter"…
model = "mistral"          # 依你的本地模型名稱調整
```

進階：每個 provider 皆可獨立調整串流閒置逾時與重試次數等網路行為，詳見《<a href="./docs/config.md#per-provider-network-tuning">Per-provider network tuning</a>》。

更多設定選項與範例請見《<a href="./docs/config.md#model_providers">Config: model_providers</a>》。

---

## 設定檔與常用旗標

- 設定檔位置：`$CODEX_HOME/config.toml`（預設 `~/.codex/config.toml`）。
- 常用旗標：`--model/-m`、`--full-auto`、`--ask-for-approval/-a`。
- 完整設定請參見《<a href="./docs/config.md">Config</a>》。

---

## System Prompt 與自訂指令

在 Codex 中，「系統提示／長期指引」以 `AGENTS.md` 為核心，並會依下列優先順序合併（由上而下）：

1. `~/.codex/AGENTS.md`（個人全域指引）
2. 專案根目錄的 `AGENTS.md`（共享專案規範）
3. 目前工作資料夾內的 `AGENTS.md`（子專案／功能區域）

新手步驟建議：

1. 在你的專案根目錄新增 `AGENTS.md`。
2. 寫入你希望 Codex 長期遵守的風格、語氣、禁止事項、程式碼規範與測試要求。
3. 需要個人偏好時，再在 `~/.codex/AGENTS.md` 加上你的常駐指示。
4. 在 TUI 中輸入 `/init` 可快速產生 `AGENTS.md` 範本（若顯示於斜線選單中）。

此外，你可以在 `~/.codex/prompts/` 放入常用的 Markdown 檔作為「斜線指令」。檔名（去除 `.md`）會成為 `/你的指令`，在對話輸入框中打 `/` 就可快速插入。詳見《<a href="./docs/prompts.md">Custom Prompts</a>》。

---

## 常見操作（速查）

- 互動模式：`codex`，或 `codex "先給一段提示"`
- 全自動：`codex --full-auto "幫我建立一個 TODO 應用"`
- 自動化（非互動）模式：`codex exec "對 utils.ts 做說明"`
- 啟動時工作目錄：用 `--cd/-C` 指定，例：`codex --cd ./subproject`
- 核准策略：以 `-a/--ask-for-approval` 或在設定檔設 `approval_policy` 來控管指令何時需要你核准，詳見《<a href="./docs/sandbox.md">Sandbox & approvals</a>》。
- 檔案搜尋：在輸入框輸入 `@` 觸發工作區快搜；上下鍵選擇、Enter/Tab 插入路徑。
- 貼圖與圖片輸入：直接貼上影像（Ctrl+V / Cmd+V），或用 `-i/--image` 旗標附檔。
- Shell 自動完成：`codex completion bash|zsh|fish` 產生對應腳本。

更多入門示例與快捷技巧，請見《<a href="./docs/getting-started.md">Getting started</a>》。

---

## 文件與 FAQ

- <a href="./docs/getting-started.md"><strong>Getting started</strong></a>
  - <a href="./docs/getting-started.md#cli-usage">CLI 使用方式</a>
  - <a href="./docs/getting-started.md#running-with-a-prompt-as-input">以提示字串啟動</a>
  - <a href="./docs/getting-started.md#example-prompts">範例提示</a>
  - <a href="./docs/getting-started.md#memory--project-docs">AGENTS.md 記憶</a>
  - <a href="./docs/config.md">完整設定</a>
- <a href="./docs/sandbox.md"><strong>Sandbox 與核准</strong></a>
- <a href="./docs/authentication.md"><strong>Authentication</strong></a>
  - <a href="./docs/authentication.md#forcing-a-specific-auth-method-advanced">指定認證方式</a>
  - <a href="./docs/authentication.md#connecting-on-a-headless-machine">Headless 主機登入</a>
- <a href="./docs/advanced.md"><strong>進階主題</strong></a>
  - <a href="./docs/advanced.md#non-interactive--ci-mode">非互動／CI 模式</a>
  - <a href="./docs/advanced.md#tracing--verbose-logging">追蹤／詳細日誌</a>
  - <a href="./docs/advanced.md#model-context-protocol-mcp">Model Context Protocol (MCP)</a>
- <a href="./docs/zdr.md"><strong>Zero data retention (ZDR)</strong></a>
- <a href="./docs/contributing.md"><strong>Contributing</strong></a>
- <a href="./docs/install.md"><strong>安裝與建置</strong></a>
  - <a href="./docs/install.md#system-requirements">系統需求</a>
  - <a href="./docs/install.md#dotslash">DotSlash</a>
  - <a href="./docs/install.md#build-from-source">從原始碼建置</a>
- <a href="./docs/faq.md"><strong>FAQ</strong></a>
- <a href="./docs/open-source-fund.md"><strong>Open source fund</strong></a>

---

## 授權

本專案採用 [Apache-2.0 License](LICENSE)。

---
