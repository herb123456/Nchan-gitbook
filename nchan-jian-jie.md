# Nchan 特色

## Nchan 簡介

Nchan是Nginx與Channel兩個字的新造字，顧名思義Nchan是一個Nginx的module，所以帶有Nginx的可擴展性，與多種服務結合的高彈性，甚至可輕鬆與現有後端程式結合，並支援將訊息暫存在記憶體、檔案或Redis內，不僅提高訊息傳遞效率，與Redis Cluster結合更可達到無縫水平擴展的強大韌性。

Nchan也同時支援，在上一章節所有提到的即時通知系統可供選擇的技術，包含Polling、Long Polling與WebSocket，讓不管新的還是舊的瀏覽器（Client），都能完全用一套伺服器與流程即可實現即時通知系統。

## 為何使用 Nchan

1. 支援協定眾多
2. 簡單易用
3. 與Nginx完美結合
4. 保證單一channel下絕對不會有重複訊息或漏失訊息的情況
5. 可同時訂閱上百個channel
6. 提供hooks跟callback可輕易與現有程式結合
7. 搭配Redis可水平擴展
8. 若與Redis Cluster搭配則可達到自動容錯與高可用性



