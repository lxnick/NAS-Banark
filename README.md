# Banark

> **Banana + Ark** — every file, at least twice.

以 Banana Pi BPI-W2（RTD1296）為核心的家用 NAS 與「政策式」備份同步系統。

NAS 是唯一的主要存放處。每個目錄（集合）各自設定要備份到哪些目標，例如移動硬碟、Google Drive。
Banark 依照政策自動同步，並提供單一入口來檢索檔案、查看每份資料目前有幾份副本和同步狀態。

## 目標

- **集中存放**：BPI-W2 透過 USB 串接多顆大小不一的 2.5" 硬碟，用 mergerfs 合併成單一儲存池
- **依目錄設定備份政策**：每個集合可以分別勾選要同步到哪些目標，份數不足時發出警告
- **單向同步，刪除前必須確認**：取消備份或移除集合時先標記為「待刪除」，使用者確認後才刪；刪除的檔案先放進回收區保留 N 天
- **離線硬碟**：筆電上的 Agent 偵測到移動硬碟插入就自動同步；在外面可以經由 Tailscale 連線
- **統一檢索**：搜尋結果會列出檔案在哪些目標上，壓縮檔（SDK／開發環境）裡的檔案也搜尋得到
- **儲存健康**：監控 SMART 和容量；某顆硬碟離線時暫停所有同步，避免把「消失」誤當成「刪除」

## 硬體

| 項目 | 內容 |
|---|---|
| 板子 | Banana Pi BPI-W2 V1.1 |
| SoC | Realtek RTD1296，4× Cortex-A53，2 GB DDR4 |
| 系統 | 官方 BSP Linux 4.9（Ubuntu 18.04 demo 映像檔） |
| 儲存 | USB 2.5" 硬碟 × N（經由獨立供電的 USB 3.0 Hub） |

## 技術選型

- **Go**：`banark-server` 交叉編譯為 linux/arm64，`banark-agent` 編譯為 windows/amd64
- **rclone**：同步引擎，以子程序方式呼叫（支援 Google Drive / SFTP / 本機）
- **SQLite**（`modernc.org/sqlite`，純 Go）：存放政策、檔案索引和工作紀錄
- **mergerfs**：合併多顆硬碟；**SnapRAID** 為選用

## 文件

| 文件 | 內容 |
|---|---|
| [docs/design.md](docs/design.md) | 系統設計：決策、架構、資料模型、同步規則、Agent 協定、儲存層 |
| [docs/hardware.md](docs/hardware.md) | 硬體筆記、首次啟動檢查表、硬碟清點、實測紀錄 |

## 開發階段

| 階段 | 內容 | 狀態 |
|---|---|---|
| P0 | 硬體實測：開機、kernel／套件確認、硬碟 SMART 清點 | 🔄 進行中 |
| P1 | Server 核心：集合／目標／政策、rclone 同步、索引、安全檢查、CLI | ⏳ |
| P2 | Web UI：政策矩陣、狀態、搜尋、待刪除確認 | ⏳ |
| P3 | Agent：離線硬碟識別、同步、回報 manifest | ⏳ |
| P4 | NAS 儲存層：硬碟登錄、SMART、mergerfs 狀態、硬碟離線保護 | ⏳ |
| P5 | 部署到 BPI-W2、Google Drive 正式上線、通知 | ⏳ |
