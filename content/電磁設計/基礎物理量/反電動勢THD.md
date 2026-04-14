---
title: 反電動勢 THD
tags: [馬達, 電磁設計]
description: "反電動勢波形中各次諧波成分的均方根值與基波之比，描述波形偏離正弦的程度。"
---

## 一句話定義

反電動勢波形中各次諧波成分的均方根值與基波之比，描述波形偏離正弦的程度。

## 物理意義 / 工程意義

THD 越高，轉矩漣波越大，振動與噪音越嚴重。高品質馬達通常要求 THD < 5%。

## 關鍵公式或參數

THD = √(ΣVh², h≥2) / V1 × 100%

## 相關知識點

[[電磁設計/基礎物理量/轉矩漣波|轉矩漣波]]、[[電磁設計/基礎物理量/頓轉轉矩|頓轉轉矩]]

## 設計注意事項

可透過斜槽、優化磁極形狀或分數槽設計降低 THD。

## 相關文章

→ [How to Measure Motor Back-EMF THD with FFT](https://0zhen.github.io/posts/how-to-measure-motor-back-emf-thd-with-fft/)

→ [FFT 頻譜分析完全解析：窗函數、補零與 THD 計算](https://0zhen.github.io/posts/2026-03-23/)
