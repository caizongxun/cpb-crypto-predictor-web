# CPB Crypto Predictor Web - AI Agent 完整提示詞

## 項目概述

你需要創建一個高級加密貨幣價格預測前端應用，整合 V5 與 V6 模型版本切換，支持多時間框架 K 線預測，具備智能幣種檢測、實時數據更新、深色淺色主題切換、響應式設計，以及自動化 GitHub 提交功能。

## 關鍵項目信息

- **GitHub 倉庫**: caizongxun/cpb-crypto-predictor-web
- **HuggingFace 組織**: zongowo111
- **HuggingFace 數據集**: cpb-models
- **後端 API 地址**: http://localhost:8001
- **前端服務端口**: http://localhost:8000
- **技術棧**: HTML5 + CSS3 + JavaScript (ES6+) + Chart.js
- **自動提交設置**: 無需用戶批准，直接推送至 main 分支

## 核心模型配置

### V5 版本 (生產環境)
- 支持幣種數: 14 種
  - BTC, ETH, BNB, SOL, XRP, ADA, DOGE, AVAX, LTC, DOT, UNI, LINK, XLM, ATOM
- 支持時間框架: 2 種
  - 1d (日線), 1h (小時線)
- HuggingFace 路徑: zongowo111/cpb-models 下的 models_v5 目錄

### V6 版本 (新增功能)
- 支持幣種數: 19 種
  - 完整支持 (1d, 1h, 15m): BTC, ETH, BNB, SOL, ADA, DOGE, AVAX, DOT, LTC, LINK, ATOM, NEAR, ICP, CRO, HBAR
  - 部分支持: VET (缺 1h), MATIC (僅 1d), FTM (僅 1d), UNI (僅 1d)
- 支持時間框架: 3 種
  - 1d (日線), 1h (小時線), 15m (15分鐘線)
- HuggingFace 路徑: zongowo111/cpb-models 下的 models_v6 目錄

## 前端頁面架構

### 導航欄結構
- Logo 與品牌名 (CPB Trading)
- 版本選擇器 (V5 / V6 按鈕切換)
- 幣種搜索框 (動態過濾)
- 主題切換按鈕 (亮色/暗色模式)
- 設定按鈕 (可選功能入口)

### 主內容區結構
- 快速選擇卡片區 (熱門幣種快捷按鈕)
- 預測結果面板
  - 幣種信息卡 (顯示幣種名稱與代碼)
  - 時間框架選擇器 (1d, 1h, 15m)
  - K 線圖表 (使用 Chart.js 展示歷史 K 線)
  - 未來預測 K 線 (展示預測的下一個 10 根 K 線)
  - 預測數據卡片
    - 當前價格 (來自 API)
    - 預測價格 (下一根 K 線收盤價)
    - 信心度 (百分比進度條)
    - 推薦信號 (BUY/SELL/HOLD，使用相應顏色)
    - 進場點 (Entry Price)
    - 止損 (Stop Loss)
    - 獲利目標 (Take Profit)
  - 波動率信息 (當前波動率 → 預測波動率，等級顯示)
- 市場分析面板
  - 趨勢方向 (上升趨勢/下降趨勢)
  - 趨勢強度 (0-1 的強度指標)
  - 最佳進場點 (第 N 根 K 線)
  - 價格極值 (最低價、最高價及其位置)
  - 預測價格序列 (未來 10 根的預測價格)

### 頁腳結構
- API 狀態指示 (在線/離線)
- 上次更新時間戳
- 自動刷新狀態
- 文檔與幫助連結

## API 規範

### 預測端點 - V5 版本

POST /predict-v5
Content-Type: application/json

Request Body:
```json
{
  "symbol": "BTC|ETH|BNB|SOL|XRP|ADA|DOGE|AVAX|LTC|DOT|UNI|LINK|XLM|ATOM",
  "timeframe": "1d|1h",
  "use_binance": false
}
```

