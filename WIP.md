# WIP — portfolio（投資組合追蹤器）
最後更新：2026-04-15 收工（台北時間）/ 在 A 電腦（peopl）

## 現在做到哪
今天做完四個功能並全數 push 到 `main`（GitHub Pages 自動部署）：

1. **CRYPTO 交易改台幣輸入**（commit `480e631`, `20a4946`）
   - 市場選 CRYPTO 時，表單變成「當日 BTC 價格(TWD/顆)」+「當日 USD/TWD 匯率」+「🔄 查當日」
   - 匯率走 `fawazahmed0/currency-api`（免 key、CDN、有歷史；主 jsdelivr、備援 Cloudflare Pages）
   - trade 物件多存 `priceTWD`、`fxAtTrade` 方便追溯
2. **交易紀錄頁 ✏️ 編輯 / 🗑 刪除**（commit `13ca992`）
   - Edit modal：DIVIDEND 用金額欄、CRYPTO 用 TWD 價+FX、其他用成交價
   - 儲存後自動重建 inventory + realized
3. **現金帳戶完全從 trades 重建**（commit `336b148`）
   - 每個帳戶多 `baseAmount` 欄位（交易前底值），載入時自動從現有資料遷移
   - `rebuildCashFromTrades()` 會在匯入 / 編輯 / 刪除交易後跑
   - 手動改餘額時自動調 `baseAmount` 讓總額一致
   - `cashLog` 區分 `source:'trade'` vs 手動，rebuild 時保留手動紀錄
4. **刪除按鈕防誤點**（commit `b8ea01d`）
   - 新增 `armDelete(btn, fnName, ...args)` helper + `.btn-armed` CSS（紅色脈動）
   - 第一次點變紅警告、3 秒倒數；第二次才觸發刪除 → confirm()
   - 套用到 trade / basis / holding / cash account / broker map 的行內刪除

## 下一步
- [ ] 使用者在部署後的 `https://people7771025.github.io/-/` 實測四項功能
- [ ] 3/20 那筆舊 BTC 基準持倉若有誤差，用新的 ✏️ 編輯或「修改成本均價」手動校正
- [ ] 看看 cashLog 500 筆上限在重建後會不會把手動紀錄擠掉（若 trades 太多可能會）

## 注意 / 卡住的點
- **repo 位置在 C:，不在 Google Drive**（node_modules 無關，是單檔 HTML 但跟著 FOS 一起放 Dev 底下）
- GitHub repo name 是 `people7771025/-`（破折號），URL 是 `https://people7771025.github.io/-/`
- `exchangerate.host` 已經壞了（免費版不回 rates.TWD），千萬別改回去
- `baseAmount` 遷移只在載入時跑一次；若之後改 `brokerMap` 導致舊帳戶對應錯誤，可能需要手動重設
- `settleCashForTrades(newTrades)` 已改成 `rebuildCashFromTrades()` 的別名（保留名字給 `mergeNewTrades` 呼叫）
