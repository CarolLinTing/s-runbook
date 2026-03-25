---
tags:
  - SAA
  - MQ
  - Channel Down
  - AMQ9999
---

# SAA MQ Channel 斷線處理流程

**ServiceNow Summary 範例**: `[CRITICAL] SAA MQ Channel down on prd-saa-app-01`

!!! failure "高危險警報"
    當發生此 Incident 時，往來外部銀行的 SWIFT 電文將會卡在 Queue 中，請盡速排查！

## 🔍 1. 排查步驟
請登入 SAA Server，並執行以下指令檢查 MQ 狀態：
```bash
dsplinkmq -all

<div style="background-color: #f5f5f5; padding: 20px; border-radius: 8px; border: 1px solid #e0e0e0;">

<div style="margin-bottom: 15px;">
<label style="font-weight: bold; display: block; margin-bottom: 5px;">1. 確認的根源 (Root Cause)：</label>
<select id="rcaSelect" style="width: 100%; padding: 8px; border-radius: 4px; border: 1px solid #ccc;">
<option value="Oracle Database Archive Log 空間已滿 (ORA-00257)，導致 SAA 路由服務停擺。">內部原因：DB Archive Log 滿載</option>
<option value="對方銀行 (Counterparty) 的 MQ Channel 斷線 (AMQ2034)。">外部原因：對方銀行 MQ 斷線</option>
</select>
</div>

<div style="margin-bottom: 15px;">
<label style="font-weight: bold; display: block; margin-bottom: 5px;">2. 處置動作 (Action Taken)：</label>
<select id="actionSelect" style="width: 100%; padding: 8px; border-radius: 4px; border: 1px solid #ccc;">
<option value="登入 DB Server 執行 RMAN 腳本清除過期 Log，並重啟 SAA Routing Service。">執行清理腳本 + 重啟 SAA</option>
<option value="已清除本地端的 Dead Letter Queue，並發信聯繫對方銀行 IT 支援團隊。">清除 DLQ + 聯繫對方銀行</option>
</select>
</div>

<div style="margin-bottom: 15px;">
<label style="font-weight: bold; display: block; margin-bottom: 5px;">3. 目前狀態 (Status)：</label>
<select id="statusSelect" style="width: 100%; padding: 8px; border-radius: 4px; border: 1px solid #ccc;">
<option value="已發送 Test Message 驗證成功，系統恢復正常，請求結案 (Resolved)。">✅ 系統恢復正常 (準備結案)</option>
<option value="等待對方銀行修復，Incident 狀態改為 Pending Vendor。">⏳ 等待外部修復 (Pending)</option>
</select>
</div>

<button onclick="generateNotes()" style="background-color: #0052cc; color: white; border: none; padding: 10px 20px; border-radius: 4px; cursor: pointer; font-weight: bold; margin-bottom: 15px;">⚙️ 產生 Resolution Notes</button>

<textarea id="outputArea" rows="4" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 4px; font-family: monospace; margin-bottom: 10px;" placeholder="產生的結案紀錄會顯示在這裡..."></textarea>

<button onclick="copyToClipboard()" style="background-color: #28a745; color: white; border: none; padding: 8px 16px; border-radius: 4px; cursor: pointer; font-weight: bold;">📋 一鍵複製到剪貼簿</button>

</div>

<script>
function generateNotes() {
var cause = document.getElementById("rcaSelect").value;
var action = document.getElementById("actionSelect").value;
var status = document.getElementById("statusSelect").value;
var finalString = "[Root Cause] " + cause + "\n[Action Taken] " + action + "\n[Status] " + status;
document.getElementById("outputArea").value = finalString;
}
function copyToClipboard() {
var copyText = document.getElementById("outputArea");
if (copyText.value === "") { alert("請先點擊「產生 Resolution Notes」按鈕！"); return; }
copyText.select();
copyText.setSelectionRange(0, 99999);
navigator.clipboard.writeText(copyText.value).then(function() {
alert("✅ 結案紀錄已成功複製！請直接去 ServiceNow 貼上 (Ctrl+V)");
}).catch(function(err) { alert("複製失敗，請手動框選複製。"); });
}
</script>
