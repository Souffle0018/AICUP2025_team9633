# AI CUP 2025 秋季賽 - 主動脈瓣物件偵測 (Aortic Valve Object Detection)

本專案為 AI CUP 2025 秋季賽「電腦斷層心臟肌肉影像分割競賽 II - 主動脈瓣物件偵測」的解決方案。我們採用 **異質模型集成 (Heterogeneous Ensemble)** 策略，結合 CNN 架構的 **YOLOv12 (Nano/Small)** 與 Transformer 架構的 **RF-DETR**。透過 **5-Fold 交叉驗證** 與 **加權邊框融合 (WBF)**，提升偵測的準確度與魯棒性。

---

## 目錄
- [1. 專案架構與檔案說明](#1-專案架構與檔案說明)
- [2. 環境配置 (Requirements)](#2-環境配置-requirements)
- [3. 資料準備 (Data Preparation)](#3-資料準備-data-preparation)
- [4. 執行流程 (Execution Flow)](#4-執行流程-execution-flow)
- [5. 生成式 AI 工具使用聲明 (Generative AI Declaration)](#5-生成式-ai-工具使用聲明-generative-ai-declaration)
- [6. 引用與致謝 (References)](#6-引用與致謝-references)

---

## 1. 專案架構與檔案說明

本儲存庫包含以下核心檔案：

- **`train10.ipynb`**  
  - 負責 **YOLOv12** 模型的訓練流程  
  - 執行 **5-Fold 交叉驗證**，訓練 **YOLOv12n** 與 **YOLOv12s**（共 10 個模型權重）

- **`Esembleparam.ipynb`**  
  - 負責 **RF-DETR** 模型訓練流程（5-Fold）  
  - 執行環境建置（自動下載 RF-DETR 原始碼、安裝依賴）  
  - 執行測試集推論（含 **TTA**）  
  - 執行最終 **15 模型集成 (Ensemble)** 與 **WBF** 後處理

- **`README.md`**：本說明文件

---

## 2. 環境配置 (Requirements)

**建議環境**：具備 NVIDIA GPU 的工作站 (Workstation)

**軟體需求**
- Python 3.8+
- PyTorch（建議 CUDA 11.8 以上）
- 相關套件：`ultralytics`, `timm`, `submitit`, `tqdm`, `pandas`, `pillow`, `pyyaml`

> 註：`Esembleparam.ipynb` 的第一個 Cell 已包含自動安裝指令，無需手動預裝。

---

## 3. 資料準備 (Data Preparation)

請將競賽提供的三個原始壓縮檔直接放置於專案**根目錄**：

1. `training_image.zip`  
2. `training_label.zip`  
3. `testing_image.zip`

**目錄結構示意：**
```text
Project_Root/
├── training_image.zip
├── training_label.zip
├── testing_image.zip
├── train10.ipynb
├── Esembleparam.ipynb
└── README.md
```

---

## 4. 執行流程 (Execution Flow)

### Step 1：訓練 YOLO 模型
- 開啟並執行 `train10.ipynb`  
- **功能**：自動解壓縮訓練資料，並訓練 **YOLOv12n (5 Folds)** 與 **YOLOv12s (5 Folds)**  
- **輸出**：訓練完成後的權重檔儲存於 `runs/detect/` 目錄

### Step 2：訓練 RF-DETR 與執行集成
- 開啟並執行 `Esembleparam.ipynb`  
- **環境初始化**：自動下載 RF-DETR 原始碼、安裝必要套件，並解壓縮 `testing_image.zip`  
- **訓練**：進行 **RF-DETR 5-Fold** 訓練，權重儲存於 `rf_detr_5fold_runs/`  
- **推論與集成**：自動載入 Step 1 產生的 **10 個 YOLO** 模型與 Step 2 的 **5 個 RF-DETR** 模型（合計 **15 模型**），對測試集進行推論並以 **WBF** 融合  
- **產出**：最終提交檔產生於  
  ```
  ./final_submission/submission_15models_v20_fixed.txt
  ```

---

## 5. 生成式 AI 工具使用聲明 (Generative AI Declaration)

本專案在開發與報告撰寫過程中，輔助使用以下生成式 AI 工具，並皆經團隊人工驗證與修訂，確保正確性且無侵權：

- **Google Gemini (Advanced model)**：協助撰寫 RF-DETR 模型架構實作程式碼；技術報告架構規劃、實驗流程說明與文獻格式生成  
- **Claude (Claude 3.5 Sonnet)**：協助設計 YOLOv12（Nano & Small）5-Fold 交叉驗證訓練策略與程式碼  
- **ChatGPT (GPT-5)**：協助分析與判斷最佳參數組合（如 WBF IoU 門檻、模型投票數及權重分配）

---

## 6. 引用與致謝 (References)

- **YOLOv12**：Ultralytics  
- **RF-DETR**：Receptive Field-based DETR

---

### 小提醒（這次的調整）
- 文件內所有出現的檔名已統一為 `train10.ipynb` 與 `Esembleparam.ipynb`（原本的 `train10 (2).ipynb`、`RF-DETR_flow.ipynb` 都已改掉）  
- 其他內容與路徑（如 `runs/detect/`、`rf_detr_5fold_runs/`、最終提交檔路徑）維持不變  