Response Body:
```json
{
  "symbol": "BTC",
  "timeframe": "1d",
  "current_price": 42150.25,
  "predicted_price": 42650.75,
  "log_return": 0.01186,
  "confidence": 0.68,
  "recommendation": "BUY|SELL|HOLD",
  "entry_price": 42150.25,
  "stop_loss": 41327.25,
  "take_profit": 43483.77,
  "volatility": {
    "current": 0.56,
    "predicted": 1.18,
    "level": "低|中|高",
    "atr_14": 125.45
  },
  "klines": [
    {
      "timestamp": 1735000000000,
      "open": 42000.00,
      "high": 42500.00,
      "low": 41800.00,
      "close": 42150.25,
      "volume": 2500000
    }
  ],
  "future_klines": [
    {
      "timestamp": 1735086400000,
      "open": 42150.25,
      "high": 42400.00,
      "low": 42100.00,
      "close": 42300.00,
      "volume": 0,
      "is_predicted": true
    }
  ],
  "timestamp": "2025-12-26T04:06:00",
  "price_source": "yfinance",
  "model_version": "V5 HYBRID + Residual Bias Compensation",
  "residual_bias_compensation": 500.00
}
```

### 預測端點 - V6 版本

POST /predict-v6
Content-Type: application/json

Request Body:
```json
{
  "symbol": "BTC|ETH|BNB|SOL|ADA|DOGE|AVAX|DOT|LTC|LINK|ATOM|NEAR|ICP|CRO|HBAR|VET|MATIC|FTM|UNI",
  "timeframe": "1d|1h|15m",
  "use_binance": false
}
```

Response Body: 同 V5 版本，但支持更多幣種和時間框架，包含 future_klines 字段

### 市場分析端點

POST /market-analysis
Content-Type: application/json

Request Body:
```json
{
  "symbol": "BTC|ETH|...",
  "timeframe": "1d|1h",
  "use_binance": false
}
```

Response Body:
```json
{
  "symbol": "BTC",
  "timeframe": "1d",
  "trend": {
    "direction": "uptrend|downtrend",
    "strength": 0.75,
    "consecutive_bars": 5,
    "average_return": 0.0235,
    "description": "多頭上升趨勢明顯"
  },
  "best_entry_bar": 3,
  "price_extremes": {
    "lowest_price": 42500.00,
    "lowest_bar": 5,
    "highest_price": 44800.00,
    "highest_bar": 9,
    "potential_profit": 0.054
  },
  "forecast_prices": [42600, 42750, 42900, 43050, 43150, 43250, 43320, 43380, 43420, 43450],
  "recommendation": "多頭信號 (第3根)"
}
```

## 技術實現要求

### 必須使用的庫
```html
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/axios@1.6.0/dist/axios.min.js"></script>
```

### K 線圖表功能
- 使用 Chart.js 的混合圖表類型
- 蠟燭圖格式展示歷史 K 線 (OHLCV 數據)
- 折線圖展示預測的下一個 10 根 K 線
- 清晰標示當前價格與預測價格
- 支持滑鼠懸停顯示數據詳情
- 響應式設計自動調整圖表大小
- 不同顏色區分上升 (綠色) 和下降 (紅色) K 線

### 幣種選擇器功能
- 根據選定版本 (V5/V6) 動態更新可用幣種列表
- 實時搜索過濾功能 (防抖處理)
- 快速選擇常用幣種的快捷按鈕 (BTC, ETH, BNB, SOL)
- 顯示該幣種在當前版本的支持時間框架
- 自動檢測部分支持幣種的限制 (如 VET 缺少 1h)

### 版本切換功能
- V5 與 V6 之間無縫切換
- 切換時自動驗證當前幣種的支持性
- 不支持的幣種自動回退至 BTC
- 時間框架自動適配新版本的限制
- 保存用戶選擇到 localStorage

### 時間框架切換
- V5: 1d, 1h
- V6: 1d, 1h, 15m (依幣種而異)
- 顯示當前版本該幣種的支持框架
- 禁用不支持的框架按鈕

### 主題切換系統
- 支持淺色模式 (Light) 和深色模式 (Dark)
- 使用 CSS 變數系統動態切換
- 使用 data-theme 屬性管理主題狀態
- 保存用戶偏好到 localStorage
- 自動檢測系統主題偏好 (prefers-color-scheme)

