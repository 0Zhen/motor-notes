---
title: PDIV / PDEV / RPDIV
tags: [馬達, 絕緣系統]
description: "PDIV：首次偵測到 PD 的電壓；PDEV：PD 消失的電壓；RPDIV：PD 穩定重複出現的電壓。"
---

## 一句話定義

PDIV：首次偵測到 PD 的電壓；PDEV：PD 消失的電壓；RPDIV：PD 穩定重複出現的電壓。

## 數值關係

```
PDEV < PDIV ≤ RPDIV < BDV（絕緣崩潰）
```

PDEV < PDIV 因為存在**遲滯效應**：放電一旦啟動，維持比重新啟動容易。

## 工程意義

對變頻器驅動馬達而言，RPDIV 比 PDIV 更具實務意義——RPDIV 代表 PD 穩定在每個交流週期重複，持續侵蝕絕緣。

## 相關知識點

[[絕緣系統/局部放電|局部放電（PD）]]

## 相關文章

→ [高壓絕緣與局部放電：從磁線規範到 PD 測試一次搞懂](https://0zhen.github.io/posts/high-voltage-insulation-pd/)
