# 楠梓廠址環境分析：潔淨室與空調設計輸入

期末專案題目是半導體廠潔淨室與空調（HVAC）設計，廠址定為**高雄楠梓產業園區**。這個 repo 負責「現場環境」：用公開的監測資料，把楠梓的外氣品質、氣候、風向等條件整理成設計組可以直接引用的數字，例如 MAU（外氣空調箱）濾網與化學濾網的外氣負荷、冷卻除濕盤管與冷卻水塔的設計溫濕度、進氣口方位。

**怎麼用這個 repo**：設計組只需要看 `data/processed/` 下兩個 `*_design_inputs_Nanzih.csv`；想知道數字怎麼來的，打開對應的 notebook，每一節開頭都寫了「為什麼這樣做」。

---

## 給設計組的輸出

兩張設計輸入表格式相同，都是長格式，欄位為 `category、item、metric、value、unit、note`，依 `category` 篩選即可取用。每個數字都是外氣條件的統計；要取平均、P95 還是 P99，由設計組依系統配置決定，表中不做選擇。

| 檔案 | category | 內容 | 對應設計 |
|---|---|---|---|
| `01_design_inputs_Nanzih.csv` | `PM` | PM2.5、PM10 的平均、P50、P95、P99、最大值 | MAU 濾網外氣負荷（Cout）、ISO 16890 濾網等級 |
| | `AMC` | SO2、NO2、O3 的平均、P95、P99（ppb 與 µg/m³），ISO 14644-8 等級；NMHC 總量 | 化學濾網選型 |
| | `WIND` | 盛行方位、靜風比例、海風頻率、各污染物濃度最高的來向 | MAU 進氣口方位、鹽霧濾網需求 |
| `02_design_inputs_Nanzih.csv` | `DESIGN_COOLING` | 0.4％／1％／2％ 設計乾球、濕球、焓，附同時值 | MAU 冷卻除濕盤管、冷卻水塔 |
| | `DESIGN_HEATING` | 99.6％／99％ 設計乾球，附同時濕球 | MAU 預熱盤管 |
| | `EXTREME` | 逐年極端乾球、濕球、焓 | 設備耐候與安全餘裕 |
| | `FREECOOL_WB`、`FREECOOL_DB` | 濕球、乾球低於各門檻的年小時數 | Free cooling 可行時數 |
| | `STATION` | 測站名稱、座標、資料期間 | 出處 |

### 主要數字（2026-10-29）

| 項目 | 數值 | 資料 |
|---|---|---|
| PM2.5 平均／P95 | 18.1／41.0 µg/m³ | 楠梓空品站，2021～2025 |
| PM10 平均／P95 | 39.6／81.0 µg/m³ | 同上 |
| SO2 平均 | 1.72 ppb（4.5 µg/m³） | 同上 |
| NO2 平均 | 13.0 ppb（24.4 µg/m³） | 同上 |
| O3 平均 | 27.9 ppb（54.8 µg/m³） | 同上 |
| 夏季 0.4％ 設計乾球 | 33.8℃（同時濕球 27.1℃） | 氣象署高雄站 467441，2022～2025 |
| 夏季 0.4％ 設計濕球 | 28.4℃（同時乾球 31.9℃） | 同上 |
| 夏季 0.4％ 設計焓 | 92.2 kJ/kg | 同上 |
| 冬季 99.6％ 設計乾球 | 11.0℃ | 同上 |
| 濕球 ≤ 12℃／≤ 16℃ 年小時數 | 約 210／1,070 小時，集中在 12～2 月 | 同上 |
| 全年盛行方位 | NE、NNE、NNW | 楠梓空品站 |

---

## 進度與待補項目