淺色模式色彩配置:
- 背景: #ffffff
- 文本: #1a1a1a
- 主色: #0066cc
- BUY (成功): #28a745
- SELL (危險): #dc3545
- HOLD (警告): #ffc107
- 邊框: #e0e0e0

深色模式色彩配置:
- 背景: #1e1e1e
- 文本: #e0e0e0
- 主色: #0088ff
- BUY (成功): #00cc44
- SELL (危險): #ff4455
- HOLD (警告): #ffbb00
- 邊框: #444444

### 自動刷新機制
- 預設刷新間隔: 60 秒
- 用戶可自訂刷新間隔 (30 秒至 5 分鐘)
- 顯示距離下次更新的倒計時
- 用戶切換視窗時自動暫停刷新
- 手動刷新按鈕禁用連續點擊 (1 秒內鎖定)

### 數據緩存策略
- 同一幣種同一時間框架 5 分鐘內不重複請求
- 快速切換回之前選擇的組合時使用緩存數據
- 超過緩存時間自動刪除舊數據
- 手動刷新強制更新數據

### 錯誤處理機制
- 網絡連接失敗: 顯示友好的錯誤信息並提供重試按鈕
- API 超時: 自動重試最多 3 次，間隔逐次增加
- 後端錯誤: 顯示具體錯誤信息與服務器返回的詳情
- 幣種不支持: 檢查版本限制並提示用戶切換版本
- 時間框架不支持: 禁用不支持的選項並顯示原因
- 加載狀態: 使用骨架屏或進度條指示數據加載中

### 加載狀態動畫
- 骨架屏展示預期的內容結構
- 圖表區域顯示加載動畫 (旋轉圖標)
- 數據卡片區域顯示脈衝效果
- 禁用交互元素防止重複操作

## 文件結構與模塊設計

你需要創建以下完整的文件結構:

```
cpb-crypto-predictor-web/
├── index.html                    # 主 HTML 文件 (完整的頁面結構)
├── styles.css                    # 全局 CSS 樣式 (含主題變數和響應式)
├── app.js                        # 主應用邏輯與初始化
├── config.js                     # 配置文件 (API URL, 幣種列表, HF 倉庫信息)
├── modules/
│   ├── api.js                   # API 調用封裝與請求管理
│   ├── chart.js                 # Chart.js 圖表初始化與更新
│   ├── coinSelector.js          # 幣種選擇器邏輯與事件處理
│   ├── versionSelector.js       # 版本切換器邏輯
│   ├── timeframeSelector.js     # 時間框架選擇器邏輯
│   ├── marketAnalysis.js        # 市場分析面板數據處理與渲染
│   ├── theme.js                 # 主題切換邏輯與 localStorage 管理
│   ├── storage.js               # localStorage 封裝與數據持久化
│   └── utils.js                 # 工具函數 (格式化, 驗證, 防抖等)
├── assets/
│   ├── icons/
│   │   ├── logo.svg            # 品牌 logo
│   │   ├── refresh.svg         # 刷新圖標
│   │   ├── search.svg          # 搜索圖標
│   │   └── settings.svg        # 設定圖標
│   └── images/
├── .gitignore                    # Git 忽略配置
├── README.md                     # 項目文檔與使用說明
└── package.json                  # (可選) 如果使用 npm 包管理
```

## 配置文件 (config.js) 詳細規範

