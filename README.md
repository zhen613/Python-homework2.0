# 智慧養殖水質監測與預警系統

## 專案簡介
專案目標： 本專案旨在利用政府開放資料，為水井村建立一套智慧養殖水質監測與預警系統。透過監測關鍵水質參數，系統能即時發出風險警報，協助漁民快速應對水質惡化，降低漁業損失。<br>
核心功能： 監測溶氧量和pH值與氨氮三個關鍵參數，一旦超出養殖魚類的健康範圍，即時發出警戒通知與建議行動。

### 理由說明
應用方向:智慧養殖,直接契合在地可能的產業需求，解決傳統養殖中因水質變動導致的「翻池」問題。<br>
開放資料集河川水質監測資料（河川水質監測.csv）：
1. 核心指標：包含溶氧量（DO）、pH 值、氨氮（NH3​-N）等關鍵指標，這些是影響魚蝦生存的直接因素。<br>
2. 預警價值：可建立明確的警戒閾值（例如 DO<4.0 mg/L），提供高時效性的預警。<br>
3. 應用擴充性：本程式架構可輕鬆切換至政府提供的即時水質 API 或村落自建的 IoT 數據源。<br>

程式碼見 
```Python
import pandas as pd
import numpy as np
data = {
    '河川名稱': ['舊濁水溪'], 
    '監測站名': ['福寶橋'], 
    '採樣日期': ['106.07.11'], 
    # 實際數值：DO 4.1, pH 7.6, NH3-N 2.31
    '溶氧量(mg/L)數值': [4.1], 
    'pH數值': [7.6], 
    '氨氮(mg/L)數值': [2.31] 
}
df_simulated = pd.DataFrame(data)

# --- 養殖警戒參數設定 (可根據不同魚種調整，此處為常見淡水/鹹水魚標準) ---
DO_CRITICAL_LOW = 4.0   # 溶氧量警戒值 (低於此值有缺氧風險)
PH_CRITICAL_LOW = 6.5   # pH下限 (低於此值易酸中毒)
PH_CRITICAL_HIGH = 8.5  # pH上限 (高於此值易鹼中毒)
NH3N_CRITICAL_HIGH = 0.5 # 氨氮警戒值 (高於此值有毒性累積風險)

def check_water_quality(df_record):
    '''檢查水質參數是否超出養殖警戒值，並返回警報列表。'''
    alerts = []
    
    # 提取參數數值 (取第一筆紀錄)
    do_val = df_record['溶氧量(mg/L)數值'].iloc[0]
    ph_val = df_record['pH數值'].iloc[0]
    nh3n_val = df_record['氨氮(mg/L)數值'].iloc[0]
    
    # 1. 溶氧量 (DO) 檢查
    if do_val < DO_CRITICAL_LOW:
        alerts.append(f"🔴 溶氧量(DO)過低: {do_val:.2f} mg/L (警戒線: < {DO_CRITICAL_LOW})")
        
    # 2. pH 檢查
    if ph_val < PH_CRITICAL_LOW or ph_val > PH_CRITICAL_HIGH:
        alerts.append(f"🟡 pH值異常: {ph_val:.1f} (警戒線: {PH_CRITICAL_LOW}~{PH_CRITICAL_HIGH})")
            
    # 3. 氨氮 (NH3-N) 檢查
    if nh3n_val > NH3N_CRITICAL_HIGH:
        alerts.append(f"🚨 氨氮(NH3-N)濃度過高: {nh3n_val:.2f} mg/L (警戒線: > {NH3N_CRITICAL_HIGH})")
        
    return alerts

alerts = check_water_quality(df_simulated)

print("--- 智慧養殖水質監測結果 ---")
print(f"**監測站點**: {df_simulated['監測站名'].iloc[0]} (模擬水井村監測點)")
print(f"**採樣日期**: {df_simulated['採樣日期'].iloc[0]}")
print("=============================")

if alerts:
    print(f" **發現 {len(alerts)} 項警戒！請立即處理！** ")
    for alert in alerts:
        print(f"* {alert}")
    print("\n👉 **漁民建議行動**: 立即檢查曝氣設備、調整pH值，並考慮部分換水以降低氨氮濃度。")
else:
    print("✅ 水質參數正常，請保持監測。")
```
# OUTPUT

智慧養殖水質監測結果
監測站點: 福寶橋 (模擬水井村監測點)
採樣日期: 106.07.11
=============================
🚨 **發現 1 項警戒！請立即處理！** 🚨
* 🚨 氨氮(NH3-N)濃度過高: 2.31 mg/L (警戒線: > 0.5)

👉 **漁民建議行動**: 立即檢查曝氣設備、調整pH值，並考慮部分換水以降低氨氮濃度。
