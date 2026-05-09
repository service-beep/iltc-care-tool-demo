# 醫囑管理工具｜展場體驗版（DEMO）

這是 [iltc-care-tool](https://github.com/service-beep/iltc-care-tool) 的**展場 DEMO 版**，專為輔具展、長照博覽會等實體推廣場合設計。

## 與正式版的差異

| 項目 | 正式版 | 展場 DEMO 版 |
|---|---|---|
| 登入 | LINE / Google OAuth | ❌ 不需登入，自動載入體驗帳號 |
| 後端 | Cloudflare Workers + D1 + Gemini | ❌ 純前端，所有 API 由 fetch 攔截器回傳預設內容 |
| AI 功能 | 真實呼叫 Gemini 2.5 Flash | ✅ 預設範例回應（含 2 秒模擬延遲）|
| 醫囑錄音 | 真實麥克風 → Gemini 轉文字 | ✅ 假裝錄音，不需麥克風權限 |
| 藥袋拍照 | 真實 OCR 辨識 | ✅ 任意上傳圖片，回傳預設藥單 |
| 資料同步 | D1 跨裝置同步 | ❌ 僅存 localStorage |
| 重置體驗 | — | ✅ 頂端「🔄 重新開始體驗」按鈕清空所有資料 |

## 預設情境

**陳美玉奶奶**（82 歲，1944/3/15 生）
- 慢性病：高血壓、第二型糖尿病、輕度失智症（疑似阿茲海默症初期）
- 過去 30 天的早晚血壓、空腹/飯後血糖紀錄
- 5 種長期用藥（含 4/15 新增的愛憶欣）
- 2 筆看診紀錄（4/15 神經內科、5/2 心臟內科），含完整 AI 摘要
- 3 筆病況交接日誌（看護阿嬌姐填寫）
- 7 條 AI 回診提醒清單

訪客可實際操作：新增看診錄音、藥袋拍照辨識、AI 醫囑摘要、列印急診摘要卡⋯⋯所有變動只存在當下瀏覽器，按頂端「重置體驗」即可清空，給下一位訪客全新體驗。

## 部署

### Cloudflare Pages 設定
- **Build command**: `cd frontend && npm install && npm run build`
- **Build output directory**: `frontend/dist`
- **Root directory**: （留空）
- **Production branch**: `main`

### CI/CD
push 到 `main` 分支 → Cloudflare Pages 自動 build & deploy（1-2 分鐘）

## 改動點

DEMO 模式的所有改造集中在 [`frontend/_source.html`](frontend/_source.html)：

1. **第 43 行**：`BACKEND_URL = '__DEMO__'`（sentinel，不會打到真網路）
2. **第 63-284 行**：DEMO PATCH 區塊
   - 預設陳美玉奶奶資料（病人、用藥、生理數值、看診、病況）
   - Mock AI 回應產生器（依 system prompt 判斷類型）
   - `window.fetch` 攔截器（接管所有 `__DEMO__` URL）
   - 展場橫幅注入（含「重新開始體驗」按鈕）
3. **VoiceRecorder**：假裝錄音 5 秒，不需真實麥克風
4. **handleLogout**：登出 = 清空 localStorage 重新體驗

正式版的更新不會自動同步到此 demo，如需同步，手動 cherry-pick `frontend/_source.html` 的變更（小心避開 DEMO PATCH 區塊）。