```javascript
const CONFIG = {
  // API 配置
  API: {
    BASE_URL: 'http://localhost:8001',
    ENDPOINTS: {
      PREDICT_V5: '/predict-v5',
      PREDICT_V6: '/predict-v6',
      MARKET_ANALYSIS: '/market-analysis'
    },
    TIMEOUT: 10000,
    RETRY_ATTEMPTS: 3,
    RETRY_DELAY: 1000
  },

  // HuggingFace 配置
  HUGGINGFACE: {
    ORG: 'zongowo111',
    DATASET: 'cpb-models',
    V5_PATH: 'models_v5',
    V6_PATH: 'models_v6',
    REPO_URL: 'https://huggingface.co/datasets/zongowo111/cpb-models'
  },

  // GitHub 配置
  GITHUB: {
    REPO: 'caizongxun/cpb-crypto-predictor-web',
    BRANCH: 'main',
    AUTO_COMMIT: true,
    AUTO_PUSH: true
  },

  // 幣種配置
  COINS: {
    V5: {
      coins: ['BTC', 'ETH', 'BNB', 'SOL', 'XRP', 'ADA', 'DOGE', 'AVAX', 'LTC', 'DOT', 'UNI', 'LINK', 'XLM', 'ATOM'],
      timeframes: ['1d', '1h'],
      names: {
        'BTC': 'Bitcoin',
        'ETH': 'Ethereum',
        'BNB': 'Binance Coin',
        'SOL': 'Solana',
        'XRP': 'Ripple',
        'ADA': 'Cardano',
        'DOGE': 'Dogecoin',
        'AVAX': 'Avalanche',
        'LTC': 'Litecoin',
        'DOT': 'Polkadot',
        'UNI': 'Uniswap',
        'LINK': 'Chainlink',
        'XLM': 'Stellar',
        'ATOM': 'Cosmos'
      }
    },
    V6: {
      coins: ['BTC', 'ETH', 'BNB', 'SOL', 'ADA', 'DOGE', 'AVAX', 'DOT', 'LTC', 'LINK', 'ATOM', 'NEAR', 'ICP', 'CRO', 'HBAR', 'VET', 'MATIC', 'FTM', 'UNI'],
      timeframes: ['1d', '1h', '15m'],
      names: {
        'BTC': 'Bitcoin',
        'ETH': 'Ethereum',
        'BNB': 'Binance Coin',
        'SOL': 'Solana',
        'ADA': 'Cardano',
        'DOGE': 'Dogecoin',
        'AVAX': 'Avalanche',
        'DOT': 'Polkadot',
        'LTC': 'Litecoin',
        'LINK': 'Chainlink',
        'ATOM': 'Cosmos',
        'NEAR': 'Near Protocol',
        'ICP': 'Internet Computer',
        'CRO': 'Cronos',
        'HBAR': 'Hedera',
        'VET': 'VeChain',
        'MATIC': 'Polygon',
        'FTM': 'Fantom',
        'UNI': 'Uniswap'
      },
      partial_support: {
        'VET': ['1d', '15m'],
        'MATIC': ['1d'],
        'FTM': ['1d'],
        'UNI': ['1d']
      }
    }
  },

  // 快速選擇幣種
  QUICK_COINS: ['BTC', 'ETH', 'BNB', 'SOL'],

  // 自動刷新配置
  AUTO_REFRESH: {
    ENABLED: true,
    INTERVAL: 60000,
    MIN_INTERVAL: 30000,
    MAX_INTERVAL: 300000
  },

  // 緩存配置
  CACHE: {
    ENABLED: true,
    DURATION: 300000
  },

  // UI 配置
  UI: {
    THEME_DEFAULT: 'auto',
    CHART_HEIGHT: 400,
    CHART_ANIMATION_DURATION: 300
  }
};
```

## 應用初始化流程

1. 頁面加載時讀取 localStorage 中的用戶偏好 (主題, 選定版本, 最後選擇的幣種)
2. 應用保存的主題設定 (深色/淺色)
3. 初始化所有 UI 組件 (導航欄, 版本選擇器, 幣種選擇器, 時間框架選擇器)
4. 根據保存的版本和幣種加載預測數據 (如無則使用 BTC 的 V5 預測)
5. 渲染 K 線圖表與預測結果卡片
6. 啟動自動刷新計時器
7. 綁定所有事件監聽器

## 用戶交互流程詳解

### 版本切換流程
```
用戶點擊版本按鈕 (V5 或 V6)
  → 驗證當前幣種在新版本中的支持性
  → 如果不支持, 自動切換至 BTC
  → 清空圖表與數據卡片 (顯示加載狀態)
  → 發起 API 請求到新版本端點
  → 獲得響應後更新所有 UI 組件
  → 保存選擇到 localStorage
```

