---
title: MTPV（最大電壓轉矩比）
tags: [馬達, 控制策略]
description: "Maximum Torque Per Voltage，在電壓限制下，選擇最優電流角以在深度弱磁區獲得最大轉矩。"
---

## 一句話定義

Maximum Torque Per Voltage，在電壓限制下，選擇最優電流角以在深度弱磁區獲得最大轉矩。

## 物理意義 / 工程意義

在極高速或電感較大時，電流限制不再是瓶頸，MTPV 軌跡取代 MTPA 成為主動約束。

## 關鍵公式或參數

工作點沿電壓限制橢圓移動，∂T/∂(id)=0 at constant |V|

## 相關知識點

[[電磁設計/控制策略/FW|FW]]、[[電磁設計/控制策略/MTPA|MTPA]]

## 設計注意事項

MTPV 區的轉矩對電流角非常敏感，需精確的電感參數，否則容易失穩。