| 狀態 | 項目 | 說明 |
|---|---|---|
| 完成 | `01_Air_Quality_Nanzih` 外氣品質 | PM、SO2、NO2、O3、NMHC、風玫瑰與污染玫瑰 |
| 完成 | `02_Climate_Nanzih` 氣候 | 設計乾濕球與焓、bin 小時數、free cooling 時數 |
| 待補 | NOx 進設計表 | 01 已有統計，設計輸入表目前只放 NO2 |
| 待補 | VOCs 物種 | 改用環境部橋頭光化測站（距楠梓約 3 km），有 54～56 種逐時 VOC，含苯、甲苯、二甲苯；夏季缺值比例偏高 |
| 待補 | 分季風向 | 依四季與日夜拆分風玫瑰；楠梓白天全年吹西向海風，季風主要改變夜間風向 |
| 缺資料 | NH3 | 一般空品站與光化站都不量氨，只能用排放清冊或文獻給量級 |
| 缺資料 | Cout 粒子數濃度 | 測站只量質量濃度；潔淨度公式要的 ≥ 0.5 µm 粒子數需另行假設或取得 |
| 未開始 | `03` 地質、微振動、供水 | 範圍待小組討論 |

---

## 資料來源

| 資料 | 來源 | 取得方式 |
|---|---|---|
| 空品測站逐時值（楠梓、橋頭、仁武、左營等） | 環境部空氣品質監測網「歷史資料下載」 https://airtw.moenv.gov.tw/CHT/Query/His_Data.aspx | **手動下載**：年份 2021～2025，資料型態「全年逐時資料」，空品區「高屏空品區」，解壓到 `data/raw/airtw/<年份>/` |
| 氣象逐時觀測（高雄站 467441，楠梓區德民路） | 氣象署氣候資料服務系統 CODiS https://codis.cwa.gov.tw/StationData | **自動下載**：02 notebook 第一次執行時逐月抓取並快取到 `data/raw/cwa/`，不需 API key |
| 光化測站 VOC（橋頭） | 環境部環境資料開放平臺 `aqx_p_43` https://data.gov.tw/dataset/34273 | 待補，規劃用 API 自動下載 |

`data/raw/` 不進版控，clone 下來後要自己準備空品年度檔；氣象資料 02 會自己抓。

---

## 環境與執行

使用 [uv](https://docs.astral.sh/uv/) 管理，Python 3.13。

```powershell
uv sync
uv run jupyter lab
```

執行順序：

1. 依「資料來源」下載空品年度檔，解壓到 `data/raw/airtw/2021/` … `data/raw/airtw/2025/`，檔名維持原樣（例如 `楠梓_2023.csv`）。
2. 開 `notebooks/01_Air_Quality_Nanzih.ipynb` 整本執行。
3. 開 `notebooks/02_Climate_Nanzih.ipynb` 整本執行；第一次會連線下載約 1 分鐘。

每本 notebook 的第 1 節是全部設定（測站、年份、門檻），要換站或換年只改那一個 cell。

---

## 目錄

| 路徑 | 內容 |
|---|---|
| `notebooks/` | 分析 notebook，依編號執行；`*_design.md` 是撰寫時的工作筆記 |
| `data/raw/` | 下載的原始資料，不進版控 |
| `data/processed/` | notebook 輸出的表；`*_design_inputs_*.csv` 是給設計組的檔，其餘是中間結果與逐時表 |
| `report/figures/` | notebook 輸出的圖，檔名以 notebook 編號開頭 |
| `ref/` | 課程講義抽出的純文字（`*.txt`）；PDF 原檔不進版控 |
| `src/site_analysis/` | 保留給共用函式，目前未使用 |

檔名規則：輸出檔以 notebook 編號開頭、地點代號結尾，例如 `02_design_inputs_Nanzih.csv`、`02_psychrometric_scatter_Nanzih.png`，不同 notebook 的輸出不會互相覆蓋。

---

## 協作注意事項

- **不要提交 `.env`**：放個人 API key 用，已在 `.gitignore`。
- **不要提交 `data/raw/`**：原始資料各自下載。
- **notebook 提交前**：若不想把輸出圖表一起提交，可先在 Jupyter 選「Clear All Outputs」再存檔，diff 會乾淨很多。