### 幣種切換流程
```
用戶搜索或選擇幣種
  → 檢查該幣種在當前版本中的支持性
  → 檢查該幣種支持的時間框架
  → 驗證當前時間框架是否支持 (不支持則切換至該幣種的第一個支持框架)
  → 顯示加載狀態
  → 發起 API 請求到當前版本端點
  → 獲得響應後更新圖表、預測卡片、市場分析面板
  → 保存選擇到 localStorage
```

### 時間框架切換流程
```
用戶選擇時間框架
  → 檢查該幣種在當前版本中是否支持該框架
  → 如果不支持, 提示用戶並恢復到原時間框架
  → 顯示加載狀態
  → 發起 API 請求 (同版本、同幣種、不同時間框架)
  → 獲得響應後更新圖表與預測數據
```

### 手動刷新流程
```
用戶點擊刷新按鈕
  → 禁用按鈕 (防止連續點擊)
  → 顯示刷新動畫
  → 發起 API 請求到當前版本端點
  → 成功: 更新所有數據、重置自動刷新計時器、恢復按鈕
  → 失敗: 顯示錯誤信息、恢復按鈕
```

### 主題切換流程
```
用戶點擊主題切換按鈕
  → 切換 document.documentElement 的 data-theme 屬性
  → 更新所有 CSS 變數
  → 保存選擇到 localStorage
  → 如果圖表已初始化, 更新圖表色彩方案
```

## 預測數據卡片展示規範

預測結果卡片應清晰展示以下信息:

```
┌─────────────────────────────────────────────┐
│ BTC / Bitcoin          [版本: V5] [框架: 1d] │
├─────────────────────────────────────────────┤
│ 當前價格: 42,150.25 USDT                    │
│ 預測價格: 42,650.75 USDT ↑ +1.19%           │
├─────────────────────────────────────────────┤
│ 推薦信號: BUY (綠色背景)                     │
│ 信心度:   68% [████████░░]                  │
├─────────────────────────────────────────────┤
│ 進場點:   42,150.25 USDT                    │
│ 止損:     41,327.25 USDT                    │
│ 獲利目標: 43,483.77 USDT                    │
├─────────────────────────────────────────────┤
│ 當前波動率: 0.56 (低)                        │
│ 預測波動率: 1.18 (中)                        │
│ ATR(14):   125.45                           │
├─────────────────────────────────────────────┤
│ 最後更新: 2025-12-26 23:25:30                │
└─────────────────────────────────────────────┘
```

## 市場分析面板展示規範

市場分析面板應包含以下內容:

```
┌─────────────────────────────────────────────┐
│ 市場分析                                     │
├─────────────────────────────────────────────┤
│ 趨勢方向: 上升趨勢 (強度: 75%)               │
│ 連續上漲 K 線: 5 根                          │
│ 平均回報率: 2.35%                           │
├─────────────────────────────────────────────┤
│ 最佳進場點: 第 3 根 K 線                     │
│ 建議: 在第 3 根 K 線買入                    │
├─────────────────────────────────────────────┤
│ 價格極值分析:                                │
│   最低價: 42,500.00 (第 5 根)                │
│   最高價: 44,800.00 (第 9 根)                │
│   潛在漲幅: 5.4%                            │
├─────────────────────────────────────────────┤
│ 未來 10 根預測價格:                          │
│ 42,600 → 42,750 → 42,900 → 43,050 → 43,150 │
│ 43,250 → 43,320 → 43,380 → 43,420 → 43,450 │
└─────────────────────────────────────────────┘
```

## 響應式設計斷點

- 超小設備 (< 480px): 單列布局，100% 寬度，垂直堆疊所有組件
- 手機設備 (480px - 768px): 單列布局，稍微增加間距，調整字體大小
- 平板設備 (768px - 1024px): 雙列布局，圖表占 70%，側邊欄占 30%
- 桌面設備 (1024px - 1440px): 三列布局或側邊欄設計
- 超大屏幕 (> 1440px): 最大寬度限制，居中展示

