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