## 色彩與設計規範

### 字體設定
- 主標題: 24px, 加粗 (font-weight: 600)
- 副標題: 18px, 半加粗 (font-weight: 500)
- 正文: 14px, 正常 (font-weight: 400)
- 代碼: Courier New, monospace, 12px

### 間距體系
- 基礎單位: 8px
- 組件間距: 16px
- 卡片內邊距: 24px
- 區域外邊距: 32px

### 圖角設定
- 按鈕: 4px border-radius
- 卡片: 8px border-radius
- 容器: 12px border-radius
- 圖表容器: 8px border-radius

### 陰影效果
- 淺陰影: 0 1px 3px rgba(0, 0, 0, 0.1)
- 中陰影: 0 4px 6px rgba(0, 0, 0, 0.15)
- 深陰影: 0 8px 12px rgba(0, 0, 0, 0.2)

## 可訪問性要求

- 遵循 WCAG 2.1 AA 標準
- 色對比度至少 4.5:1 (一般文本) 和 3:1 (大文本)
- 使用語義化 HTML 標籤 (header, nav, main, section, footer, button, label 等)
- 所有圖片包含有意義的 alt 文本
- 表單輸入與 label 關聯
- 複雜組件使用 ARIA 屬性 (aria-label, aria-describedby, aria-live 等)
- 支持鍵盤導航 (Tab 鍵遍歷, Enter 鍵激活按鈕)
- 提供視覺焦點指示 (focus-visible 狀態)
- 屏幕閱讀器友好

## 性能優化要求

- 使用 SVG 格式的圖標而非光柵圖像
- 實現 API 響應數據的 5 分鐘緩存機制
- Chart.js 圖表使用性能優化配置 (避免過度動畫)
- 搜索框實現防抖處理 (延遲 300ms)
- 窗口大小變化實現節流處理 (延遲 300ms)
- 自動刷新使用節流機制
- 避免重流 (reflow) 和重繪 (repaint)

## GitHub 自動提交配置

應用應具有以下自動化提交功能:

- 每次成功生成新頁面或更新功能時自動提交到 GitHub
- 提交信息格式: "Auto-update: [功能描述] - [時間戳]"
- 自動推送至 main 分支
- 無需用戶手動批准
- 提供提交狀態反饋 (成功/失敗)
- 包含生成時間戳和版本信息
- 自動添加所有已修改文件

提交信息範例:
```
Auto-update: Add K-line chart visualization for BTC predictions - 2025-12-26T23:25:30Z
Auto-update: Implement V5 and V6 model version switching - 2025-12-26T23:26:00Z
Auto-update: Deploy responsive design and theme toggle - 2025-12-26T23:27:15Z
```

## 代碼規範與最佳實踐

- 使用 ES6+ 語法 (const/let, 箭頭函數, 模板字面量, 解構賦值等)
- 模塊化設計，每個功能對應獨立的 JavaScript 文件
- 完整的代碼註釋 (函數、複雜邏輯、重要變數均需註釋)
- 遵循駝峰命名規則 (變數、函數)
- 常數使用大寫蛇形命名法
- HTML 使用語義化標籤
- CSS 使用 BEM 命名法或相似的模塊化方案
- 避免使用全局變數
- 實現錯誤邊界與異常捕獲
- 驗證所有用戶輸入
- 實現防 XSS 攻擊的內容轉義

## 錯誤處理與用戶提示

### 網絡連接錯誤
顯示信息: "無法連接到服務器，請檢查網絡連接。"
包含重試按鈕和詳細錯誤日誌 (開發者控制台)

### API 超時
顯示信息: "請求超時，系統將自動重試 (x/3)..."
在第 3 次失敗後顯示完整錯誤

### 幣種不支持
顯示信息: "該版本不支持此幣種，請切換至 V6 或選擇其他幣種。"
提供快速切換版本的選項

### 時間框架不支持
顯示信息: "選定的幣種在此版本不支持 [時間框架] 框架。支持的框架: [列表]"
自動將時間框架重置為支持的選項

### 數據缺失
顯示信息: "暫無可用數據，請稍後重試或選擇其他幣種。"
提供手動刷新按鈕

### 後端服務錯誤
顯示信息: "服務器返回錯誤: [具體錯誤信息]"
包含服務名稱和錯誤代碼以便調試

## 測試驗收清單

- [ ] V5 版本所有 14 個幣種在 1d 和 1h 時間框架的預測功能
- [ ] V6 版本所有 19 個幣種在支持的時間框架的預測功能
- [ ] 版本之間的無縫切換 (包含自動幣種適配)
- [ ] 時間框架切換及驗證邏輯
- [ ] 主題切換功能 (淺色和深色模式)
- [ ] 自動刷新機制及倒計時顯示
- [ ] 手動刷新功能及按鈕狀態管理
- [ ] 幣種搜索和過濾功能
- [ ] 響應式設計在手機、平板、桌面的表現
- [ ] 錯誤處理與網絡故障模擬
- [ ] localStorage 持久化 (刷新頁面後保留用戶選擇)
- [ ] K 線圖表的正確渲染和交互
- [ ] 未來預測 K 線的視覺區分
- [ ] 市場分析面板的數據準確性
- [ ] 預測數據卡片的所有欄位顯示
- [ ] 鍵盤導航支持 (Tab 鍵、Enter 鍵)
- [ ] 屏幕閱讀器友好性測試
- [ ] 色對比度檢查 (使用無障礙工具)

## 代碼複製禁區

禁止使用的方式:
- 使用已棄用的 API (eval, document.write 等)
- 使用阻塞式同步 API (XMLHttpRequest 的同步模式)
- 直接操作 DOM 進行樣式更改 (應使用 CSS 類切換)
- 硬編碼 API 端點或配置值 (應從 config.js 讀取)
- 使用 CSS !important 覆蓋樣式 (除非無法避免)
- 在事件監聽器中創建大量閉包導致記憶體洩漏
- 直接修改 API 響應對象而不進行深度複製
- 使用 setTimeout/setInterval 而不清理 (應保存計時器 ID 以便清理)

## 啟動與部署說明

本地開發啟動步驟:

1. 確保後端服務運行在 http://localhost:8001
2. 在項目目錄執行: python -m http.server 8000
3. 在瀏覽器訪問: http://localhost:8000
4. (可選) 使用 VSCode Live Server 擴展

GitHub 自動部署:

1. 所有代碼更改自動提交至 caizongxun/cpb-crypto-predictor-web
2. 無需手動批准，應用自動推送至 main 分支
3. 包含時間戳和版本信息的提交信息
4. 支持從任何環境觸發自動提交

## 文件生成優先順序

應按以下順序生成文件:

1. config.js (所有其他文件的基礎)
2. modules/utils.js (工具函數)
3. modules/storage.js (數據持久化)
4. modules/theme.js (主題系統)
5. modules/api.js (API 調用)
6. modules/chart.js (圖表初始化)
7. modules/coinSelector.js (幣種選擇)
8. modules/versionSelector.js (版本選擇)
9. modules/timeframeSelector.js (時間框架選擇)
10. modules/marketAnalysis.js (市場分析)
11. styles.css (完整樣式)
12. index.html (完整頁面結構)
13. app.js (應用邏輯初始化)
14. README.md (文檔)
15. .gitignore (Git 配置)

## 最終交付要求

完成後應提交以下內容:

- 完整的前端應用，包含所有 15 個文件
- 所有文件均應有完整的中文註釋
- 完整的 README.md 文檔，包含安裝、使用和開發指南
- 無需用戶指定提交，應用應自動提交至 GitHub
- 所有功能均應完全實現，無占位符或待實現標記
- 代碼應符合上述所有規範與最佳實踐
- 應支持所有聲明的功能且通過測試清單

---

立即開始執行上述規範，創建完整的 CPB Crypto Predictor Web 前端應用。所有文件應一次性完成，並自動上傳至 GitHub 倉庫 caizongxun/cpb-crypto-predictor-web。無需等待用戶批准，直接執行。
